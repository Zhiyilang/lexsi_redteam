# lexsi_redteam

Red-team-driven safety repair for a small open-weights model — entry for the
**Lexsi AI Safety & Alignment Hackathon** (Lexsi Labs × Cohere Labs ×
Hugging Face), Track 2 (AI Red Teaming, Safety and Alignment).

**Status: pre-window build (private until Nov 1, 2026).** The hackathon
requires projects to be developed during the event, so nothing here is
public before the window opens. The attack vectors come from our
already-public red-team suites (swarmbox sandbox-escape patterns,
alexaguard-mcp prompt-injection patterns) — pre-existing open-source
libraries, used the way any library would be.

## What is here

| File | Role |
|---|---|
| `attack_corpus.py` | Zero-dependency generator: 34 agentic attacks (4 categories) + 17 benign probes (9 deliberately edgy) → `corpus/*.jsonl` |
| `run_pipeline.py` | The chain: CuratorKIT → SafeTune → CircuitKIT → AuditKIT. `--dry-run` runs the whole measurement plumbing on a mock model, no GPU |
| `eval.py` | 35 assertions: 33 stdlib (corpus schema/counts/determinism + dry-run metric semantics + hostile-locale LC_ALL=C) + 2 optional AuditKIT integration (skipped when `auditkit` not installed) |
| `DESIGN.md` | Thesis, pipeline, metrics, window discipline, risks |
| `SUBMISSION_CHECKLIST.md` | User actions + the Nov 1–14 execution table |
| `results/metrics.json` | Latest dry-run output (plumbing-verified numbers only) |

## The thesis in one paragraph

Most red-team submissions reuse chat-jailbreak benchmarks and report a
single attack-success number. We bring **agentic tool-abuse vectors** —
exfiltration through tool calls, budget evasion by splitting, sandbox
escape attempts, autonomous escalation — and we report **two** numbers:
attack success rate *and* over-refusal rate on benign-but-edgy probes. A
"repair" that refuses everything is a lobotomy, and it is the failure mode
nobody scores. Both numbers are computed by the same harness, tagged with
AuditKIT run fingerprints, and broken down per attack category so an
average cannot hide a regression.

## Run it

```bash
python eval.py                      # 33 assertions green on any stdlib python; +2 AuditKIT checks when `auditkit` installed; green under LC_ALL=C too
python run_pipeline.py --dry-run    # full chain on a mock model, no GPU
python run_pipeline.py              # real run: needs torch + a Cohere model
```

Zero third-party dependencies for the corpus generator and the test suite;
`auditkit` (zero-dep core) is used by the pipeline when available and is
the only package the real run strictly needs beyond torch/transformers.

## Prize note (recorded honestly)

Top prizes are **credits** ($1,750 each in Lexsi Labs and Cohere credits +
6 months of HF Pro per teammate for 1st), not cash, plus $20 HF credits
for registered teams that join Discord. Value is real (compute) but it is
not a cash prize; this file exists so no future summary overclaims it.
