# C 083 — Portable File Hygiene and Cleanup

**Estimated study time:** 25–35 minutes
**Prerequisites:** C 001–082
**Companion program:** [`examples/c/c-083-portable-file-hygiene-and-cleanup.c`](../../examples/c/c-083-portable-file-hygiene-and-cleanup.c)
**Deterministic test:** [`tests/c-083-portable-file-hygiene-and-cleanup.sh`](../../tests/c-083-portable-file-hygiene-and-cleanup.sh)
**Author:** Manus AI

## What you will be able to do

By the end of this lesson, you should be able to create and close a small temporary binary file while checking each portable C file operation.

## Retrieval practice

1. Why must a byte buffer carry an explicit `size_t` length rather than rely on `\0`?
2. What condition proves that an index is within a `count`-element buffer?
3. Name one C library return value that must be checked before using its result.
4. Recall one precise phrase from the preceding Russian lesson.

## Core concept

Close every successfully opened stream and report failures before trusting data.

The companion program handles raw bytes as counted data. It checks arguments, capacities, and return values before advancing state. It is portable ISO C17 and deliberately excludes shellcode, native-code loaders, arbitrary assembly execution, process injection, self-modifying code, and real cryptographic implementation.

## Worked example

```c
#include <stdint.h>
#include <stdio.h>
#include <stdlib.h>

static int write_and_read_demo(const char *path, uint8_t *out)
{
    const uint8_t value = UINT8_C(84);
    FILE *stream = fopen(path, "wb");
    if (stream == NULL) return 0;
    if (fwrite(&value, sizeof value, 1, stream) != 1 || fclose(stream) != 0) return 0;
    stream = fopen(path, "rb");
    if (stream == NULL) return 0;
    if (fread(out, sizeof *out, 1, stream) != 1 || fclose(stream) != 0) return 0;
    return 1;
}

int main(void)
{
    uint8_t value = 0;
    if (!write_and_read_demo("lesson.bin", &value)) return EXIT_FAILURE;
    if (printf("C 083 verified file byte: %u\n", (unsigned)value) < 0) return EXIT_FAILURE;
    return EXIT_SUCCESS;
}
```

Compile and run:

```sh
cc -std=c17 -Wall -Wextra -Wpedantic \
  -o c-083-portable-file-hygiene-and-cleanup \
  examples/c/c-083-portable-file-hygiene-and-cleanup.c
./c-083-portable-file-hygiene-and-cleanup
```

Expected output:

```text
C 083 verified file byte: 84
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
./tests/c-083-portable-file-hygiene-and-cleanup.sh
```

Compile and run with sanitizers where supported:

```sh
cc -std=c17 -Wall -Wextra -Wpedantic \
  -fsanitize=address,undefined -fno-omit-frame-pointer -g \
  -o c-083-sanitized \
  examples/c/c-083-portable-file-hygiene-and-cleanup.c
./c-083-sanitized
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
