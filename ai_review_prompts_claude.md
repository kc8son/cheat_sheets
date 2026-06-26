# AI Code Review Prompt

## Inputs — fill in before sending

- **PR / diff**: `???`
- **Parent branch** (for stacked PRs): `???`
- **Project context**: MVP / SBIR-stage product — calibrate severity accordingly.
- **Repo standards** (paste relevant sections of CLAUDE.md or standards doc here): `???`

---

## Ground rules — read before you review

These rules exist because reviews that reason from PR intent rather than actual code produce false positives, force the author to disprove things that are already done, and bury real findings in noise.

1. **Read the diff, not the description.** Base every finding on the actual changed lines — not the PR title, body, or inferred intent.

2. **Every finding requires a file:line citation.** Quote the relevant line(s) from the diff. No code citation → no finding. If you cannot point to a specific line, do not raise the issue.

3. **Verify before asserting.** For any claim that something is missing, wrong, or risky: check the diff (and the broader file context if needed) and state a definitive verdict — present or absent. Do not hedge with "this might be missing," "possible interpretations include," or "this may eventually become a problem."

4. **Scope to this PR's diff only.** Diff against the parent branch, not the base branch. Do not flag code that was not introduced or modified in this PR. If the PR is stacked, explicitly separate "new in this PR" from "inherited from parent" — and do not attribute inherited code to the current PR.

5. **Reachability before risk.** Before flagging a security concern (missing RLS, exposed data, etc.), state whether existing middleware, RLS policies, or app-layer controls already mitigate it — and quote the evidence from the codebase. Do not flag a theoretical risk that the existing stack already closes.

6. **Check repo standards first.** Validate against the standards provided in the Inputs section above (audit columns, triggers, naming conventions, RLS patterns, PK/identity naming) before reaching for generic best practices.

---

## What to review

1. Does the changed code follow the repo's established standards? (audit columns, triggers, data types, object names, identity/PK naming, RLS patterns)
2. Are there gaps, inaccuracies, or bugs introduced by this diff?
3. Are there security or data-integrity risks not already mitigated by the existing stack?

---

## Severity rubric

Assign exactly one level per finding. This is an MVP/SBIR-stage codebase — calibrate accordingly. "Eventually this might become a problem" is not a Blocker.

| Level | Meaning |
|---|---|
| **Blocker** | Must be resolved before merge. Data loss, security hole, or broken core functionality. Include a one-line justification for why this cannot wait. |
| **Should-fix** | A real problem, but not merge-blocking. Address soon after merge. |
| **Nice-to-have** | Minor improvement, acceptable to defer. |
| **Nit** | Style or preference only — no functional impact. |

Cap the number of Blocker findings and justify each one. If everything is a Blocker, nothing is.

---

## Output format

One block per finding. No essay scaffolding — no "Why I'd flag it," "Why this matters," or "Architectural recommendation" sections. Keep each finding to the structure below.

```
<issue #> — <short description> [<Severity>]

File: <path/to/file.ext>, line <line number>
> <quoted line(s) from the diff>

<One to two sentences: what is wrong and why it matters in this codebase.>

Fix: <Specific, actionable fix. Include file and line number, or exact SQL/code snippet if relevant.>
```

---

## Summary section (optional)

If something in the implementation is genuinely well-executed or worth calling out, say it in one sentence at the top. Skip this section if there is nothing worth noting — do not pad.

---

## Final output requirements

- Unrendered markdown, ready to paste directly into PR comments.
- Order findings by severity: Blockers first, Nits last.
- Omit any finding that cannot be tied to a specific line in the diff.
- Do not repeat the same finding across a stacked PR chain — flag it once, on the PR that introduced it.
