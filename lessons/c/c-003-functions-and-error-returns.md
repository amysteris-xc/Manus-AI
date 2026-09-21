# C 003 — Functions, Output Parameters, and Explicit Error Returns

**Estimated study time:** 25–35 minutes
**Prerequisites:** [C 001 — Build, Types, and Output](c-001-build-types-output.md) and [C 002 — Expressions, Decisions, Loops, and Checked Input](c-002-control-flow-and-simple-input.md)
**Companion program:** [`examples/c/c-003-functions-and-error-returns.c`](../../examples/c/c-003-functions-and-error-returns.c)
**Deterministic test:** [`tests/c-003-functions-and-error-returns.sh`](../../tests/c-003-functions-and-error-returns.sh)
**Author:** Manus AI

## What you will be able to do

By the end of this lesson, you should be able to split a small C program into functions with clear responsibilities, read a function signature, pass values through parameters, use a narrow output-parameter pattern when a function must provide both a computed value and a success/failure result, and propagate named error statuses back to `main`.

## Retrieval warm-up

Without opening C 002, answer these questions.

1. Why does the previous lesson read a bounded line with `fgets` before calling `strtol`?
2. Which check detects that `strtol` converted no digits?
3. Which condition rejects trailing non-whitespace text such as `12cats`?
4. What does `return 1;` conventionally communicate from the earlier examples?

Then recall one item from Russian 003: which pronoun matches the neuter noun **окно**? The answers are in the spoiler section.

## 1. A function is a contract with a focused job

A C function has a name, parameters, a body, and optionally a return value. Parameters receive values from the caller; a `return` statement ends the function and can send a value back to the caller.[1] [2]

```c
static int double_count(int count)
{
    return count * 2;
}
```

The signature tells a reader how to call it:

| Part | Meaning |
|---|---|
| `static` | This helper is private to this source file. |
| `int` before the name | The function returns an `int`. |
| `double_count` | The function’s purpose-oriented name. |
| `int count` | One input parameter, named `count`, with type `int`. |
| `return count * 2;` | Computes the result and sends it back to the caller. |

A function should have one clear responsibility. A function called `parse_bounded_int` should parse and validate a bounded integer. A function called `write_plan` should write an already-valid plan. It should not quietly decide unrelated policy, read a second input source, or modify hidden global state.

## 2. Use named status codes for expected failure

C functions often need to distinguish success from expected, recoverable failure. This lesson uses a small enumeration rather than unexplained numeric return values.

```c
typedef enum {
    STATUS_OK = 0,
    STATUS_INVALID_ARGUMENT = 1,
    STATUS_OUTPUT_FAILURE = 2
} Status;
```

A `Status` result gives the caller a clear question to answer: did the function succeed? The caller must check the result immediately and decide what to do next.

```c
Status status = parse_bounded_int(text, 1, 100, &count);

if (status != STATUS_OK) {
    return EXIT_FAILURE;
}
```

Do not use a valid numeric result such as `0` as both data and an error signal unless the interface has a separate, unambiguous way to distinguish them. For example, a plan may legitimately contain zero review checkpoints. A status value separate from the computed result avoids ambiguity.

## 3. One result plus one status: a first output parameter

Suppose a function must calculate a total **and** report whether the calculation was valid. Returning only an `int` is ambiguous because a total might itself be `0`. One simple C pattern is:

- Return a `Status` for success or failure.
- Receive an `int *result` parameter that points to storage owned by the caller.
- Write the computed value to `*result` only after all validation succeeds.

```c
static Status calculate_total_minutes(int sessions,
                                      int minutes_per_session,
                                      int *result)
{
    if (result == NULL) {
        return STATUS_INVALID_ARGUMENT;
    }

    if (sessions < 1 || minutes_per_session < 1) {
        return STATUS_INVALID_ARGUMENT;
    }

    *result = sessions * minutes_per_session;
    return STATUS_OK;
}
```

The `*` in `int *result` says that `result` is a pointer to an `int`. The `*` in `*result = ...` accesses the caller’s `int` through that pointer. When calling the function, `&total_minutes` passes the address of the caller’s local variable.

```c
int total_minutes;
Status status = calculate_total_minutes(4, 25, &total_minutes);
```

This is a deliberately narrow first use of pointers. It does **not** involve dynamic memory, pointer arithmetic, or raw bytes. Those topics come later. For now, follow one rule: only dereference a pointer after the interface has required and checked that it is not `NULL`.

## 4. Protect the calculation before it happens

Before multiplying two positive `int` values, check whether the product would exceed `INT_MAX`.

```c
if (sessions > INT_MAX / minutes_per_session) {
    return STATUS_INVALID_ARGUMENT;
}

*result = sessions * minutes_per_session;
```

The division is safe here because the function has already required `minutes_per_session >= 1`. This is a small example of **precondition checking**: establish the conditions that make the next operation safe before performing it.

