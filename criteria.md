# Criteria

The master agent may stop with success only when the current `variant/variant.s` candidate passes all checks below.

## Baseline

Run the captured baseline benchmark:

```bash
kerncap-plus bench-baseline <workspace> -n <iterations>
```

The baseline is valid only if `bench-baseline` succeeds and reports timing.

## Candidate Checks

Run these commands on the current candidate:

```bash
kerncap-plus assemble <workspace>
kerncap-plus validate <workspace>
kerncap-plus bench <workspace> -n <iterations>
```

The candidate passes only if:

1. `assemble` succeeds.
2. `validate` succeeds.
3. `bench` succeeds and reports timing.
4. The candidate benchmark is faster than the baseline by at least `<required_speedup_percent>`.

Speedup is computed as:

```text
speedup_percent = ((baseline_time - candidate_time) / baseline_time) * 100
```

Use the average benchmark time as `baseline_time` and `candidate_time` unless the user specifies another metric.

## Candidate Integrity

Before accepting success, the master must inspect the ASM diff against
`reference/kernel.s`. Reject changes that bypass required computation or exploit
replay/validation artifacts instead of optimizing the kernel.

## Stop Rule

If all candidate checks pass and `speedup_percent >= <required_speedup_percent>`, the master agent may stop with success.

Otherwise, the master agent must continue the optimization loop.
