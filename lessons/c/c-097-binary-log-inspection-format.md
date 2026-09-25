# C 097 — Binary Log Inspection Format

**Estimated study time:** 25–35 minutes
**Prerequisites:** C 001–096
**Companion program:** [`examples/c/c-097-binary-log-inspection-format.c`](../../examples/c/c-097-binary-log-inspection-format.c)
**Deterministic test:** [`tests/c-097-binary-log-inspection-format.sh`](../../tests/c-097-binary-log-inspection-format.sh)
**Author:** Manus AI

## What you will be able to do

By the end of this lesson, you should be able to inspect a bounded educational binary log record and print its fields safely.

## Retrieval practice

1. Why must a byte buffer carry an explicit `size_t` length rather than rely on `\0`?
2. What condition proves that an index is within a `count`-element buffer?
3. Name one C library return value that must be checked before using its result.
4. Recall one precise phrase from the preceding Russian lesson.

## Core concept

Inspection output must derive from validated bytes and explicit field widths.

The companion program handles raw bytes as counted data. It checks arguments, capacities, and return values before advancing state. It is portable ISO C17 and deliberately excludes shellcode, native-code loaders, arbitrary assembly execution, process injection, self-modifying code, and real cryptographic implementation.

## Worked example

```c
#include <inttypes.h>
#include <stdint.h>
#include <stdio.h>
#include <stdlib.h>

static int print_hex(const uint8_t *data, size_t len)
{
    if (data == NULL && len != 0) return 0;
    for (size_t i = 0; i < len; ++i)
        if (printf("%02" PRIX8, data[i]) < 0) return 0;
    return 1;
}

int main(void)
{
    const uint8_t data[] = {UINT8_C(0), UINT8_C(97), UINT8_C(255)};
    if (printf("C 097 hex: ") < 0 || !print_hex(data, sizeof data) || printf("\n") < 0) return EXIT_FAILURE;
    return EXIT_SUCCESS;
}
```

Compile and run:

```sh
cc -std=c17 -Wall -Wextra -Wpedantic \
  -o c-097-binary-log-inspection-format \
  examples/c/c-097-binary-log-inspection-format.c
./c-097-binary-log-inspection-format
```

Expected output:

```text
C 097 hex: 0061FF
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
./tests/c-097-binary-log-inspection-format.sh
```

Compile and run with sanitizers where supported:

```sh
cc -std=c17 -Wall -Wextra -Wpedantic \
  -fsanitize=address,undefined -fno-omit-frame-pointer -g \
  -o c-097-sanitized \
  examples/c/c-097-binary-log-inspection-format.c
./c-097-sanitized
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
