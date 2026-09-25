# C 035 — Fixed-Size Circular Byte Buffer

**Estimated study time:** 25–35 minutes
**Prerequisites:** C 001–034 and the immediately preceding lessons
**Companion program:** [`examples/c/c-035-fixed-size-circular-byte-buffer.c`](../../examples/c/c-035-fixed-size-circular-byte-buffer.c)
**Deterministic test:** [`tests/c-035-fixed-size-circular-byte-buffer.sh`](../../tests/c-035-fixed-size-circular-byte-buffer.sh)
**Author:** Manus AI

## What you will be able to do

By the end of this lesson, you should be able to implement a simple ring buffer of bytes that reports fullness and empties cleanly.

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

typedef struct { uint8_t data[4]; size_t head; size_t tail; size_t count; } Ring;

static int ring_push(Ring *r, uint8_t b)
{
    if (r == NULL || r->count >= 4) return 0;
    r->data[r->head] = b;
    r->head = (r->head + 1) % 4;
    r->count++;
    return 1;
}

static int ring_pop(Ring *r, uint8_t *b)
{
    if (r == NULL || b == NULL || r->count == 0) return 0;
    *b = r->data[r->tail];
    r->tail = (r->tail + 1) % 4;
    r->count--;
    return 1;
}

int main(void)
{
    Ring r = {{0}, 0, 0, 0};
    uint8_t out = 0;
    if (!ring_push(&r, 42) || !ring_push(&r, 99)) return EXIT_FAILURE;
    if (!ring_pop(&r, &out) || out != 42) return EXIT_FAILURE;
    if (printf("Popped: %u\nRemaining: %zu\n", out, r.count) < 0) return EXIT_FAILURE;
    return EXIT_SUCCESS;
}
```

Compile and run:

```sh
cc -std=c17 -Wall -Wextra -Wpedantic \
  -o c-035-fixed-size-circular-byte-buffer \
  examples/c/c-035-fixed-size-circular-byte-buffer.c
./c-035-fixed-size-circular-byte-buffer 
```

Expected output:

```text
Popped: 42
Remaining: 1
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
./tests/c-035-fixed-size-circular-byte-buffer.sh
```

Compile and run with sanitizers where supported:

```sh
cc -std=c17 -Wall -Wextra -Wpedantic \
  -fsanitize=address,undefined -fno-omit-frame-pointer -g \
  -o c-035-sanitized \
  examples/c/c-035-fixed-size-circular-byte-buffer.c
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
