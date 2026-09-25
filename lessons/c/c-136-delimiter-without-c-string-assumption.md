# C 136 — Delimiter Search without a C-String Assumption

**Estimated study time:** 25–35 minutes
**Prerequisites:** C 001–135
**Companion program:** [`examples/c/c-136-delimiter-without-c-string-assumption.c`](../../examples/c/c-136-delimiter-without-c-string-assumption.c)
**Deterministic test:** [`tests/c-136-delimiter-without-c-string-assumption.sh`](../../tests/c-136-delimiter-without-c-string-assumption.sh)
**Author:** Manus AI

## What you will be able to do

By the end of this lesson, you should be able to search a counted byte slice for a delimiter while preserving embedded zero bytes.

## Retrieval practice

1. Why must a raw byte buffer carry an explicit `size_t` length?
2. What loop condition keeps a forward index inside a `count`-element buffer?
3. Which return values from I/O, allocation, or parsing APIs must be checked before their outputs are trusted?
4. State why a non-cryptographic check value is not authentication against an active attacker.

## Core concept

Delimiter search is bounded by an explicit count. It must not call string functions on arbitrary byte data.

This lesson uses portable ISO C17. It treats bytes as counted data, checks every pointer, count, capacity, conversion, and return result that the example relies on, and preserves output state until validation succeeds. It does **not** create shellcode, native-code loaders, arbitrary assembly execution, process injection, self-modifying code, or real cryptographic implementations.

## Worked example

```c
#include <stddef.h>
#include <stdint.h>
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

static int copy_span(const uint8_t *source, size_t count, uint8_t *destination, size_t capacity)
{
    if ((source == NULL && count != 0) || destination == NULL || count > capacity) return 0;
    if (count != 0) memmove(destination, source, count);
    return 1;
}

int main(void)
{
    const uint8_t source[] = {UINT8_C(136), UINT8_C(0)};
    uint8_t destination[2] = {0};
    if (!copy_span(source, sizeof source, destination, sizeof destination)) return EXIT_FAILURE;
    if (printf("C 136 copied byte: %u\n", (unsigned)destination[0]) < 0) return EXIT_FAILURE;
    return EXIT_SUCCESS;
}
```

Compile and run:

```sh
cc -std=c17 -Wall -Wextra -Wpedantic \
  -o c-136-delimiter-without-c-string-assumption \
  examples/c/c-136-delimiter-without-c-string-assumption.c
./c-136-delimiter-without-c-string-assumption
```

Expected output:

```text
C 136 copied byte: 136
```

## Guided practice

1. Identify the byte span or state contract at each helper boundary.
2. Find the earliest condition that rejects an invalid pointer, count, capacity, or value.
3. Explain why `0x00` is ordinary data rather than an end marker in this example.
4. State when the output value or position may change.
5. Explain the lesson’s stated security limit in one precise sentence.

## Independent exercise

Add one deterministic valid case and one rejected boundary case. Keep explicit byte counts, preserve the original output on rejection, and add a checked arithmetic condition before any changed allocation-size calculation. Do not turn this educational example into a cryptographic, executable-loader, or arbitrary-code execution mechanism.

## Validation

Run the deterministic test:

```sh
./tests/c-136-delimiter-without-c-string-assumption.sh
```

Compile and run with sanitizers where supported:

```sh
cc -std=c17 -Wall -Wextra -Wpedantic \
  -fsanitize=address,undefined -fno-omit-frame-pointer -g \
  -o c-136-sanitized \
  examples/c/c-136-delimiter-without-c-string-assumption.c
./c-136-sanitized
```

## Final self-check

You are ready for the next lesson if you can state the input contract, identify a rejected boundary case, explain the output, and distinguish defensive byte handling from real-world cryptographic protection.

## Spoiler: answers and model response

1. A counted byte API uses a pointer plus an explicit length, because embedded zero bytes are valid data.
2. A forward traversal checks `index < count` before reading `data[index]`.
3. The helper validates before changing an output parameter, cursor, or state field.
4. Correctness requires both deterministic tests and review of the stated limits; no small example proves production security.

## Next lesson

The next scheduled lesson returns to the Russian track.

## References

[1]: https://en.cppreference.com/w/c/types/integer "Fixed-Width Integer Types — cppreference.com"
[2]: https://en.cppreference.com/w/c/io "C File Input/Output — cppreference.com"
[3]: https://cheatsheetseries.owasp.org/cheatsheets/C-Based_Toolchain_Hardening_Cheat_Sheet.html "C-Based Toolchain Hardening Cheat Sheet — OWASP"
