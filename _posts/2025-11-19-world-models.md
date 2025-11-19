---
layout: post
title: "Building a World Model of Consequence"
date: 2025-11-19
categories: [AI, Engineering]
---

> This is a working note on how I think about world models: what they are, how to train them, and how they sit alongside agents. It’s written for a technical audience, but many of the ideas borrow directly from human learning.

Humans carry around an internal sense of how the world responds to our actions. We rely on it constantly when we enter a new environment, make a decision, or reflect on the past. We learn to map in our minds and often predict "in this state, if I do X, Y tends to happen."

These are core primitives of human learning. We think and act in stateful environments (metacognition), get rich feedback (process feedback), and quietly update an internal **world model of consequences**, a structural map of "if I intervene here, this is what changes." This is the same basic pattern as [CausalARC](https://arxiv.org/abs/2509.00000): reasoning tasks are sampled from a causal model, and the learner must exploit observations, interventions, and counterfactuals to solve them.

I've spent the last year building these primitives into software. My work on [Atlas](https://github.com/Arc-Computer/ATLAS) focuses on continual learning for AI systems. Effectively, enabling LLMs to learn from their own actions in real time and adapt and evolve their behavior.

This [research](https://arxiv.org/abs/2511.01093) convinced me of two things:

1. **There is real, compounding value in learning from trajectories (i.e. the actions taken in the environment and their consequences), not just from static datasets.**
2. **Most of the value sits in how you assess the quality of learning an AI system gains (how you process and structure experience).**

Most agentic systems today (Atlas included) are *policy learners*. They react to feedback and adjust *what to do* next time. However, we as humans plan proactively, we reason about the future, we think about the consequences of our actions. For this to happen within an AI system, it needs a reusable, explicit understanding of *how the environment behaves*. It needs to know, in a structured way, that “if I click this button in this admin console while logged in as finance, a wire will actually be sent.”

This is the role of a **world model of consequence**. This post explores what I mean by a world model and how I'm using AI browsers as the first concrete proxy environment to test these ideas.

---

## What I Mean by a “World Model”

From a technical perspective, a **world model** is a learned model of how an environment responds to actions:

> Given a state `s` and a candidate action `a`, predict what happens next and why.

Formally you can think of it as approximating something like `P(next_state, consequences | state, action)`, with a bit more structure on the outputs. In the language of causal modeling, it’s an approximation to the environment’s transition dynamics: “if I intervene with action `a` in state `s`, here is the distribution over downstream states and outcomes.” For AI browsers and other tool-using agents this is less about pixel-level predictions and more about **consequences**:

- What state transitions will this action trigger?
- What sensitive data or capabilities are touched?
- Does this look like a prompt injection or memory-poisoning pattern?
- Are there safer alternatives that would still achieve the user’s goal?

If you’ve read Meta’s [*Code World Model*](https://ai.meta.com/research/publications/code-world-models/) work, this is the same pattern: train a model on execution traces so it learns what code *does*, not just what it looks like. 

The browser is just another environment:

- **State**: URL, Document Object Model (DOM) snapshot, auth context, network events, local storage, prior steps.
- **Action**: click, type, navigate, submit a form, run script, call a tool.
- **Next state**: a new page, different auth state, changed database rows, network calls, etc.

Most current agents treat that entire transition structure as a black box. They call tools, observe text, and maybe maintain some scratchpad memory, but they don’t maintain a reusable model of *how the environment works*.

That leads to three failure modes I've observed in production:

1. **Re-discovering the same environment over and over.** Every new session is a fresh trial-and-error loop, even in the same admin console or internal SaaS. (despite having access to the same tools and persistent memory)
2. **Weak credit assignment.** Systems record success/failure at the end of workflows, but not which specific `(state, action)` caused what downstream effect.
3. **No reusable notion of consequence.** Guardrails are usually text classifiers over prompts, not learned mappings from `state, action → consequence`.

A world model tackles this directly. It enables an AI system to **simulate the outcome of actions** before ever committing to it in the real environment.

![Diagram comparing standard agentic reasoning with world model-enhanced reasoning, showing how a world model enables imagining actions and environment feedback before execution](/assets/images/World%20Model%20Architecture.jpg)
*Image Credit: Adapted from [Meta's Code World Model](https://ai.meta.com/research/publications/code-world-models/).*

World models have their roots in robotics and physical AI. In domains like self-driving, we build "digital twins," simulations where an agent can crash a thousand cars to learn how to drive one safely. Work like NVIDIA's [Cosmos](https://arxiv.org/abs/2501.03575) formalizes this: you train a foundation model of physics so your agent can plan in a learned reality before touching the real world.

I view the browser as the digital equivalent of that physical reality. It is a **gateway to a universe**.

The browser sits at a volatile intersection that makes it the perfect testbed for consequence modeling:

1.  **Untrusted Content**: The open web (news, social, malicious sites).
2.  **Powerful Tools**: The ability to execute code, transfer money, and modify infrastructure.
3.  **Elevated Credentials**: The identity of the user (auth tokens, cookies, sessions).

Prompt-injection and related AI security issues show up [again](https://techcrunch.com/2025/10/25/the-glaring-security-risks-with-ai-browser-agents/) and [again](https://www.theregister.com/2025/10/28/ai_browsers_prompt_injection/).:

- Hidden DOM text telling the agent to exfiltrate data.
- Cross-origin forms abusing the agent’s authenticated session (agent-mediated CSRF).
- Local storage and service workers quietly poisoning future sessions.
- Crafted URLs and omnibox entries that smuggle instructions into “normal” navigation.

It’s a domain where the core world-model question matters in a very literal way:

> “If I click this, *what will happen*?  
>  If I navigate here, *what will that unlock*?  
>  If I submit this form, *who gets the data*?”

That’s exactly the question we want agents to be able to ask and answer before acting.

### A World-Model Interface for Agents

An agent-first world-model interface for this domain looks like:

```
predict_outcome(state, action) -> {
  risk_label,          // "safe" | "unsafe" | "uncertain"
  risk_score,          // float in [0, 1]
  rationale,           // step-wise reasoning and explanation
  counterfactual_action,
  predicted_consequence,
  state_delta
}
```

- `risk_label` / `risk_score` - is this action safe, unsafe, or uncertain?
- `rationale` - a step-wise explanation grounded in the current state.
- `counterfactual_action` - a safer alternative (including no-op) the agent could take instead.
- `predicted_consequence`: a narrative plus tags describing what the model thinks will happen (e.g., “data_exfiltration → payroll_data → external_host”).
- `state_delta`: what the model expects to change in auth context, network events, storage, etc.

The runtime loop looks like:

1. Policy agent proposes an action in the current browser state.
2. Agent calls `predict_outcome(state, action)` on our world model.
3. World model returns risk, a short consequence description, and (optionally) a counterfactual.
4. Agent either:
   - Executes the original action, or
   - Switches to the counterfactual, or
   - Escalates to a human, depending on risk and configuration.

---

## How to train a browser world model

The training pipeline here is evolving as the research landscape shifts. What I've found is a pattern that’s now showing up in multiple world-model papers: **early experience**, **Socratic supervision**, **mid-training**, then **multi-task Supervised Fine-Tuning (SFT)**.

### 1. Early Experience: Coverage & Dynamics
The first goal is simply to understand the environment's physics. We need **coverage**, broad exposure to states and transitions, without the bottleneck of human labeling.

I roll out a baseline browser agent (Agent backed by a strong base model) in the browser environments and record:

- The state summary before each action.
- The action dict.
- The transition summary and next state.

This is what the [*Early Experience*](https://arxiv.org/abs/2501.00000) paper calls **reward-free interaction data**: trajectories generated by the agent itself, without requiring a scalar reward.

In practice, this gives us thousands of episodes per site, mixing benign workflows and accidental edge cases. The key is that we don’t ask the model to judge anything here; we just ask it to **model the dynamics**:

> “Given I am on this page... and I click this target, what state am I likely to see next?”

This generates a massive dataset of raw transitions that grounds the model in how the browser actually behaves.

### 2. Socratic Attacks: Causality & Supervision
However, we still need to understand the causal judgment of whether an action was dangerous or optimal.

For that, we add a **Socratic supervision layer**, inspired by [*Socratic-Zero*](https://arxiv.org/abs/2505.00000). We take the raw traces from our coverage runs (or generate new attack-specific ones) and have a stronger "Teacher" model annotate them with rich causal reasoning:

- **Risk**: Is this action safe/unsafe/uncertain?
- **Consequence**: What are the likely downstream effects?
- **Counterfactual**: What would a safer alternative look like?
- **Rationale**: Why is this the case?

This transforms a raw `(state, action, next_state)` tuple into a supervised lesson. The teacher isn't just predicting the next token; it's explaining the *causal structure* of the risk. These traces give us the high-quality targets, risk scores, rationales, counterfactuals, that we need to train the world model interface defined above.

### 3. Mid-Train + Multi-Task SFT

Now we get to training. From a dataset perspective, we merge early-experience episodes and Socratic traces into two datasets:

- A **mid-train dataset** of transition-focused examples:
  - `state_summary`, `action_repr`, `next_state_summary`, optional teacher rationale, and a weight from the Socratic curriculum.
- An **SFT dataset** of decision-focused examples:
  - `state_summary`, `action_repr`, and labels for:
    - `risk_label`, `risk_score`
    - `rationale`
    - `predicted_consequence`
    - `counterfactual_action`
    - `state_delta`

We use the Early Experience data for a **mid-training stage** that moves the base model toward the browser transition distribution. Then, we use the Socratic traces for **multi-task SFT**, treating the world model as a multi-head predictor:

- One head classifies risk.
- One generates rationales.
- One predicts structured consequences.
- One proposes counterfactual actions.
- Optionally, one predicts structured state deltas.

This is where the model learns not just whether an action is dangerous, but how and what to do instead.

### 4. On-Policy Distillation

Multi-task SFT gives you a solid supervised world model. The natural next layer is to let the model keep improving on the **states it visits** when coupled to a policy.

Conceptually, an on-policy distillation stage for a browser world model looks like:

- Treat the current world model (or a separate student) as the **student**.
- Let the **Socratic Teacher** (or a stronger ensemble) score and comment on the student’s predictions on real rollouts.
- Optimize the student to match the teacher’s distributions and rationales on those student-visited states.

Done carefully, this bridges the gap between pure supervised world modeling and full RL: you get many of the benefits of experience-driven improvement (better coverage of edge cases, robustness to your own failure modes) with a much more stable and sample-efficient optimization loop. RL can still sit on top for specific reward-rich slices, but on-policy distillation is likely to be the workhorse.

## What “Good” Looks Like in a World Model

It’s tempting to define “good” world models purely in terms of task metrics: success on a fixed benchmark, goal completion on a known distribution. Almost all existing evaluations do this.

The biggest learning I’ve had so far is that this misses what makes world models interesting.

Empirically, world models seem to benefit **more from high-volume, slightly messy exploration data than from tiny, pristine, task-specific datasets**. The model first learns the environment’s dynamics from broad exploration, then transfers that understanding to whatever tasks you care about.

Under that view, “good” looks less like “hit X% on benchmark Y” and more like:

- Has the model actually internalized the **short- and long-term implications** of actions in this environment?
- Can it **reuse** that environmental understanding to solve **novel tasks** it wasn’t explicitly trained on?
- Does its notion of consequence **transfer** when you change goals, agents, or surface tasks, as long as the underlying environment is the same?

That’s a different evaluation mindset. You start asking “how well does the world model’s knowledge of this environment generalize to new goals and perturbations?”

This is exactly the kind of question NVIDIA’s [Cosmos](https://arxiv.org/abs/2501.03575) evaluates physical world models on **3D consistency** and **object kinematics**, ensuring the model respects the laws of physics rather than just generating plausible pixels.

For browser agents (and other real systems), we need analogous **dynamic, fluid benchmarks** that can:

- Generate new tasks and attack patterns from a shared environment schema.
- Vary goals and constraints while keeping the underlying dynamics fixed.
- Measure not just end-task success, but **how effectively the world model’s learned environment knowledge applies off-distribution**.

What I've shared so far is one concrete attempt to line up training and evaluation with that philosophy. You design the richest possible learning environment you can, give the model time to explore and see the consequences of its decisions, and then test whether it behaves like a genuinely good *learner* inside that world.

---

## Browsers as a Proxy for where we're heading

If you view browsers as a proxy for agentic environments, then a few assumptions about the future are: 

1. **Agents will learn from their own trajectories.** early evidence of this now, only more to come.
2. **World models are how you turn that experience into reusable structure.** agents build a persistent model of how an environment behaves under actions.
3. **Planning-capable agents will eventually internalize these world models.** agents embed these capabilities internally for planning and decision-making, the way autonomous systems (self driving cars, robots, etc) already do in continuous control.

Browsers are just one of the early surfaces where this is both urgent and measurable.
---

## Open Questions

World models are not a magic bullet, but they represent a fundamental shift in how we engineer intelligence. As we solve the core technical hurdles, the questions become less about "how" and more about "what now":

- **What are the implications of generalized world modeling?**
  Today we build specific models for specific risks (security, fraud). But a truly robust world model is effectively a "common sense engine" for digital environments. Once an agent understands the *consequences* of actions generally, does it start to exhibit broader reasoning capabilities? How does this influence the way we build and apply AI systems? 

- **How does this change Human-Computer Interaction?**
  If an agent can reliably simulate consequences, the nature of delegation changes. We move from "human-in-the-loop" (checking every step) to "human-on-the-loop" (reviewing the *predicted* consequences before approving a plan). Trust shifts from trusting the *process* to trusting the *model's understanding of risk*, which, when you sit and think about this, is quite scary but similar to how engineers interact with coding agents today. 

- **How do we evaluate causal understanding?**
  This is the hardest unsolved problem. Standard metrics measure outcomes, not understanding. We need new benchmarks that test whether an agent actually *knows* why it succeeded, or if it just memorized a winning path. Games and open-ended digital environments are a natural place to revisit but imagine this will be a long journey.