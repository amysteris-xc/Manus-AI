# C 013 — Safe `uint32_t` Rotations and Test Vectors

**Estimated study time:** 25–35 minutes
**Prerequisites:** C 001–007 and the immediately preceding lessons in this expanded sequence
**Companion program:** [`examples/c/c-013-safe-uint32-rotations-and-test-vectors.c`](../../examples/c/c-013-safe-uint32-rotations-and-test-vectors.c)
**Deterministic test:** [`tests/c-013-safe-uint32-rotations-and-test-vectors.sh`](../../tests/c-013-safe-uint32-rotations-and-test-vectors.sh)
**Author:** Manus AI

## What you will be able to do

By the end of this lesson, you should be able to implement a bounded 32-bit left rotation with a checked count and verify it against fixed test vectors.

## Retrieval practice

Answer before reopening earlier lessons.

1. Why does a byte buffer need an explicit `size_t` length?
2. State the safe forward loop condition for `count` valid elements.
3. What must be true of a shift count for an exact 32-bit word?
4. From Russian 007, translate **Я не читаю в библиотеке.**

## Core concept

A rotation is not a plain shift. The helper returns the input unchanged for count zero, then uses only counts 1 through 31 so that neither shift operand reaches 32.

The program uses portable ISO C17, checks return values at every external boundary, and keeps logical values separate from C strings. It uses `uint8_t` and `uint32_t` only where their exact-width meaning is part of the lesson’s contract. It does not create shellcode, load executable code, inject into processes, or modify its own instructions.

## Worked example

```c
#include <inttypes.h>
#include <stdint.h>
#include <stdio.h>
#include <stdlib.h>

static int rotate_left32(uint32_t input, unsigned int count, uint32_t *out)
{
    if (out == NULL || count >= 32U) return 0;
    if (count == 0U) { *out = input; return 1; }
    *out = (input << count) | (input >> (32U - count));
    return 1;
}

int main(void)
{
    uint32_t first, second;
    if (!rotate_left32(UINT32_C(0x12345678), 8U, &first) || !rotate_left32(UINT32_C(0x80000001), 1U, &second)) return EXIT_FAILURE;
    if (printf("rotl32(0x12345678, 8) = 0x%08" PRIX32 "\nrotl32(0x80000001, 1) = 0x%08" PRIX32 "\n", first, second) < 0) return EXIT_FAILURE;
    return EXIT_SUCCESS;
}
```

Compile and run the committed companion program exactly as follows:

```sh
cc -std=c17 -Wall -Wextra -Wpedantic \
  -o c-013-safe-uint32-rotations-and-test-vectors \
  examples/c/c-013-safe-uint32-rotations-and-test-vectors.c
./c-013-safe-uint32-rotations-and-test-vectors
```

Expected output:

```text
rotl32(0x12345678, 8) = 0x34567812
rotl32(0x80000001, 1) = 0x00000003
```

## Guided practice

1. Identify the explicit input boundary in the example.
2. Name the status or return value checked after each external operation.
3. Explain why the example does not use a sentinel such as `0x00` as a general array boundary.
4. State one invalid input that the companion program rejects or handles safely.
5. From the immediately preceding Russian lesson, produce one accurate target phrase.

## Independent exercise

Write a matching right-rotation helper. Handle count zero before computing `32 - count`, and reject counts of 32 or greater.

Keep all counts in `size_t`, validate any conversion before narrowing it, and preserve the original object when an allocation or parsing operation fails. This is a data-processing exercise, not a cryptographic primitive. It must never be presented as suitable for security, encryption, authentication, or protection of real data.

## Validation

Run the repository test:

```sh
./tests/c-013-safe-uint32-rotations-and-test-vectors.sh
```

Then, where supported, build with AddressSanitizer and UndefinedBehaviorSanitizer:

```sh
cc -std=c17 -Wall -Wextra -Wpedantic \
  -fsanitize=address,undefined -fno-omit-frame-pointer -g \
  -o c-013-sanitized \
  examples/c/c-013-safe-uint32-rotations-and-test-vectors.c
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

The next scheduled lesson is **Russian 014 — simple future plans with буду and infinitives.**

## References

[1]: https://en.cppreference.com/w/c/types/integer "Fixed width integer types — cppreference.com"
[2]: https://en.cppreference.com/w/c/language/operator_arithmetic "Arithmetic operators — cppreference.com"
[3]: https://en.cppreference.com/w/c/io/fread "fread — cppreference.com"
[4]: https://en.cppreference.com/w/c/memory/realloc "realloc — cppreference.com"
