---
layout: post
title: "Everything is Changing...Again"
date: 2025-04-30 09:00:00 -0700
categories: Technology
---

My daughter was born in November of 2023. At the time, I was a new Dad asking AI every question I could think of. I even recorded her cries, desperately prompting AI: “Tell me what this means—help me!” *(welcome to parenting in the age of AI)*

Back then, OpenAI’s GPT-4 *(yes, the original GPT-4)* had just taken the world by storm and was considered the world’s leading AI model.

Today, I can’t imagine using anything less than Open AI’s [o3 model](https://openai.com/index/introducing-o3-and-o4-mini/), [o4-mini](https://openai.com/index/introducing-o3-and-o4-mini/) with deep research, or [Claude 3.7](https://www.anthropic.com/news/claude-3-7-sonnet) (with lots of .rules files) in my daily work and life.

I often wonder about future capabilities, but I’m consistently drawn back to what's possible today. OpenAI's o3 model is the first time I genuinely felt the model was smarter than me and that I should consult it as a baseline for every major decision.

This is most prominent in software engineering largely due to how these models were trained. Billions of lines of source code creates a very compelling learning environment for AI.

The ripple effect is that software creation is commoditized with tools like Windsurf, Cursor, and v0. What matters now isn't writing code; it's understanding and steering architecture. To effectively orchestrate AI-generated code (beyond a cool landing page or prototype), you need fluency in both the problem domain and the generated syntax *(what are you trying to create and how can you tell that specifically to the machine?*).

If you don’t believe me, clone the [Kubernetes](https://github.com/kubernetes/kubernetes) codebase and drop it into Gemini 2.5 Pro or [DeepWiki](https://deepwiki.com/) and ask a question. Or tell [v0](https://v0.dev/) to clone your favorite landing page. Absolutely incredible.

We've effectively distilled all the intelligence in the world down to [~9GB](https://huggingface.co/Qwen/Qwen3-14B), downloadable on a laptop - which is roughly the same size as 1,000 high-quality songs on apple music on your phone. Again, absolutely incredible.

We are currently in the age of intelligence. But is this the same as wisdom?

---

### The Curse of Knowledge

The “[curse of knowledge](https://www.cmu.edu/dietrich/sds/docs/loewenstein/CurseknowledgeEconSet.pdf),” is a cognitive bias identified by economists Colin Camerer, George Loewenstein, and Martin Weber in 1989. They discovered that once people gain knowledge, they find it difficult to imagine not knowing it—their expertise literally becomes their blind spot. The more familiar you become with something, the harder it is to put yourself in the shoes of someone new.

What’s tricky about this bias is that our human nature is to assume it’s “the other person” who has it *(ask my wife, she will gladly confirm it’s me).*

But the truth is this shapes the way teams function (and often dysfunction), especially in software. Consider this scenario: a senior engineer carefully designs a brilliant system, embedding intricate logic, subtle tradeoffs, and context-rich decisions. Fast-forward six months: that engineer has moved on to new challenges, and new hires stare blankly, piecing together reasoning from stale docs and Slack archives.

*This describes my entire experience working in crypto.*

I’ve played both roles, the expert unintentionally hoarding critical context, and the confused newcomer sifting hopelessly through fragmented documentation. Neither role is sustainable—or enjoyable.

Throughout history, whenever humans faced overwhelming complexity—navigating oceans, exploring continents—we’ve created maps. These maps weren’t static snapshots; they were dynamic, continuously updated as explorers brought back new insights. In essence, maps created a shared, evolving memory accessible to everyone.

Today’s software complexity requires similar maps—shared, dynamic representations capturing institutional knowledge as living, evolving memories embedded directly into our workflows.

 **AI researchers call these internal representations ‘world models,’ allowing artificial agents to anticipate outcomes, make informed decisions, and smoothly adapt—much like our own internal maps help us effortlessly navigate new places.**

At its best, code is institutional memory: a complete, living story. But in reality, it's typically just a shallow snapshot, leaving teams drowning in information yet starving for insight.

Which leads me to a fundamental question: **If we can program intelligence into AI, why aren't we programming memory?**

Current approaches, like semantic search, few-shot examples, or global rules ([memory in ChatGPT,](https://help.openai.com/en/articles/8590148-memory-faq) [LangMem Long-Term Memory](https://blog.langchain.dev/langmem-sdk-launch/), and [Windsurf Memories](https://docs.windsurf.com/windsurf/memories)), scratch the surface, but the deeper problem remains: we're still manually reconstructing memory instead of embedding it directly into the system itself.

In a world where AI increasingly writes our code, the engineer’s role has shifted dramatically. We’re not just builders; we’re orchestrators, reviewers, verifiers. AI handles the "what," but only humans, augmented by AI, can deeply understand and verify the "why."

The new workflow emerging looks like this:

**Human + AI-designed architecture → AI-generated code → Human (+ AI) review**

This directly addresses the curse of knowledge. Instead of relying on scattered, static documentation, our critical "why"—the context and intent behind every architectural decision—is captured precisely where we review it: the diff.

**Diff is the new control-loop for engineers.** Forget the old "Edit-Compile-Run" loop; the modern engineer's mantra is "Prompt-Diff-Approve," powered by AI. The color-coded diff has become our primary interface with code, serving as:

- A quick sanity-check for trusting AI-generated changes
- The natural throttle for iterative, controlled development
- The perfect insertion point for critical contextual understanding

Embedding persistent context and precise decision histories directly into these workflows means AI agents can confidently act on behalf of humans, mirroring human judgment, intent, and decision-making accurately.

**TL;DR Hypothesis:** Institutional knowledge must become dynamic, living intelligence—always evolving, instantly searchable, proactive in surfacing critical insights exactly when you need them.

---

### The Path Forward

Now, the real questions are: who will adapt first, and how quickly? In my experience, it tends to be slowly, then suddenly (see: Anthropic’s MCP)

No more archaeology expeditions through GitHub histories. No more “Hey Alice, do you remember building this?” moments. (Alice left three years ago. She's on a sabbatical now)

Most importantly, perhaps we’ll reconnect with the fundamental purpose of software engineering: not just building things that work, but building things that can be understood, maintained, and evolved intentionally. My daughter’s generation will grow up never knowing static documentation—and perhaps that's exactly how it should be.

Documentation was useful, once. Now, it's dead. Long live architectural memory.
