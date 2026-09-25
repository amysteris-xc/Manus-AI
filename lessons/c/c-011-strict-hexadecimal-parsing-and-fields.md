# C 011 — Strict Hexadecimal Parsing and Field Validation

**Estimated study time:** 25–35 minutes
**Prerequisites:** C 001–007 and the immediately preceding lessons in this expanded sequence
**Companion program:** [`examples/c/c-011-strict-hexadecimal-parsing-and-fields.c`](../../examples/c/c-011-strict-hexadecimal-parsing-and-fields.c)
**Deterministic test:** [`tests/c-011-strict-hexadecimal-parsing-and-fields.sh`](../../tests/c-011-strict-hexadecimal-parsing-and-fields.sh)
**Author:** Manus AI

## What you will be able to do

By the end of this lesson, you should be able to parse one hexadecimal `uint32_t` argument strictly, reject malformed or overflowing text, and report its named bit fields.

## Retrieval practice

Answer before reopening earlier lessons.

1. Why does a byte buffer need an explicit `size_t` length?
2. State the safe forward loop condition for `count` valid elements.
3. What must be true of a shift count for an exact 32-bit word?
4. From Russian 007, translate **Я не читаю в библиотеке.**

## Core concept

Text-to-integer conversion is a boundary. `strtoul` must be checked with `errno`, an end pointer, and a range check before casting to `uint32_t`.

The program uses portable ISO C17, checks return values at every external boundary, and keeps logical values separate from C strings. It uses `uint8_t` and `uint32_t` only where their exact-width meaning is part of the lesson’s contract. It does not create shellcode, load executable code, inject into processes, or modify its own instructions.

## Worked example

```c
#include <errno.h>
#include <inttypes.h>
#include <stdint.h>
#include <stdio.h>
#include <stdlib.h>

int main(int argc, char *argv[])
{
    char *end = NULL;
    unsigned long parsed;
    uint32_t word;
    if (argc != 2) { (void)fputs("Usage: fields HEX_WORD\n", stderr); return EXIT_FAILURE; }
    errno = 0;
    parsed = strtoul(argv[1], &end, 0);
    if (errno != 0 || end == argv[1] || *end != '\0' || parsed > UINT32_MAX) { (void)fputs("Invalid uint32 value.\n", stderr); return EXIT_FAILURE; }
    word = (uint32_t)parsed;
    if (printf("Word: 0x%08" PRIX32 "\nCategory: %" PRIu32 "\nFlags: %" PRIu32 "\nSequence: %" PRIu32 "\n", word, word & UINT32_C(0xFF), (word >> 8) & UINT32_C(0xFF), (word >> 16) & UINT32_C(0xFFFF)) < 0) return EXIT_FAILURE;
    return EXIT_SUCCESS;
}
```

Compile and run the committed companion program exactly as follows:

```sh
cc -std=c17 -Wall -Wextra -Wpedantic \
  -o c-011-strict-hexadecimal-parsing-and-fields \
  examples/c/c-011-strict-hexadecimal-parsing-and-fields.c
./c-011-strict-hexadecimal-parsing-and-fields 0xBEEFA010
```

Expected output:

```text
Word: 0xBEEFA010
Category: 16
Flags: 160
Sequence: 48879
```

## Guided practice

1. Identify the explicit input boundary in the example.
2. Name the status or return value checked after each external operation.
3. Explain why the example does not use a sentinel such as `0x00` as a general array boundary.
4. State one invalid input that the companion program rejects or handles safely.
5. From the immediately preceding Russian lesson, produce one accurate target phrase.

## Independent exercise

Require a `0x` prefix explicitly before calling the parser, then retain all existing malformed-text and range checks.

Keep all counts in `size_t`, validate any conversion before narrowing it, and preserve the original object when an allocation or parsing operation fails. This is a data-processing exercise, not a cryptographic primitive. It must never be presented as suitable for security, encryption, authentication, or protection of real data.

## Validation

Run the repository test:

```sh
./tests/c-011-strict-hexadecimal-parsing-and-fields.sh
```

Then, where supported, build with AddressSanitizer and UndefinedBehaviorSanitizer:

```sh
cc -std=c17 -Wall -Wextra -Wpedantic \
  -fsanitize=address,undefined -fno-omit-frame-pointer -g \
  -o c-011-sanitized \
  examples/c/c-011-strict-hexadecimal-parsing-and-fields.c
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

The next scheduled lesson is **Russian 012 — singular past tense and gender.**

## References

[1]: https://en.cppreference.com/w/c/types/integer "Fixed width integer types — cppreference.com"
[2]: https://en.cppreference.com/w/c/language/operator_arithmetic "Arithmetic operators — cppreference.com"
[3]: https://en.cppreference.com/w/c/io/fread "fread — cppreference.com"
[4]: https://en.cppreference.com/w/c/memory/realloc "realloc — cppreference.com"
