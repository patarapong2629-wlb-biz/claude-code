# What's New in Testing Tools and AI Testing — Past 30 Days (Feb 20 – Mar 22, 2026)

## The Big Theme: Agentic Everything

If there's one word defining testing news right now, it's "agentic." The industry has moved past talking about AI-assisted testing and is now shipping full agentic quality engineering platforms. Multiple vendors made major launches in the past month, and the conversation around what QA even means is getting a serious rethink.

---

## Major Platform Launches

**Tricentis had a busy March.** They launched what they're calling the first end-to-end enterprise agentic quality platform (March 10), introduced an AI Workspace for managing AI agents across the SDLC (March 3), added agentic performance testing to NeoLoad with claimed 95% faster analysis (March 10), and announced remote MCP (Model Context Protocol) server support for enterprise test automation (March 12). A Forrester study they commissioned reported 403% ROI for their SAP QA solutions. It's clearly a major push to position themselves as the dominant enterprise AI testing vendor.

**Sauce Labs launched Sauce AI for Test Authoring** (March 17), targeting automated test generation — teams can generate "reliable, maintainable tests in minutes." They also released a Real Device Access API (announced just outside the 30-day window in late February) giving more programmatic device control for mobile testing.

**TestSprite updated its agentic testing tool** (March 5, per i-programmer.info), specifically aimed at AI-native development environments where tests need to keep pace with AI-generated code.

---

## AI Testing Challenges Getting Honest Coverage

Not all the news is bullish hype. A few pieces pushed back or surfaced real problems:

- **Trust is still the blocker.** A Leapwork survey (covered by DevOps.com, Feb 20) found that AI testing adoption is being slowed specifically by trust issues — teams see the potential but won't rely on the tools. This tension keeps showing up.

- **Applitools published a notably blunt piece** (Feb 26): "What Test Execution Demands That Generative AI Can't Guarantee" — arguing that deterministic, repeatable execution is exactly what LLMs can't reliably provide, and that teams are overreaching by applying GenAI to test execution.

- **AI guardrails are creating new testing headaches.** QA Financial reported (March 17) that safety guardrails in AI systems are introducing novel validation challenges that testing teams haven't faced before — the guardrails themselves need testing.

- **"Just-in-time tests"** are being floated as a new QA pattern (QA Financial, March 19), specifically in financial services where agentic coding is generating code faster than traditional test suites can cover. Dynamic, on-demand test generation rather than maintained test suites.

---

## AI Systems Testing (Testing the AI, Not Just Testing with AI)

- **DoorDash built an LLM conversation simulator** (InfoQ, March 13) to test customer support chatbots at scale — a good concrete example of how product teams are building internal tooling to evaluate AI behavior systematically.

- **Applause presented at the Agentiq World chatbot summit** (March 19) on AI QA expertise, positioning themselves in the growing space of testing intelligent systems and conversational AI.

- **DevOps.com asked the bigger question** (March 6): "Can QA Reignite its Purpose in the Agentic Code Generation Era?" — as AI writes more code, what does QA's role become? No clean answer, but the industry is actively wrestling with it.

---

## Tools and Frameworks

- **Applitools** published a technical deep-dive on Playwright-native visual testing (March 19) — specifically how a single configuration flag can provide three different strategies for handling visual diffs, worth reading if you're managing Playwright visual test flakiness.

- **SmartBear** covered advanced object recognition approaches in test automation (March 20), comparing how enterprise tools handle UI element identification — relevant as AI-generated UIs make traditional locators less stable.

- **BrowserStack** integrated with Cursor (the AI code editor) to bring testing into the AI-native coding workflow. They also published their "State of AI in Software Testing 2026" report, noting that high-performing teams are moving from AI experimentation to measurable ROI.

- **BrowserStack released Accessibility DevTools** — catching accessibility issues during coding rather than post-deployment, integrating into the developer workflow directly.

---

## Industry / Broader Context

- **Sauce Labs published a roundup of the 20 best visual testing tools of 2026** (March 18) — useful reference if you're evaluating options.

- **Ministry of Testing** is expanding community chapters (Philippines, Manchester, Edinburgh, Newcastle) and is running MoTaCon 2026.

- **The ServiceNow piece** (Business Insider, March 18) on using internal AI pilots before customer release is indirectly relevant to testing — their approach of extensive internal dogfooding as a form of staged validation is a pattern worth noting.

---

## What to Watch

1. **MCP (Model Context Protocol) in testing** — Tricentis's remote MCP server support signals that the AI tool interoperability protocol is entering the enterprise testing stack. Worth watching how other vendors respond.

2. **Trust and reliability of AI test generation** — the tension between velocity (generate tests fast) and reliability (can you trust them?) is the defining friction point of the moment.

3. **Testing AI guardrails** — as AI systems add safety layers, those layers themselves need testing. New discipline emerging.

4. **Agentic code + testing at speed** — "just-in-time tests" and dynamic test generation are early responses to the problem of AI generating code faster than humans can write test coverage.

---

*Sources: Google News RSS, Tricentis blog, Applitools blog, Sauce Labs blog, BrowserStack blog, DevOps.com, InfoQ, SmartBear blog, Ministry of Testing, QA Financial*
