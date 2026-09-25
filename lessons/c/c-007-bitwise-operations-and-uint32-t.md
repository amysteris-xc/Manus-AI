# C 007 — `uint32_t`, Masks, Shifts, and Bounded Word Fields

**Estimated study time:** 25–35 minutes
**Prerequisites:** [C 001 — Build, Types, and Output](c-001-build-types-output.md), [C 002 — Expressions, Decisions, Loops, and Checked Input](c-002-control-flow-and-simple-input.md), [C 003 — Functions, Output Parameters, and Explicit Error Returns](c-003-functions-and-error-returns.md), [C 004 — Fixed-Size Arrays, C Strings, and Index Bounds](c-004-arrays-strings-and-bounds.md), [C 005 — Array Parameters, `size_t` Counts, and Bounded Traversal](c-005-array-parameters-and-bounded-iteration.md), and [C 006 — `uint8_t`, Byte Arrays, and Explicit Lengths](c-006-unsigned-integers-and-byte-arrays.md)
**Companion program:** [`examples/c/c-007-bitwise-operations-and-uint32-t.c`](../../examples/c/c-007-bitwise-operations-and-uint32-t.c)
**Deterministic test:** [`tests/c-007-bitwise-operations-and-uint32-t.sh`](../../tests/c-007-bitwise-operations-and-uint32-t.sh)
**Author:** Manus AI

## What you will be able to do

By the end of this lesson, you should be able to use an exact-width `uint32_t` word to hold small logical fields, validate each field before packing it with masks and shifts, extract fields with right shifts and masks, traverse an explicit-length array of words safely, and state the main shift-count rule that prevents undefined behavior.

## Retrieval warm-up

Without opening earlier lessons, answer these questions.

1. Why must a `uint8_t` buffer use an explicit `size_t` length rather than stop at `0x00`?
2. Write the safe loop condition for processing every element of an array with `count` valid elements.
3. Which headers provide `uint8_t` and `PRIX8`?
4. What does a C string’s first `\0` represent?
5. From Russian 007, say “I do not study at school.”

The spoiler section has model answers. Attempt recall before reopening the earlier material.

## 1. A `uint32_t` word is exactly 32 bits when it is available

When an implementation provides `uint32_t` in `<stdint.h>`, it is an unsigned integer type with exactly 32 bits and no padding bits.[1] This is useful when a data specification defines a 32-bit logical word. Like `uint8_t`, `uint32_t` is optional in ISO C, so a program that needs exactly 32-bit words should verify that the implementation supplies it.

```c
#include <stdint.h>

#if !defined(UINT32_MAX)
#error "This program requires an exact 32-bit uint32_t type."
#endif
```

This lesson treats a `uint32_t` value as a **logical word** with named fields. It does not write that word to a file or network. The order of bytes in memory, called endianness, matters only when data crosses a byte boundary such as a file, network protocol, or hardware interface. Endianness will be handled explicitly in a later lesson; do not assume that shifting a number creates a portable sequence of memory bytes.

## 2. Name a small field layout before writing bitwise code

The companion example reserves three fields in each 32-bit word:

| Field | Bit positions | Width | Allowed value range |
|---|---:|---:|---:|
| Category | 0–7 | 8 bits | `0`–`255` |
| Flags | 8–15 | 8 bits | `0`–`255` |
| Sequence | 16–31 | 16 bits | `0`–`65535` |

A word with category `42`, flags `5`, and sequence `291` is assembled as follows:

```text
sequence = 0x0123        -> 0x01230000 after << 16
flags    = 0x05          -> 0x00000500 after << 8
category = 0x2A          -> 0x0000002A
combined                    0x0123052A
```

The field plan is part of the program’s contract. Write it down in a table or comment before using shifts. Otherwise, it is easy to overlap fields, leave gaps accidentally, or apply the wrong mask.

## 3. Masks select bits; shifts move fields

C provides bitwise AND (`&`), OR (`|`), exclusive OR (`^`), and NOT (`~`) for integer values.[2] A **mask** has `1` bits where a field is kept and `0` bits where other bits are discarded.

