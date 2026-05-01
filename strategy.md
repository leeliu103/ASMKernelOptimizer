# Strategy Guide

## Core

Assume this kernel is limited by global-load latency and hot-loop scheduling.
Compute pressure is relatively low.

The current candidate is already specialized for the captured shape, so focus on
the hot loop rather than broad rewrites.

## What To Try

Try different schedule orders in the hot loop:

- move global loads earlier
- move waits later
- interleave independent address, unpack, dot, and accumulation work
- use extra VGPRs to keep more values or addresses live
- try carrying next-iteration address or load work earlier

Do not optimize for fewer instructions or fewer VGPRs by default. Measure the
result.

## Use ATT

Use `rocprofv3 --att` to guide scheduling experiments.

Check whether the hot loop is stalled on memory waits or instruction issue. If
so, adjust the schedule to move loads earlier, move waits later, or place
independent work before the stall.

Then validate and benchmark normally.
