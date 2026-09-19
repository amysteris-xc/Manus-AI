# Curriculum State

## Course configuration

| Setting | Value |
|---|---|
| Tracks | Russian and C programming |
| Delivery rhythm | Alternating tracks; one lesson per scheduled run |
| Intended lesson duration | 25–35 minutes |
| C language baseline | ISO C17 unless a lesson explicitly says otherwise |
| Course root | Repository root |

## Current position

C lesson 001 is complete. The next scheduled run must create **Russian lesson 002**. No review or remediation lesson is currently scheduled.

| Status | Lesson identifier | Topic | Prerequisites |
|---|---|---|---|
| Completed | `russian-001-cyrillic-greetings` | Cyrillic orientation, basic pronunciation, greetings, and first recall practice | None |
| Completed | `c-001-build-types-output` | C program structure, compiling, fundamental types, and output | None |
| Next | `russian-002-sounds-and-introductions` | High-value Cyrillic sound patterns, stress awareness, and fuller introductions | Russian 001 |
| Planned after next | `c-002-control-flow-and-simple-input` | Expressions, decisions, loops, and a first checked input boundary | C 001 |

## Long-range C milestones

| Stage | Topics |
|---|---|
| Foundations | Build process, types, control flow, functions, arrays, strings, pointers, lifetime, and errors |
| File handling | Introduced no later than lesson 10: text/binary modes, checked I/O, bounded chunk processing, partial reads/writes, and I/O errors |
| Byte-oriented programming | `uint8_t`, `uint32_t`, byte buffers versus C strings, endianness, shifts, masks, and unsigned arithmetic |
| Secure implementation practice | Bounds checks, allocation-size checks, overflow/truncation prevention, tests, sanitizers, fuzzing concepts, and review |
| Hashing and cryptography literacy | Threat models, checksums versus cryptographic hashes, test vectors, side channels, and safe use of established libraries |

> Educational cryptographic examples must be labeled insecure and must never be presented as suitable for protecting real data. The curriculum excludes shellcode, native-code loaders, arbitrary assembly execution, process injection, and self-modifying code.

## Review and adaptation record

- **2026-09-18 — Russian 001 completed.** No earlier lesson was available for retrieval practice, so the lesson established a cover-and-recall routine using a phrase table. It introduced Cyrillic as a sound-based writing system, highlighted high-risk Latin look-alikes, and practised formal versus informal greetings plus a short self-introduction. No learner feedback was available. The next topic is C 001: program structure, compilation, fundamental types, and output.
- **2026-09-19 — C 001 completed.** The lesson briefly recalled the preceding Russian material, then introduced the hosted `main` function, `#include <stdio.h>`, strict C17 compilation, `char`, `int`, `double`, `void`, and type-matched `printf` conversions. The companion program checks output errors, and its expected output was verified by a deterministic shell test plus AddressSanitizer and UndefinedBehaviorSanitizer builds. No learner feedback was available. The next topic is Russian 002: high-value Cyrillic sound patterns, stress awareness, and fuller introductions.
