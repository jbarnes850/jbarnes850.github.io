---
layout: post
title: "Consequence Infrastructure: What Agents Need to Become Employees"
date: 2025-11-25
categories: [AI, Engineering]
---

> This is Part 2 of my notes on world models. [Part 1](/2025/11/19/world-models) covered what world models are, how to train them, and why browsers are a useful proxy environment. This piece steps back to ask: what does this change about how humans work with AI at scale?

---

## From Tools to Actors

The productivity evidence is in. Anthropic's recent analysis of 100,000 real conversations shows AI reducing task completion time by 80%. Tasks that would cost $55 in human labor, done in a fraction of the time. The gains are real and compounding.

What I keep thinking about is where this leads.

When I work with coding agents now, I focus on one thing: how quickly can I get them real-time feedback loops? Spin up a sandbox, compile, run tests, see what breaks. The agent isn't just generating code. It's reasoning about consequences by watching what happens when the code executes.

That's a different kind of capability than autocomplete. And it's already here for code. The question is what happens when we extend it to everything else:

- Agents that run support tickets end-to-end, including issuing refunds and modifying accounts.
- Agents that triage security alerts, open investigations, and draft incident reports.
- Agents that apply runbooks, adjust infrastructure, and roll back bad deploys.
- Agents that process payment approvals, verify counterparty risk, and move money.

The capability is largely here. What's missing is *how organizations trust them*.

When a human employee takes a high-impact action, there's an implicit model of consequence running in their head: "If I approve this batch while we're near our daily limit, and the counterparty defaults tomorrow, here's what happens." They weigh that against alternatives. They escalate when uncertain.

Agents don't have this yet. They react to prompts, call tools, and hope the guardrails catch mistakes. But guardrails aren't the answer. They're a stopgap. This is the gap that consequence infrastructure fills.

Most conversations about AI safety start with what to block. Input filters, output classifiers, judge models. All useful, all oriented around the same question: "How do we stop bad things from happening?" I've started asking a different question: what can we learn from forward simulation?

When an agent can predict consequences before acting, you don't just get a guardrail. You get a planning tool. The agent doesn't just avoid the bad path. It finds a better one.

The framing shift matters. Guardrails are about blocking. Consequence modeling is about enabling. It makes the best people even better at their jobs and opens up work that wasn't possible before.

A judge can tell you "this action looks risky." It cannot tell you:

- What the expected exposure is if this action succeeds.
- What downstream state changes will occur.
- What rollback would cost if you need to undo it.
- What a safer alternative would be that still achieves the goal.

For low-stakes work, basic filters are fine. For high-stakes work, you need something that reasons about consequences in the language of the environment: dollars, policies, limits, dependencies, rollback cost.

## Forward Simulation

Here's the pattern I think we're converging toward: every high-impact agent action gets a forward simulation before execution.

In [Part 1](/2025/11/19/world-models), I described the `predict_outcome` interface: given a state and action, return risk scores, rationales, predicted consequences, and counterfactual alternatives. The runtime loop routes actions based on those predictions.

What matters is that simulation happens in environment-specific terms:

- Not "this looks unsafe" (generic).
- But "this pushes counterparty exposure to 98% of daily limit, bypasses dual-approval policy, and would cost $24k to roll back if they default" (grounded).

Consequence infrastructure translates raw agent behavior into business-legible predictions about what will happen and why.

## Human-Agent Interaction

If you project forward a few years, consequence infrastructure changes the shape of how humans work with agents.

**Today**, high-stakes agent deployments look like this:

```
Agent proposes action → Human reviews action → Human approves/rejects → Execute
```

The human is reviewing what the agent wants to do, not what will happen if it does it. Every action needs a human in the loop because there's no other way to catch mistakes before they execute. The agent is a capable assistant that can't be trusted to act alone. This defeats the point. You've built an agent, but you're still doing the work.

**With forward simulation**, the loop changes:

