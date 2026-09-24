# C 006 — `uint8_t`, Byte Arrays, and Explicit Lengths

**Estimated study time:** 25–35 minutes
**Prerequisites:** [C 001 — Build, Types, and Output](c-001-build-types-output.md), [C 002 — Expressions, Decisions, Loops, and Checked Input](c-002-control-flow-and-simple-input.md), [C 003 — Functions, Output Parameters, and Explicit Error Returns](c-003-functions-and-error-returns.md), [C 004 — Fixed-Size Arrays, C Strings, and Index Bounds](c-004-arrays-strings-and-bounds.md), and [C 005 — Array Parameters, `size_t` Counts, and Bounded Traversal](c-005-array-parameters-and-bounded-iteration.md)
**Companion program:** [`examples/c/c-006-unsigned-integers-and-byte-arrays.c`](../../examples/c/c-006-unsigned-integers-and-byte-arrays.c)
**Deterministic test:** [`tests/c-006-unsigned-integers-and-byte-arrays.sh`](../../tests/c-006-unsigned-integers-and-byte-arrays.sh)
**Author:** Manus AI

## What you will be able to do

By the end of this lesson, you should be able to explain why an explicit-length `uint8_t` array is not a C string, represent ordinary zero-valued data safely, pass a byte buffer together with a `size_t` length, traverse it with `index < count`, and print its units in hexadecimal using the standard integer-format macros.

## Retrieval warm-up

Without opening earlier lessons, answer these questions.

1. Why does a function that receives an array parameter also need an explicit count?
2. Write the safe forward loop condition for an array whose valid count is `count`.
3. What does `\0` mean to a C string?
4. Why is `strlen` unsuitable for an arbitrary byte buffer?
5. From Russian 006, translate **Я читаю в библиотеке.**

The spoiler section has model answers. Attempt recall before checking the earlier lessons.

## 1. An 8-bit unit is data, not automatically text

A C **byte** is the unit measured by `sizeof`; its bit width is given by `CHAR_BIT` and is at least 8 bits.[3] Many modern systems use 8-bit bytes, but portable C should not casually assume that a language byte is always eight bits.

When an implementation provides `uint8_t` in `<stdint.h>`, it is an unsigned integer type with **exactly 8 bits** and no padding bits.[1] That makes it useful when a protocol, file format, test vector, or hardware interface defines units of eight bits. The exact-width typedef is optional in ISO C, so a portable program that requires it should fail clearly at compile time if the platform does not supply it.

```c
#include <stdint.h>

#if !defined(UINT8_MAX)
#error "This program requires an exact 8-bit uint8_t type."
#endif
```

Do not use `uint8_t` merely because it sounds more technical. Use it when the data’s specification says “eight-bit unsigned unit.” Use other types when their semantics are clearer for the problem.

## 2. A byte buffer and a C string have different boundaries

A C string ends at its first null character, `\0`. By contrast, a byte buffer uses an explicit length. A zero-valued unit is ordinary data inside a byte buffer.

```c
char text[] = "A\0B";
uint8_t bytes[] = {UINT8_C(0x41), UINT8_C(0x00), UINT8_C(0x42)};
```

| Object | Stored units | Logical boundary |
|---|---|---|
| `text` | `A`, `\0`, `B`, final `\0` | C-string functions stop at the first `\0` |
| `bytes` | `0x41`, `0x00`, `0x42` | The caller-provided length, here `3` |

For the string, `strlen(text)` is `1`, because the first null character follows `A`. For the byte array, all three units are part of the buffer, including `0x00`. Calling `strlen` on `bytes` is not a way to obtain its length and is incorrect: a byte buffer may not be terminated, and an embedded zero does not mark its end.[2]

> **Boundary rule:** For text that is a C string, establish null termination before calling string functions. For binary or byte-oriented data, carry an explicit pointer-plus-length pair and do not invent a terminator.

## 3. Pass `uint8_t` buffers with `size_t` lengths

C 005 established the general array interface: an array parameter is adjusted to a pointer, so the function does not receive the caller’s element count. The same rule applies to a byte buffer.

```c
static Status count_zero_bytes(const uint8_t bytes[], size_t count,
                               size_t *zero_count_out);
```

The function contract is:

| Parameter | Meaning |
|---|---|
| `bytes` | Pointer to the first valid `uint8_t` unit |
| `count` | Exact number of valid units starting at `bytes` |
| `zero_count_out` | Address where the successful result is written |

Inside the helper, the safe traversal is unchanged:

```c
for (size_t index = 0; index < count; ++index) {
    if (bytes[index] == UINT8_C(0)) {
        ++zero_count;
    }
}
```

