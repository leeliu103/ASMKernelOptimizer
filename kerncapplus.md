# KerncapPlus Context

KerncapPlus provides an ASM-first workflow for optimizing one captured GPU kernel from a real workload.

This workflow assumes `kerncap-plus list` and `kerncap-plus capture` have already been run. The agent starts from an existing captured workspace.

## Workspace

A captured workspace contains replay data, reference artifacts, and one editable ASM variant.

Important files:

```text
reference/module.s       full reference assembly module
reference/kernel.s       read-only assembly for the captured kernel
reference/kernel.ll      read-only LLVM IR for the captured kernel
debug/llvm-passes.log    LLVM pass log for the captured kernel
variant/variant.s        editable assembly for optimization
variant/merged_module.s  generated full module using the edited variant
variant/variant.hsaco    assembled code object for replay
workspace.json           captured kernel and compile metadata
```

## Commands

Benchmark the captured baseline:

```bash
kerncap-plus bench-baseline <workspace> -n <iterations>
```

Assemble the current variant:

```bash
kerncap-plus assemble <workspace>
```

Validate the current variant against the captured workload:

```bash
kerncap-plus validate <workspace>
```

Benchmark the current variant:

```bash
kerncap-plus bench <workspace> -n <iterations>
```

`bench-baseline` measures the captured baseline. `bench` measures the current ASM candidate.
