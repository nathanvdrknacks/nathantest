---
name: check-my-skill-status
description: Check the real Knacks review or publication status of a submitted company skill. Use for "where is my skill", "skill status", "has it been approved", "is it published", "ou en est mon skill", or follow-up after a Knacks submission.
---

# Check My Skill Status

Works identically for skills and agents (the server labels the candidate accordingly).

Use Knacks as the only authority for workflow status.

## Connection gate (always first)

If the Knacks tool `get_candidate_status` is not available in this session, or the call fails with an authorization error, stop and tell the employee in one sentence: "Knacks is not connected yet — click Authorize when Claude offers it, or open Claude's connector settings → knacks → Connect, and I'll retry." Never answer a status question from chat memory while the connector is unauthorized.

## Flow

1. Resolve the candidate: reuse the candidate ID from the conversation, or find it by name with `find_company_skills` (results include `candidate_id`), or use the skill's slug — `get_candidate_status` accepts either. Ask the employee only as a last resort; never guess an ID.
2. Call the Knacks `get_candidate_status` tool with that candidate ID or slug.
3. Report the returned name, status, audience, author, reviewer when present, publication PR when present, and last update.
4. Explain the next action only when it follows directly from the returned status. For `changes_requested`, offer `improve-company-skill`. For a published skill, offer to use it.

## Limits

- Never infer approval, rejection, publication, reviewer identity, or timing from earlier chat text.
- Never claim a change happened unless the server returned it.
- If the tool is unavailable, say that live status cannot be checked in this session.
