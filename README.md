# Manus-AI

## Russian and C Programming Lessons

This private repository holds a progressive self-study course in **Russian** and **C programming**. A scheduled task creates one lesson at a time, alternating between the two tracks and preserving the course state in the repository.

## Repository layout

```text
curriculum_state.md     Course position, prerequisites, review notes, and next topic
LESSON_INDEX.md         One-line index of completed and planned lessons
lessons/russian/        Russian lesson Markdown files
lessons/c/              C lesson Markdown files
examples/c/             Runnable companion programs for C lessons
tests/                  Deterministic tests and test fixtures
```

## Lesson standard

Every lesson includes objectives, a short retrieval-practice review, explanation, worked examples, guided practice, independent exercises, hints or answer keys after a spoiler heading, and a final self-check. Russian lessons pair Cyrillic with precise translations and use transliteration only temporarily at the beginning. C lessons state an exact compile command and expected output where applicable.

C examples should target portable ISO C17 and compile with at least:

```sh
cc -std=c17 -Wall -Wextra -Wpedantic -o program program.c
```

When the environment supports them, examples should also be exercised with AddressSanitizer and UndefinedBehaviorSanitizer. Lesson material must emphasize checked I/O, bounded buffers, safe integer conversion, and avoidance of undefined behavior.

## Safety boundary

This course develops understanding of byte-oriented programming, hashing, and cryptography, but it does not create shellcode, native-code loaders, arbitrary assembly execution, process injection, or self-modifying code. Educational toy cryptographic algorithms, if used, are explicitly labeled **insecure** and never recommended for real data.

## Automated writing cadence

The task is intended to run twice daily, at 09:00 and 19:00 Europe/Helsinki time, and creates **one** alternating Russian or C lesson per run. It examines the state file, lesson index, and recent lessons before making a change, and it must validate C examples before recording progress.
