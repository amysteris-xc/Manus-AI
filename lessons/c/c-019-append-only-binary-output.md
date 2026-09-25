# C 019 — Append-Only Binary Output

**Estimated study time:** 25–35 minutes
**Prerequisites:** C 001–017 and the immediately preceding lessons
**Companion program:** [`examples/c/c-019-append-only-binary-output.c`](../../examples/c/c-019-append-only-binary-output.c)
**Deterministic test:** [`tests/c-019-append-only-binary-output.sh`](../../tests/c-019-append-only-binary-output.sh)
**Author:** Manus AI

## What you will be able to do

By the end of this lesson, you should be able to append a fixed byte record to a file while checking all writes and close status.

## Retrieval practice

1. Why is a byte buffer not the same thing as a C string?
2. State the bounded loop condition for `count` valid elements.
3. Why must file-operation return values be checked?
4. From the preceding Russian lesson, translate one familiar model phrase.

## Core concept

The example treats bytes as counted data. It uses explicit `size_t` lengths, checks every external result, and avoids relying on host memory layout or a zero byte as a terminator. It is portable ISO C17 and does not create executable payloads, native-code loaders, arbitrary assembly execution, process injection, or self-modifying code.

## Worked example

```c
#include <stdint.h>
#include <stdio.h>
#include <stdlib.h>

int main(int argc, char *argv[])
{
    const uint8_t record[] = {UINT8_C(0x48),UINT8_C(0x45),UINT8_C(0x4C),UINT8_C(0x4C),UINT8_C(0x4F)};
    FILE *out;
    if (argc != 2) return EXIT_FAILURE;
    out = fopen(argv[1], "ab");
    if (out == NULL) return EXIT_FAILURE;
    if (fwrite(record, 1, sizeof record, out) != sizeof record) {
        (void)fclose(out);
        return EXIT_FAILURE;
    }
    {
        int flush_status = fflush(out);
        int close_status = fclose(out);

        if (flush_status != 0 || close_status != 0) return EXIT_FAILURE;
    }
    if (printf("Bytes appended: %zu\n", sizeof record) < 0) return EXIT_FAILURE;
    return EXIT_SUCCESS;
}
```

Compile and run it exactly as follows:

```sh
cc -std=c17 -Wall -Wextra -Wpedantic \
  -o c-019-append-only-binary-output \
  examples/c/c-019-append-only-binary-output.c
./c-019-append-only-binary-output log.bin
```

Expected output:

```text
Bytes appended: 5
```

## Guided practice

1. Identify the program’s input and output boundaries.
2. Name one count, capacity, range, or return-value check.
3. Explain why embedded `0x00` bytes remain valid data here.
4. Describe one malformed or oversize input that should be rejected.
5. Recall one accurate phrase from the immediately preceding Russian lesson.

## Independent exercise

Extend the program with one additional checked value or test case. Preserve explicit counts, avoid unchecked casts, and validate every external result before using it.

## Validation

Run the deterministic test:

```sh
./tests/c-019-append-only-binary-output.sh
```

Then, where supported, compile and run with sanitizers:

```sh
cc -std=c17 -Wall -Wextra -Wpedantic \
  -fsanitize=address,undefined -fno-omit-frame-pointer -g \
  -o c-019-sanitized \
  examples/c/c-019-append-only-binary-output.c
```

## Final self-check

You are ready for the next lesson if you can explain the explicit boundary, each relevant return-value check, the `size_t` count contract, and why this program is not a cryptographic security mechanism.

## Spoiler: answers and model response

1. A byte buffer has an explicit length and may contain any byte value, including `0x00`; a C string ends at its first zero byte.
2. The safe forward condition is `index < count`.
3. I/O can be partial or fail, so its result determines whether the requested work completed.
4. A valid answer names a null pointer, capacity, arithmetic overflow, short read/write, or malformed input check.
5. The example is a data-processing exercise, not encryption, authentication, a secure hash, or a security control.

## Next lesson

The next scheduled lesson returns to the Russian track.

## References

[1]: https://en.cppreference.com/w/c/io/fread "fread — cppreference.com"
[2]: https://en.cppreference.com/w/c/io/fwrite "fwrite — cppreference.com"
[3]: https://en.cppreference.com/w/c/types/integer "Fixed width integer types — cppreference.com"
