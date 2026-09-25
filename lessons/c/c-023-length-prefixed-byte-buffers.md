# C 023 — Length-Prefixed Byte Buffers

**Estimated study time:** 25–35 minutes
**Prerequisites:** C 001–017 and the immediately preceding lessons
**Companion program:** [`examples/c/c-023-length-prefixed-byte-buffers.c`](../../examples/c/c-023-length-prefixed-byte-buffers.c)
**Deterministic test:** [`tests/c-023-length-prefixed-byte-buffers.sh`](../../tests/c-023-length-prefixed-byte-buffers.sh)
**Author:** Manus AI

## What you will be able to do

By the end of this lesson, you should be able to encode and decode a byte payload with a two-byte big-endian length prefix and strict capacity checks.

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

static int encode(uint8_t out[], size_t capacity, const uint8_t payload[], size_t length, size_t *used)
{
    if (out == NULL || payload == NULL || used == NULL || length > UINT16_MAX || capacity < 2 || length > capacity - 2) return 0;
    out[0] = (uint8_t)(length >> 8); out[1] = (uint8_t)length;
    for (size_t index = 0; index < length; ++index) out[index + 2] = payload[index];
    *used = length + 2; return 1;
}
int main(void)
{
    const uint8_t payload[] = {UINT8_C(0x41),UINT8_C(0),UINT8_C(0x42)};
    uint8_t frame[8]; size_t used;
    if (!encode(frame, sizeof frame, payload, sizeof payload, &used)) return EXIT_FAILURE;
    if (printf("Frame bytes: %zu\nLength: %u\n", used, (unsigned)(((unsigned)frame[0] << 8) | frame[1])) < 0) return EXIT_FAILURE;
    return EXIT_SUCCESS;
}
```

Compile and run it exactly as follows:

```sh
cc -std=c17 -Wall -Wextra -Wpedantic \
  -o c-023-length-prefixed-byte-buffers \
  examples/c/c-023-length-prefixed-byte-buffers.c
./c-023-length-prefixed-byte-buffers
```

Expected output:

```text
Frame bytes: 5
Length: 3
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
./tests/c-023-length-prefixed-byte-buffers.sh
```

Then, where supported, compile and run with sanitizers:

```sh
cc -std=c17 -Wall -Wextra -Wpedantic \
  -fsanitize=address,undefined -fno-omit-frame-pointer -g \
  -o c-023-sanitized \
  examples/c/c-023-length-prefixed-byte-buffers.c
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
