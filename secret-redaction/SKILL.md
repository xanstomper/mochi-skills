---
name: secret-redaction
description: Extending the secret redaction DFA correctly — new token formats, false-positive control, test vectors. Use when touching redaction, logging, or trace output.
---

# Secret Redaction

Mochi redacts secrets (API keys, tokens, connection strings) from logs, traces, and session exports via a DFA pattern set. Wrong extensions leak or destroy output.

## Trigger
- New provider with a novel token format.
- A secret found in a trace/log (incident).
- False-positive report (benign string being redacted).

## Steps
1. Add the pattern to the redaction module with a named group for the secret portion only — redact `sk-...` values, not surrounding JSON syntax.
2. Anchor precisely: provider docs publish the exact prefixes/alphabets (e.g. `sk-`, `Bearer `, base62). Over-broad regexes mangle legitimate text.
3. Add both test vectors: (a) realistic secret → must be redacted, (b) lookalike benign string (version numbers, IDs sharing the prefix) → must survive.
4. Re-run redaction over all existing fixtures — confirm no output shape changes beyond the new pattern.
5. Check every emission path: console logs, run traces, session export, daemon responses, error messages. Redaction is one function called everywhere, never reimplemented.

## Pitfalls
- Greedy matching across line breaks eats whole logs — constrain with character classes, not `.+`.
- Don't redact in place for structured data (breaks JSON) — redact at the string-serialization boundary.
- Case: some tokens are case-sensitive (JWT), some aren't (AWS IDs) — encode correctly.
- Performance: the DFA runs on every log line; keep patterns linear, no catastrophic backtracking.

## Verification
- `npx vitest run src/redact/ src/observability/` green.
- Inject a fake secret through a full session, export the trace, grep the export — zero hits.
