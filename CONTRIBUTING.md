# Contributing

## What belongs here

**A defect entry.** If a paper-trading component produced a number that looked
right and was wrong, that is the highest-value contribution to this repository.

## Format

Match the existing entries exactly:

```markdown
## N. Short symptom, in the reader's words

**Symptom:** what you observed — including everything that looked healthy at the
time, because that is what misleads the next person.
**Cause:** the actual defect, not the first suspect.
**Fix:** the change, plus the test that would have caught it.
```

## Rules

**Symptom first.** Write what a future reader will see. The most expensive
defects are the ones whose symptom points somewhere else — "the chain is empty"
led to a freshness budget, not a data problem.

**Distinguish measured from assumed.** If a number came from sampling real feed
cadence, say so and give the sample size. If it was a guess, label it as one.

**Record deliberate deviations.** If you ported a fix that a source system had as
a hard veto, say that it is now a sizing input and why. The reasoning is the
value; the change alone loses it in six months.

**Preserve unfixed defects.** If you found a latent bug and did not fix it, add
it with a note. A documented latent defect is worth more than a silent repair —
someone else will hit it under a different stack.

**No secrets.** No credentials, tokens, hostnames, IPs, customer data or vendor
identities. Generic terms keep the document portable and safe to publish.

**No performance claims.** This is a paper-trading document. Nothing here
authorises live execution, and a profitable backtest does not.

## What does not belong

- Strategy opinions dressed as defects. Part 1 holds guidance that changes with
  strategy belief; keep it separate.
- Restyling the existing entries. Rewriting working prose for variety makes the
  diff unreviewable and the history worthless.
- Numbers tuned to one broker's cadence presented as universal. State the
  measurement conditions.

## Process

1. Fork, branch, one defect per PR.
2. Run the disclosure scan before submitting:
   ```bash
   grep -rniE "([0-9]{1,3}\.){3}[0-9]{1,3}|password|secret|api[_-]?key|token|BEGIN [A-Z ]*PRIVATE" .
   ```
   Any hit must be justified in the PR description or removed.
3. Open the PR describing the symptom you observed, not the fix you applied.

## Licence

Content is published under CC BY 4.0. Use it, adapt it, improve it — attribution
is the only condition.