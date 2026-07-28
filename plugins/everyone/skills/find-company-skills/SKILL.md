---
name: find-company-skills
description: Find an existing company skill and prevent duplicate creation. Use for "find a skill", "do we already have a method", "approved workflow", "company playbook", "how does our team do this", "trouve le skill", or before turning work into a new company skill.
---

# Find Company Skills

The catalog covers skills AND agents (rows tagged [AGENT] are Claude Code subagents — they act with their own tools). Treat both as reusable company assets.

Search the company catalog before creating another skill.

## Connection gate (always first)

If the Knacks tool `find_company_skills` is not available in this session, or the call fails with an authorization error, stop and tell the employee in one sentence: "Knacks is not connected yet — click Authorize when Claude offers it, or open Claude's connector settings → knacks → Connect, and I'll retry." Do not improvise a fallback and never present a local inspection as a catalog search. Retry once the connector is authorized.

## Flow

1. Derive a short task intent from the conversation. Ask one clarifying question only if the intended outcome is ambiguous.
2. Call the Knacks `find_company_skills` tool with that query.
3. Present the returned matches compactly: name, status, audience, and the meaningful difference from the user's request.
4. Prefer a published or approved exact match. Offer to use it when it clearly fits.
5. If no useful match exists, say only that none was found in the queried company catalog and offer `turn-work-into-company-skill` when the employee wants to capture the method.

## Limits

- Search is read-only. Do not modify, retarget, submit, or publish a skill from this flow.
- Never invent catalog entries or conceal a returned status.
- If the Knacks tool is unavailable, do not substitute a local search: apply the connection gate above and wait for the authorization.