The loop accesses indexes from `0` through `count - 1`. It does not stop at `0x00`; it treats that value as data and continues until it reaches the explicit boundary.

## 4. Use `UINT8_C` and `PRIX8` for clear fixed-width code

`UINT8_C(value)` creates an integer constant appropriate for the `uint_least8_t` family when the corresponding exact-width type is provided.[1] It makes a literal’s width intention visible in a buffer initializer:

```c
UINT8_C(0x52)
UINT8_C(0x00)
UINT8_C(0xA7)
```

When printing fixed-width integer types, `<inttypes.h>` provides matching format macros. `PRIX8` is the uppercase hexadecimal format macro associated with `uint8_t` when that type exists.[1]

```c
printf("%02" PRIX8, bytes[index]);
```

The `02` requests at least two hexadecimal digits, so values appear as `00`, `41`, `52`, or `A7`. Do not assume that `%x` is universally the right direct format for every typedef. The standard macros express the type relationship explicitly.

## 5. Validate the interface before reading the buffer

The companion helper rejects a null pointer, a zero count, and a null output pointer before it accesses `bytes[index]` or assigns an output.

```c
if (bytes == NULL || count == 0 || zero_count_out == NULL) {
    return STATUS_INVALID_ARGUMENT;
}
```

A zero-length buffer can be valid in some broader APIs, but this teaching program rejects it so that the report always describes at least one unit. The important design lesson is not that every real API must reject zero length. It is that the function’s contract should state what the pointer and count combination means, then enforce that contract before traversal.

The helper assigns `*zero_count_out` only after the full loop finishes successfully. This preserves C 003’s rule that a caller must inspect a status before using an output value.

## 6. Worked example: report an explicit-length byte buffer

The companion program contains six `uint8_t` values. Two are `0x00`, and both must appear in the hexadecimal report and contribute to the zero count. There is no C-string operation in the program.

```c
#include <inttypes.h>
#include <stddef.h>
#include <stdint.h>
#include <stdio.h>
#include <stdlib.h>

#if !defined(UINT8_MAX)
#error "This program requires an exact 8-bit uint8_t type."
#endif

typedef enum {
    STATUS_OK = 0,
    STATUS_INVALID_ARGUMENT = 1,
    STATUS_OUTPUT_FAILURE = 2
} Status;

static Status count_zero_bytes(const uint8_t bytes[], size_t count,
                               size_t *zero_count_out)
{
    size_t zero_count = 0;

    if (bytes == NULL || count == 0 || zero_count_out == NULL) {
        return STATUS_INVALID_ARGUMENT;
    }

    for (size_t index = 0; index < count; ++index) {
        if (bytes[index] == UINT8_C(0)) {
            ++zero_count;
        }
    }

    *zero_count_out = zero_count;
    return STATUS_OK;
}

static Status write_byte_report(const uint8_t bytes[], size_t count,
                                size_t zero_count)
{
    if (bytes == NULL || count == 0) {
        return STATUS_INVALID_ARGUMENT;
    }

    if (printf("Byte count: %zu\nBytes:", count) < 0) {
        return STATUS_OUTPUT_FAILURE;
    }

    for (size_t index = 0; index < count; ++index) {
        if (printf(" %02" PRIX8, bytes[index]) < 0) {
            return STATUS_OUTPUT_FAILURE;
        }
    }

    if (printf("\nZero-valued bytes: %zu\n", zero_count) < 0) {
        return STATUS_OUTPUT_FAILURE;
    }

    return STATUS_OK;
}

int main(void)
{
    const uint8_t payload[] = {
        UINT8_C(0x52), UINT8_C(0x00), UINT8_C(0x41),
        UINT8_C(0xA7), UINT8_C(0x00), UINT8_C(0x7F)
    };
    const size_t payload_count = sizeof payload / sizeof payload[0];
    size_t zero_count;
    Status status;

    status = count_zero_bytes(payload, payload_count, &zero_count);
    if (status != STATUS_OK) {
        (void)fputs("Cannot inspect the byte buffer.\n", stderr);
        return EXIT_FAILURE;
    }

    status = write_byte_report(payload, payload_count, zero_count);
    if (status != STATUS_OK) {
        return EXIT_FAILURE;
    }

    return EXIT_SUCCESS;
}
```

Compile and run the committed companion file exactly as follows:

```sh
cc -std=c17 -Wall -Wextra -Wpedantic \
  -o c-006-unsigned-integers-and-byte-arrays \
  examples/c/c-006-unsigned-integers-and-byte-arrays.c
./c-006-unsigned-integers-and-byte-arrays
```

Expected output:

```text
Byte count: 6
Bytes: 52 00 41 A7 00 7F
Zero-valued bytes: 2
```

