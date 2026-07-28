---
name: turn-work-into-company-agent
description: Create a company AGENT (Claude Code subagent) from a role or delegation pattern, with Knacks governance (team audience, security review of tools, publication). ALWAYS use this instead of writing a raw subagent file when the user wants to create or share an agent in a company/team context - "create an agent", "make an agent", "cree un agent", "turn this into an agent", "make an agent that does X for the team", "automate this role", "un agent qui review les deals" - or when a repeatable DELEGATED role emerges in conversation (something a teammate would hand off entirely, not just a method they follow).
---

# Turn Work Into A Company Agent

An agent is not a skill. A skill guides how the employee works; an agent WORKS
IN THEIR PLACE — it runs with its own tools, its own model, its own judgment.
Approving an agent means approving what it is allowed to do. Everything in this
flow exists to make that mandate explicit, minimal, and reviewable.

## Connection gate (always first)

If the Knacks tools (`find_company_skills`, `list_teams`, `create_candidate`) are not available in this session, or any call fails with an authorization error, stop and tell the employee in one sentence: "Knacks is not connected yet — click Authorize when Claude offers it, or open Claude's connector settings → knacks → Connect, and I'll retry." You may still help BUILD the agent locally in the meantime, but say explicitly that company submission waits for the authorization — never present a local draft as submitted.

## Skill or agent? (decide before building)

Offer an AGENT only when the work is a **delegated role**: a bounded job someone
hands off entirely, with a clear trigger and a verifiable output ("review every
deal before forecast", "triage the support inbox"). If the employee will stay
in the loop step by step, it is a skill — switch to `turn-work-into-company-skill`
and say why in one sentence. When in doubt, ask one interactive question:
"Should this run as a delegated agent, or guide you while you work (skill)?"

## Flow

1. Identify from the conversation: the agent's single job, its trigger, its inputs, its expected output, and its boundaries (what it must NEVER do).
2. Call `find_company_skills` with a short intent query. Prefer an existing approved agent or skill over a duplicate.
3. **Establish the mandate — least privilege, out loud.** List the tools the agent genuinely needs for its job, and ONLY those (each MCP tool by exact name, e.g. `mcp__hubspot__get_deal`). For every tool, be able to say in one line why the job requires it. No write/send tool unless the job is impossible without it — an agent that drafts is safer than an agent that sends. If no specific tools are needed, declare none: the agent inherits the conversation's context. Choose a model only if the job requires it; default is inherited.
4. Ask permission before creating anything: `This looks like a delegated role. Turn it into a company agent?` **State where it will run, in the same breath**: approved agents run in Cowork (and Claude Code) — in regular chat they appear but stay disabled. If the employee works only in chat and has no Cowork, say so honestly and propose a skill instead: a skill works everywhere.
5. After confirmation, write the complete agent file: YAML frontmatter (`name`, `description`, `tools` — comma-separated exact names, omit if none — and `model` only if justified) followed by the instruction body. The description must say WHEN the agent triggers. The body must state the job, the method, the output format, and the boundaries — including "never invent facts" and any data the agent must not touch. One file, no annexes.
6. **Show the finished agent to its author, mandate first.** Present: the tools list with the one-line justification for each, the model, then the full agent file in a readable form (artifact or equivalent). The author must be able to proofread what will run in the team's name.
7. **Run a quick evaluation by default** — announce it in one short sentence and proceed (the agent body evaluates like a system prompt on 2-3 representative cases, graded). Same rules as skills: escalate to full benchmark only on a concrete signal, show the real score and real weaknesses, never invent improvements, `eval_status: "not_applicable"` + one-line `eval_reason` if the output is genuinely subjective, warn once if the employee declines.
8. Call `list_teams` and ask the employee to choose the audience from the returned teams only — one interactive multiple-choice question, Everyone last. **Remind them: every member of that team will be able to run this agent with these tools.**
9. Never ask for the employee's email: authorship is derived from their verified Knacks authorization.
10. **Ask for explicit confirmation to submit** — the employee has now seen the mandate, the file, the score (or the reason there is none), and the audience. Only after a clear yes, call `create_candidate` with `kind: "agent"`, `title`, the full agent file as `skill_md`, exact `team_slug`, plus `summary` (one sentence: the job), `when_to_use` (the trigger), `connectors` (the MCP servers behind the declared tools), and `eval_benchmark` (the raw grading/benchmark object AS IS) or `eval_status`/`eval_reason`. Do NOT pass `files` — an agent is a single file and the server refuses annexes.
11. Report only the candidate ID, audience, author, and workflow status returned by the server. Tell the employee the reviewer will see the mandate (tools, model) and a risk scan on top of the usual review. Suggest `check-my-skill-status` for follow-up.

## Asking the employee

Every decision — the skill-vs-agent question, the initial offer, each tool kept or dropped from the mandate when the employee pushes back, the evaluation choices, the audience, the final confirmation — goes through the client's interactive option selector whenever available: one question, 2-4 short labeled options, never a bare text question. Fall back to a plain one-line question only when no selector exists.

## Proactive offer

Offer once, only when a genuinely delegated role has emerged — a bounded job with trigger, method, and verifiable output that someone would hand off entirely. Building a method together then handing it off is the right moment. Do not offer for one-off tasks, methods the employee stays inside of (that's a skill), or work that should not run unattended. Do not repeat the offer after a decline.

## Limits

- **Least privilege is not negotiable**: never request a tool "in case it helps". If the employee insists on a broad mandate, keep it but tell them plainly the reviewer will see it flagged by the risk scan and may request changes.
- An agent file is ONE markdown file. Never attach scripts or references — the server refuses `files` for agents. If the job needs scripts, it is probably a skill.
- The evaluation never blocks submission, and its content is never fabricated or edited.
- Never submit an agent the author has not seen: mandate summary + full readable file + explicit confirmation are mandatory, including for revisions (`revision_of` keeps the same kind — the server refuses a type change).
- Never invent candidate IDs, teams, authors, review states, or publication states. Report only what the server returns.
- Never ask for, accept, or pass an author email.
- If the Knacks tools are unavailable, create only a local draft and say that company submission is unavailable in this session.
