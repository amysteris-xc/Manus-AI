# C 010 — Bounded Chunk File Statistics

**Estimated study time:** 25–35 minutes
**Prerequisites:** C 001–007 and the immediately preceding lessons in this expanded sequence
**Companion program:** [`examples/c/c-010-bounded-chunk-file-statistics.c`](../../examples/c/c-010-bounded-chunk-file-statistics.c)
**Deterministic test:** [`tests/c-010-bounded-chunk-file-statistics.sh`](../../tests/c-010-bounded-chunk-file-statistics.sh)
**Author:** Manus AI

## What you will be able to do

By the end of this lesson, you should be able to scan a binary file in bounded chunks, count all bytes, zero-valued bytes, and high-valued bytes, and check `size_t` counters for overflow.

## Retrieval practice

Answer before reopening earlier lessons.

1. Why does a byte buffer need an explicit `size_t` length?
2. State the safe forward loop condition for `count` valid elements.
3. What must be true of a shift count for an exact 32-bit word?
4. From Russian 007, translate **Я не читаю в библиотеке.**

## Core concept

A large input should not be read into an unbounded buffer. The scanner reuses a small `uint8_t` chunk, uses the `fread` result as its valid count, and checks `ferror` after a short read.

The program uses portable ISO C17, checks return values at every external boundary, and keeps logical values separate from C strings. It uses `uint8_t` and `uint32_t` only where their exact-width meaning is part of the lesson’s contract. It does not create shellcode, load executable code, inject into processes, or modify its own instructions.

## Worked example

```c
#include <stdint.h>
#include <stdio.h>
#include <stdlib.h>

int main(int argc, char *argv[])
{
    uint8_t buffer[64];
    size_t bytes = 0, zeros = 0, high = 0;
    FILE *in;
    if (argc != 2) { (void)fputs("Usage: stats INPUT\n", stderr); return EXIT_FAILURE; }
    in = fopen(argv[1], "rb");
    if (in == NULL) return EXIT_FAILURE;
    for (;;) {
        size_t got = fread(buffer, 1, sizeof buffer, in);
        if (SIZE_MAX - bytes < got) { (void)fclose(in); return EXIT_FAILURE; }
        for (size_t index = 0; index < got; ++index) {
            if (buffer[index] == UINT8_C(0)) ++zeros;
            if (buffer[index] >= UINT8_C(0x80)) ++high;
        }
        bytes += got;
        if (got < sizeof buffer) { if (ferror(in)) { (void)fclose(in); return EXIT_FAILURE; } break; }
    }
    if (fclose(in) != 0) return EXIT_FAILURE;
    if (printf("Bytes: %zu\nZero bytes: %zu\nHigh bytes: %zu\n", bytes, zeros, high) < 0) return EXIT_FAILURE;
    return EXIT_SUCCESS;
}
```

Compile and run the committed companion program exactly as follows:

```sh
cc -std=c17 -Wall -Wextra -Wpedantic \
  -o c-010-bounded-chunk-file-statistics \
  examples/c/c-010-bounded-chunk-file-statistics.c
./c-010-bounded-chunk-file-statistics fixture.bin
```

Expected output:

```text
Bytes: 5
Zero bytes: 1
High bytes: 2
```

## Guided practice

1. Identify the explicit input boundary in the example.
2. Name the status or return value checked after each external operation.
3. Explain why the example does not use a sentinel such as `0x00` as a general array boundary.
4. State one invalid input that the companion program rejects or handles safely.
5. From the immediately preceding Russian lesson, produce one accurate target phrase.

## Independent exercise

Add a count of printable ASCII values from `0x20` through `0x7E`. Keep the chunk boundary and use only indexes strictly less than `got`.

Keep all counts in `size_t`, validate any conversion before narrowing it, and preserve the original object when an allocation or parsing operation fails. This is a data-processing exercise, not a cryptographic primitive. It must never be presented as suitable for security, encryption, authentication, or protection of real data.

## Validation

Run the repository test:

```sh
./tests/c-010-bounded-chunk-file-statistics.sh
```

Then, where supported, build with AddressSanitizer and UndefinedBehaviorSanitizer:

```sh
cc -std=c17 -Wall -Wextra -Wpedantic \
  -fsanitize=address,undefined -fno-omit-frame-pointer -g \
  -o c-010-sanitized \
  examples/c/c-010-bounded-chunk-file-statistics.c
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

The next scheduled lesson is **Russian 011 — time words and a simple daily schedule.**

## References

[1]: https://en.cppreference.com/w/c/types/integer "Fixed width integer types — cppreference.com"
[2]: https://en.cppreference.com/w/c/language/operator_arithmetic "Arithmetic operators — cppreference.com"
[3]: https://en.cppreference.com/w/c/io/fread "fread — cppreference.com"
[4]: https://en.cppreference.com/w/c/memory/realloc "realloc — cppreference.com"