```
Agent proposes action → World model predicts consequence → Automatic routing:
  - Low risk: Execute
  - Medium risk: Substitute safer alternative, execute
  - High risk: Escalate with full prediction context
```

The human is no longer reviewing individual actions. They're reviewing predicted consequences when they exceed risk thresholds, and tuning policies that govern automatic routing. The cognitive load shifts. Instead of asking "should I approve this click?", the human asks "do I trust the world model's assessment that this $50k exposure is acceptable given our current limits?"

**What the human sees** are outputs written in the language of business, not prompts:

- **Expected exposure**: "$47,200 if counterparty defaults within 30 days."
- **Policy flags**: `dual_approval_missing`, `near_daily_limit`, `high_risk_counterparty_unverified`.
- **Predicted state change**: "Account limit utilization moves from 72% to 98%."
- **Counterfactual**: "If we split this into two batches with dual approval, exposure caps at $23,600."
- **Confidence**: "High confidence (0.91). Similar scenarios observed 847 times in training data."

When something goes wrong, the first question becomes "what did the world model predict at the time, and why did we override it?"

**The management analogy.** This resembles how good organizations manage junior staff:

1. Junior employee proposes a plan.
2. Manager (or process) checks the plan against policies and expected outcomes.
3. Low-risk plans proceed; high-risk plans get coaching or escalation.
4. Everyone sees the same reasoning, so feedback loops actually work.

The world model plays the role of that embedded manager brain: not doing the work, but constantly asking "what will happen if you do that?" and "is there a better way?"

The difference from traditional guardrails is critical. Guardrails say "stop" or "go." A world model says "here's what happens, here's the risk, here's an alternative, and here's why." That's the difference between a red light and a navigator.

## From Observation to Intervention

When I managed teams, the thing that separated good individual contributors from people ready for leadership wasn't technical skill. It was situational awareness: the ability to think through consequences before acting, to read the moment and adjust. That's not something you can checklist.

There's a formal structure underneath this intuition. In causal inference, Pearl's Causal Hierarchy defines three levels of reasoning:

- **Observational (L1)**: What do I see? *P(Y | X)*
- **Interventional (L2)**: What happens if I act? *P(Y | do(X))*
- **Counterfactual (L3)**: What would have happened if I'd acted differently? *P(Y_x | observed)*

Most AI systems today operate at L1. They observe prompts, predict likely completions, and generate outputs. They're reactive pattern matchers that don't model what their outputs cause in the environment.

Forward simulation is the jump to L2 and L3. When an agent asks "what will happen if I execute this action?", it's asking an interventional question. When it asks "what would have happened if I'd done something else?", it's asking a counterfactual question.

Concretely:

- An agent that approves a payment because it matches past patterns (observational).
- An agent that simulates the downstream state: "If I approve this, counterparty exposure rises to X, daily limit utilization hits Y%, and rollback cost is Z" (interventional).
- An agent that also computes: "If I had required dual approval, exposure would have been capped at X/2" (counterfactual).

The interventional and counterfactual layers are what make agents trustworthy for high-stakes work.

![Diagram showing the progression from L1 observational reasoning (agent alone, opaque to humans) through L2 interventional reasoning (shared view emerges) to L3 counterfactual reasoning (full shared understanding between human and agent)](/assets/images/counterfactual.jpeg)
*The shift from pattern matching to causal simulation is also the shift from opaque reasoning to shared understanding. At L2 and L3, humans and agents see the same predicted consequences.*

This is the same pattern showing up across recent research:

- In code, the shift from modeling what code looks like to modeling what code does when executed.
- In reinforcement learning, the shift from reward-based feedback to learning from the consequences of your own actions, even in the absence of external reward.
- In world modeling generally, the shift from recognizing familiar environments to actively learning environment dynamics from context.

The common thread: turn raw experience into a reusable, cross-domain, environment-conditioned model of consequence.

