# C 020 — Counted Byte Copy and Equality

**Estimated study time:** 25–35 minutes
**Prerequisites:** C 001–017 and the immediately preceding lessons
**Companion program:** [`examples/c/c-020-counted-byte-copy-and-equality.c`](../../examples/c/c-020-counted-byte-copy-and-equality.c)
**Deterministic test:** [`tests/c-020-counted-byte-copy-and-equality.sh`](../../tests/c-020-counted-byte-copy-and-equality.sh)
**Author:** Manus AI

## What you will be able to do

By the end of this lesson, you should be able to copy and compare byte buffers using explicit lengths, without treating zero-valued bytes as terminators.

## Retrieval practice

1. Why is a byte buffer not the same thing as a C string?
2. State the bounded loop condition for `count` valid elements.
3. Why must file-operation return values be checked?
4. From the preceding Russian lesson, translate one familiar model phrase.

## Core concept

The example treats bytes as counted data. It uses explicit `size_t` lengths, checks every external result, and avoids relying on host memory layout or a zero byte as a terminator. It is portable ISO C17 and does not create executable payloads, native-code loaders, arbitrary assembly execution, process injection, or self-modifying code.

## Worked example

```c
#include <inttypes.h>
#include <stdint.h>
#include <stdio.h>
#include <stdlib.h>

static int copy_bytes(uint8_t out[], size_t out_count, const uint8_t in[], size_t count)
{
    if ((out == NULL || in == NULL) && count != 0) return 0;
    if (count > out_count) return 0;
    for (size_t index = 0; index < count; ++index) out[index] = in[index];
    return 1;
}
static int equal_bytes(const uint8_t a[], const uint8_t b[], size_t count)
{
    if ((a == NULL || b == NULL) && count != 0) return 0;
    for (size_t index = 0; index < count; ++index) if (a[index] != b[index]) return 0;
    return 1;
}
int main(void)
{
    const uint8_t source[] = {UINT8_C(0x41),UINT8_C(0),UINT8_C(0x42)};
    uint8_t copy[3] = {0};
    if (!copy_bytes(copy, sizeof copy, source, sizeof source) || !equal_bytes(source, copy, sizeof source)) return EXIT_FAILURE;
    if (printf("Copied bytes: %zu\nEqual: yes\n", sizeof source) < 0) return EXIT_FAILURE;
    return EXIT_SUCCESS;
}
```

Compile and run it exactly as follows:

```sh
cc -std=c17 -Wall -Wextra -Wpedantic \
  -o c-020-counted-byte-copy-and-equality \
  examples/c/c-020-counted-byte-copy-and-equality.c
./c-020-counted-byte-copy-and-equality
```

Expected output:

```text
Copied bytes: 3
Equal: yes
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
./tests/c-020-counted-byte-copy-and-equality.sh
```

Then, where supported, compile and run with sanitizers:

```sh
cc -std=c17 -Wall -Wextra -Wpedantic \
  -fsanitize=address,undefined -fno-omit-frame-pointer -g \
  -o c-020-sanitized \
  examples/c/c-020-counted-byte-copy-and-equality.c
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
