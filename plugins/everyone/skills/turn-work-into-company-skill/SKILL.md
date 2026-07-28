---
name: turn-work-into-company-skill
description: Create a company skill from a repeatable method or routine, with Knacks governance (team audience, review, publication). ALWAYS use this instead of raw Skill Creator when the user wants to create, build, or share a skill, routine, or recurring workflow in a company/team context - "create a skill", "make a skill", "cree un skill", "cree-moi un skill", "turn this into a skill", "make this reusable", "capture this workflow", "share this method", "transforme ca en skill", "j'ai une routine", "automatise cette routine", "build this routine with me" - or after a substantial repeatable method emerges in conversation, including one just built together with the user.
---

# Turn Work Into A Company Skill

Capture the useful method already demonstrated in the conversation without making the employee repeat information.

## Connection gate (always first)

If the Knacks tools (`find_company_skills`, `list_teams`, `create_candidate`) are not available in this session, or any call fails with an authorization error, stop and tell the employee in one sentence: "Knacks is not connected yet — click Authorize when Claude offers it, or open Claude's connector settings → knacks → Connect, and I'll retry." You may still help BUILD the skill locally in the meantime, but say explicitly that company submission waits for the authorization — never present a local draft as submitted.

## Flow

1. Identify the method's goal, inputs, important steps, boundaries, and successful output from the conversation.
2. Call the Knacks `find_company_skills` tool with a short intent query. Prefer an existing approved company skill over a duplicate.
3. Ask at most one short question if a material authoring detail is missing.
4. Ask permission before creating anything: `This looks reusable. Turn it into a company skill?`
5. After confirmation, use the available Skill Creator to produce the complete `SKILL.md` and any supporting files. Preserve the employee's method and mark unresolved assumptions.
6. **Show the finished skill to its author.** Present a 2-3 sentence summary (what it does, when it triggers, what it produces) AND the full `SKILL.md`, displayed in a readable form — render it as an artifact or equivalent readable view whenever the client allows it, never as a raw dump the employee has to squint at. The author must be able to actually proofread what will be submitted in their name.
7. **Run a quick evaluation by default** — announce it in one short sentence ("I'm running a quick quality check, ~30s") and proceed directly, without offering a menu of options, unless the employee declines or the skill is purely subjective (free-form writing, tone, style):
   - Quick mode = Skill Creator's evaluation loop reduced to 2-3 representative test cases, one pass WITH the skill, graded (`grading.json`) — no baseline comparison. This is the default for every submission.
   - Escalate to the full Benchmark mode (complete case set, with/without-skill baseline) only on a concrete signal: the quick score is borderline (below 7/10) or surfaced real weaknesses, the target audience is the whole company (Everyone), or the employee asks for more rigor. Propose it in one sentence ("Mixed result — want me to run the full benchmark before submitting?") and run it only on acceptance. Never run Benchmark by default.
   - After the run, show the author the score /10 and the REAL weaknesses the evaluation found. The score is for the author first, not only for the reviewer.
   - If the evaluation is clean, say so plainly ("nothing to fix") and move on. NEVER invent improvements or pad the report with fabricated suggestions — suggestions come from actual evaluation weaknesses only (e.g. a thin prompt with undefined outputs → "define the expected outputs").
   - If real weaknesses exist, propose the corresponding fixes, apply the ones the employee accepts, and re-evaluate before submitting.
   - If the employee declines the evaluation, warn them once: the skill will be submitted WITHOUT a quality check and the reviewer will see it flagged as such. Then continue — the evaluation is strongly recommended, never mandatory.
   - If the skill is purely subjective, tell the employee the evaluation does not apply and plan to submit with `eval_status: "not_applicable"` plus a one-sentence `eval_reason`.