```c
#define CATEGORY_MASK UINT32_C(0x000000FF)
#define FLAGS_MASK    UINT32_C(0x0000FF00)
#define SEQUENCE_MASK UINT32_C(0xFFFF0000)
```

Use a left shift (`<<`) to place a checked field into its assigned bit positions. Use bitwise OR to combine non-overlapping fields.

```c
word = category | (flags << 8) | (sequence << 16);
```

Use a right shift (`>>`) to bring a field down to the low bits, then use a mask to discard unrelated bits.

```c
category = word & CATEGORY_MASK;
flags = (word >> 8) & CATEGORY_MASK;
sequence = (word >> 16) & CATEGORY_MASK;
```

The final `CATEGORY_MASK` works for the extracted 8-bit fields because they have already been shifted down. The sequence field occupies 16 bits, so this example combines two 8-bit pieces when extracting it. A later lesson will expand the mask vocabulary; today’s goal is to make each operation visible and checked.

## 4. Use unsigned operands and prove shift counts are safe

A shift is not “always safe because the program compiled.” C defines shift behavior only when the right operand is nonnegative and strictly less than the number of bits in the promoted left operand.[2]

For this exact 32-bit program, the shifts are fixed at `0`, `8`, and `16`. Each is less than `32`, and every shifted value has type `uint32_t`. The program never shifts by `32` or more.

```c
const uint32_t packed = category | (flags << 8) | (sequence << 16);
```

The `pack_metadata` helper first proves that category and flags fit eight bits and that sequence fits sixteen bits. Therefore its shifts cannot spill information from a field into a neighboring field.

```c
if (category > UINT32_C(0xFF) || flags > UINT32_C(0xFF) ||
    sequence > UINT32_C(0xFFFF)) {
    return STATUS_VALUE_OUT_OF_RANGE;
}
```

For unsigned left operands, a defined left shift discards bits shifted out of the destination type.[2] That defined modulo behavior is **not** permission to ignore the data format. Validate the field ranges before packing so that loss does not occur in the first place. Do not shift signed values as a substitute for this design, and do not use unvalidated shift counts derived from input.

> **Shift rule:** Before `value << count` or `value >> count`, establish the intended unsigned type and prove `0 <= count < word_width`. For this lesson, the shifts are compile-time constants `8` and `16` in an exact 32-bit word.

## 5. Separate packing, unpacking, and reporting

The companion program uses three small responsibilities:

| Helper | Responsibility | Key contract |
|---|---|---|
| `pack_metadata` | Validate and combine three field values | Output pointer is non-null; fields fit their widths |
| `unpack_metadata` | Extract three field values from one word | All output pointers are non-null |
| `write_word_report` | Traverse and display an array of words | Word pointer is non-null; count is nonzero; loop uses `index < count` |

This separation lets tests exercise error paths directly. It also keeps the report code from hiding the packing and extraction rules.

The word array itself still follows C 005’s pointer-plus-count rule:

```c
for (size_t index = 0; index < count; ++index) {
    status = unpack_metadata(words[index], &category, &flags, &sequence);
    /* Check status before using outputs. */
}
```

The program does not scan memory for a sentinel word. A value of `0x00000000` could be valid data; the explicit `count` establishes the logical boundary.

## 6. Worked example: pack, unpack, and report two words

The companion program packs two bounded metadata words and reports their hexadecimal representation plus decimal field values. It is a data-layout exercise, **not** encryption, hashing, key handling, or protection for real data.

