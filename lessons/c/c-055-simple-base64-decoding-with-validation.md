# C 055 — Simple Base64 Decoding with Validation

**Estimated study time:** 25–35 minutes
**Prerequisites:** C 001–054 and the immediately preceding lessons
**Companion program:** [`examples/c/c-055-simple-base64-decoding-with-validation.c`](../../examples/c/c-055-simple-base64-decoding-with-validation.c)
**Deterministic test:** [`tests/c-055-simple-base64-decoding-with-validation.sh`](../../tests/c-055-simple-base64-decoding-with-validation.sh)
**Author:** Manus AI

## What you will be able to do

By the end of this lesson, you should be able to decode 4 Base64 characters back into 3 raw bytes with boundary and character validation.

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

static int b64_idx(char c)
{
    if (c >= 'A' && c <= 'Z') return c - 'A';
    if (c >= 'a' && c <= 'z') return c - 'a' + 26;
    if (c >= '0' && c <= '9') return c - '0' + 52;
    if (c == '+') return 62;
    if (c == '/') return 63;
    return -1;
}

static int b64_decode4(const char in[4], uint8_t out[3])
{
    int i0 = b64_idx(in[0]), i1 = b64_idx(in[1]), i2 = b64_idx(in[2]), i3 = b64_idx(in[3]);
    if (i0 < 0 || i1 < 0 || i2 < 0 || i3 < 0) return 0;
    out[0] = (uint8_t)((i0 << 2) | (i1 >> 4));
    out[1] = (uint8_t)(((i1 & 0x0F) << 4) | (i2 >> 2));
    out[2] = (uint8_t)(((i2 & 3) << 6) | i3);
    return 1;
}

int main(void)
{
    uint8_t dec[3] = {0};
    if (!b64_decode4("TWFu", dec)) return EXIT_FAILURE;
    if (printf("Decoded: %c%c%c\n", dec[0], dec[1], dec[2]) < 0) return EXIT_FAILURE;
    return EXIT_SUCCESS;
}
```

Compile and run:

```sh
cc -std=c17 -Wall -Wextra -Wpedantic \
  -o c-055-simple-base64-decoding-with-validation \
  examples/c/c-055-simple-base64-decoding-with-validation.c
./c-055-simple-base64-decoding-with-validation 
```

Expected output:

```text
Decoded: Man
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
./tests/c-055-simple-base64-decoding-with-validation.sh
```

Compile and run with sanitizers where supported:

```sh
cc -std=c17 -Wall -Wextra -Wpedantic \
  -fsanitize=address,undefined -fno-omit-frame-pointer -g \
  -o c-055-sanitized \
  examples/c/c-055-simple-base64-decoding-with-validation.c
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