The companion program also uses application-specific limits: `1–100` sessions and `1–180` minutes per session. These limits make the program’s expectations explicit and keep the demonstration values far below `INT_MAX`. The overflow check remains valuable because function interfaces should defend their own arithmetic assumptions, not rely only on the current caller.

## 5. Error propagation means the caller decides

A low-level helper can identify a failure, but it often lacks enough context to decide how the whole program should respond. It returns a status to its caller. The caller checks it, adds context if useful, and returns failure to its own caller if the work cannot continue.

```c
status = calculate_total_minutes(sessions, minutes_per_session, &total_minutes);
if (status != STATUS_OK) {
    (void)write_error("Cannot calculate total planned minutes.\n");
    return EXIT_FAILURE;
}
```

This is **explicit error propagation**. No failure is silently ignored. The main function translates a failed helper operation into a nonzero process exit status.

The output helper follows the same pattern. `fputs` returns a non-negative value on success and `EOF` on failure.[3] A wrapper turns that library-specific rule into this program’s `Status` contract.

```c
static Status write_error(const char *message)
{
    if (message == NULL || fputs(message, stderr) == EOF) {
        return STATUS_OUTPUT_FAILURE;
    }

    return STATUS_OK;
}
```

The `(void)` before a `write_error` call means the program has no further useful recovery path if writing the diagnostic also fails. The program still returns `EXIT_FAILURE`; it does not falsely claim success.

## 6. Worked example: a modular session-plan calculator

The companion program accepts two command-line values: a number of sessions and minutes per session. It parses each value with the checked `strtol` pattern from C 002, calculates the total through an output parameter, and writes a plan only after every prior step succeeds.

```c
#include <ctype.h>
#include <errno.h>
#include <limits.h>
#include <stdio.h>
#include <stdlib.h>

typedef enum {
    STATUS_OK = 0,
    STATUS_INVALID_ARGUMENT = 1,
    STATUS_OUTPUT_FAILURE = 2
} Status;

enum {
    MIN_SESSIONS = 1,
    MAX_SESSIONS = 100,
    MIN_MINUTES_PER_SESSION = 1,
    MAX_MINUTES_PER_SESSION = 180
};

static Status parse_bounded_int(const char *text, int minimum, int maximum,
                                int *result)
{
    char *end = NULL;
    long value;

    if (text == NULL || result == NULL || minimum > maximum) {
        return STATUS_INVALID_ARGUMENT;
    }

    errno = 0;
    value = strtol(text, &end, 10);

    if (end == text || errno == ERANGE) {
        return STATUS_INVALID_ARGUMENT;
    }

    while (isspace((unsigned char)*end)) {
        ++end;
    }

    if (*end != '\0' || value < minimum || value > maximum) {
        return STATUS_INVALID_ARGUMENT;
    }

    *result = (int)value;
    return STATUS_OK;
}

static Status calculate_total_minutes(int sessions, int minutes_per_session,
                                      int *result)
{
    if (result == NULL || sessions < MIN_SESSIONS ||
        minutes_per_session < MIN_MINUTES_PER_SESSION) {
        return STATUS_INVALID_ARGUMENT;
    }

    if (sessions > INT_MAX / minutes_per_session) {
        return STATUS_INVALID_ARGUMENT;
    }

    *result = sessions * minutes_per_session;
    return STATUS_OK;
}

static Status write_plan(FILE *stream, int sessions, int minutes_per_session,
                         int total_minutes)
{
    if (stream == NULL) {
        return STATUS_INVALID_ARGUMENT;
    }

    if (fprintf(stream, "Sessions: %d\n", sessions) < 0 ||
        fprintf(stream, "Minutes per session: %d\n", minutes_per_session) < 0 ||
        fprintf(stream, "Total planned minutes: %d\n", total_minutes) < 0) {
        return STATUS_OUTPUT_FAILURE;
    }

    return STATUS_OK;
}

static Status write_error(const char *message)
{
    if (message == NULL || fputs(message, stderr) == EOF) {
        return STATUS_OUTPUT_FAILURE;
    }

    return STATUS_OK;
}

int main(int argc, char *argv[])
{
    int sessions;
    int minutes_per_session;
    int total_minutes;
    Status status;

    if (argc != 3) {
        (void)write_error("Usage: c-003-functions-and-error-returns SESSIONS MINUTES_PER_SESSION\n");
        return EXIT_FAILURE;
    }

    status = parse_bounded_int(argv[1], MIN_SESSIONS, MAX_SESSIONS, &sessions);
    if (status != STATUS_OK) {
        (void)write_error("Invalid session count. Use 1 through 100.\n");
        return EXIT_FAILURE;
    }

    status = parse_bounded_int(argv[2], MIN_MINUTES_PER_SESSION,
                               MAX_MINUTES_PER_SESSION, &minutes_per_session);
    if (status != STATUS_OK) {
        (void)write_error("Invalid minutes per session. Use 1 through 180.\n");
        return EXIT_FAILURE;
    }

    status = calculate_total_minutes(sessions, minutes_per_session,
                                     &total_minutes);
    if (status != STATUS_OK) {
        (void)write_error("Cannot calculate total planned minutes.\n");
        return EXIT_FAILURE;
    }

    status = write_plan(stdout, sessions, minutes_per_session, total_minutes);
    if (status != STATUS_OK) {
        return EXIT_FAILURE;
    }

    return EXIT_SUCCESS;
}
```

