# C 047 — Checked Big-Endian 64-Bit Integer Encoding

**Estimated study time:** 25–35 minutes
**Prerequisites:** C 001–046 and the immediately preceding lessons
**Companion program:** [`examples/c/c-047-checked-big-endian-64bit-integer-encoding.c`](../../examples/c/c-047-checked-big-endian-64bit-integer-encoding.c)
**Deterministic test:** [`tests/c-047-checked-big-endian-64bit-integer-encoding.sh`](../../tests/c-047-checked-big-endian-64bit-integer-encoding.sh)
**Author:** Manus AI

## What you will be able to do

By the end of this lesson, you should be able to serialize a uint64_t into 8 big-endian bytes portably without assuming host endianness.

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

static int encode_u64_be(uint64_t v, uint8_t *out, size_t cap)
{
    if (out == NULL || cap < 8) return 0;
    for (int i = 7; i >= 0; --i) {
        out[i] = (uint8_t)(v & 0xFF);
        v >>= 8;
    }
    return 1;
}

int main(void)
{
    uint8_t buf[8];
    if (!encode_u64_be(0x0102030405060708ULL, buf, sizeof buf)) return EXIT_FAILURE;
    if (printf("Encoded byte 0: %u\nEncoded byte 7: %u\n", buf[0], buf[7]) < 0) return EXIT_FAILURE;
    return EXIT_SUCCESS;
}
```

Compile and run:

```sh
cc -std=c17 -Wall -Wextra -Wpedantic \
  -o c-047-checked-big-endian-64bit-integer-encoding \
  examples/c/c-047-checked-big-endian-64bit-integer-encoding.c
./c-047-checked-big-endian-64bit-integer-encoding 
```

Expected output:

```text
Encoded byte 0: 1
Encoded byte 7: 8
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
./tests/c-047-checked-big-endian-64bit-integer-encoding.sh
```

Compile and run with sanitizers where supported:

```sh
cc -std=c17 -Wall -Wextra -Wpedantic \
  -fsanitize=address,undefined -fno-omit-frame-pointer -g \
  -o c-047-sanitized \
  examples/c/c-047-checked-big-endian-64bit-integer-encoding.c
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
