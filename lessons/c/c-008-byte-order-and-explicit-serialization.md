# C 008 — Byte Order and Explicit Serialization

**Estimated study time:** 25–35 minutes
**Prerequisites:** C 001–007 and the immediately preceding lessons in this expanded sequence
**Companion program:** [`examples/c/c-008-byte-order-and-explicit-serialization.c`](../../examples/c/c-008-byte-order-and-explicit-serialization.c)
**Deterministic test:** [`tests/c-008-byte-order-and-explicit-serialization.sh`](../../tests/c-008-byte-order-and-explicit-serialization.sh)
**Author:** Manus AI

## What you will be able to do

By the end of this lesson, you should be able to encode and decode one `uint32_t` value as four explicitly ordered `uint8_t` units without relying on memory layout.

## Retrieval practice

Answer before reopening earlier lessons.

1. Why does a byte buffer need an explicit `size_t` length?
2. State the safe forward loop condition for `count` valid elements.
3. What must be true of a shift count for an exact 32-bit word?
4. From Russian 007, translate **Я не читаю в библиотеке.**

## Core concept

A logical numeric word is not automatically an external byte format. The program names big-endian order and uses shifts plus masks to write and read each byte explicitly.

The program uses portable ISO C17, checks return values at every external boundary, and keeps logical values separate from C strings. It uses `uint8_t` and `uint32_t` only where their exact-width meaning is part of the lesson’s contract. It does not create shellcode, load executable code, inject into processes, or modify its own instructions.

## Worked example

```c
#include <inttypes.h>
#include <stdint.h>
#include <stdio.h>
#include <stdlib.h>

typedef enum { STATUS_OK = 0, STATUS_INVALID_ARGUMENT = 1, STATUS_OUTPUT_FAILURE = 2 } Status;

static Status encode_be32(uint32_t value, uint8_t out[4])
{
    if (out == NULL) return STATUS_INVALID_ARGUMENT;
    out[0] = (uint8_t)(value >> 24);
    out[1] = (uint8_t)(value >> 16);
    out[2] = (uint8_t)(value >> 8);
    out[3] = (uint8_t)value;
    return STATUS_OK;
}

static Status decode_be32(const uint8_t in[4], uint32_t *value_out)
{
    if (in == NULL || value_out == NULL) return STATUS_INVALID_ARGUMENT;
    *value_out = ((uint32_t)in[0] << 24) | ((uint32_t)in[1] << 16) |
                 ((uint32_t)in[2] << 8) | (uint32_t)in[3];
    return STATUS_OK;
}

int main(void)
{
    const uint32_t input = UINT32_C(0x0123052A);
    uint8_t bytes[4];
    uint32_t decoded;
    if (encode_be32(input, bytes) != STATUS_OK || decode_be32(bytes, &decoded) != STATUS_OK) return EXIT_FAILURE;
    if (printf("Input word: 0x%08" PRIX32 "\nBig-endian bytes: %02" PRIX8 " %02" PRIX8 " %02" PRIX8 " %02" PRIX8 "\nDecoded word: 0x%08" PRIX32 "\n", input, bytes[0], bytes[1], bytes[2], bytes[3], decoded) < 0) return EXIT_FAILURE;
    return EXIT_SUCCESS;
}
```

Compile and run the committed companion program exactly as follows:

```sh
cc -std=c17 -Wall -Wextra -Wpedantic \
  -o c-008-byte-order-and-explicit-serialization \
  examples/c/c-008-byte-order-and-explicit-serialization.c
./c-008-byte-order-and-explicit-serialization
```

Expected output:

```text
Input word: 0x0123052A
Big-endian bytes: 01 23 05 2A
Decoded word: 0x0123052A
```

## Guided practice

1. Identify the explicit input boundary in the example.
2. Name the status or return value checked after each external operation.
3. Explain why the example does not use a sentinel such as `0x00` as a general array boundary.
4. State one invalid input that the companion program rejects or handles safely.
5. From the immediately preceding Russian lesson, produce one accurate target phrase.

## Independent exercise

Write `encode_le32` and `decode_le32` for a documented little-endian format. Test a word with different values in all four bytes. Do not cast a `uint32_t *` to a byte pointer as a substitute for defining the format.

Keep all counts in `size_t`, validate any conversion before narrowing it, and preserve the original object when an allocation or parsing operation fails. This is a data-processing exercise, not a cryptographic primitive. It must never be presented as suitable for security, encryption, authentication, or protection of real data.

## Validation

Run the repository test:

```sh
./tests/c-008-byte-order-and-explicit-serialization.sh
```

Then, where supported, build with AddressSanitizer and UndefinedBehaviorSanitizer:

```sh
cc -std=c17 -Wall -Wextra -Wpedantic \
  -fsanitize=address,undefined -fno-omit-frame-pointer -g \
  -o c-008-sanitized \
  examples/c/c-008-byte-order-and-explicit-serialization.c
```

## Final self-check

You are ready for the next lesson if you can explain the input boundary, the relevant integer width, the return-value checks, the overflow or shift guard, the deterministic test vector, and why the example is not a cryptographic construction.

## Spoiler: answers and model response

1. A byte buffer uses an explicit length because zero-valued bytes can be ordinary data.
2. The safe condition is `index < count`.
3. The shift count must be nonnegative and strictly less than the promoted left operand’s width.
4. **Я не читаю в библиотеке.** means “I do not read at the library.”
5. The example’s checked boundary and deterministic test make its behaviour reviewable; they do not turn it into a cryptographic security mechanism.

## Next lesson

The next scheduled lesson is **Russian 009 — plural nouns and small groups.**

## References

[1]: https://en.cppreference.com/w/c/types/integer "Fixed width integer types — cppreference.com"
[2]: https://en.cppreference.com/w/c/language/operator_arithmetic "Arithmetic operators — cppreference.com"
[3]: https://en.cppreference.com/w/c/io/fread "fread — cppreference.com"
[4]: https://en.cppreference.com/w/c/memory/realloc "realloc — cppreference.com"
