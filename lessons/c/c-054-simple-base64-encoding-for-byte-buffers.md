# C 054 — Simple Base64 Encoding for Byte Buffers

**Estimated study time:** 25–35 minutes
**Prerequisites:** C 001–053 and the immediately preceding lessons
**Companion program:** [`examples/c/c-054-simple-base64-encoding-for-byte-buffers.c`](../../examples/c/c-054-simple-base64-encoding-for-byte-buffers.c)
**Deterministic test:** [`tests/c-054-simple-base64-encoding-for-byte-buffers.sh`](../../tests/c-054-simple-base64-encoding-for-byte-buffers.sh)
**Author:** Manus AI

## What you will be able to do

By the end of this lesson, you should be able to encode triplets of bytes into 4-character ASCII Base64 chunks with explicit length math.

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

static const char b64_table[] = "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789+/";

static int b64_encode3(const uint8_t in[3], char out[4])
{
    if (in == NULL || out == NULL) return 0;
    out[0] = b64_table[in[0] >> 2];
    out[1] = b64_table[((in[0] & 3) << 4) | (in[1] >> 4)];
    out[2] = b64_table[((in[1] & 0x0F) << 2) | (in[2] >> 6)];
    out[3] = b64_table[in[2] & 0x3F];
    return 1;
}

int main(void)
{
    const uint8_t raw[3] = {'M', 'a', 'n'};
    char enc[5] = {0};
    if (!b64_encode3(raw, enc)) return EXIT_FAILURE;
    if (printf("Base64: %s\n", enc) < 0) return EXIT_FAILURE;
    return EXIT_SUCCESS;
}
```

Compile and run:

```sh
cc -std=c17 -Wall -Wextra -Wpedantic \
  -o c-054-simple-base64-encoding-for-byte-buffers \
  examples/c/c-054-simple-base64-encoding-for-byte-buffers.c
./c-054-simple-base64-encoding-for-byte-buffers 
```

Expected output:

```text
Base64: TWFu
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
./tests/c-054-simple-base64-encoding-for-byte-buffers.sh
```

Compile and run with sanitizers where supported:

```sh
cc -std=c17 -Wall -Wextra -Wpedantic \
  -fsanitize=address,undefined -fno-omit-frame-pointer -g \
  -o c-054-sanitized \
  examples/c/c-054-simple-base64-encoding-for-byte-buffers.c
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
