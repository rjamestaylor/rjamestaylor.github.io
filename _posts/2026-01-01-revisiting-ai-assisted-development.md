---
layout: post
title: "From 'Exercise Left to the Reader' to Production-Ready: Revisiting AI-Assisted Development One Year Later"
date: 2026-01-01 00:00:00 -0800
categories: [technology, ai]
tags: [claude-code, serverless, infrastructure-as-code, ai-assisted-development, aws]
excerpt: "Nearly a year ago, the TerrorTunnels VPN control API was just a Lambda function with manual setup steps. Returning to it with Claude Code transformed it into a complete infrastructure-as-code solution—and revealed how AI-assisted development has evolved."
---

Nearly a year ago, I was on sabbatical in Taipei working on a personal project I called TerrorTunnels, which a system to control my AWS-hosted OpenVPN server from an iOS app burdened with my nickname ("Robot Terror"). Like many side projects born in the age of ChatGPT, the Lambda function at its core was AI-assisted. And like many AI-assisted projects from early 2025, it came with a familiar asterisk: *some assembly required*.

*NOTE: This post is a follow-up to [AI-Assisted Development: Building Projects with Claude and ChatGPT](/technology/ai/2025/03/01/ai-assisted-development.html), written in March 2025.*

The original repository contained a working `handler.py` and a markdown file titled `api-gateway-how-to.md`, which is essentially a set of manual steps that ChatGPT had outlined but couldn't execute. Create a REST API. Add a resource. Configure Lambda proxy integration. Set up API keys. The AI could tell me *what* to do but couldn't do it *for* me, and more importantly, couldn't package it into something reproducible. Every deployment was a click-through exercise in the AWS Console.

