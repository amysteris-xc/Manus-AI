# C 056 — Binary Safe Merge of Two Sorted Byte Lists

**Estimated study time:** 25–35 minutes
**Prerequisites:** C 001–055 and the immediately preceding lessons
**Companion program:** [`examples/c/c-056-binary-safe-merge-of-two-sorted-byte-lists.c`](../../examples/c/c-056-binary-safe-merge-of-two-sorted-byte-lists.c)
**Deterministic test:** [`tests/c-056-binary-safe-merge-of-two-sorted-byte-lists.sh`](../../tests/c-056-binary-safe-merge-of-two-sorted-byte-lists.sh)
**Author:** Manus AI

## What you will be able to do

By the end of this lesson, you should be able to merge two sorted byte arrays into an allocated buffer with capacity verification.

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

static int merge_sorted(const uint8_t *a, size_t a_len, const uint8_t *b, size_t b_len, uint8_t *out, size_t out_cap)
{
    if ((a == NULL && a_len != 0) || (b == NULL && b_len != 0) || out == NULL) return 0;
    if (a_len > out_cap || b_len > out_cap - a_len) return 0;
    size_t i = 0, j = 0, k = 0;
    while (i < a_len && j < b_len) {
        if (a[i] <= b[j]) out[k++] = a[i++];
        else out[k++] = b[j++];
    }
    while (i < a_len) out[k++] = a[i++];
    while (j < b_len) out[k++] = b[j++];
    return 1;
}

int main(void)
{
    const uint8_t l1[] = {1, 4, 7};
    const uint8_t l2[] = {2, 5, 8};
    uint8_t res[6];
    if (!merge_sorted(l1, sizeof l1, l2, sizeof l2, res, sizeof res)) return EXIT_FAILURE;
    if (printf("Merged 6 items: %u %u ... %u\n", res[0], res[1], res[5]) < 0) return EXIT_FAILURE;
    return EXIT_SUCCESS;
}
```

Compile and run:

```sh
cc -std=c17 -Wall -Wextra -Wpedantic \
  -o c-056-binary-safe-merge-of-two-sorted-byte-lists \
  examples/c/c-056-binary-safe-merge-of-two-sorted-byte-lists.c
./c-056-binary-safe-merge-of-two-sorted-byte-lists 
```

Expected output:

```text
Merged 6 items: 1 2 ... 8
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
./tests/c-056-binary-safe-merge-of-two-sorted-byte-lists.sh
```

Compile and run with sanitizers where supported:

```sh
cc -std=c17 -Wall -Wextra -Wpedantic \
  -fsanitize=address,undefined -fno-omit-frame-pointer -g \
  -o c-056-sanitized \
  examples/c/c-056-binary-safe-merge-of-two-sorted-byte-lists.c
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
