# Goal

Optimize an extracted GPU kernel at the assembly level.

The system must produce an edited `variant/variant.s` that preserves the captured kernel's correctness and improves runtime versus the captured baseline.

A subagent works toward this single objective: identify a safe ASM optimization, apply it to the editable variant assembly, explain the change, and produce a candidate that assembles, validates, and benchmarks faster than the captured baseline.

The master agent decides success only after independent verification against `criteria.md`.
