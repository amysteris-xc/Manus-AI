# C 045 — Binary Key-Value Frame Serialization

**Estimated study time:** 25–35 minutes
**Prerequisites:** C 001–044 and the immediately preceding lessons
**Companion program:** [`examples/c/c-045-binary-key-value-frame-serialization.c`](../../examples/c/c-045-binary-key-value-frame-serialization.c)
**Deterministic test:** [`tests/c-045-binary-key-value-frame-serialization.sh`](../../tests/c-045-binary-key-value-frame-serialization.sh)
**Author:** Manus AI

## What you will be able to do

By the end of this lesson, you should be able to serialize a tag-length-value (TLV) frame into a byte slice with bound checks.

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

static int write_tlv(uint8_t tag, const uint8_t *val, uint8_t val_len, uint8_t *out, size_t cap, size_t *written)
{
    if (val == NULL && val_len != 0) return 0;
    if (out == NULL || written == NULL || cap < (size_t)(val_len + 2)) return 0;
    out[0] = tag;
    out[1] = val_len;
    for (size_t i = 0; i < val_len; ++i) out[2 + i] = val[i];
    *written = val_len + 2;
    return 1;
}

int main(void)
{
    uint8_t frame[10];
    const uint8_t payload[] = {0xDE, 0xAD};
    size_t used = 0;
    if (!write_tlv(0x01, payload, sizeof payload, frame, sizeof frame, &used)) return EXIT_FAILURE;
    if (printf("TLV written: %zu bytes\n", used) < 0) return EXIT_FAILURE;
    return EXIT_SUCCESS;
}
```

Compile and run:

```sh
cc -std=c17 -Wall -Wextra -Wpedantic \
  -o c-045-binary-key-value-frame-serialization \
  examples/c/c-045-binary-key-value-frame-serialization.c
./c-045-binary-key-value-frame-serialization 
```

Expected output:

```text
TLV written: 4 bytes
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
./tests/c-045-binary-key-value-frame-serialization.sh
```

Compile and run with sanitizers where supported:

```sh
cc -std=c17 -Wall -Wextra -Wpedantic \
  -fsanitize=address,undefined -fno-omit-frame-pointer -g \
  -o c-045-sanitized \
  examples/c/c-045-binary-key-value-frame-serialization.c
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
