# C 015 — Dynamic File Reading with Checked Growth

**Estimated study time:** 25–35 minutes
**Prerequisites:** C 001–007 and the immediately preceding lessons in this expanded sequence
**Companion program:** [`examples/c/c-015-dynamic-file-reading-with-checked-growth.c`](../../examples/c/c-015-dynamic-file-reading-with-checked-growth.c)
**Deterministic test:** [`tests/c-015-dynamic-file-reading-with-checked-growth.sh`](../../tests/c-015-dynamic-file-reading-with-checked-growth.sh)
**Author:** Manus AI

## What you will be able to do

By the end of this lesson, you should be able to read a binary file into a dynamic `uint8_t` buffer using bounded chunks, checked arithmetic, a temporary `realloc` pointer, and final cleanup.

## Retrieval practice

Answer before reopening earlier lessons.

1. Why does a byte buffer need an explicit `size_t` length?
2. State the safe forward loop condition for `count` valid elements.
3. What must be true of a shift count for an exact 32-bit word?
4. From Russian 007, translate **Я не читаю в библиотеке.**

## Core concept

The reader combines bounded I/O with dynamic storage. It appends only the bytes `fread` actually returned, checks `ferror` after a short read, and keeps the old allocation valid if growth fails.

The program uses portable ISO C17, checks return values at every external boundary, and keeps logical values separate from C strings. It uses `uint8_t` and `uint32_t` only where their exact-width meaning is part of the lesson’s contract. It does not create shellcode, load executable code, inject into processes, or modify its own instructions.

## Worked example

```c
#include <inttypes.h>
#include <stdint.h>
#include <stdio.h>
#include <stdlib.h>

typedef struct { uint8_t *data; size_t length; size_t capacity; } Buffer;

static int append(Buffer *buffer, const uint8_t *source, size_t count)
{
    size_t needed;
    uint8_t *temporary;
    if (buffer == NULL || source == NULL || count == 0) return count == 0;
    if (SIZE_MAX - buffer->length < count) return 0;
    needed = buffer->length + count;
    if (needed > buffer->capacity) {
        size_t capacity = buffer->capacity == 0 ? 64 : buffer->capacity;
        while (capacity < needed) { if (capacity > SIZE_MAX / 2) return 0; capacity *= 2; }
        temporary = realloc(buffer->data, capacity * sizeof *buffer->data);
        if (temporary == NULL) return 0;
        buffer->data = temporary; buffer->capacity = capacity;
    }
    for (size_t index = 0; index < count; ++index) buffer->data[buffer->length + index] = source[index];
    buffer->length = needed;
    return 1;
}

int main(int argc, char *argv[])
{
    uint8_t chunk[32];
    Buffer buffer = { NULL, 0, 0 };
    FILE *in;
    if (argc != 2) return EXIT_FAILURE;
    in = fopen(argv[1], "rb"); if (in == NULL) return EXIT_FAILURE;
    for (;;) { size_t got = fread(chunk, 1, sizeof chunk, in); if (!append(&buffer, chunk, got)) { free(buffer.data); (void)fclose(in); return EXIT_FAILURE; } if (got < sizeof chunk) { if (ferror(in)) { free(buffer.data); (void)fclose(in); return EXIT_FAILURE; } break; } }
    if (fclose(in) != 0 || buffer.length == 0 || buffer.data == NULL) {
        free(buffer.data);
        return EXIT_FAILURE;
    }
    if (printf("Bytes loaded: %zu\nFirst byte: 0x%02" PRIX8 "\nLast byte: 0x%02" PRIX8 "\n", buffer.length, buffer.data[0], buffer.data[buffer.length - 1]) < 0) { free(buffer.data); return EXIT_FAILURE; }
    free(buffer.data);
    return EXIT_SUCCESS;
}
```

Compile and run the committed companion program exactly as follows:

```sh
cc -std=c17 -Wall -Wextra -Wpedantic \
  -o c-015-dynamic-file-reading-with-checked-growth \
  examples/c/c-015-dynamic-file-reading-with-checked-growth.c
./c-015-dynamic-file-reading-with-checked-growth fixture.bin
```

Expected output:

```text
Bytes loaded: 5
First byte: 0x41
Last byte: 0x43
```

## Guided practice

1. Identify the explicit input boundary in the example.
2. Name the status or return value checked after each external operation.
3. Explain why the example does not use a sentinel such as `0x00` as a general array boundary.
4. State one invalid input that the companion program rejects or handles safely.
5. From the immediately preceding Russian lesson, produce one accurate target phrase.

## Independent exercise

Add an explicit maximum permitted file size and reject input before a growth request would exceed it. Explain why a maximum is a product requirement rather than an accidental allocator limit.

Keep all counts in `size_t`, validate any conversion before narrowing it, and preserve the original object when an allocation or parsing operation fails. This is a data-processing exercise, not a cryptographic primitive. It must never be presented as suitable for security, encryption, authentication, or protection of real data.

## Validation

Run the repository test:

```sh
./tests/c-015-dynamic-file-reading-with-checked-growth.sh
```

Then, where supported, build with AddressSanitizer and UndefinedBehaviorSanitizer:

```sh
cc -std=c17 -Wall -Wextra -Wpedantic \
  -fsanitize=address,undefined -fno-omit-frame-pointer -g \
  -o c-015-sanitized \
  examples/c/c-015-dynamic-file-reading-with-checked-growth.c
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

The next scheduled lesson is **Russian 016 — wants with хочу and infinitives.**

## References

[1]: https://en.cppreference.com/w/c/types/integer "Fixed width integer types — cppreference.com"
[2]: https://en.cppreference.com/w/c/language/operator_arithmetic "Arithmetic operators — cppreference.com"
[3]: https://en.cppreference.com/w/c/io/fread "fread — cppreference.com"
[4]: https://en.cppreference.com/w/c/memory/realloc "realloc — cppreference.com"
