---
layout: post
title: "Are We Over-Engineering Intelligence?"
date: 2025-09-03
categories: [ai, agents, learning]
---

We have reached an inflection point. The first half of the AI race was about scaling. The industry placed its bet on size, and the latest frontier models are increasingly built on Mixture-of-Experts (MoE) architectures trained on tens of trillions of tokens with a diet increasingly composed of synthetic data (see [Nano 2](https://arxiv.org/pdf/2508.14444), [Kimi K2](https://arxiv.org/pdf/2507.20534) and [GLM-4.5](https://arxiv.org/pdf/2508.06471)). The post-training process has become an arms race of its own, with immense compute dedicated to complex, multi-stage reinforcement learning and iterative distillation.

This approach has produced powerful models. It has also created a critical vulnerability. Recent research forces us to ask what we are actually buying with these billions spent on RL. Does it expand a model's core reasoning capacity, or does it merely improve the sampling of knowledge the model already possesses? If Yue et al. (2025) are correct, we are spending vast resources to [sharpen existing skills, not create new ones](https://arxiv.org/pdf/2504.13837).

**We are building systems that constrain models with the latent ability to acquire skills at a superhuman rate.**

*TLDR: The [economics of AI](https://ethanding.substack.com/p/ai-subscriptions-get-short-squeezed) has caused us to build ever-more-complex harnesses (over-engineered systems) around incredibly powerful learners (the models), when we should be building better classrooms. We are focused on the wrong problem, adding more complexity to refine existing knowledge, while the opportunity is in creating a simpler architecture that unlocks a model's innate ability to learn.*

This brings us to the second half of the AI story: the AI-AI loop, where systems learn from each other and efficiently build and refine understanding through active interaction with an environment. 

The last mile of sequential tool-calling will be solved; the next generation of frontier models is already demonstrating [near-perfect execution](https://artificialanalysis.ai/articles/gpt-5-benchmarks-and-analysis) on complex tool use. But our industry is not in the business of selling tool-calling; we are in the business of selling intelligence. 

The real challenge is not execution, but learning. This is the [Judgment Gap](https://www.arc.computer/blog/Navigating-Dual-Control-Environments): the disconnect between an agent's ability to plan a solution and its ability to apply collaborative judgment in implementing it. 

This gap persists even as models improve, manifesting as stark performance drops in collaborative benchmarks like [τ²-bench](https://arxiv.org/pdf/2506.07982) and [MCP-Bench](https://papers-pdfs.assets.alphaxiv.org/2508.20453v1.pdf) where success requires not just tool use, but genuine multi-step, cross-domain coordination.

The industry’s answer has fractured into two distinct camps, both products of the same over-engineering mindset. 

1. **Domain-specific reinforcement learning** - where compute is dedicated to complex, multi-stage RL to sharpen an agent's skills for a narrow set of tasks. While powerful, this creates specialized models that struggle to generalize and require retraining as the business environment changes.

2. **Orchestration (i.e. the multi-agent system)** - which attempts to brute-force reliability by adding more agents, more communication, and more complexity. While hierarchical, orchestrator-led systems have shown significant value in code (claude code, Codex, etc), the world operates outside the IDE. The default sub-agent architecture often turns into confused assistants or introduces numerous points of failure from task decomposition, wasting compute on coordination rather than problem-solving. 

We believe this is the wrong frame. Agent reliability is a learning problem that demands a better, [simpler architecture](https://www.interconnects.ai/p/contra-dwarkesh-on-continual-learning). 

#### **Why We’re Building Multi-Agent Systems Wrong**

To be clear, this critique is not aimed at all multi-agent systems. Hierarchical, orchestrator-led approaches have shown real value for breadth-first, parallelizable problems. The world has greatly benefitted from Claude Code. [Anthropic’s sub-agent architecture](https://www.anthropic.com/engineering/multi-agent-research-system) successfully uses a lead agent to coordinate parallel [sub-agents](https://docs.anthropic.com/en/docs/claude-code/sub-agents) for complex research, outperforming even a single, more powerful model. But these are architectural exceptions that prove the rule. They succeed by imposing a structure, a hierarchy, that the flat, peer-to-peer swarm lacks. The default failure mode remains in a lack of [shared context that leads to compounding errors](https://cognition.ai/blog/dont-build-multi-agents). This is compounded as the cost of agentic workflows skyrockets, the sheer compute wasted on coordination makes the swarm an economic dead end.

Just as a [single vector embedding has theoretical limits on what it can represent](https://papers-pdfs.assets.alphaxiv.org/2508.21038v1.pdf), a swarm's ability to reason is capped by its need to collapse complex strategies into a unified plan.

The conversation needs to be reframed. The goal is not only capability; but the rate of learning (this is where evals turn to vibes and you feel the AGI). Our definition of AGI is the **speed of skill acquisition**. This is the only metric that matters for building truly autonomous and reliable systems.

![Curve of AI Capability showing progression from General Reasoning to Continual Learning with skill acquisition over time](/assets/images/curve.png)

### **An Architecture for Learning**

The alternative is a master-apprentice workshop, grounded in live, continuous knowledge distillation. A [**Teacher-Student framework**](https://sakana.ai/rlt/) is an architectural choice designed to maximize the speed of learning. We’ve seen this approach align with emerging research on [hierarchical models](https://arxiv.org/pdf/2506.21734) and [robotics](https://nvidianews.nvidia.com/news/nvidia-isaac-gr00t-n1-open-humanoid-robot-foundation-model-simulation-frameworks).  

* The **Teacher** is our high-level, "slow" reasoner. Its job is not to perform the task, but to think. It forms a high-level strategic plan. It understands the *why*.

* The **Student** is the low-level, "fast" executor. It takes the Teacher's strategy and masters the tactical implementation, the tool use, and the messy details of interaction with the world. It learns the *how*.

This is a live learning loop that builds persistent skills into the model's parameters, rather than providing temporary access to information in a context window. It is the difference between an agent that can access a library and an agent that possesses the skill of reading. Every token is spent on either high-leverage strategic reasoning or efficient, task-focused execution. 

Nothing is wasted.

Abstract principles (the Teacher's domain) provide the priors that enable rapid, sample-efficient learning of specific instances (the Student's domain). 

### **Knowledge ≠ Wisdom**

![DIKW Pyramid illustrating the hierarchy from Data to Information to Knowledge to Wisdom](/assets/images/Pyramid.png)  
The true power of this architecture is how it moves an organization up the [**Hierarchy of Insight.**](https://faculty.ung.edu/kmelton/Documents/DataWisdom.pdf) 

* An ounce of information is worth a pound of data. 

* An ounce of knowledge is worth a pound of information. 

* An ounce of understanding is worth a pound of knowledge. 

Teacher-Student pairs are designed to create **Knowledge**. The Teacher produces strategic principles, and the Student masters tactical procedures. This knowledge is structured, reusable, and becomes a permanent asset.

The goal, however, is **Wisdom** or the application of knowledge across different contexts. This emerges when you scale from a single pair to an entire organization. Imagine a CRO's "Teacher" agent masters sales forecasting and distills an insight about market headwinds. This is a learned principle, not just data. It gets shared with the CFO's "Teacher" agent, which then guides its "Student" to adjust the company's financial model. 

This is top-down wisdom transfer.

At the same time, a Sales Student learns a new tactic for handling an objection. The successful outcome is the signal. The agent autonomously shares this proven tactic with the Customer Success Student. This is the bottom-up creation of a resilient, self-improving execution layer. This is how a network of agents builds a durable "organizational memory." 

**Learning becomes an emergent property of the system.**

### **The Infrastructure for Intelligence**

The future of AI is fleets of persistent, goal-seeking agents. In this world, **verification, and the “reward engineering” it requires will replace prompt engineering as the critical bottleneck.** To enable this AI-AI future, you need a new layer of infrastructure. This is what we are building at [Arc](https://arc.computer/): a **learning substrate** for intelligent systems.

This is an active system where agents learn to [manage structured memory operations](https://papers-pdfs.assets.alphaxiv.org/2508.19828v2.pdf) and orchestrate these learning loops at the network level, balanced by an [online](https://arxiv.org/pdf/2507.19457), [in-context exploration and reflection](https://arxiv.org/pdf/2506.09026).

If AGI is the speed of skill acquisition, then an architecture that maximizes agent-to-agent knowledge distillation is the only path forward. The goal is no longer to build agents that complete tasks. The goal is to build an ecosystem of agents that can learn together. 

### **Open Research Questions**

This work opens up a new set of fundamental research questions that sit at the intersection of reinforcement learning, systems design, and organizational science. We are looking for researchers to help us solve them.

#### **The Science of Skill Acquisition & Teaching**

If the rate of learning is the true measure of intelligence, we must move beyond static benchmarks to measure how systems acquire and transfer procedural skills. 

* **How generalizable is cross-domain knowledge transfer?** What are the ideal "source" domains for training Teacher models? Does a Teacher trained on legal reasoning provide better guidance for finance tasks than one trained on pure logic puzzles?   
* **What is the optimal curriculum for a Teacher?** Instead of just one Teacher, can we create a "faculty" of specialized Teachers (e.g., a "data analysis" Teacher, a "creative writing" Teacher)? How do we orchestrate these specialists to guide a single Student on a complex, multi-faceted task?  
* **Can Students teach Teachers?** The current model is top-down. But what is the mechanism for a Student agent, which masters the messy details of execution, to distill a novel tactic *back up* to the Teacher?

#### **The Economics of Intelligence**

Our Teacher-Student architecture is designed for token efficiency, creating a new set of economic questions around the cost and value of machine-generated knowledge.

* **What is the optimal balance between online and offline learning?** Our system uses online learning for real-time preference alignment and offline RL to embed skills permanently. But when does it become more cost-effective to pause online learning and initiate a full offline fine-tuning run? We need to model the trade-offs between immediate performance gains (online) and long-term cost reduction (offline).  
* **How do we price "Wisdom"?** Our system creates durable "organizational memory." How do we quantify the economic value of a shared principle or a proven tactic that is autonomously shared across an entire agent workforce?   
* **Can we create a "market" for knowledge?** Can a Teacher agent from one organization "sell" a learned principle (e.g., a highly effective strategy for negotiating with a specific vendor) to another organization's agent network without revealing any proprietary data? 

#### **The Sociology of an Agentic Workforce**

As we build a collaborative workforce of agents, we must solve the problems of multi-agent social dynamics at scale, from goal alignment to trust.

* **How do we resolve goal conflicts between agents?** What happens when a Sales Teacher's goal (close a deal at all costs) conflicts with a Finance Teacher's goal (maintain margin)? We need to design and build protocols for goal composition and conflict resolution that allow agents to negotiate and align on a global optimum for the organization.  
* **What does a "verifier" agent look like?** We envision a hierarchy of verifier agents that act as an immune system for the network. What is the architecture for these verifiers? How do we train an agent whose sole job is to audit the reasoning and actions of other agents to ensure they align with the organization's strategic intent?  
* **Can we formalize "trust" in an agent-to-agent network?** In human organizations, trust is built over time through reliable interactions. How do we create a formal system where agents can build a "trust score" for each other based on the reliability and usefulness of the knowledge they share?  
* **What are the mechanisms for creating a resilient "organizational memory"?** How should a network of agents decide what knowledge is critical to retain, what can be compressed into a general principle, and what should be forgotten? This involves exploring trade-offs between memory fidelity, storage cost, and retrieval speed. 

If these are the questions that drive you, reach out, we believe you’ll find a home at Arc.