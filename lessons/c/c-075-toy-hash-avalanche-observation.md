# C 075 — Toy Hash Avalanche Observation

**Estimated study time:** 25–35 minutes
**Prerequisites:** C 001–074
**Companion program:** [`examples/c/c-075-toy-hash-avalanche-observation.c`](../../examples/c/c-075-toy-hash-avalanche-observation.c)
**Deterministic test:** [`tests/c-075-toy-hash-avalanche-observation.sh`](../../tests/c-075-toy-hash-avalanche-observation.sh)
**Author:** Manus AI

## What you will be able to do

By the end of this lesson, you should be able to observe how a small input change can alter an educational toy-hash output without claiming security.

## Retrieval practice

1. Why must a byte buffer carry an explicit `size_t` length rather than rely on `\0`?
2. What condition proves that an index is within a `count`-element buffer?
3. Name one C library return value that must be checked before using its result.
4. Recall one precise phrase from the preceding Russian lesson.

## Core concept

A changing output is not evidence of cryptographic strength; established algorithms and independent review are required.

The companion program handles raw bytes as counted data. It checks arguments, capacities, and return values before advancing state. It is portable ISO C17 and deliberately excludes shellcode, native-code loaders, arbitrary assembly execution, process injection, self-modifying code, and real cryptographic implementation.

## Worked example

```c
#include <inttypes.h>
#include <stdint.h>
#include <stdio.h>
#include <stdlib.h>

/* Deliberately non-cryptographic teaching hash; never use for real security. */
static uint32_t toy_fnv1a(const uint8_t *data, size_t len)
{
    uint32_t hash = UINT32_C(2166136261);
    if (data == NULL && len != 0) return 0;
    for (size_t i = 0; i < len; ++i) {
        hash ^= data[i];
        hash *= UINT32_C(16777619);
    }
    return hash;
}

int main(void)
{
    const uint8_t data[] = {'L', '0', '1'};
    uint32_t value = toy_fnv1a(data, sizeof data);
    if (printf("C 075 toy hash: %08" PRIX32 "\n", value) < 0) return EXIT_FAILURE;
    return EXIT_SUCCESS;
}
```

Compile and run:

```sh
cc -std=c17 -Wall -Wextra -Wpedantic \
  -o c-075-toy-hash-avalanche-observation \
  examples/c/c-075-toy-hash-avalanche-observation.c
./c-075-toy-hash-avalanche-observation
```

Expected output:

```text
C 075 toy hash: 5B779590
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
./tests/c-075-toy-hash-avalanche-observation.sh
```

Compile and run with sanitizers where supported:

```sh
cc -std=c17 -Wall -Wextra -Wpedantic \
  -fsanitize=address,undefined -fno-omit-frame-pointer -g \
  -o c-075-sanitized \
  examples/c/c-075-toy-hash-avalanche-observation.c
./c-075-sanitized
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
