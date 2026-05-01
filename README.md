# ASMKernelOptimizer

ASMKernelOptimizer is a small workflow wrapper for ASM-level GPU kernel optimization.

## Principle

ASMKernelOptimizer is not a new agent harness. It is a thin specialization layer
on top of existing SOTA coding-agent harnesses such as Codex and Claude Code.

The repo should only add what ASM kernel optimization needs: captured-kernel
workspace setup, domain context, validation criteria, and a strict
supervisor/worker contract. General reasoning, editing, tool use, and iteration
stay inside the underlying agent harness.

The optimization target is always one captured real workload kernel. The editable file is:

```text
<workspace>/variant/variant.s
```

## Commands

### 1. Prepare A Workspace

```bash
./scripts/prepare-workspace \
  --cmd '<real workload command>' \
  --source-dir <source-root> \
  --workspace <workspace>
```

Arguments:

- `--cmd`: real workload command used for profiling and capture
- `--source-dir`: project source directory passed to KerncapPlus; it must contain the original kernel source
- `--workspace`: new output directory for the captured ASM workspace

Example for llama.cpp:

```bash
./scripts/prepare-workspace \
  --cmd '/app/llama.cpp//build/bin/llama-bench -ngl 999 -fa 1 -n "128" -p 0 -m /mnt/nas_share/models/gguf/gpt-oss-20b-GGUF/gpt-oss-20b-mxfp4.gguf' \
  --source-dir /app/llama.cpp/ggml/src \
  --workspace /app/mmvq_g39_128_true_asmko
```

This command:

- runs `kerncap-plus list`
- asks which kernel to capture
- runs `kerncap-plus capture`
- installs the profiler source context needed by the optimization workflow
- generates `<workspace>/isa/isa.json` for the RDNA3/RDNA4 instruction reference
- leaves a ready-to-edit KerncapPlus workspace

The workspace must not already exist.

### 2. Run The Optimizer

```bash
./scripts/asm-kernel-optimizer \
  --workspace <workspace> \
  --iterations 50 \
  --required-speedup-percent 3 \
  --agent codex
```

Use `--agent claude` to launch Claude Code instead:

```bash
./scripts/asm-kernel-optimizer \
  --workspace <workspace> \
  --iterations 50 \
  --required-speedup-percent 3 \
  --agent claude
```

This command writes the concrete run configuration to `run.md`, initializes
`<workspace>/trace.md`, and launches the selected agent with:

```text
Read master.md and run the ASM kernel optimization workflow.
```

## Agent Roles

The workflow has two separate agent roles: a supervisor and a worker.

### Supervisor

The supervisor follows `master.md` and owns acceptance. It creates the worker,
waits for a reported candidate, records the report in `<workspace>/trace.md`,
and independently runs the checks defined in `criteria.md`.

The supervisor is the only role that may stop the workflow with success, and only
after the current candidate strictly passes the criteria check.

### Worker

The worker follows `subagent.md` and owns candidate generation. It uses the
provided optimization context to search for an improved ASM candidate, but edits
only:

```text
<workspace>/variant/variant.s
```

The worker may report that a candidate appears to satisfy the criteria, including
timing and a change summary, but it must not declare final success. If the
supervisor rejects the candidate, the worker continues from the reported failure
reason.

## Acceptance Criteria

The stop condition is defined in `criteria.md`. The supervisor must run that
check itself and may stop only when the candidate strictly passes it.

## Files

```text
goal.md          optimization objective
kerncapplus.md   KerncapPlus workspace and command context
isa.md           generated RDNA ISA reference context
criteria.md      objective stop rule
run.md           concrete workspace, iteration count, and speedup target
master.md        supervisor-agent instructions
subagent.md      worker-subagent instructions
profiler.md      profiling context
```
