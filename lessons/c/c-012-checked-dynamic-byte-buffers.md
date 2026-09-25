# C 012 — Checked Dynamic Byte Buffers

**Estimated study time:** 25–35 minutes
**Prerequisites:** C 001–007 and the immediately preceding lessons in this expanded sequence
**Companion program:** [`examples/c/c-012-checked-dynamic-byte-buffers.c`](../../examples/c/c-012-checked-dynamic-byte-buffers.c)
**Deterministic test:** [`tests/c-012-checked-dynamic-byte-buffers.sh`](../../tests/c-012-checked-dynamic-byte-buffers.sh)
**Author:** Manus AI

## What you will be able to do

By the end of this lesson, you should be able to grow a dynamic byte buffer with checked capacity arithmetic and a temporary `realloc` pointer.

## Retrieval practice

Answer before reopening earlier lessons.

1. Why does a byte buffer need an explicit `size_t` length?
2. State the safe forward loop condition for `count` valid elements.
3. What must be true of a shift count for an exact 32-bit word?
4. From Russian 007, translate **Я не читаю в библиотеке.**

## Core concept

Dynamic storage is a pointer-plus-length-plus-capacity contract. Before growing, check arithmetic against `SIZE_MAX`; assign a `realloc` result to a temporary pointer so a failed allocation does not lose the old buffer.

The program uses portable ISO C17, checks return values at every external boundary, and keeps logical values separate from C strings. It uses `uint8_t` and `uint32_t` only where their exact-width meaning is part of the lesson’s contract. It does not create shellcode, load executable code, inject into processes, or modify its own instructions.

## Worked example

```c
#include <inttypes.h>
#include <stdint.h>
#include <stdio.h>
#include <stdlib.h>

typedef struct { uint8_t *data; size_t length; size_t capacity; } ByteVector;

static int append(ByteVector *vector, uint8_t value)
{
    if (vector == NULL) return 0;
    if (vector->length == vector->capacity) {
        size_t new_capacity = vector->capacity == 0 ? 4 : vector->capacity * 2;
        uint8_t *temporary;
        if (new_capacity < vector->capacity || new_capacity > SIZE_MAX / sizeof *vector->data) return 0;
        temporary = realloc(vector->data, new_capacity * sizeof *vector->data);
        if (temporary == NULL) return 0;
        vector->data = temporary;
        vector->capacity = new_capacity;
    }
    vector->data[vector->length++] = value;
    return 1;
}

int main(void)
{
    const uint8_t input[] = { UINT8_C(0x41), UINT8_C(0x00), UINT8_C(0xA7), UINT8_C(0x42), UINT8_C(0x7F) };
    ByteVector vector = { NULL, 0, 0 };
    for (size_t index = 0; index < sizeof input / sizeof input[0]; ++index) if (!append(&vector, input[index])) { free(vector.data); return EXIT_FAILURE; }
    if (printf("Length: %zu\nBytes:", vector.length) < 0) { free(vector.data); return EXIT_FAILURE; }
    for (size_t index = 0; index < vector.length; ++index) if (printf(" %02" PRIX8, vector.data[index]) < 0) { free(vector.data); return EXIT_FAILURE; }
    if (printf("\nCapacity: %zu\n", vector.capacity) < 0) { free(vector.data); return EXIT_FAILURE; }
    free(vector.data);
    return EXIT_SUCCESS;
}
```

Compile and run the committed companion program exactly as follows:

```sh
cc -std=c17 -Wall -Wextra -Wpedantic \
  -o c-012-checked-dynamic-byte-buffers \
  examples/c/c-012-checked-dynamic-byte-buffers.c
./c-012-checked-dynamic-byte-buffers
```

Expected output:

```text
Length: 5
Bytes: 41 00 A7 42 7F
Capacity: 8
```

## Guided practice

1. Identify the explicit input boundary in the example.
2. Name the status or return value checked after each external operation.
3. Explain why the example does not use a sentinel such as `0x00` as a general array boundary.
4. State one invalid input that the companion program rejects or handles safely.
5. From the immediately preceding Russian lesson, produce one accurate target phrase.

## Independent exercise

Write `append_many` with a checked `length + count` calculation. Do not calculate the required size after overflow could already have occurred.

Keep all counts in `size_t`, validate any conversion before narrowing it, and preserve the original object when an allocation or parsing operation fails. This is a data-processing exercise, not a cryptographic primitive. It must never be presented as suitable for security, encryption, authentication, or protection of real data.

## Validation

Run the repository test:

```sh
./tests/c-012-checked-dynamic-byte-buffers.sh
```

Then, where supported, build with AddressSanitizer and UndefinedBehaviorSanitizer:

```sh
cc -std=c17 -Wall -Wextra -Wpedantic \
  -fsanitize=address,undefined -fno-omit-frame-pointer -g \
  -o c-012-sanitized \
  examples/c/c-012-checked-dynamic-byte-buffers.c
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

The next scheduled lesson is **Russian 013 — plural past tense and groups of people.**

## References

[1]: https://en.cppreference.com/w/c/types/integer "Fixed width integer types — cppreference.com"
[2]: https://en.cppreference.com/w/c/language/operator_arithmetic "Arithmetic operators — cppreference.com"
[3]: https://en.cppreference.com/w/c/io/fread "fread — cppreference.com"
[4]: https://en.cppreference.com/w/c/memory/realloc "realloc — cppreference.com"
