# An AI QA teammate: from a ticket to executed, evidence-backed test cases

A write-up of how I designed and built my company's first AI-powered QA agent.

This is a case study, not a code repository. The implementation belongs to my employer, so what you will find here is the design: what it does, how the pieces fit, and the decisions that made it trustworthy enough to use on real releases.

## The problem

Before a single test ran, every ticket went through the same slow manual path:

1. Read the ticket and work out what it really means.
2. Find the gaps and ambiguities in the requirement, then chase answers.
3. Write a test plan, an effort estimate and the test cases by hand.
4. Upload the cases to the test management tool, run them, and gather evidence.

Steps 1 to 3 were repetitive and varied from person to person, and they produced a lot of documentation.

## What I built

An agent that takes a ticket ID and carries the work as far as executed test cases, while people stay in charge of every judgment call. It runs inside Claude Code, and is made of:

- **Slash commands** for the main workflows: generate tests from a ticket, upload to the test management tool, execute in a browser.
- **Subagents** for the autonomous jobs: gathering requirements, planning, investigating production bugs, organising evidence.
- **Skills**: written procedures the agent reads on demand (gap analysis, code search, estimation).
- **Scripts** that generate the deliverables in a fixed format, so output never depends on the model's mood.
- **MCP connections** to the ticketing system and to Playwright, so the agent uses real tools instead of guessing.

## The workflow

```
ticket ID
  -> brainstorm       (2 to 5 scoping questions)
  -> gather           ticket, backend and frontend code, product docs, git history
  -> gap analysis     code first, asks a person only about genuine business-logic gaps
  -> ticket summary   plain English        <- person approves
  -> test plan        document             <- person approves
  -> estimate         with PERT            <- person approves
  -> test cases       full detail          <- person approves
  -> CSV              stop
```

Uploading to the test management tool and running the tests in a browser are separate commands that a person chooses to run. They are deliberately not chained on, because browser runs need live credentials and an upload writes to an external system.

## Design decisions I would defend

- **Code first, questions second.** The agent resolves behaviour from the actual source and only asks people about real business-logic gaps. Every requirement in the spec points back to evidence in the code.
- **Approval gates are hard stops.** The ticket summary, test plan, estimate and test cases each need explicit approval, even if someone asks for "just the test cases quickly".
- **Writes to the ticketing system are double-confirmed.** The agent drafts in chat, a person approves the text, then it asks once more before posting. Reads are free.
- **Credentials live in memory only.** They never reach a file, a report or a screenshot.
- **Generators over free text.** Deliverables come from scripts with enforced formats (valid priorities, no repeated login steps, right-skewed estimates), not from the model writing documents by hand.
- **Plain language.** Summaries are written from a tester's point of view, not as API jargon, so non-engineers can approve them.
- **A person can steer a run.** During browser execution you can type a correction ("skip this test", "click Next, not Sign In") and the agent follows. A person's instruction always wins.

## What it produces

- A test plan document and an effort estimate workbook with PERT calculations.
- Test cases covering positive, negative, boundary and role-based scenarios.
- A CSV ready for the test management tool.
- An execution report with screenshots taken at verification points and failures. Bugs are reported in chat and never filed unless asked.

## Results

Reported on my resume, measured on the project:

- About **70% faster time-to-test**.
- About **85% less documentation overhead**.

## What this repository does not include

Source code, prompts, product or customer names, ticket data, credentials and internal tooling. If you want to talk through the design, reach out on LinkedIn.

## Author

Mayank Agrawal, Senior Software Quality Engineer.
