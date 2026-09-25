# C 036 — Prefix Length Matching on Byte Slices

**Estimated study time:** 25–35 minutes
**Prerequisites:** C 001–035 and the immediately preceding lessons
**Companion program:** [`examples/c/c-036-prefix-length-matching-on-byte-slices.c`](../../examples/c/c-036-prefix-length-matching-on-byte-slices.c)
**Deterministic test:** [`tests/c-036-prefix-length-matching-on-byte-slices.sh`](../../tests/c-036-prefix-length-matching-on-byte-slices.sh)
**Author:** Manus AI

## What you will be able to do

By the end of this lesson, you should be able to compute the common prefix length between two explicit byte buffers.

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

static size_t common_prefix(const uint8_t *a, size_t a_len, const uint8_t *b, size_t b_len)
{
    if (a == NULL || b == NULL) return 0;
    size_t limit = (a_len < b_len) ? a_len : b_len;
    size_t i = 0;
    while (i < limit && a[i] == b[i]) ++i;
    return i;
}

int main(void)
{
    const uint8_t b1[] = {1, 2, 3, 4};
    const uint8_t b2[] = {1, 2, 0, 4};
    size_t p = common_prefix(b1, sizeof b1, b2, sizeof b2);
    if (printf("Common prefix length: %zu\n", p) < 0) return EXIT_FAILURE;
    return EXIT_SUCCESS;
}
```

Compile and run:

```sh
cc -std=c17 -Wall -Wextra -Wpedantic \
  -o c-036-prefix-length-matching-on-byte-slices \
  examples/c/c-036-prefix-length-matching-on-byte-slices.c
./c-036-prefix-length-matching-on-byte-slices 
```

Expected output:

```text
Common prefix length: 2
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
./tests/c-036-prefix-length-matching-on-byte-slices.sh
```

Compile and run with sanitizers where supported:

```sh
cc -std=c17 -Wall -Wextra -Wpedantic \
  -fsanitize=address,undefined -fno-omit-frame-pointer -g \
  -o c-036-sanitized \
  examples/c/c-036-prefix-length-matching-on-byte-slices.c
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
