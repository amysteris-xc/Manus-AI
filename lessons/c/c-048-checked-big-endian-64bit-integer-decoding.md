# C 048 — Checked Big-Endian 64-Bit Integer Decoding

**Estimated study time:** 25–35 minutes
**Prerequisites:** C 001–047 and the immediately preceding lessons
**Companion program:** [`examples/c/c-048-checked-big-endian-64bit-integer-decoding.c`](../../examples/c/c-048-checked-big-endian-64bit-integer-decoding.c)
**Deterministic test:** [`tests/c-048-checked-big-endian-64bit-integer-decoding.sh`](../../tests/c-048-checked-big-endian-64bit-integer-decoding.sh)
**Author:** Manus AI

## What you will be able to do

By the end of this lesson, you should be able to decode 8 big-endian bytes into a uint64_t with explicit bounds.

## Retrieval practice

1. Why must byte buffers use explicit `size_t` lengths rather than sentinel termination?
2. State the safe traversal condition for `count` elements.
3. Explain why I/O and conversion return values must be verified before using outputs.
4. Recall one accurate phrase from the preceding Russian lesson.

## Core concept

The example treats bytes as counted raw data. It enforces explicit `size_t` capacities, verifies all external function returns, and maintains portability under ISO C17. It avoids native loaders, process injection, shellcode, and self-modifying code.

## Worked example

```c
#include <inttypes.h>
#include <stdint.h>
#include <stdio.h>
#include <stdlib.h>

static int decode_u64_be(const uint8_t *in, size_t len, uint64_t *out)
{
    if (in == NULL || out == NULL || len < 8) return 0;
    uint64_t v = 0;
    for (size_t i = 0; i < 8; ++i) v = (v << 8) | in[i];
    *out = v;
    return 1;
}

int main(void)
{
    const uint8_t raw[8] = {0, 0, 0, 0, 0, 0, 1, 0};
    uint64_t val = 0;
    if (!decode_u64_be(raw, sizeof raw, &val)) return EXIT_FAILURE;
    if (printf("Decoded u64: %" PRIu64 "\n", val) < 0) return EXIT_FAILURE;
    return EXIT_SUCCESS;
}
```

Compile and run:

```sh
cc -std=c17 -Wall -Wextra -Wpedantic \
  -o c-048-checked-big-endian-64bit-integer-decoding \
  examples/c/c-048-checked-big-endian-64bit-integer-decoding.c
./c-048-checked-big-endian-64bit-integer-decoding 
```

Expected output:

```text
Decoded u64: 256
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
./tests/c-048-checked-big-endian-64bit-integer-decoding.sh
```

Compile and run with sanitizers where supported:

```sh
cc -std=c17 -Wall -Wextra -Wpedantic \
  -fsanitize=address,undefined -fno-omit-frame-pointer -g \
  -o c-048-sanitized \
  examples/c/c-048-checked-big-endian-64bit-integer-decoding.c
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