Compile and run the committed companion file exactly as follows:

```sh
cc -std=c17 -Wall -Wextra -Wpedantic \
  -o c-003-functions-and-error-returns \
  examples/c/c-003-functions-and-error-returns.c
./c-003-functions-and-error-returns 4 25
```

Expected output:

```text
Sessions: 4
Minutes per session: 25
Total planned minutes: 100
```

Try an invalid value:

```sh
./c-003-functions-and-error-returns 0 25
```

The program should exit with failure and write this diagnostic to standard error:

```text
Invalid session count. Use 1 through 100.
```

## 7. Guided practice

Answer before reading the spoiler section.

1. In `Status parse_bounded_int(const char *text, int minimum, int maximum, int *result)`, which parameters are inputs and which parameter receives an output?
2. Why does `calculate_total_minutes` return `Status` instead of returning the total directly?
3. What does `&total_minutes` provide to the function call?
4. Why must `result == NULL` be checked before `*result = ...`?
5. Which function is responsible for formatting a successful plan?
6. Which function decides that an invalid session count makes the program exit with failure?
7. Why does the calculation check `sessions > INT_MAX / minutes_per_session` before multiplying?

## 8. Independent exercise

Create `exercise.c` beside the companion file. Write three functions for a break-plan calculator:

1. `parse_bounded_int` or an equivalent checked parser that accepts break minutes from `1` through `60`.
2. `calculate_weekly_minutes` that receives sessions, minutes per session, and an output pointer; it must reject a null output pointer and guard the multiplication before performing it.
3. `write_summary` that receives a `FILE *` stream and writes a short plan, returning a status on output failure.

Make `main` parse two command-line arguments: sessions and break minutes. Use named status values, check every helper return immediately, and return `EXIT_FAILURE` when the plan cannot continue. Test valid input such as `5 10`, invalid text such as `five 10`, an out-of-range value such as `5 61`, and a missing argument.

## 9. Validate with sanitizers

Run the repository’s deterministic test:

```sh
./tests/c-003-functions-and-error-returns.sh
```

Then build and run with AddressSanitizer and UndefinedBehaviorSanitizer when supported:

```sh
cc -std=c17 -Wall -Wextra -Wpedantic \
  -fsanitize=address,undefined -fno-omit-frame-pointer -g \
  -o c-003-sanitized \
  examples/c/c-003-functions-and-error-returns.c
./c-003-sanitized 4 25
./c-003-sanitized 0 25
```

The valid run should print the three plan lines. The invalid run should return nonzero and print the expected diagnostic, with no sanitizer report. The sanitizers help detect certain runtime errors, but the source-level status checks and range checks remain necessary.

## 10. Final self-check

You are ready for the next lesson if you can do all of the following without looking back.

1. Explain the difference between a function parameter and an argument.
2. State why named status values can be clearer than a bare `0` or `1`.
3. Describe the output-parameter pattern used by `calculate_total_minutes`.
4. Explain why `main` checks each function’s return value immediately.
5. Identify the condition that protects the multiplication from signed integer overflow.
6. State which Russian pronoun matches **окно**.

## Spoiler: answers and model response

1. The input parameters are `text`, `minimum`, and `maximum`. `result` is an output parameter; on success, the function stores the parsed `int` through it.
2. The total itself is data and may be a valid numeric value. Returning a separate `Status` makes success or failure unambiguous, while the output parameter receives the total only on success.
3. `&total_minutes` passes the address of the caller’s `int total_minutes` variable.
4. Dereferencing a null pointer is invalid. Checking first ensures the function writes only to a valid output location.
5. `write_plan` formats successful output.
6. `main` decides that the error prevents further work and returns `EXIT_FAILURE` after a parsing helper reports failure.
7. The condition proves that `sessions * minutes_per_session` fits in `int` before the multiplication occurs.
8. **окно** is neuter, so its pronoun is **оно**.

A minimal status-check pattern is:

```c
status = helper(...);
if (status != STATUS_OK) {
    return EXIT_FAILURE;
}
```

## Next lesson

The next scheduled lesson returns to Russian: **Russian 004 — gender agreement in common adjectives and concise object descriptions.**

## References

[1]: https://en.cppreference.com/w/c/language/functions "Functions — cppreference.com"
[2]: https://en.cppreference.com/w/c/language/return "return statement — cppreference.com"
[3]: https://en.cppreference.com/w/c/io/fputs "fputs — cppreference.com"