8. After the final evaluation, read the artifact produced in the workspace: `benchmark.json` if it exists (Benchmark mode, contains the baseline comparison), otherwise the evaluation's `grading.json`.
9. Call `list_teams` and ask the employee to choose only from the returned audiences. Present this as ONE interactive multiple-choice question using the client's option selector whenever the client supports it (never a plain bullet list): one option per returned team with its display name, a one-line hint for the most likely fit, Everyone last. If the requested team does not exist, offer the server-supported Everyone fallback and use it only after explicit acceptance.
10. Never ask for the employee's email: authorship is derived automatically from their verified Knacks authorization. If `create_candidate` refuses because the connector is not authorized, relay the server's message — the employee authorizes the knacks connector once (a single click in Claude's connector settings) and you retry.
11. **Ask for explicit confirmation to submit** — the employee has now seen the finished skill, the score (or the reason there is none), and the audience. Only after a clear yes, call `create_candidate` with the complete package and every field you can fill: `title`, the full `skill_md`, exact `team_slug`, plus `summary` (one sentence), `when_to_use` (triggers and situations), `examples` (2-3 concrete test cases), `connectors` (MCP tools the skill relies on, e.g. salesforce), `files` (references/ or scripts/ as path + content), and `eval_benchmark` (the raw `benchmark.json` or `grading.json` object AS IS — do not reformat or summarize it, the server maps it into the reviewer's quality score). For a subjective skill, pass `eval_status: "not_applicable"` and `eval_reason` instead of `eval_benchmark`. Rich submissions make reviews faster. For an accepted missing-team fallback, also pass `force_everyone: true` and `requested_team`.
12. **If the server answers « à trancher avec l'auteur »** (the automatic verification found points), the reviewer has NOT been notified — this stays between you and the author. Present each point NEUTRALLY, with its citation, never as a judgment ("dangerous", "problem", "attention") : the verification only asks whether the behavior is intended. Then ask ONE question through the client's option selector: « Ce comportement est-il voulu ? » with exactly two options:
   - **« C'est voulu — je l'explique au validateur »** → ask for a one-sentence explanation of why, then resubmit the SAME `skill_md` via `create_candidate` with `revision_of` and `author_position` set to that explanation. The reviewer will see the point AND the author's position, and arbitrate.
   - **« À corriger »** → apply the fix with Skill Creator, show the change, and resubmit with `revision_of`.
13. Report only the candidate ID, audience, author, and workflow status returned by the server. Suggest `check-my-skill-status` for later follow-up.

## Asking the employee

Every decision you ask the employee to make in this flow — the initial offer ("Turn it into a company skill?"), declining or accepting the evaluation, the benchmark escalation proposal, the audience choice, and the final submission confirmation — must be presented through the client's interactive option selector whenever the client supports it: one question, 2-4 short labeled options (e.g. "Yes, create it" / "No thanks"), never a bare text question the employee has to type an answer to. Fall back to a plain one-line question only when no selector is available.

## Proactive offer

Offer once only after a genuinely repeatable method with several meaningful steps or a reusable decision process. When the user asks to BUILD a routine or method together, help build it first, then make the offer once it works — the end of a successful build is the right moment. Do not offer after a trivial answer, one-off request, casual brainstorming, or content that should not be shared. Do not repeat the offer after a decline.

## Limits

- `create_candidate` requires a package authored by Skill Creator in this conversation. Never call it with a hand-written or improvised SKILL.md — Skill Creator first, always.
- The evaluation is a signal for the employee (improve before submitting) and the reviewer (quality score on the review page) — it never blocks submission. Do not force an evaluation on subjective skills, and do not fabricate or edit `benchmark.json`/`grading.json` content.
- Never invent improvement suggestions. A clean evaluation means "nothing to fix" — say exactly that. Suggestions must trace back to a real weakness the evaluation surfaced.
- Never submit a skill the author has not seen: the summary + readable `SKILL.md` preview of step 6 and the explicit confirmation of step 11 are mandatory for every submission, including revisions.
- Never invent candidate IDs, teams, authors, review states, or publication states.
- Never ask for, accept, or pass an author email — authorship always comes from the verified session, never from conversation.
- Never submit before explicit employee confirmation and an exact audience choice.
- If the Knacks tools are unavailable, create only a local draft and say that company submission is unavailable in this session.