```c
#include <inttypes.h>
#include <stddef.h>
#include <stdint.h>
#include <stdio.h>
#include <stdlib.h>

#if !defined(UINT32_MAX)
#error "This program requires an exact 32-bit uint32_t type."
#endif

typedef enum {
    STATUS_OK = 0,
    STATUS_INVALID_ARGUMENT = 1,
    STATUS_VALUE_OUT_OF_RANGE = 2,
    STATUS_OUTPUT_FAILURE = 3
} Status;

enum {
    FLAGS_SHIFT = 8,
    SEQUENCE_SHIFT = 16
};

#define CATEGORY_MASK UINT32_C(0x000000FF)
#define SEQUENCE_VALUE_MASK UINT32_C(0x0000FFFF)

static Status pack_metadata(uint32_t category, uint32_t flags,
                            uint32_t sequence, uint32_t *word_out)
{
    if (word_out == NULL) {
        return STATUS_INVALID_ARGUMENT;
    }

    if (category > CATEGORY_MASK || flags > CATEGORY_MASK ||
        sequence > SEQUENCE_VALUE_MASK) {
        return STATUS_VALUE_OUT_OF_RANGE;
    }

    *word_out = category | (flags << FLAGS_SHIFT) |
                (sequence << SEQUENCE_SHIFT);
    return STATUS_OK;
}

static Status unpack_metadata(uint32_t word, uint32_t *category_out,
                              uint32_t *flags_out, uint32_t *sequence_out)
{
    if (category_out == NULL || flags_out == NULL || sequence_out == NULL) {
        return STATUS_INVALID_ARGUMENT;
    }

    *category_out = word & CATEGORY_MASK;
    *flags_out = (word >> FLAGS_SHIFT) & CATEGORY_MASK;
    *sequence_out = (word >> SEQUENCE_SHIFT) & SEQUENCE_VALUE_MASK;
    return STATUS_OK;
}

static Status write_word_report(const uint32_t words[], size_t count)
{
    if (words == NULL || count == 0) {
        return STATUS_INVALID_ARGUMENT;
    }

    if (printf("Word count: %zu\n", count) < 0) {
        return STATUS_OUTPUT_FAILURE;
    }

    for (size_t index = 0; index < count; ++index) {
        uint32_t category;
        uint32_t flags;
        uint32_t sequence;
        Status status = unpack_metadata(words[index], &category, &flags,
                                        &sequence);

        if (status != STATUS_OK) {
            return status;
        }

        if (printf("Word %zu: 0x%08" PRIX32
                   " category=%" PRIu32 " flags=%" PRIu32
                   " sequence=%" PRIu32 "\n",
                   index, words[index], category, flags, sequence) < 0) {
            return STATUS_OUTPUT_FAILURE;
        }
    }

    return STATUS_OK;
}

int main(void)
{
    uint32_t words[2];
    Status status;

    status = pack_metadata(UINT32_C(42), UINT32_C(5), UINT32_C(291),
                           &words[0]);
    if (status != STATUS_OK) {
        (void)fputs("Cannot pack the first metadata word.\n", stderr);
        return EXIT_FAILURE;
    }

    status = pack_metadata(UINT32_C(16), UINT32_C(160), UINT32_C(48879),
                           &words[1]);
    if (status != STATUS_OK) {
        (void)fputs("Cannot pack the second metadata word.\n", stderr);
        return EXIT_FAILURE;
    }

    status = write_word_report(words, sizeof words / sizeof words[0]);
    if (status != STATUS_OK) {
        return EXIT_FAILURE;
    }

    return EXIT_SUCCESS;
}
```

Compile and run the committed companion file exactly as follows:

```sh
cc -std=c17 -Wall -Wextra -Wpedantic \
  -o c-007-bitwise-operations-and-uint32-t \
  examples/c/c-007-bitwise-operations-and-uint32-t.c
./c-007-bitwise-operations-and-uint32-t
```

Expected output:

```text
Word count: 2
Word 0: 0x0123052A category=42 flags=5 sequence=291
Word 1: 0xBEEFA010 category=16 flags=160 sequence=48879
```

## 7. Guided practice

Answer before reading the spoiler section.

1. Why does a program that requires exact 32-bit words check for `UINT32_MAX`?
2. Which operator selects bits with a mask: `&`, `|`, or `^`?
3. What is the safe allowed range for a shift count in an exact 32-bit word?
4. Why must `category`, `flags`, and `sequence` be range-checked before packing?
5. Which expression extracts the flags field from `word` in this layout?
6. Why does the report loop use an explicit `count` instead of stopping when a word is zero?
7. From Russian 007, say “I do not read at the library.”

