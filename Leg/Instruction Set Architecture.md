\# Turing Complete

\# LEG Instruction Set Architecture

&#x20;

\*\*Author:\*\* Chris Manlove

\*\*Last Updated:\*\* 3/23/2026

&#x20;

\---

This is a custom 8-bit computer build in the game Turing Complete. It executes instructions sequentially from a program ROM, with support for arithmetic, logic, branching, and RAM access. The computer has 6 general-purpose registers (r0-r5), all initialized to zero at start up.



\---

&#x20;

\## Instruction Format

&#x20;

Every instruction is 4 bytes wide and follows the pattern:

&#x20;

```

opcode arg1 arg2 arg3

```



&#x20;

The opcode is 1 byte. The 2 highest bits of the opcode control whether the arguments are register references or immediate values:

&#x20;

\- \*\*Bit 7 (value 128):\*\* If set, arg1 is an immediate value instead of a register reference.

\- \*\*Bit 6 (value 64):\*\* If set, arg2 is an immediate value instead of a register reference.

&#x20;

The assembler handles this automatically. For example, writing `mvi 42 x r0` encodes opcode 0 with the arg1-immediate bit set, since 42 is a literal value rather than a register.

&#x20;

\---



\## Registers

&#x20;

There are 6 general-purpose registers ranging from r0–r5. All registers start at 0 when the program begins. There is currently no dedicated stack pointer or flags register.

&#x20;

| Register | Description |

|----------|-------------|

| r0–r5 | General-purpose registers |

| r6 (PC) | Program counter. Can be read from or written to like any other register. Always the destination of branch instructions. |

| r7 (I/O) | Input can be read like any register. Output can be written to like any register. |

&#x20;

\---



\## Instructions

&#x20;

\### ALU \& Logic (Opcodes 0–31)

&#x20;

These perform arithmetic and bitwise operations. The format is:

&#x20;

```

opcode source1 source2 dest

```



The result of the operation on source1 and source2 is written to dest.

&#x20;

| Opcode | Name | Description |

|--------|------|-------------|

| 0 | add | `dest = source1 + source2` |

| 1 | sub | `dest = source1 - source2` |

| 2 | and | `dest = source1 AND source2` |

| 3 | or | `dest = source1 OR source2` |

| 4 | not | `dest = NOT source1` (source2 ignored) |

| 5 | xor | `dest = source1 XOR source2` |

| 6 | nand | `dest = source1 NAND source2` |

| 7 | nor | `dest = source1 NOR source2` |

| 8 | shl | `dest = source1 << source2` |

| 9 | shr | `dest = source1 >> source2` (logical) |

| 10 | rol | `dest = source1` rotated left by source2 |

| 11 | ror | `dest = source1` rotated right by source2 |

| 12 | not\_2 | `dest = NOT source2` (source1 ignored) |

| 13 | xnor | `dest = source1 XNOR source2` |

| 14 | ashr | `dest = source1` arithmetic shift right by source2 |

| 15 | ashl\_Nop | Not implemented (intended as ashl, component unavailable) |

| 16 | multi\_l | `dest = LOW(source1 \* source2)` |

| 17 | multi\_h | `dest = HIGH(source1 \* source2)` |

| 18 | div | `dest = source1 / source2` |

| 19 | mod | `dest = source1 % source2` |

| 20–31 | — | Not implemented (behave as nop) |



\### Branch (Opcodes 32–47)

&#x20;

These perform a comparison and conditionally overwrite the program counter to jump to a new address. The format is:

&#x20;

```

opcode source1 source2 label

```

&#x20;

If the condition is true, execution jumps to the label. If false, execution continues to the next instruction. The program counter (r6) is always the implicit destination register for branch instructions.



\#### Unsigned Comparisons (32–39)

&#x20;

| Opcode | Name | Description |

|--------|------|-------------|

| 32 | eq | Jump if `source1 == source2` |

| 33 | neq | Jump if `source1 != source2` |

| 34 | lt | Jump if `source1 < source2` |

| 35 | lte | Jump if `source1 <= source2` |

| 36 | gt | Jump if `source1 > source2` |

| 37 | gte | Jump if `source1 >= source2` |

| 38 | jmp | Unconditional jump (sources ignored) |

| 39 | nop | No operation |



\#### Signed Comparisons (40–47)

&#x20;

| Opcode | Name | Description |

|--------|------|-------------|

| 40 | s\_eq | Jump if `source1 == source2` (signed) |

| 41 | s\_neq | Jump if `source1 != source2` (signed) |

| 42 | s\_lt | Jump if `source1 < source2` (signed) |

| 43 | s\_lte | Jump if `source1 <= source2` (signed) |

| 44 | s\_gt | Jump if `source1 > source2` (signed) |

| 45 | s\_gte | Jump if `source1 >= source2` (signed) |

| 46 | s\_jmp | Unconditional jump (signed context) |

| 47 | nop | No operation |



\### RAM (Opcodes 48–63)

&#x20;

These read from and write to RAM. Only two are currently implemented.

&#x20;

```

save address value x     # RAM\[address] = value

load address x dest      # dest = RAM\[address]

```

&#x20;

| Opcode | Name | Format | Description |

|--------|------|--------|-------------|

| 48 | save | `save address value x` | Writes value to RAM at address |

| 49 | load | `load address x dest` | Reads RAM at address into dest |

| 50–63 | — | — | Not implemented |

&#x20;

For both save and load, the address and value arguments follow the same immediate encoding rules - bit 7 for arg1 immediate, bit 6 for arg2 immediate.



\---

&#x20;

\## Assembly Syntax

&#x20;

\### Labels

&#x20;

Labels define jump targets. Declare them on their own line:

&#x20;

```

label my\_label

```

&#x20;

Reference them as the third argument in branch instructions:

&#x20;

```

jmp x x my\_label

gt r0 r1 my\_label

```







































































































