# ISA Context

The prepared workspace includes generated AMD RDNA ISA data:

```text
<workspace>/isa/isa.json
```

Use the ISA JSON as the instruction reference when editing ASM. Check that any
added or changed instruction exists in the JSON, uses a valid encoding variant,
and matches the operand fields expected by that variant. Use `pcode` when
available to confirm the instruction semantics before replacing or reordering
instructions.