I returned to this repo in January 2026 with a different tool: [Claude Code](https://claude.ai/code), Anthropic's CLI agent powered by Claude Opus 4.5. What happened next exemplifies how far we've come and where we are with AI-assisted development.


<div style="position: relative; padding-bottom: 56.25%; height: 0; overflow: hidden; max-width: 100%; margin: 2rem 0; border: 1px solid #333;">
  <iframe src="/assets/presentations/revisiting-ai-assisted-development/index.html" frameborder="0" allowfullscreen style="position: absolute; top: 0; left: 0; width: 100%; height: 100%;"></iframe>
</div>
<div style="text-align: center; margin-bottom: 2rem;">
  <a href="/assets/presentations/revisiting-ai-assisted-development/index.html" target="_blank">View Presentation Fullscreen ↗</a>
</div>

## The Starting Point

Here's what the repository looked like in February 2025:

```
vpn-control-api/
├── handler.py           # AI-generated Lambda function
├── api-gateway-how-to.md # Manual setup instructions
├── README.md            # Basic documentation
└── LICENSE
```

The Lambda code worked fine. It could start, stop, and check the status of an EC2 instance. But the "last mile" of actually deploying it—the API Gateway configuration, IAM roles, API key authentication, rate limiting, custom domain mapping—was a 60-step manual process that I had to repeat every time I wanted to make changes.

This was the state of AI-assisted development in early 2025: powerful code generation wrapped in manual infrastructure toil.

## One Year Later

I asked Claude Code to "develop a robust, secure, cost-efficient AWS API Gateway implementation" and let it work. Within a conversation, it had:

1. Explored the existing codebase to understand the Lambda function's interface
2. Chosen AWS SAM as the deployment framework (explaining why it was appropriate for an open-source project)
3. Generated a complete `template.yaml` with Lambda, API Gateway, API keys, usage plans, and rate limiting
4. Updated `handler.py` with proper CORS headers and API Gateway proxy integration handling
5. Created a `Makefile` for one-command deployments
6. Added test events for local development
7. Built a curl-based test harness

When the first deployment failed due to a CloudWatch logging IAM requirement I hadn't configured at the account level, Claude Code diagnosed the error and fixed the template. When I mentioned my existing custom domain, it asked clarifying questions about my setup, then added conditional base path mapping support.

The final structure:

```
vpn-control-api/
├── handler.py           # Enhanced with CORS, better error handling
├── template.yaml        # Complete SAM infrastructure
├── samconfig.toml       # Deployment configuration
├── Makefile             # Build/deploy/test commands
├── test.sh              # API test harness
├── events/              # Local testing events
├── requirements.txt
└── README.md            # Comprehensive documentation
```

One command to deploy. One command to test. Infrastructure as code that I can version, review, and reproduce.

## What Changed?

The obvious answer is "the tools got better," but that undersells the shift. What changed is the *level of abstraction* at which I can productively engage with AI assistance.

In early 2025, AI code generation was like having a very knowledgeable colleague who could answer questions and write snippets, but couldn't touch the keyboard. I was still the executor, translating natural language suggestions into clicks and commands.

In 2026, Claude Code operates more like a junior engineer who can be given a task, explore a codebase, make decisions within appropriate guardrails, and produce working code. The shift isn't just capability—it's *agency*.

## Domain Knowledge Still Matters (Maybe More)

Here's the insight that surprised me: as the tools became more capable, my domain knowledge became *more* valuable, not less.

When Claude Code proposed the SAM template, I needed to understand enough about API Gateway, IAM, and CloudFormation to review whether its choices were sensible. When it added CORS headers, I needed to know whether they were appropriate for my iOS client. When deployment failed, diagnosing whether it was a code problem or an account configuration issue required understanding AWS's layered permission model.

The work I can now delegate is execution. What I can't delegate—and what became more important—is:

- **Design judgment**: Is this the right architecture? Are these the right tradeoffs?
- **Security review**: Are these IAM permissions appropriately scoped? Is the API key strategy sufficient?
- **Testing strategy**: What scenarios matter? How do I verify this works?
- **Operational awareness**: What will this cost? How will I monitor it? What happens when it fails?

The paradox is that trusting AI with more execution requires *more* expertise to validate, not less. I couldn't have reviewed Claude Code's SAM template if I didn't understand CloudFormation. I couldn't have caught the custom domain mapping conflict if I didn't know how API Gateway base paths work.

## The Trust Gradient

I've started thinking about AI-assisted development in terms of a trust gradient:

| Task Type | 2025 | 2026 |
|-----------|------|------|
| Write a function | High trust | High trust |
| Explain existing code | High trust | High trust |
| Generate boilerplate | Medium trust | High trust |
| Create deployment config | Low trust | Medium-high trust |
| Make architectural decisions | Very low trust | Medium trust |
| Operate in production | No trust | Still no trust |

The gradient has shifted upward, but it's still a gradient. I review everything Claude Code produces before committing. I test before deploying. I don't give it production credentials.

But the *volume* of work I can delegate has increased dramatically. A task that would have taken an afternoon of documentation-reading and console-clicking now takes a conversation.

## What This Means for Side Projects

For hobbyist developers and side project tinkerers, this shift is genuinely transformative. The TerrorTunnels project isn't my day job—it's something I built to scratch an itch. The original implementation sat half-finished for months because the remaining work (API Gateway setup) was tedious enough to kill my momentum.

With Claude Code, I went from "I should really finish that API Gateway setup sometime" to "deployed and tested" in a single session. The activation energy for completing side projects has dropped significantly.

This matters because side projects are where developers learn, experiment, and build things for themselves. Reducing the friction between "idea" and "working system" means more ideas get realized.

## Looking Forward

I expect this trust gradient to continue shifting upward. The jump from 2025 to 2026 was substantial—from "code snippet generator" to "task-executing agent." The next year might bring agents that can manage deployment pipelines, respond to alerts, or coordinate multi-service architectures.

But I also expect domain knowledge to remain essential. The more powerful the tools, the more important it is to understand what they're doing and why. AI assistance amplifies expertise; it doesn't replace it.

For now, I'm enjoying the sweet spot: powerful enough to delegate significant work, constrained enough that I remain meaningfully in the loop. The TerrorTunnels VPN control API is deployed, tested, and documented—infrastructure as code that I can modify, extend, and trust.

Not bad for an afternoon's conversation.

---

*The [vpn-control-api repository](https://github.com/TerrorTunnels/vpn-control-api) is open source under the MIT license. The commit history shows the transformation from manual-setup to SAM infrastructure.*
