# eval

Measure whether a change to AI behavior (a prompt edit, a skill rewording, an
agent-loop change, a RAG pipeline tweak) actually improved things, the way you
would measure a code change: a grid-sampled eval set with negative controls
and a generated response side, a hand-labeled answer key, a judge written like
code, and calibration of that judge against the key before any system number
is trusted. "It looks better" is not evidence.

[`SKILL.md`](SKILL.md) is the full method, written as an
[agent skill](https://code.claude.com/docs/en/skills): drop this directory into
`~/.claude/skills/eval/` and ask your agent to evaluate a behavior change, or
read it as a checklist and run the loop by hand.

The toolkit is one dependency-free script:

```bash
# judge the human-labeled subset, write one {"human","judge"} pair per line,
# then read the judge's calibration before trusting it at scale:
python3 toolkit/calibrate.py labels.jsonl                  # FAIL positive, the usual
python3 toolkit/calibrate.py labels.jsonl --positive PASS  # the swapped convention
```

It prints the confusion counts (TP/TN/FP/FN), TPR (recall), TNR
(specificity), accuracy and precision at 4 decimals, plus a one-line bias
reading: harsh when the judge fails good work more than it waves bad work
through, lenient when the reverse. A two-column CSV (human first) also reads.

Python 3 standard library only; nothing to install, no model calls.

## Tests

```bash
bats tests/eval-toolkit.bats
```

The suite pins the method's worked example (80 labeled runs: counts, all four
metrics, and the harsh reading with both error shares) so a regression in the
arithmetic is caught against a known-good case rather than the
implementation's own echo.

## License

MIT, see [LICENSE](LICENSE). All code here is original and standard-library
only; no third-party data ships, so there is no NOTICE file.
