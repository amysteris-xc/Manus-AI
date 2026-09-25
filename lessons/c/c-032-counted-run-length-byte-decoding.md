# C 032 — Counted Run-Length Byte Decoding

**Estimated study time:** 25–35 minutes
**Prerequisites:** C 001–031 and the immediately preceding lessons
**Companion program:** [`examples/c/c-032-counted-run-length-byte-decoding.c`](../../examples/c/c-032-counted-run-length-byte-decoding.c)
**Deterministic test:** [`tests/c-032-counted-run-length-byte-decoding.sh`](../../tests/c-032-counted-run-length-byte-decoding.sh)
**Author:** Manus AI

## What you will be able to do

By the end of this lesson, you should be able to expand simple run-length encoded pairs (count, byte) into an output buffer with bound enforcement.

## Retrieval practice

1. Why must byte buffers use explicit `size_t` lengths rather than sentinel termination?
2. State the safe traversal condition for `count` elements.
3. Explain why I/O and conversion return values must be verified before using outputs.
4. Recall one accurate phrase from the preceding Russian lesson.

## Core concept

The example treats bytes as counted raw data. It enforces explicit `size_t` capacities, verifies all external function returns, and maintains portability under ISO C17. It avoids native loaders, process injection, shellcode, and self-modifying code.

## Worked example

```c
#include <stdint.h>
#include <stdio.h>
#include <stdlib.h>

static int rle_decode(const uint8_t *in, size_t in_len, uint8_t *out, size_t out_cap, size_t *out_len)
{
    if (in == NULL || out == NULL || out_len == NULL || (in_len % 2) != 0) return 0;
    size_t written = 0;
    for (size_t i = 0; i < in_len; i += 2) {
        uint8_t count = in[i];
        uint8_t byte = in[i + 1];
        if (count > out_cap - written) return 0;
        for (uint8_t c = 0; c < count; ++c) out[written++] = byte;
    }
    *out_len = written;
    return 1;
}

int main(void)
{
    const uint8_t encoded[] = {3, 'A', 2, 'B'};
    uint8_t decoded[10];
    size_t total = 0;
    if (!rle_decode(encoded, sizeof encoded, decoded, sizeof decoded, &total)) return EXIT_FAILURE;
    if (printf("Decoded size: %zu\n", total) < 0) return EXIT_FAILURE;
    return EXIT_SUCCESS;
}
```

Compile and run:

```sh
cc -std=c17 -Wall -Wextra -Wpedantic \
  -o c-032-counted-run-length-byte-decoding \
  examples/c/c-032-counted-run-length-byte-decoding.c
./c-032-counted-run-length-byte-decoding 
```

Expected output:

```text
Decoded size: 5
```

## Guided practice

1. Identify the input and output buffer boundaries.
2. Locate each return value and capacity check.
3. Explain why embedded `0x00` values remain valid data in raw buffers.
4. Name one malformed input condition that causes early error return.
5. Recall one phrase from the preceding Russian lesson.

## Independent exercise

Extend the program to validate an additional edge case or test input. Preserve explicit lengths and check every return status.

## Validation

Run the deterministic test:

```sh
./tests/c-032-counted-run-length-byte-decoding.sh
```

Compile and run with sanitizers where supported:

```sh
cc -std=c17 -Wall -Wextra -Wpedantic \
  -fsanitize=address,undefined -fno-omit-frame-pointer -g \
  -o c-032-sanitized \
  examples/c/c-032-counted-run-length-byte-decoding.c
```

## Final self-check

You are ready for the next lesson if you can explain the buffer contract, error return values, and why this lesson’s code is not an operational cryptographic security mechanism.

## Spoiler: answers and model response

1. Raw byte buffers can contain `0x00` as legitimate data; `strlen` cannot determine their size.
2. Forward loop bound: `index < count`.
3. Unchecked results can lead to use of uninitialized memory or silent failure.
4. Malformed inputs violate capacity or structure constraints and must return an error code.

## Next lesson

The next scheduled lesson returns to the Russian track.

## References

[1]: https://en.cppreference.com/w/c/types/integer "Fixed width integer types — cppreference.com"
[2]: https://en.cppreference.com/w/c/language/operator_arithmetic "Arithmetic operators — cppreference.com"