The output proves that `0x00` remains in the report twice. If the program used a C-string boundary, it would stop at the first zero and lose the remaining data.

## 7. Guided practice

Answer before reading the spoiler section.

1. What is the difference between a C string and an explicit-length byte buffer?
2. In `{0x41, 0x00, 0x42}` with count `3`, how many units are valid?
3. Why must a byte-buffer function receive `size_t count`?
4. What loop condition visits every valid unit exactly once?
5. Why must the loop not stop when `bytes[index] == 0`?
6. Which header supplies `uint8_t`?
7. Which header supplies `PRIX8`?
8. From Russian 006, say “I am at home.”

## 8. Independent exercise

Create `exercise.c` beside the companion program. Define a fixed `uint8_t` array containing six values, including at least one `UINT8_C(0x00)` and one value above `UINT8_C(0x7F)`. Write a `count_high_bytes` helper that receives `const uint8_t bytes[]`, `size_t count`, and a `size_t *result` output parameter.

The helper must reject a null buffer pointer, a zero count, and a null output pointer. It must use `index < count` and count values for which `bytes[index] >= UINT8_C(0x80)`. Print every unit as two uppercase hexadecimal digits with `PRIX8`, then print the result. Do not use `strlen`, `%s`, or any sentinel-based stopping condition. Test an array with an embedded zero and confirm that units after the zero still appear in the output.

This is data inspection, not a cryptographic algorithm. It must not be described as encryption, hashing, or protection for real data.

## 9. Validate with sanitizers

Run the repository’s deterministic test:

```sh
./tests/c-006-unsigned-integers-and-byte-arrays.sh
```

Then build and run with AddressSanitizer and UndefinedBehaviorSanitizer when supported:

```sh
cc -std=c17 -Wall -Wextra -Wpedantic \
  -fsanitize=address,undefined -fno-omit-frame-pointer -g \
  -o c-006-sanitized \
  examples/c/c-006-unsigned-integers-and-byte-arrays.c
./c-006-sanitized
```

The program should print all six units and a zero count of `2`, with no sanitizer report. Sanitizers assist testing, but explicit pointer checks, an explicit count, and the `index < count` guard remain the source-level proof of the buffer boundary.

## 10. Final self-check

You are ready for the next lesson if you can do all of the following without looking back.

1. Explain why a `uint8_t` buffer needs an explicit length even when it contains `0x00`.
2. State the difference between `\0` as a C-string terminator and `0x00` as byte-buffer data.
3. Write a safe loop header for a byte buffer with `count` valid units.
4. Name the headers that provide `uint8_t` and `PRIX8`.
5. Explain why the example’s hexadecimal loop prints values after an embedded zero.
6. State why `uint8_t` availability should be checked when exact 8-bit units are a program requirement.
7. Recall the Russian phrase **Я дома.**

## Spoiler: answers and model response

1. The function receives a pointer, not the caller’s complete array object, so it needs the count to know the valid boundary.
2. `index < count`.
3. `\0` marks the end of a C string.
4. `strlen` searches for a C-string terminator; an arbitrary byte buffer may have embedded zeros or no terminator at all.
5. **Я читаю в библиотеке.** means “I read at the library.”
6. `uint8_t` provides exactly 8 unsigned bits when the implementation supplies it; a C string is a separate text convention.
7. All **3** units are valid. The middle `0x00` is data, not an end marker.
8. A function parameter is a pointer, so it does not retain the caller’s array length. Pass `count` explicitly.
9. `for (size_t index = 0; index < count; ++index)`.
10. A zero-valued byte may be ordinary binary data, so the loop continues until the explicit count is reached.
11. `<stdint.h>` supplies `uint8_t`; `<inttypes.h>` supplies `PRIX8`.
12. “I am at home” is **Я дома.**
13. Exact-width types are optional in ISO C. A program that requires an exact 8-bit unsigned type should detect the absence of `uint8_t` rather than quietly substitute a type with different width or representation.

A model buffer declaration is:

```c
const uint8_t bytes[] = {
    UINT8_C(0x41), UINT8_C(0x00), UINT8_C(0xC3)
};
const size_t count = sizeof bytes / sizeof bytes[0];
```

All three values are part of the buffer.

## Next lesson

The next scheduled lesson returns to Russian: **Russian 007 — yes/no questions, `не`, and concise responses about routines and locations.**

## References

[1]: https://en.cppreference.com/w/c/types/integer "Fixed width integer types — cppreference.com"
[2]: https://en.cppreference.com/w/c/string/byte/strlen "strlen, strnlen_s — cppreference.com"
[3]: https://en.cppreference.com/w/c/types/limits "Numeric limits — cppreference.com"
