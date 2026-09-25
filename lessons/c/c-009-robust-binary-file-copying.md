# C 009 — Robust Binary File Copying

**Estimated study time:** 25–35 minutes
**Prerequisites:** C 001–007 and the immediately preceding lessons in this expanded sequence
**Companion program:** [`examples/c/c-009-robust-binary-file-copying.c`](../../examples/c/c-009-robust-binary-file-copying.c)
**Deterministic test:** [`tests/c-009-robust-binary-file-copying.sh`](../../tests/c-009-robust-binary-file-copying.sh)
**Author:** Manus AI

## What you will be able to do

By the end of this lesson, you should be able to copy a binary file in fixed chunks with checked `fread`, partial-write handling, `ferror`, `fflush`, and checked close results.

## Retrieval practice

Answer before reopening earlier lessons.

1. Why does a byte buffer need an explicit `size_t` length?
2. State the safe forward loop condition for `count` valid elements.
3. What must be true of a shift count for an exact 32-bit word?
4. From Russian 007, translate **Я не читаю в библиотеке.**

## Core concept

File I/O returns results that must be checked. `fread` can return fewer bytes at end-of-file or on error, while `fwrite` can write fewer bytes than requested. This program tracks byte counts with `size_t` and writes each non-empty chunk completely before reading again.

The program uses portable ISO C17, checks return values at every external boundary, and keeps logical values separate from C strings. It uses `uint8_t` and `uint32_t` only where their exact-width meaning is part of the lesson’s contract. It does not create shellcode, load executable code, inject into processes, or modify its own instructions.

## Worked example

```c
#include <stdio.h>
#include <stdlib.h>
#include <stdint.h>

static int write_all(FILE *out, const uint8_t *buffer, size_t count)
{
    size_t written = 0;
    while (written < count) {
        size_t step = fwrite(buffer + written, 1, count - written, out);
        if (step == 0) return 0;
        written += step;
    }
    return 1;
}

int main(int argc, char *argv[])
{
    uint8_t buffer[64];
    size_t total = 0;
    FILE *in;
    FILE *out;
    if (argc != 3) { (void)fputs("Usage: copy INPUT OUTPUT\n", stderr); return EXIT_FAILURE; }
    in = fopen(argv[1], "rb");
    if (in == NULL) { (void)fputs("Cannot open input.\n", stderr); return EXIT_FAILURE; }
    out = fopen(argv[2], "wb");
    if (out == NULL) { (void)fclose(in); (void)fputs("Cannot open output.\n", stderr); return EXIT_FAILURE; }
    for (;;) {
        size_t got = fread(buffer, 1, sizeof buffer, in);
        if (got > 0) {
            if (SIZE_MAX - total < got || !write_all(out, buffer, got)) { (void)fclose(in); (void)fclose(out); return EXIT_FAILURE; }
            total += got;
        }
        if (got < sizeof buffer) {
            if (ferror(in)) { (void)fclose(in); (void)fclose(out); return EXIT_FAILURE; }
            break;
        }
    }
    {
        int flush_status = fflush(out);
        int input_close_status = fclose(in);
        int output_close_status = fclose(out);

        if (flush_status != 0 || input_close_status != 0 ||
            output_close_status != 0) return EXIT_FAILURE;
    }
    if (printf("Copied bytes: %zu\n", total) < 0) return EXIT_FAILURE;
    return EXIT_SUCCESS;
}
```

Compile and run the committed companion program exactly as follows:

```sh
cc -std=c17 -Wall -Wextra -Wpedantic \
  -o c-009-robust-binary-file-copying \
  examples/c/c-009-robust-binary-file-copying.c
./c-009-robust-binary-file-copying fixture.bin copied.bin
```

Expected output:

```text
Copied bytes: 5
```

## Guided practice

1. Identify the explicit input boundary in the example.
2. Name the status or return value checked after each external operation.
3. Explain why the example does not use a sentinel such as `0x00` as a general array boundary.
4. State one invalid input that the companion program rejects or handles safely.
5. From the immediately preceding Russian lesson, produce one accurate target phrase.

## Independent exercise

Extend the copier to reject identical input and output path strings before opening the destination. Preserve checked reads, partial-write handling, and close checks.

Keep all counts in `size_t`, validate any conversion before narrowing it, and preserve the original object when an allocation or parsing operation fails. This is a data-processing exercise, not a cryptographic primitive. It must never be presented as suitable for security, encryption, authentication, or protection of real data.

## Validation

Run the repository test:

```sh
./tests/c-009-robust-binary-file-copying.sh
```

Then, where supported, build with AddressSanitizer and UndefinedBehaviorSanitizer:

```sh
cc -std=c17 -Wall -Wextra -Wpedantic \
  -fsanitize=address,undefined -fno-omit-frame-pointer -g \
  -o c-009-sanitized \
  examples/c/c-009-robust-binary-file-copying.c
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

The next scheduled lesson is **Russian 010 — inanimate direct objects in the accusative.**

## References

[1]: https://en.cppreference.com/w/c/types/integer "Fixed width integer types — cppreference.com"
[2]: https://en.cppreference.com/w/c/language/operator_arithmetic "Arithmetic operators — cppreference.com"
[3]: https://en.cppreference.com/w/c/io/fread "fread — cppreference.com"
[4]: https://en.cppreference.com/w/c/memory/realloc "realloc — cppreference.com"
