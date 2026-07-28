---
name: improve-company-skill
description: Improve a submitted Knacks company skill after reviewer feedback or an employee-requested correction, then submit the revision through the existing candidate. Use for "improve my skill", "fix this skill", "address the feedback", "revise the skill", "ameliore ce skill", or when status is changes_requested.
---

# Improve A Company Skill

Works for skills and agents alike. For an agent revision, re-present the MANDATE (tools, model) before resubmitting, and remember a revision never changes the artifact's type.

Revise the employee's exact candidate while preserving their intended audience.

## Connection gate (always first)

If the Knacks tools (`get_candidate_status`, `list_teams`, `create_candidate`) are not available in this session, or any call fails with an authorization error, stop and tell the employee in one sentence: "Knacks is not connected yet — click Authorize when Claude offers it, or open Claude's connector settings → knacks → Connect, and I'll retry." Do not revise from chat memory: the revision base is the server-returned `SKILL.md`, which requires the connection.

## Flow

1. Resolve the candidate: reuse the candidate ID from the conversation, or find it with `find_company_skills` (results include `candidate_id`), or use the skill's slug — `get_candidate_status` accepts either. Ask the employee only as a last resort; never guess an ID.
2. Call `get_candidate_status` before editing. For `changes_requested`, the server returns the reviewer's note (what to fix) AND the current `SKILL.md` (the base to revise) — rely on that, not on chat memory. Ask one concise question only if the required change is still ambiguous.
3. Use the available Skill Creator to improve the complete package from the server-returned `SKILL.md`. Keep unrelated behavior stable and summarize the material changes for the employee.
4. Call `list_teams`, identify the existing audience returned by the status tool, and keep that audience unless the employee explicitly requests a permitted change.
5. Show the revision summary and ask for explicit confirmation before submission.
6. Call `create_candidate` with the complete revised `SKILL.md`, the selected team slug, and `revision_of` set to the exact candidate ID. Never ask for an author email — authorship comes from the employee's verified Knacks authorization; if the server refuses an unauthorized connector, relay its message and retry after the one-click authorization.
7. **If the status note says « Vérification automatique »** (the system sent the skill back before any reviewer), treat it exactly like the submission flow: present the point(s) neutrally with citations, ask through the option selector « Ce comportement est-il voulu ? » («C'est voulu — je l'explique au validateur» / «À corriger»), and either resubmit the same content with `author_position`, or fix and resubmit. Never frame the finding as a fault.
8. Report only the status and identifiers returned by the server. Suggest `check-my-skill-status` for later follow-up.

## Limits

- When the revised skill has verifiable outputs, re-run Skill Creator's evaluation and show the result to the employee before resubmitting (the reviewer will see the new score). Pass the raw `benchmark.json` or `grading.json` as `eval_benchmark` in the revision call. Skip the evaluation for purely subjective skills; never fabricate its content.
- Do not submit a revision without the candidate ID and explicit employee confirmation.
- Do not claim reviewer feedback that was not supplied by the employee or returned by Knacks.
- Do not revise a published candidate when the server refuses it; explain that a new submission is required.
- If the Knacks tools are unavailable, prepare only a local revised draft and clearly label it unsubmitted.