If I had to name what we're building toward, I'd call it consequence infrastructure: systems that give agents (and the humans who deploy them) a reusable, environment-specific understanding of what actions will cause, how risky they are, and what alternatives exist. This is distinct from agent frameworks (how you build and orchestrate agents), observability (how you monitor what agents did), and safety classifiers (how you filter harmful content). Consequence infrastructure sits between the agent and the environment. It simulates what will happen before anything executes.

## What Good Looks Like

In [Part 1](/2025/11/19/world-models), I focused on what makes a world model good at learning: broad coverage, transfer across tasks, internalization of environment dynamics. Those criteria still matter.

For consequence infrastructure specifically, the question shifts to what makes a world model good at deployment:

1. **Grounded predictions**: Risk scores and consequences map to real metrics (dollars, violations, rollback cost), not abstract safety categories.

2. **Actionable counterfactuals**: The system says "here's a safer alternative that still achieves the goal."

3. **Cross-task transfer**: Understanding learned in one workflow (payment approvals) transfers to related workflows (refunds, limit changes) without full retraining.

4. **Calibrated uncertainty**: When the model doesn't know, it says so, and escalates appropriately.

5. **Auditability**: Every prediction is logged with enough context that a risk committee or regulator could reconstruct the decision.

The evaluation mindset shifts from "did we hit X% on benchmark Y?" to "does the world model's understanding of consequence generalize to new goals and perturbations in this environment?"

The economics follow from this. The question that matters: how do we do more work, at lower risk, for lower cost?

Safety, performance, and efficiency aren't trade-offs in this model. They're the same thing. When you can accurately predict consequences:

- You can let agents execute more actions autonomously (higher throughput).
- You catch expensive mistakes before they happen (lower exposure).
- You don't need humans to review every step (lower cost per unit of work).
- You can deploy in higher-stakes contexts where the ROI justifies the investment.

The metric that matters: cost per correct unit of work, adjusted for exposure and rollback.

Organizations that can answer this question will outcompete those that can't. They'll be able to trust agents with payment approvals, security operations, infrastructure changes, and customer account modifications while their competitors are still manually reviewing every action. This is why I think consequence infrastructure is foundational, not optional.

## Where This Goes

If forward simulation becomes standard for high-impact agent actions, a few things follow:

**Agents get deployed in higher-stakes contexts.** Organizations that can quantify expected exposure and rollback cost will be more willing to let agents operate autonomously. The bottleneck shifts from "capability" to "governance."

**Risk and compliance become first-class stakeholders.** Today, most AI deployment decisions are made by engineering and product. With consequence infrastructure, risk teams have a native interface into agent behavior. They become partners in deployment, not just auditors after incidents.

**Human-agent collaboration stabilizes.** Instead of the current pattern (excitement, deployment, incident, pullback, repeat), we get a more measured adoption curve: agents earn trust by demonstrating predictable consequences, and humans gradually delegate more as the world model proves reliable.

**The delegation surface expands.** Today, we delegate tasks where the cost of failure is low. With consequence infrastructure, we can delegate tasks where the cost of failure is *predictable and bounded*. That's a much larger surface.

A few things I'm still working through. How do you train consequence models when ground truth is rare? In games and puzzles, you can verify outcomes cheaply. In real business environments, you often don't know if a decision was "right" until much later, if ever. How do you handle distributional shift when environments change, policies update, new edge cases appear? Where's the line between consequence prediction and planning? At some point, the "consequence layer" starts looking like "the agent's brain."

The pattern I keep coming back to: every high-impact AI action should come with a forward simulation. A simulation that predicts what will change, what the exposure is, what a better alternative looks like. This is what makes delegation possible. Today, humans supervise agents because there's no other mechanism for catching mistakes before execution. With forward simulation, the world model provides that mechanism. Humans move from reviewing actions to reviewing predictions and tuning policies.

But here's the question I don't have an answer to yet:

If forward simulation becomes standard, if agents can see around corners too, what does human work *become*? The productivity gains are already reshaping how we work. Consequence modeling will reshape it further. I'm not sure what the stable equilibrium looks like, or what we'll do when the thing that made human judgment valuable is something agents can do too.

That's what I'm working through.
