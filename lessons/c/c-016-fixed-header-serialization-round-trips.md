# C 016 — Fixed Header Serialization and Round-Trip Tests

**Estimated study time:** 25–35 minutes
**Prerequisites:** C 001–007 and the immediately preceding lessons in this expanded sequence
**Companion program:** [`examples/c/c-016-fixed-header-serialization-round-trips.c`](../../examples/c/c-016-fixed-header-serialization-round-trips.c)
**Deterministic test:** [`tests/c-016-fixed-header-serialization-round-trips.sh`](../../tests/c-016-fixed-header-serialization-round-trips.sh)
**Author:** Manus AI

## What you will be able to do

By the end of this lesson, you should be able to encode a small fixed header into an eight-byte big-endian format and decode it back with an exact round-trip test.

## Retrieval practice

Answer before reopening earlier lessons.

1. Why does a byte buffer need an explicit `size_t` length?
2. State the safe forward loop condition for `count` valid elements.
3. What must be true of a shift count for an exact 32-bit word?
4. From Russian 007, translate **Я не читаю в библиотеке.**

## Core concept

A fixed format needs field widths, byte order, and offsets written down. The code writes each `uint16_t` and `uint32_t` field into specific byte indexes and decodes the same indexes without relying on struct padding or host endianness.

The program uses portable ISO C17, checks return values at every external boundary, and keeps logical values separate from C strings. It uses `uint8_t` and `uint32_t` only where their exact-width meaning is part of the lesson’s contract. It does not create shellcode, load executable code, inject into processes, or modify its own instructions.

## Worked example

```c
#include <inttypes.h>
#include <stdint.h>
#include <stdio.h>
#include <stdlib.h>

static void put_be16(uint8_t out[2], uint16_t value) { out[0] = (uint8_t)(value >> 8); out[1] = (uint8_t)value; }
static uint16_t get_be16(const uint8_t in[2]) { return (uint16_t)(((uint16_t)in[0] << 8) | in[1]); }
static void put_be32(uint8_t out[4], uint32_t value) { out[0] = (uint8_t)(value >> 24); out[1] = (uint8_t)(value >> 16); out[2] = (uint8_t)(value >> 8); out[3] = (uint8_t)value; }
static uint32_t get_be32(const uint8_t in[4]) { return ((uint32_t)in[0] << 24) | ((uint32_t)in[1] << 16) | ((uint32_t)in[2] << 8) | in[3]; }

int main(void)
{
    uint8_t header[8];
    const uint16_t version = UINT16_C(3), flags = UINT16_C(5);
    const uint32_t length = UINT32_C(291);
    put_be16(header, version); put_be16(header + 2, flags); put_be32(header + 4, length);
    if (get_be16(header) != version || get_be16(header + 2) != flags || get_be32(header + 4) != length) return EXIT_FAILURE;
    if (printf("Header bytes: %02" PRIX8 " %02" PRIX8 " %02" PRIX8 " %02" PRIX8 " %02" PRIX8 " %02" PRIX8 " %02" PRIX8 " %02" PRIX8 "\nVersion: %" PRIu16 " Flags: %" PRIu16 " Length: %" PRIu32 "\n", header[0], header[1], header[2], header[3], header[4], header[5], header[6], header[7], version, flags, length) < 0) return EXIT_FAILURE;
    return EXIT_SUCCESS;
}
```

Compile and run the committed companion program exactly as follows:

```sh
cc -std=c17 -Wall -Wextra -Wpedantic \
  -o c-016-fixed-header-serialization-round-trips \
  examples/c/c-016-fixed-header-serialization-round-trips.c
./c-016-fixed-header-serialization-round-trips
```

Expected output:

```text
Header bytes: 00 03 00 05 00 00 01 23
Version: 3 Flags: 5 Length: 291
```

## Guided practice

1. Identify the explicit input boundary in the example.
2. Name the status or return value checked after each external operation.
3. Explain why the example does not use a sentinel such as `0x00` as a general array boundary.
4. State one invalid input that the companion program rejects or handles safely.
5. From the immediately preceding Russian lesson, produce one accurate target phrase.

## Independent exercise

Add a one-byte type field at byte 0 and move every other field to documented new offsets. Update both encoder, decoder, and test vector together.

Keep all counts in `size_t`, validate any conversion before narrowing it, and preserve the original object when an allocation or parsing operation fails. This is a data-processing exercise, not a cryptographic primitive. It must never be presented as suitable for security, encryption, authentication, or protection of real data.

## Validation

Run the repository test:

```sh
./tests/c-016-fixed-header-serialization-round-trips.sh
```

Then, where supported, build with AddressSanitizer and UndefinedBehaviorSanitizer:

```sh
cc -std=c17 -Wall -Wextra -Wpedantic \
  -fsanitize=address,undefined -fno-omit-frame-pointer -g \
  -o c-016-sanitized \
  examples/c/c-016-fixed-header-serialization-round-trips.c
```

## Final self-check

You are ready for the next lesson if you can explain the input boundary, the relevant integer width, the return-value checks, the overflow or shift guard, the deterministic test vector, and why the example is not a cryptographic construction.

## Spoiler: answers and model response

1. A byte buffer uses an explicit length because zero-valued bytes can be ordinary data.
2. The safe condition is `index < count`.
3. The shift count must be nonnegative and strictly less than the promoted left operand’s width.
4. **Я не читаю в библиотеке.** means “I do not read at the library.”
5. The example’s checked boundary and deterministic test make its behaviour reviewable; they do not turn it into a cryptographic security mechanism.

## Next lesson

The next scheduled lesson is **Russian 017 — integrated routines across past, present, and future.**

## References

[1]: https://en.cppreference.com/w/c/types/integer "Fixed width integer types — cppreference.com"
[2]: https://en.cppreference.com/w/c/language/operator_arithmetic "Arithmetic operators — cppreference.com"
[3]: https://en.cppreference.com/w/c/io/fread "fread — cppreference.com"
[4]: https://en.cppreference.com/w/c/memory/realloc "realloc — cppreference.com"