## 8. Independent exercise

Create `exercise.c` beside the companion program. Define a word layout with a 4-bit priority field in bits 0–3, a 4-bit type field in bits 4–7, and a 16-bit identifier field in bits 8–23. Leave the upper eight bits zero for this exercise.

Write a `pack_record` helper that accepts `uint32_t` inputs and an output pointer. Reject null output pointers and inputs larger than their field masks before shifting. Write a matching `unpack_record` helper and a report helper that processes an explicit-length `uint32_t` array with `size_t index` and `index < count`.

Use only shift counts that are compile-time constants below `32`. Print words with `PRIX32`. Test a valid value for every field and one priority value larger than `15` to confirm the packer returns an error without replacing the caller’s existing output value.

This is an educational data-layout exercise. It is not a cryptographic algorithm and must never be presented as a way to secure real data.

## 9. Validate with sanitizers

Run the repository’s deterministic test:

```sh
./tests/c-007-bitwise-operations-and-uint32-t.sh
```

Then build and run with AddressSanitizer and UndefinedBehaviorSanitizer when supported:

```sh
cc -std=c17 -Wall -Wextra -Wpedantic \
  -fsanitize=address,undefined -fno-omit-frame-pointer -g \
  -o c-007-sanitized \
  examples/c/c-007-bitwise-operations-and-uint32-t.c
./c-007-sanitized
```

The valid report should show two words and their fields with no sanitizer report. Sanitizers support testing, but they do not replace the explicit field-range checks, fixed safe shift counts, non-null output checks, or `index < count` traversal bound in the source.

## 10. Final self-check

You are ready for the next lesson if you can do all of the following without looking back.

1. Explain why `uint32_t` is preferable to plain `unsigned int` when a format requires exactly 32 bits.
2. State what `&`, `|`, `<<`, and `>>` do in this field-packing context.
3. State the shift-count rule for an exact 32-bit word.
4. Explain why defined unsigned wraparound does not remove the need to validate field widths.
5. Write the bounded loop condition for an array of words with `count` elements.
6. Explain why this lesson has not yet defined a byte serialization or endianness format.
7. Recall the Russian sentence **Я не читаю в библиотеке.**

## Spoiler: answers and model response

1. An array parameter is adjusted to a pointer, so the explicit count establishes how many elements are valid.
2. `index < count`.
3. `<stdint.h>` provides `uint8_t`; `<inttypes.h>` provides `PRIX8`.
4. `\0` marks the end of a C string.
5. **Я не учусь в школе.**
6. The macro is conditionally present with `uint32_t`; its presence verifies that this implementation supplies the exact-width type the format requires.
7. `&` selects the bits retained by a mask.
8. The shift count must be nonnegative and strictly less than `32`; this program uses only `8` and `16`.
9. Without validation, excess field bits could overlap another field or be discarded, silently changing the data representation.
10. `flags = (word >> FLAGS_SHIFT) & CATEGORY_MASK;`.
11. A zero word can be valid data. The explicit count defines the array boundary.
12. “I do not read at the library” is **Я не читаю в библиотеке.**
13. A model safe packing expression after range checks is:

```c
word = category | (flags << FLAGS_SHIFT) | (sequence << SEQUENCE_SHIFT);
```

14. Endianness describes the order of bytes in memory or external representation. This lesson only manipulates logical numeric fields inside a `uint32_t`; it does not define a file, network, or memory-byte format.

## Next lesson

The next scheduled lesson returns to Russian: **Russian 008 — possessive pronouns, familiar people, and short ownership statements.**

## References

[1]: https://en.cppreference.com/w/c/types/integer "Fixed width integer types — cppreference.com"
[2]: https://en.cppreference.com/w/c/language/operator_arithmetic "Arithmetic operators — cppreference.com"
