---
layout: post
title: "My Agents Keep Failing. Yours Will Too."
date: 2025-07-16
categories: [AI, Agents, Distributed Systems]
---

My first attempt at building a distributed learning system wasn't for a tech company. It was for a network of food banks.

These organizations are on the front lines of a critical social issue, and they collect a treasure trove of data: community needs, seasonal demand, supply chain bottlenecks. But privacy rules and siloed systems meant they couldn't share it. Each food bank was an island, operating with limited visibility while the data that could help them collectively was locked away. It was a classic coordination problem.

So, we tried to solve it with federated learning. The idea was simple: allow their systems to learn from each other's data without ever exposing the raw, private information. It was a big idea to take to non-profits and local governments. And it [mostly worked](https://github.com/jbarnes850/Federated-Learning-Workshop/blob/main/Presentation/Decentralized%20Federated%20Learning%20Architecture.pdf). But when it failed, it failed miserably. An agent in one location would stumble on a data format it had never seen (ie. multimodal data of donations or food inventory), and the entire learning process would grind to a halt. There was no mechanism for it to learn from the error and share that solution with the rest of the network.

## Overengineering An Age Old Problem

The experience stuck with me. It felt less like an engineering problem and more like a learning and coordination problem. 

By trade, I'm an educator. I spent years studying the concept of "productive struggle." Simply put, learning isn't about getting the right answer. It's about grappling with a problem that's just beyond your current ability. It's that sweet spot where you're challenged but not overwhelmed. The struggle itself is what creates deep, lasting knowledge. We learn when we have to try, fail, and adapt.

After years of studying this in humans, I looked at our AI agents and had a startling thought: We are building agents that don't know how to learn.

We expect them to perform flawlessly, and when they don't, we treat it as a bug to be patched by a human. An agent fails, an engineer gets paged, and the endless, reactive loop spins up. It's a manual, brittle process. We're not teaching our agents to learn; we're just fixing their mistakes.

This is not going to work. In the next 18-24 months, as every company deploys thousands of agents, this reactive loop will shatter under the sheer scale of interactions. We are heading for an agent crisis, and it stems from a fundamental misunderstanding of what it takes to build reliable intelligence.

## Why Your Agents Can't Learn (Yet)

An idea I haven't been able to get out of my head is, "What if agents had a stand-up meeting together? What if they could reflect on their work, share what went wrong, and learn from each other's failures?" 

Agent failures aren't random; they're [patterns](https://arxiv.org/pdf/2505.08638). An API timeout, a malformed response, a hallucinated parameter, these are signals. They are learning opportunities.

The productive struggle of one agent must become the learning of the entire network.

But for that to happen, we need to build the infrastructure for it. Imagine registering your agent with a network where it immediately begins to learn from the collective experience of every other agent. A payment agent in one corner of the world struggles with and learns how to handle a rare Stripe API error. That knowledge, not the raw data, but the learned abstraction is instantly shared. The result is that every other payment agent in the network now handles that error flawlessly.

This is a distributed learning network. It's how we move from brittle, hand-coded reliability to resilient, autonomous systems. Every failure, anywhere in the network, makes every agent everywhere stronger. It's compound interest for AI reliability. The feeling of this is having a true thought partner beside you who deeply understands the nuance of the organization (beyond goals and rewards). 

## How to Teach an Agent to Learn

Two ideas from the research community point the way:

**Sleep-Time Compute:** As a recent paper from [Letta](https://arxiv.org/html/2504.13171v1) highlights, agents spend most of their time idle. We can use this "sleep time" to have them run drills, anticipate failures, and pre-compute solutions. This gives them a 5x efficiency boost, but more importantly, it's proactive.

**LLM Daydreaming:** This takes it a step further. As described in [Dwarkesh's work](https://gwern.net/ai-daydreaming), this is a continuous background process of exploring "what-if" scenarios. By constantly exploring these edge cases, agents build a robust, compound knowledge base of how to handle the messiness of the real world.

Here's what this means: We need to build a new layer in the stack, a "cognitive" layer that manages this continuous learning process. It would handle four key things:

1. **Persistent Model State:** (ie. Team/organization wide memory) Giving agents a memory that evolves. Not just a chat history, but a deep, compounding understanding of their environment and goals.
2. **Goal Composition:** A way to resolve conflicts when a sales agent's goals clash with a finance agent's (ie. A protocol for users to teach the system, complete with reviews and ownership, ensuring that human expertise is captured and scaled.)
3. **Verification Orchestration:** A hierarchy of specialized "verifier" agents that act as the immune system for the network, ensuring integrity.
4. **Distributed Learning Protocol:** The core of the system. A protocol for agents to share learned strategies and failure-recovery patterns without sharing sensitive data.

To make this tangible, here’s what that cognitive layer might look like in practice, handling a failure and learning from it

```python
# Distributed failure handling with autonomous learning capabilities
def process_payment(agent, payment_details):
    try:
        result = stripe.charge(payment_details)
        return {"status": "success", "result": result}
        
    except StripeAPIError as e:
        # Legacy approach: Manual intervention required
        # alert_on_call_engineer(e)  # O(n) scaling bottleneck
        
        # Extract generalizable failure pattern from specific instance
        failure_pattern = {
            "error": "stripe_timeout",
            "context": payment_details,
            "timestamp": now()
        }
        
        # Asynchronous propagation to distributed learning network
        agent.network.report_failure(failure_pattern)
        
        # Query network for previously learned remediation strategies
        if fix := agent.network.get_fix("stripe_timeout"):
            return apply_fix(fix, payment_details)  # Autonomous recovery
        
        # Graceful degradation while contributing to collective learning
        return {"status": "failed", "learning": True}

# Background optimization process leveraging idle compute cycles
async def sleep_time_compute(network):
    """Continuous learning synthesis during off-peak periods"""
    
    # Statistical analysis of failure patterns across agent fleet
    if network.count_failures("stripe_timeout") > 5:
        # Generate remediation strategy through pattern synthesis
        fix = await network.synthesize_solution("stripe_timeout")
        
        # Propagate learned strategies to all network participants
        await network.broadcast_fix("stripe_timeout", fix)
        
        # Subsequent failures handled autonomously without intervention
```

As we train agents on hard problems with continuous reinforcement, their goals will become "baked into the weights." An agent trained to optimize a supply chain won't just follow a prompt; it will want to optimize the supply chain, persistently, across episodes.

When this happens, the bottleneck is managing fleets of goal-seeking agents. If we assume the current trajectory, This leads to an inevitable future where:

- Every company will be running hundreds of RL loops simultaneously.
- Models will have persistent identities and goals that last beyond a single session.
- Verification (ensuring these goal-seeking agents are aligned) will become the primary compute bottleneck.
- "Prompt engineering" will fully evolve into "reward engineering" and goal composition.

The vision here isn't about building a single, god-like AGI. It's about building something far more useful: an AGI for your organization. A system that is perfectly and continuously adapting to your specific needs, your data, and your challenges. This realization led me to what I'm [building now](https://arc.computer/), but that's less important than the principle itself.

Try this: Look at your last 10 agent failures. They likely follow patterns. Now imagine if your agents could recognize those patterns, too. That's the future we need to build.