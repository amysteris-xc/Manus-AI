# C 109 — Portable Integer Formatting

**Estimated study time:** 25–35 minutes
**Prerequisites:** C 001–108
**Companion program:** [`examples/c/c-109-portable-integer-formatting.c`](../../examples/c/c-109-portable-integer-formatting.c)
**Deterministic test:** [`tests/c-109-portable-integer-formatting.sh`](../../tests/c-109-portable-integer-formatting.sh)
**Author:** Manus AI

## What you will be able to do

By the end of this lesson, you should be able to format fixed-width integer values with the matching `<inttypes.h>` macros.

## Retrieval practice

1. Why must a raw byte buffer carry an explicit `size_t` length?
2. What loop condition keeps a forward index inside a `count`-element buffer?
3. Which return values from I/O, allocation, or parsing APIs must be checked before their outputs are trusted?
4. State why a non-cryptographic check value is not authentication against an active attacker.

## Core concept

Portable output uses the appropriate `PRI...` macros or a safe promotion with a documented format, not assumptions about typedef aliases.

This lesson uses portable ISO C17. It treats bytes as counted data, checks every pointer, count, capacity, conversion, and return result that the example relies on, and preserves output state until validation succeeds. It does **not** create shellcode, native-code loaders, arbitrary assembly execution, process injection, self-modifying code, or real cryptographic implementations.

## Worked example

```c
#include <inttypes.h>
#include <stddef.h>
#include <stdint.h>
#include <stdio.h>
#include <stdlib.h>

static int print_hex_prefix(const uint8_t *data, size_t count, size_t maximum)
{
    size_t shown = count < maximum ? count : maximum;
    if (data == NULL && count != 0) return 0;
    for (size_t i = 0; i < shown; ++i)
        if (printf("%02" PRIX8, data[i]) < 0) return 0;
    return 1;
}

int main(void)
{
    const uint8_t bytes[] = {UINT8_C(0), UINT8_C(109), UINT8_C(255)};
    if (printf("C 109 hex: ") < 0 || !print_hex_prefix(bytes, sizeof bytes, sizeof bytes) || printf("\n") < 0) return EXIT_FAILURE;
    return EXIT_SUCCESS;
}
```

Compile and run:

```sh
cc -std=c17 -Wall -Wextra -Wpedantic \
  -o c-109-portable-integer-formatting \
  examples/c/c-109-portable-integer-formatting.c
./c-109-portable-integer-formatting
```

Expected output:

```text
C 109 hex: 006DFF
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
./tests/c-109-portable-integer-formatting.sh
```

Compile and run with sanitizers where supported:

```sh
cc -std=c17 -Wall -Wextra -Wpedantic \
  -fsanitize=address,undefined -fno-omit-frame-pointer -g \
  -o c-109-sanitized \
  examples/c/c-109-portable-integer-formatting.c
./c-109-sanitized
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
