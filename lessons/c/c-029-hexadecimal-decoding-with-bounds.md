# C 029 — Hexadecimal Decoding with Bounds

**Estimated study time:** 25–35 minutes
**Prerequisites:** C 001–028 and the immediately preceding lessons
**Companion program:** [`examples/c/c-029-hexadecimal-decoding-with-bounds.c`](../../examples/c/c-029-hexadecimal-decoding-with-bounds.c)
**Deterministic test:** [`tests/c-029-hexadecimal-decoding-with-bounds.sh`](../../tests/c-029-hexadecimal-decoding-with-bounds.sh)
**Author:** Manus AI

## What you will be able to do

By the end of this lesson, you should be able to decode a pairs of ASCII hexadecimal characters into raw bytes with strict validation.

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

static int hex_val(uint8_t c)
{
    if (c >= '0' && c <= '9') return c - '0';
    if (c >= 'a' && c <= 'f') return c - 'a' + 10;
    if (c >= 'A' && c <= 'F') return c - 'A' + 10;
    return -1;
}

static int decode_hex(const uint8_t *hex, size_t hex_len, uint8_t *out, size_t out_cap, size_t *out_len)
{
    if (hex == NULL || out == NULL || out_len == NULL || (hex_len % 2) != 0 || hex_len / 2 > out_cap) return 0;
    for (size_t i = 0; i < hex_len / 2; ++i) {
        int hi = hex_val(hex[i * 2]);
        int lo = hex_val(hex[i * 2 + 1]);
        if (hi < 0 || lo < 0) return 0;
        out[i] = (uint8_t)((hi << 4) | lo);
    }
    *out_len = hex_len / 2;
    return 1;
}

int main(void)
{
    const uint8_t hex[] = {'4', '1', '0', '0', '4', '2'};
    uint8_t bin[3];
    size_t written = 0;
    if (!decode_hex(hex, sizeof hex, bin, sizeof bin, &written)) return EXIT_FAILURE;
    if (printf("Bytes decoded: %zu\nFirst byte: 0x%02" PRIX8 "\n", written, bin[0]) < 0) return EXIT_FAILURE;
    return EXIT_SUCCESS;
}
```

Compile and run:

```sh
cc -std=c17 -Wall -Wextra -Wpedantic \
  -o c-029-hexadecimal-decoding-with-bounds \
  examples/c/c-029-hexadecimal-decoding-with-bounds.c
./c-029-hexadecimal-decoding-with-bounds 
```

Expected output:

```text
Bytes decoded: 3
First byte: 0x41
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
./tests/c-029-hexadecimal-decoding-with-bounds.sh
```

Compile and run with sanitizers where supported:

```sh
cc -std=c17 -Wall -Wextra -Wpedantic \
  -fsanitize=address,undefined -fno-omit-frame-pointer -g \
  -o c-029-sanitized \
  examples/c/c-029-hexadecimal-decoding-with-bounds.c
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
