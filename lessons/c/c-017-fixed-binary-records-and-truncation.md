# C 017 — Fixed Binary Records and Truncation Handling

**Estimated study time:** 25–35 minutes
**Prerequisites:** C 001–007 and the immediately preceding lessons in this expanded sequence
**Companion program:** [`examples/c/c-017-fixed-binary-records-and-truncation.c`](../../examples/c/c-017-fixed-binary-records-and-truncation.c)
**Deterministic test:** [`tests/c-017-fixed-binary-records-and-truncation.sh`](../../tests/c-017-fixed-binary-records-and-truncation.sh)
**Author:** Manus AI

## What you will be able to do

By the end of this lesson, you should be able to read a file of explicit eight-byte records, reject a truncated final record, and decode each record without using a C struct as the file format.

## Retrieval practice

Answer before reopening earlier lessons.

1. Why does a byte buffer need an explicit `size_t` length?
2. State the safe forward loop condition for `count` valid elements.
3. What must be true of a shift count for an exact 32-bit word?
4. From Russian 007, translate **Я не читаю в библиотеке.**

## Core concept

A fixed record format still needs checked I/O. This reader asks `fread` for eight byte-units, treats `0` as normal end only after checking `ferror`, and rejects any nonzero result smaller than eight as truncation.

The program uses portable ISO C17, checks return values at every external boundary, and keeps logical values separate from C strings. It uses `uint8_t` and `uint32_t` only where their exact-width meaning is part of the lesson’s contract. It does not create shellcode, load executable code, inject into processes, or modify its own instructions.

## Worked example

```c
#include <inttypes.h>
#include <stdint.h>
#include <stdio.h>
#include <stdlib.h>

static uint32_t get_be32(const uint8_t in[4]) { return ((uint32_t)in[0] << 24) | ((uint32_t)in[1] << 16) | ((uint32_t)in[2] << 8) | in[3]; }
static uint16_t get_be16(const uint8_t in[2]) { return (uint16_t)(((uint16_t)in[0] << 8) | in[1]); }

int main(int argc, char *argv[])
{
    uint8_t record[8];
    size_t count = 0;
    FILE *in;
    if (argc != 2) return EXIT_FAILURE;
    in = fopen(argv[1], "rb"); if (in == NULL) return EXIT_FAILURE;
    for (;;) {
        size_t got = fread(record, 1, sizeof record, in);
        if (got == 0) { if (ferror(in)) { (void)fclose(in); return EXIT_FAILURE; } break; }
        if (got != sizeof record) { (void)fclose(in); (void)fputs("Truncated record.\n", stderr); return EXIT_FAILURE; }
        if (printf("Record %zu: id=%" PRIu32 " flags=%" PRIu16 " kind=%" PRIu8 " status=%" PRIu8 "\n", count, get_be32(record), get_be16(record + 4), record[6], record[7]) < 0) { (void)fclose(in); return EXIT_FAILURE; }
        if (count == SIZE_MAX) { (void)fclose(in); return EXIT_FAILURE; }
        ++count;
    }
    if (fclose(in) != 0) return EXIT_FAILURE;
    if (printf("Records: %zu\n", count) < 0) return EXIT_FAILURE;
    return EXIT_SUCCESS;
}
```

Compile and run the committed companion program exactly as follows:

```sh
cc -std=c17 -Wall -Wextra -Wpedantic \
  -o c-017-fixed-binary-records-and-truncation \
  examples/c/c-017-fixed-binary-records-and-truncation.c
./c-017-fixed-binary-records-and-truncation fixture.bin
```

Expected output:

```text
Record 0: id=42 flags=5 kind=1 status=0
Record 1: id=291 flags=160 kind=2 status=1
Records: 2
```

## Guided practice

1. Identify the explicit input boundary in the example.
2. Name the status or return value checked after each external operation.
3. Explain why the example does not use a sentinel such as `0x00` as a general array boundary.
4. State one invalid input that the companion program rejects or handles safely.
5. From the immediately preceding Russian lesson, produce one accurate target phrase.

## Independent exercise

Add a maximum record count and a checked summary field. Keep the explicit byte layout; do not write or read raw C structs as a portable file format.

Keep all counts in `size_t`, validate any conversion before narrowing it, and preserve the original object when an allocation or parsing operation fails. This is a data-processing exercise, not a cryptographic primitive. It must never be presented as suitable for security, encryption, authentication, or protection of real data.

## Validation

Run the repository test:

```sh
./tests/c-017-fixed-binary-records-and-truncation.sh
```

Then, where supported, build with AddressSanitizer and UndefinedBehaviorSanitizer:

```sh
cc -std=c17 -Wall -Wextra -Wpedantic \
  -fsanitize=address,undefined -fno-omit-frame-pointer -g \
  -o c-017-sanitized \
  examples/c/c-017-fixed-binary-records-and-truncation.c
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

The next scheduled lesson is **Russian 018 — directions and prepositions in general places.**

## References

[1]: https://en.cppreference.com/w/c/types/integer "Fixed width integer types — cppreference.com"
[2]: https://en.cppreference.com/w/c/language/operator_arithmetic "Arithmetic operators — cppreference.com"
[3]: https://en.cppreference.com/w/c/io/fread "fread — cppreference.com"
[4]: https://en.cppreference.com/w/c/memory/realloc "realloc — cppreference.com"
