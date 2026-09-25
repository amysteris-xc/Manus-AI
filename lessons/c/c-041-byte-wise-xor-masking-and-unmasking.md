# C 041 — Byte-Wise XOR Masking and Unmasking

**Estimated study time:** 25–35 minutes
**Prerequisites:** C 001–040 and the immediately preceding lessons
**Companion program:** [`examples/c/c-041-byte-wise-xor-masking-and-unmasking.c`](../../examples/c/c-041-byte-wise-xor-masking-and-unmasking.c)
**Deterministic test:** [`tests/c-041-byte-wise-xor-masking-and-unmasking.sh`](../../tests/c-041-byte-wise-xor-masking-and-unmasking.sh)
**Author:** Manus AI

## What you will be able to do

By the end of this lesson, you should be able to apply a cyclic multi-byte mask to an input buffer and demonstrate symmetry.

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

static void apply_mask(uint8_t *data, size_t len, const uint8_t *key, size_t key_len)
{
    if (data == NULL || key == NULL || key_len == 0) return;
    for (size_t i = 0; i < len; ++i) data[i] ^= key[i % key_len];
}

int main(void)
{
    uint8_t msg[] = {'H', 'E', 'L', 'L', 'O'};
    const uint8_t key[] = {0xAA, 0x55};
    apply_mask(msg, sizeof msg, key, sizeof key);
    apply_mask(msg, sizeof msg, key, sizeof key);
    if (printf("Restored: %c%c%c%c%c\n", msg[0], msg[1], msg[2], msg[3], msg[4]) < 0) return EXIT_FAILURE;
    return EXIT_SUCCESS;
}
```

Compile and run:

```sh
cc -std=c17 -Wall -Wextra -Wpedantic \
  -o c-041-byte-wise-xor-masking-and-unmasking \
  examples/c/c-041-byte-wise-xor-masking-and-unmasking.c
./c-041-byte-wise-xor-masking-and-unmasking 
```

Expected output:

```text
Restored: HELLO
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
./tests/c-041-byte-wise-xor-masking-and-unmasking.sh
```

Compile and run with sanitizers where supported:

```sh
cc -std=c17 -Wall -Wextra -Wpedantic \
  -fsanitize=address,undefined -fno-omit-frame-pointer -g \
  -o c-041-sanitized \
  examples/c/c-041-byte-wise-xor-masking-and-unmasking.c
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
