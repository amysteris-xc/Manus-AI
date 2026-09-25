# C 046 — Binary Key-Value Frame Parsing

**Estimated study time:** 25–35 minutes
**Prerequisites:** C 001–045 and the immediately preceding lessons
**Companion program:** [`examples/c/c-046-binary-key-value-frame-parsing.c`](../../examples/c/c-046-binary-key-value-frame-parsing.c)
**Deterministic test:** [`tests/c-046-binary-key-value-frame-parsing.sh`](../../tests/c-046-binary-key-value-frame-parsing.sh)
**Author:** Manus AI

## What you will be able to do

By the end of this lesson, you should be able to parse a tag-length-value (TLV) frame from a byte slice with strict bounds validation.

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

static int read_tlv(const uint8_t *frame, size_t len, uint8_t *tag, const uint8_t **val, size_t *val_len)
{
    if (frame == NULL || tag == NULL || val == NULL || val_len == NULL || len < 2) return 0;
    *tag = frame[0];
    size_t l = frame[1];
    if (len - 2 < l) return 0;
    *val = frame + 2;
    *val_len = l;
    return 1;
}

int main(void)
{
    const uint8_t raw[] = {0x05, 0x02, 0xAA, 0xBB};
    uint8_t tag = 0;
    const uint8_t *val = NULL;
    size_t val_len = 0;
    if (!read_tlv(raw, sizeof raw, &tag, &val, &val_len)) return EXIT_FAILURE;
    if (printf("Tag: %" PRIu8 "\nVal length: %zu\n", tag, val_len) < 0) return EXIT_FAILURE;
    return EXIT_SUCCESS;
}
```

Compile and run:

```sh
cc -std=c17 -Wall -Wextra -Wpedantic \
  -o c-046-binary-key-value-frame-parsing \
  examples/c/c-046-binary-key-value-frame-parsing.c
./c-046-binary-key-value-frame-parsing 
```

Expected output:

```text
Tag: 5
Val length: 2
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
./tests/c-046-binary-key-value-frame-parsing.sh
```

Compile and run with sanitizers where supported:

```sh
cc -std=c17 -Wall -Wextra -Wpedantic \
  -fsanitize=address,undefined -fno-omit-frame-pointer -g \
  -o c-046-sanitized \
  examples/c/c-046-binary-key-value-frame-parsing.c
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
