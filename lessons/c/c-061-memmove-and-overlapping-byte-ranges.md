# C 061 — Overlapping Byte Ranges with `memmove`

**Estimated study time:** 25–35 minutes
**Prerequisites:** C 001–060
**Companion program:** [`examples/c/c-061-memmove-and-overlapping-byte-ranges.c`](../../examples/c/c-061-memmove-and-overlapping-byte-ranges.c)
**Deterministic test:** [`tests/c-061-memmove-and-overlapping-byte-ranges.sh`](../../tests/c-061-memmove-and-overlapping-byte-ranges.sh)
**Author:** Manus AI

## What you will be able to do

By the end of this lesson, you should be able to move a counted byte range safely when source and destination overlap.

## Retrieval practice

1. Why must a byte buffer carry an explicit `size_t` length rather than rely on `\0`?
2. What condition proves that an index is within a `count`-element buffer?
3. Name one C library return value that must be checked before using its result.
4. Recall one precise phrase from the preceding Russian lesson.

## Core concept

Use `memmove` only after proving the source range and destination capacity are valid.

The companion program handles raw bytes as counted data. It checks arguments, capacities, and return values before advancing state. It is portable ISO C17 and deliberately excludes shellcode, native-code loaders, arbitrary assembly execution, process injection, self-modifying code, and real cryptographic implementation.

## Worked example

```c
#include <stdint.h>
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

static int copy_bounded(const uint8_t *src, size_t len, uint8_t *dst, size_t cap)
{
    if ((src == NULL && len != 0) || dst == NULL || len > cap) return 0;
    if (len != 0) memmove(dst, src, len);
    return 1;
}

int main(void)
{
    const uint8_t source[] = {UINT8_C(0), UINT8_C(61), UINT8_C(255)};
    uint8_t destination[3] = {0};
    if (!copy_bounded(source, sizeof source, destination, sizeof destination)) return EXIT_FAILURE;
    if (printf("C 061 copied bytes: %u %u %u\n", (unsigned)destination[0], (unsigned)destination[1], (unsigned)destination[2]) < 0) return EXIT_FAILURE;
    return EXIT_SUCCESS;
}
```

Compile and run:

```sh
cc -std=c17 -Wall -Wextra -Wpedantic \
  -o c-061-memmove-and-overlapping-byte-ranges \
  examples/c/c-061-memmove-and-overlapping-byte-ranges.c
./c-061-memmove-and-overlapping-byte-ranges
```

Expected output:

```text
C 061 copied bytes: 0 61 255
```

## Guided practice

1. Identify every pointer-plus-length or buffer-plus-capacity contract in the program.
2. Locate the earliest rejected malformed-input condition.
3. Explain why the program does not treat `0x00` as the end of a raw byte buffer.
4. State which output is written only after validation succeeds.
5. Describe why this lesson does not provide production cryptography, even when its topic discusses hashing or integrity.

## Independent exercise

Add one additional deterministic valid input and one rejected boundary input. Preserve the existing argument checks, explicit lengths, and output-return checks. If you change any allocation arithmetic, add a checked overflow condition before the calculation.

## Validation

Run the deterministic test:

```sh
./tests/c-061-memmove-and-overlapping-byte-ranges.sh
```

Compile and run with sanitizers where supported:

```sh
cc -std=c17 -Wall -Wextra -Wpedantic \
  -fsanitize=address,undefined -fno-omit-frame-pointer -g \
  -o c-061-sanitized \
  examples/c/c-061-memmove-and-overlapping-byte-ranges.c
./c-061-sanitized
```

## Final self-check

You are ready for the next lesson if you can state the byte-buffer contract, identify a rejected input, explain the output, and distinguish educational integrity or toy hashing examples from suitable real-world cryptographic protection.

## Spoiler: answers and model response

1. Raw bytes may include `0x00`, so a sentinel scan is not a valid length calculation.
2. The forward traversal condition is `index < count`.
3. I/O, allocation, parsing, and formatting results must be checked before their outputs are used.
4. A validated program changes output state only after every prerequisite succeeds.

## Next lesson

The next scheduled lesson returns to the Russian track.

## References

[1]: https://en.cppreference.com/w/c/types/integer "Fixed Width Integer Types — cppreference.com"
[2]: https://en.cppreference.com/w/c/io "C File Input/Output — cppreference.com"
[3]: https://cheatsheetseries.owasp.org/cheatsheets/C-Based_Toolchain_Hardening_Cheat_Sheet.html "C-Based Toolchain Hardening Cheat Sheet — OWASP"
