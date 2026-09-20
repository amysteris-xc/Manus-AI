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

Russian lesson 003 is complete. The next scheduled run must create **C lesson 003**. No review or remediation lesson is currently scheduled.

| Status | Lesson identifier | Topic | Prerequisites |
|---|---|---|---|
| Completed | `russian-001-cyrillic-greetings` | Cyrillic orientation, basic pronunciation, greetings, and first recall practice | None |
| Completed | `c-001-build-types-output` | C program structure, compiling, fundamental types, and output | None |
| Completed | `russian-002-sounds-and-introductions` | High-value Cyrillic sound patterns, stress awareness, and fuller introductions | Russian 001 |
| Completed | `c-002-control-flow-and-simple-input` | Expressions, decisions, loops, and a first checked input boundary | C 001 |
| Completed | `russian-003-gender-and-everyday-nouns` | Grammatical gender, basic noun patterns, and familiar objects | Russian 001–002 |
| Next | `c-003-functions-and-error-returns` | Function boundaries, parameters, return values, and explicit error propagation | C 001–002 |
| Planned after next | `russian-004-adjectives-and-simple-descriptions` | Gender agreement in common adjectives and concise object descriptions | Russian 001–003 |

## Long-range C milestones

| Stage | Topics |
|---|---|
| Foundations | Build process, types, control flow, functions, arrays, strings, pointers, lifetime, and errors |
| File handling | Introduced no later than lesson 10: text/binary modes, checked I/O, bounded chunk processing, partial reads/writes, and I/O errors |
| Byte-oriented programming | `uint8_t`, `uint32_t`, byte buffers versus C strings, endianness, shifts, masks, and unsigned arithmetic |
| Secure implementation practice | Bounds checks, allocation-size checks, overflow/truncation prevention, tests, sanitizers, fuzzing concepts, and review |
| Hashing and cryptography literacy | Threat models, checksums versus cryptographic hashes, test vectors, and safe use of established libraries |

> Educational cryptographic examples must be labeled insecure and must never be presented as suitable for protecting real data. The curriculum excludes shellcode, native-code loaders, arbitrary assembly execution, process injection, and self-modifying code.

## Review and adaptation record

- **2026-09-18 — Russian 001 completed.** No earlier lesson was available for retrieval practice, so the lesson established a cover-and-recall routine using a phrase table. It introduced Cyrillic as a sound-based writing system, highlighted high-risk Latin look-alikes, and practised formal versus informal greetings plus a short self-introduction. No learner feedback was available. The next topic is C 001: program structure, compilation, fundamental types, and output.
- **2026-09-19 — C 001 completed.** The lesson briefly recalled the preceding Russian material, then introduced the hosted `main` function, `#include <stdio.h>`, strict C17 compilation, `char`, `int`, `double`, `void`, and type-matched `printf` conversions. The companion program checks output errors, and its expected output was verified by a deterministic shell test plus AddressSanitizer and UndefinedBehaviorSanitizer builds. No learner feedback was available. The next topic is Russian 002: high-value Cyrillic sound patterns, stress awareness, and fuller introductions.
- **2026-09-19 — Russian 002 completed.** The lesson retrieved Russian greetings and the prior C format-specifier concept, then introduced word stress, vowel reduction awareness, the soft sign, high-value consonants, and formal/informal origin questions. It added a short formal self-introduction plus a spaced-repetition plan. No learner feedback was available. The next topic is C 002: expressions, decisions, loops, and a first checked input boundary.
- **2026-09-20 — C 002 completed.** The lesson retrieved core C output and Russian introduction material, then introduced arithmetic and comparison expressions, `if`/`else`, bounded `for` loops, and a checked line-input boundary using `fgets`, `strtol`, `errno`, an end pointer, trailing-text checks, and a pre-cast application range. Its companion program rejects missing, malformed, out-of-range, and overlong input. Strict compilation, exact-output tests, long-line rejection, and AddressSanitizer/UndefinedBehaviorSanitizer runs all passed. No learner feedback was available. The next topic is Russian 003: grammatical gender, basic noun patterns, and familiar objects.
- **2026-09-20 — Russian 003 completed.** The lesson retrieved earlier Russian and C material, introduced the three grammatical genders through dictionary-form endings, highlighted soft-sign ambiguity and common exceptions, and paired everyday objects with `он`, `она`, and `оно`. It included a short object-identification dialogue and a gender-package recall routine. No learner feedback was available. The next topic is C 003: function boundaries, parameters, return values, and explicit error propagation.
