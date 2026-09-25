# C 099 — Defensive Code Review Checklist

**Estimated study time:** 25–35 minutes
**Prerequisites:** C 001–098
**Companion program:** [`examples/c/c-099-defensive-code-review-checklist.c`](../../examples/c/c-099-defensive-code-review-checklist.c)
**Deterministic test:** [`tests/c-099-defensive-code-review-checklist.sh`](../../tests/c-099-defensive-code-review-checklist.sh)
**Author:** Manus AI

## What you will be able to do

By the end of this lesson, you should be able to apply a compact review checklist to a small byte parser before accepting its result.

## Retrieval practice

1. Why must a byte buffer carry an explicit `size_t` length rather than rely on `\0`?
2. What condition proves that an index is within a `count`-element buffer?
3. Name one C library return value that must be checked before using its result.
4. Recall one precise phrase from the preceding Russian lesson.

## Core concept

Review asks whether every input length, conversion, pointer, and return value has an explicit contract.

The companion program handles raw bytes as counted data. It checks arguments, capacities, and return values before advancing state. It is portable ISO C17 and deliberately excludes shellcode, native-code loaders, arbitrary assembly execution, process injection, self-modifying code, and real cryptographic implementation.

## Worked example

```c
#include <stdint.h>
#include <stdio.h>
#include <stdlib.h>

typedef enum { STATUS_OK = 0, STATUS_ARGUMENT = 1, STATUS_FORMAT = 2 } Status;

static Status validate_one_byte(const uint8_t *data, size_t len, uint8_t *out)
{
    if (data == NULL || out == NULL) return STATUS_ARGUMENT;
    if (len != 1) return STATUS_FORMAT;
    *out = data[0];
    return STATUS_OK;
}

int main(void)
{
    const uint8_t input[] = {UINT8_C(99)};
    uint8_t value = 0;
    if (validate_one_byte(input, sizeof input, &value) != STATUS_OK) return EXIT_FAILURE;
    if (printf("C 099 status-checked byte: %u\n", (unsigned)value) < 0) return EXIT_FAILURE;
    return EXIT_SUCCESS;
}
```

Compile and run:

```sh
cc -std=c17 -Wall -Wextra -Wpedantic \
  -o c-099-defensive-code-review-checklist \
  examples/c/c-099-defensive-code-review-checklist.c
./c-099-defensive-code-review-checklist
```

Expected output:

```text
C 099 status-checked byte: 99
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
./tests/c-099-defensive-code-review-checklist.sh
```

Compile and run with sanitizers where supported:

```sh
cc -std=c17 -Wall -Wextra -Wpedantic \
  -fsanitize=address,undefined -fno-omit-frame-pointer -g \
  -o c-099-sanitized \
  examples/c/c-099-defensive-code-review-checklist.c
./c-099-sanitized
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
