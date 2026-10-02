# Option-Chain Paper Trading — Playbook & Defect Library

A skill for anyone building or debugging an **option-chain paper-trading loop**.

It is two things in one document, deliberately:

1. **The discipline** — entry gates, sizing, dynamic exits, the accounting rule.
   The beliefs that keep a loop honest.
2. **The defect library** — 14 failures that produced *plausible-looking wrong
   numbers* while the tests were green. The scar tissue.

The second half is the one people usually lack. Every entry is written
**symptom → cause → fix**, because the symptom is almost never the cause.

> The recurring lesson: **green tests are not evidence.**
> Three of these defects were invisible to reading the code *and* to a passing
> suite. When a component disagrees with reality, instrument the actual call and
> print intermediate values — do not re-read the code, and do not rewrite the same
> query hoping to spot it.

---

## Install

**Hermes / Claude skills** — clone into your skills directory, or copy one file:

```bash
git clone https://github.com/methakon/option-chain-paper-trading-playbook.git
cp -r option-chain-paper-trading-playbook/.claude/skills/* ~/.claude/skills/
cp -r option-chain-paper-trading-playbook/.hermes/skills/* ~/.hermes/skills/
```

The two copies are byte-identical; they exist so either runtime finds them.

**Just reading it?** Open [`PLAYBOOK.md`](PLAYBOOK.md).

---

## What is in it

| Section | Covers |
|---|---|
| Non-negotiable | NO_TRADE as a real outcome, no averaging down, no auto-reversal, journal everything |
| Entry gates | nine ordered gates, and why a gate that gives a *reason* beats one that just refuses |
| Sizing | size after the stop is known; never a tighter stop to force a trade |
| Exits | dynamic HOLD/REDUCE/EXIT as *labels*, ATR-trail ratchets, exit reasons for learning |
| The accounting rule | never update the model with an unrealised outcome |
| Defect library | 14 entries, symptom → cause → fix |
| Verification | the seven checks before you may claim a loop works |

### The defects, briefly

| # | Symptom | Real cause |
|---|---|---|
| 1 | every position closes in minutes on liquidity | `volume=0` because the feed does not report it — absence read as zero |
| 2 | feed looks healthy, persists nothing | ticks arriving fresh but carrying *yesterday's* exchange timestamp |
| 3 | live ladder comes back empty | freshness budget inherited from a different feed (15s vs p95 30s) |
| 4 | failover never happens | both providers feed one canonical pipeline but persist to **two tables** |
| 5 | a lookup returns zero against one feed | same field named differently per store — and valued differently |
| 6 | `expiry = 'Tue Oct 06'`, then a SQL error | `DATE` columns arrive as JS `Date` objects |
| 7 | an entire expiry series silently ignored | two expiry-code forms; and the code does not contain the day |
| 8 | live quote reported `found: false` | the query aliased the key, the reader read the old name |
| 9 | capital invariant falsely breached | unrealised mark folded into sizing equity |
| 10 | "gave back 3150%" on a 0.20-pt run | ratio with no floor on the denominator |
| 11 | position closed one second after entry | deciding on the same tick you entered |
| 12 | structural stop always used the fallback | read a field the other module never returns |
| 13 | reported a fault that did not exist | recited a stale note instead of querying |
| 14 | feed cannot self-heal | vendor disables the token refresh API — a human blocker |

---

## Improving it

**The most valuable contribution is a new defect entry**, in the same shape as
the existing ones. If you hit something that produced plausible wrong numbers,
that is exactly what belongs here.

Format:

```
## N. Short symptom, in the reader's words

**Symptom:** what you observed, including anything that looked fine.
**Cause:** the actual defect — not the first suspect.
**Fix:** the change, with a test that would have caught it.
```

Please keep these properties:

- **Symptom first.** Write what a future reader will see, not what you deduced.
- **Say when a fix is a deliberate deviation** from the source system, and why.
- **Preserve defects you did not fix**, with a note that they are unfixed. A known
  latent defect is worth more than a silent repair.
- **No credentials, hostnames, IPs or vendor identities.** Generic terms
  (`broker A`, `feed_option_quotes`) keep it portable and safe to publish.

Guidance that changes with strategy belief belongs in Part 1. Guidance that is
true regardless of strategy belongs in Part 2.

Open a pull request. One defect per PR is ideal.

---

## Scope and honesty

This is a **paper-trading** document. Live execution is disabled in the system it
came from, and nothing here authorises it. A short profitable backtest does not
authorise live trading.

Two numbers in the document are **measured, not universal**: the freshness budget
(45s) and the two-store asymmetry come from one broker's cadence. Re-measure
before trusting them anywhere else — that is step 3 of the defect library, and the
one part that does not travel.

A loop that has never completed a full lifecycle has produced **zero**
observations, no matter how many skip records it wrote. Neither a loss is failure
nor a win is validation — both are training observations. Do not tune from one.