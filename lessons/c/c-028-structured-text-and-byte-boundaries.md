# C 028 — Structured Text and Byte Boundaries

**Estimated study time:** 25–35 minutes
**Prerequisites:** C 001–027 and the immediately preceding lessons
**Companion program:** [`examples/c/c-028-structured-text-and-byte-boundaries.c`](../../examples/c/c-028-structured-text-and-byte-boundaries.c)
**Deterministic test:** [`tests/c-028-structured-text-and-byte-boundaries.sh`](../../tests/c-028-structured-text-and-byte-boundaries.sh)
**Author:** Manus AI

## What you will be able to do

By the end of this lesson, you should be able to parse a simple key-value record from a bounded buffer without reading past the end or assuming a null terminator.

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

static int find_colon(const uint8_t *buf, size_t len, size_t *colon_idx)
{
    if (buf == NULL || colon_idx == NULL) return 0;
    for (size_t i = 0; i < len; ++i) {
        if (buf[i] == (uint8_t)':') {
            *colon_idx = i;
            return 1;
        }
    }
    return 0;
}

int main(void)
{
    const uint8_t record[] = {'k', 'e', 'y', ':', 'v', 'a', 'l'};
    size_t colon_pos = 0;
    if (!find_colon(record, sizeof record, &colon_pos)) return EXIT_FAILURE;
    if (printf("Key length: %zu\nValue length: %zu\n", colon_pos, sizeof record - colon_pos - 1) < 0) return EXIT_FAILURE;
    return EXIT_SUCCESS;
}
```

Compile and run:

```sh
cc -std=c17 -Wall -Wextra -Wpedantic \
  -o c-028-structured-text-and-byte-boundaries \
  examples/c/c-028-structured-text-and-byte-boundaries.c
./c-028-structured-text-and-byte-boundaries 
```

Expected output:

```text
Key length: 3
Value length: 3
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
./tests/c-028-structured-text-and-byte-boundaries.sh
```

Compile and run with sanitizers where supported:

```sh
cc -std=c17 -Wall -Wextra -Wpedantic \
  -fsanitize=address,undefined -fno-omit-frame-pointer -g \
  -o c-028-sanitized \
  examples/c/c-028-structured-text-and-byte-boundaries.c
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
