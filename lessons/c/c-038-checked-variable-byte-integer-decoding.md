# C 038 — Checked Variable-Byte Integer Decoding

**Estimated study time:** 25–35 minutes
**Prerequisites:** C 001–037 and the immediately preceding lessons
**Companion program:** [`examples/c/c-038-checked-variable-byte-integer-decoding.c`](../../examples/c/c-038-checked-variable-byte-integer-decoding.c)
**Deterministic test:** [`tests/c-038-checked-variable-byte-integer-decoding.sh`](../../tests/c-038-checked-variable-byte-integer-decoding.sh)
**Author:** Manus AI

## What you will be able to do

By the end of this lesson, you should be able to decode a 7-bit continuation byte sequence into a uint32_t with overflow guards.

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

static int decode_varint(const uint8_t *in, size_t len, uint32_t *val, size_t *consumed)
{
    if (in == NULL || val == NULL || consumed == NULL || len == 0) return 0;
    uint32_t result = 0;
    size_t shift = 0;
    for (size_t i = 0; i < len; ++i) {
        uint8_t byte = in[i];
        if (shift >= 32) return 0;
        result |= (uint32_t)(byte & 0x7F) << shift;
        if ((byte & 0x80) == 0) {
            *val = result;
            *consumed = i + 1;
            return 1;
        }
        shift += 7;
    }
    return 0;
}

int main(void)
{
    const uint8_t encoded[] = {0xAC, 0x02};
    uint32_t val = 0;
    size_t used = 0;
    if (!decode_varint(encoded, sizeof encoded, &val, &used) || val != 300) return EXIT_FAILURE;
    if (printf("Decoded: %" PRIu32 "\nConsumed: %zu\n", val, used) < 0) return EXIT_FAILURE;
    return EXIT_SUCCESS;
}
```

Compile and run:

```sh
cc -std=c17 -Wall -Wextra -Wpedantic \
  -o c-038-checked-variable-byte-integer-decoding \
  examples/c/c-038-checked-variable-byte-integer-decoding.c
./c-038-checked-variable-byte-integer-decoding 
```

Expected output:

```text
Decoded: 300
Consumed: 2
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
./tests/c-038-checked-variable-byte-integer-decoding.sh
```

Compile and run with sanitizers where supported:

```sh
cc -std=c17 -Wall -Wextra -Wpedantic \
  -fsanitize=address,undefined -fno-omit-frame-pointer -g \
  -o c-038-sanitized \
  examples/c/c-038-checked-variable-byte-integer-decoding.c
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
