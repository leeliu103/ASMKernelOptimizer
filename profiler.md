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

`rocprofv3 --att` emits a `ui_*` folder. Use that folder as the trace artifact.

Use the `rocprof-trace-decoder` source code to understand the data generated in the `ui_*` folder.
