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

No lessons have been completed. The course begins with **Russian lesson 001** and then alternates to **C lesson 001**.

| Next run | Lesson identifier | Planned topic | Prerequisites |
|---|---|---|---|
| 1 | `russian-001-cyrillic-greetings` | Cyrillic orientation, basic pronunciation, greetings, and first recall practice | None |
| 2 | `c-001-build-types-output` | C program structure, compiling, fundamental types, and output | None |

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

Add a dated entry after every lesson noting concepts reviewed, learner difficulties evident from project feedback, validation results, and the next planned topic.
