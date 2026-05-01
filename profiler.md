# Profiler Context

Use `rocprofv3 --att` when profiling is needed to guide ASM optimization.

## Source

Relevant source paths:

```text
<workspace>/rocm-systems/projects/rocprofiler-sdk
<workspace>/rocm-systems/projects/aqlprofile
<workspace>/rocm-systems/projects/rocprof-trace-decoder
```

## Trace

Run ATT profiling on the workload being investigated:

```bash
rocprofv3 --att -- <workload command>
```

`rocprofv3 --att` emits a `ui_output_agent_*_dispatch_*` folder. Use that folder as the trace artifact.

Use the `rocprof-trace-decoder` source code to understand the data generated in the `ui_output_agent_*_dispatch_*` folder.

## KerncapPlus ATT

For KerncapPlus workspaces, prefer HIP-launch replay for ATT instead of wrapping `kerncap-plus bench`; the direct-HSA benchmark replay path can crash under
`rocprofv3 --att` before producing a decoded trace directory.

Run `kerncap-plus assemble <workspace>` first if the variant changed, then collect ATT with:

```bash
rocprofv3 --att -d <workspace>/att -- \
  kerncap replay <workspace> \
    --hsaco <workspace>/variant/variant.hsaco \
    --hip-launch
```

Use the generated `<workspace>/att/ui_output_agent_*_dispatch_*` directory as the ATT artifact.
