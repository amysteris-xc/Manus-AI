# C 057 — Comprehensive Byte-Stream State Machine

**Estimated study time:** 25–35 minutes
**Prerequisites:** C 001–056 and the immediately preceding lessons
**Companion program:** [`examples/c/c-057-comprehensive-byte-stream-state-machine.c`](../../examples/c/c-057-comprehensive-byte-stream-state-machine.c)
**Deterministic test:** [`tests/c-057-comprehensive-byte-stream-state-machine.sh`](../../tests/c-057-comprehensive-byte-stream-state-machine.sh)
**Author:** Manus AI

## What you will be able to do

By the end of this lesson, you should be able to parse a framing protocol (header, payload, checksum) using an explicit state machine and error transitions.

## Retrieval practice

1. Why must byte buffers use explicit `size_t` lengths rather than sentinel termination?
2. State the safe traversal condition for `count` elements.
3. Explain why I/O and conversion return values must be verified before using outputs.
4. Recall one accurate phrase from the preceding Russian lesson.

## Core concept

The example treats bytes as counted raw data. It enforces explicit `size_t` capacities, verifies all external function returns, and maintains portability under ISO C17. It avoids native loaders, process injection, shellcode, and self-modifying code.

## Worked example

```c
#include <inttypes.h>
#include <stdint.h>
#include <stdio.h>
#include <stdlib.h>

typedef enum { STATE_MAGIC, STATE_LEN, STATE_PAYLOAD, STATE_DONE, STATE_ERROR } ParserState;

typedef struct {
    ParserState state;
    size_t expected_len;
    size_t read_len;
    uint8_t payload[8];
} ProtocolParser;

static void parser_feed(ProtocolParser *p, uint8_t byte)
{
    switch (p->state) {
    case STATE_MAGIC:
        p->state = (byte == 0xAA) ? STATE_LEN : STATE_ERROR;
        break;
    case STATE_LEN:
        if (byte > sizeof p->payload) p->state = STATE_ERROR;
        else {
            p->expected_len = byte;
            p->read_len = 0;
            p->state = (byte == 0) ? STATE_DONE : STATE_PAYLOAD;
        }
        break;
    case STATE_PAYLOAD:
        p->payload[p->read_len++] = byte;
        if (p->read_len == p->expected_len) p->state = STATE_DONE;
        break;
    default:
        break;
    }
}

int main(void)
{
    ProtocolParser p = {STATE_MAGIC, 0, 0, {0}};
    const uint8_t stream[] = {0xAA, 0x02, 0x10, 0x20};
    for (size_t i = 0; i < sizeof stream; ++i) parser_feed(&p, stream[i]);
    if (p.state != STATE_DONE) return EXIT_FAILURE;
    if (printf("Parser finished successfully with %zu payload bytes\n", p.read_len) < 0) return EXIT_FAILURE;
    return EXIT_SUCCESS;
}
```

Compile and run:

```sh
cc -std=c17 -Wall -Wextra -Wpedantic \
  -o c-057-comprehensive-byte-stream-state-machine \
  examples/c/c-057-comprehensive-byte-stream-state-machine.c
./c-057-comprehensive-byte-stream-state-machine 
```

Expected output:

```text
Parser finished successfully with 2 payload bytes
```

## Guided practice

1. Identify the input and output buffer boundaries.
2. Locate each return value and capacity check.
3. Explain why embedded `0x00` values remain valid data in raw buffers.
4. Name one malformed input condition that causes early error return.
5. Recall one phrase from the preceding Russian lesson.

## Independent exercise

Extend the program to validate an additional edge case or test input. Preserve explicit lengths and check every return status.

## Validation

Run the deterministic test:

```sh
./tests/c-057-comprehensive-byte-stream-state-machine.sh
```

Compile and run with sanitizers where supported:

```sh
cc -std=c17 -Wall -Wextra -Wpedantic \
  -fsanitize=address,undefined -fno-omit-frame-pointer -g \
  -o c-057-sanitized \
  examples/c/c-057-comprehensive-byte-stream-state-machine.c
```

## Final self-check

You are ready for the next lesson if you can explain the buffer contract, error return values, and why this lesson’s code is not an operational cryptographic security mechanism.

## Spoiler: answers and model response

1. Raw byte buffers can contain `0x00` as legitimate data; `strlen` cannot determine their size.
2. Forward loop bound: `index < count`.
3. Unchecked results can lead to use of uninitialized memory or silent failure.
4. Malformed inputs violate capacity or structure constraints and must return an error code.

## Next lesson

The next scheduled lesson returns to the Russian track.

## References

[1]: https://en.cppreference.com/w/c/types/integer "Fixed width integer types — cppreference.com"
[2]: https://en.cppreference.com/w/c/language/operator_arithmetic "Arithmetic operators — cppreference.com"
