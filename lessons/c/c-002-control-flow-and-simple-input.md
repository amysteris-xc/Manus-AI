# C 002 — Expressions, Decisions, Loops, and Checked Input

**Estimated study time:** 25–35 minutes
**Prerequisites:** [C 001 — Build, Types, and Output](c-001-build-types-output.md)
**Companion program:** [`examples/c/c-002-control-flow-and-simple-input.c`](../../examples/c/c-002-control-flow-and-simple-input.c)
**Deterministic test:** [`tests/c-002-control-flow-and-simple-input.sh`](../../tests/c-002-control-flow-and-simple-input.sh)
**Author:** Manus AI

## What you will be able to do

By the end of this lesson, you should be able to evaluate basic C expressions, choose between paths with `if` and `else`, repeat a bounded action with `for`, read one bounded line with `fgets`, and convert a decimal whole number with `strtol` only after checking for missing digits, extra text, range errors, and the program’s accepted range.

## Retrieval warm-up

Without opening C 001, answer these questions.

1. Which header declares `printf`?
2. Which conversion specification matches an `int`?
3. What does `return 0;` conventionally report from `main`?
4. Which type is more appropriate for a whole-number lesson count: `int` or `double`?

Then recall one item from Russian 002: how do you politely ask “Where are you from?” The answers are in the spoiler section. This short mixed review maintains both tracks of the course.

## 1. Expressions create values

An **expression** computes a value. C programs make decisions and repeat work by evaluating expressions such as comparisons and arithmetic.

```c
const int completed = 12;
const int group_size = 5;
const int checkpoints = completed / group_size;
const int remainder = completed % group_size;
```

The `/` operator between two `int` values performs integer division, so `12 / 5` is `2`. The `%` operator is the remainder operator, so `12 % 5` is `2`. Use `%` only with integer types.

| Expression | Meaning when `completed` is 12 |
|---|---|
| `completed > 0` | true, represented by a nonzero value |
| `completed == 12` | true |
| `completed != 12` | false, represented by zero |
| `completed % 5 == 0` | false |
| `completed / 5` | `2` |

Do not use `=` when you mean “is equal to.” `=` assigns a value, while `==` compares values. Compilers can warn about some accidental assignments in conditions, but clear code and careful review are the first defense.

## 2. Decisions choose one path

An `if` statement executes its body when its condition is nonzero. `else` supplies the alternative path. Keep the braces even when a body has one statement; they make later edits safer and easier to review.

```c
if (lesson_count == 1) {
    printf("Plan: 1 lesson\n");
} else {
    printf("Plan: %d lessons\n", lesson_count);
}
```

The condition is evaluated once. Exactly one branch runs. In the companion program, invalid input takes an error path; valid input then takes either the singular or plural output path.

> **Boundary rule:** Never let a conversion function’s return value alone decide that input is valid. Validation must account for the entire input, the conversion range, and the range that the program itself accepts.

## 3. A `for` loop repeats a bounded action

A `for` statement has an initialization clause, a condition, and an iteration expression.[3] The condition is tested before each loop body. When it becomes zero, the loop stops.

```c
int checkpoint_count = 0;

for (int day = 1; day <= lesson_count; ++day) {
    if (day % 5 == 0) {
        ++checkpoint_count;
    }
}
```

This example counts every fifth day from `1` through `lesson_count`. The loop is **bounded** because `day` starts at `1`, increases once per iteration, and stops when it exceeds `lesson_count`. A loop needs a reason to terminate that can be inspected in code review.

For `lesson_count == 12`, the `if` condition succeeds for days `5` and `10`, so `checkpoint_count` finishes at `2`.

## 4. Read a line before converting it

For a first checked input boundary, do not ask `scanf` to place an integer directly into an `int`. Instead, read a line into a fixed-size buffer, then parse and validate that string deliberately.

```c
char input[64];

if (fgets(input, sizeof input, stdin) == NULL) {
    /* Handle end of input or an input error. */
}
```

`fgets` reads at most one fewer character than the supplied count, stops after a newline when one is read, and appends a null terminator after successful input.[1] `sizeof input` lets the compiler calculate the correct capacity from the actual array; it avoids manually repeating the number `64`.

A fixed buffer is a deliberate boundary. It means the program must decide what to do if a line is longer than the buffer. The companion program rejects that line after discarding the remaining characters from standard input. It does **not** silently truncate an input and pretend it was complete.

## 5. Convert and validate with `strtol`

`strtol` converts a null-terminated byte string to a `long` and reports where parsing stopped through an end pointer.[2] A robust call needs several checks:

1. Set `errno = 0` before the call, because `errno` may hold an older error.
2. Check `end == text`; this means no digits were converted.
3. Check `errno == ERANGE`; this means the converted value did not fit in `long`.
4. Skip only permitted trailing whitespace, then require `*end == '\0'`; this rejects `12cats`.
5. Check the program’s own accepted range before casting from `long` to `int`.

```c
errno = 0;
value = strtol(text, &end, 10);

if (end == text || errno == ERANGE) {
    return 0;
}

while (isspace((unsigned char)*end)) {
    ++end;
}

if (*end != '\0' || value < 1 || value > 100) {
    return 0;
}
```

The cast to `unsigned char` in `isspace((unsigned char)*end)` matters. Functions from `<ctype.h>` are defined for `EOF` or values representable as `unsigned char`; passing an arbitrary negative `char` value is not safe.

This program accepts decimal numbers from `1` to `100`, optionally surrounded by whitespace. It rejects empty input, non-numbers, mixed input such as `12cats`, numbers outside the chosen range, and lines too long for the fixed buffer. These are application rules, not universal rules: a future program may choose a different range or input format, but it still needs explicit checks.

## 6. Worked example: a checked study-plan count

The companion program below reads a lesson count, validates it, chooses singular or plural output, and uses a `for` loop to count review checkpoints.

```c
#include <ctype.h>
#include <errno.h>
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

enum {
    INPUT_CAPACITY = 64,
    MIN_LESSONS = 1,
    MAX_LESSONS = 100,
    REVIEW_INTERVAL = 5
};

static int parse_lesson_count(const char *text, int *result)
{
    char *end = NULL;
    long value;

    errno = 0;
    value = strtol(text, &end, 10);

    if (end == text || errno == ERANGE) {
        return 0;
    }

    while (isspace((unsigned char)*end)) {
        ++end;
    }

    if (*end != '\0' || value < MIN_LESSONS || value > MAX_LESSONS) {
        return 0;
    }

    *result = (int)value;
    return 1;
}

static int discard_remainder_of_line(void)
{
    int character;

    do {
        character = getchar();
    } while (character != '\n' && character != EOF);

    return character == EOF && ferror(stdin) ? 0 : 1;
}

int main(void)
{
    char input[INPUT_CAPACITY];
    int lesson_count;
    int checkpoint_count = 0;

    if (fputs("How many lessons (1-100)? ", stdout) == EOF) {
        return 1;
    }

    if (fgets(input, sizeof input, stdin) == NULL) {
        if (ferror(stdin)) {
            fputs("Input error.\n", stderr);
        } else {
            fputs("No input received.\n", stderr);
        }
        return 1;
    }

    if (strchr(input, '\n') == NULL) {
        if (!discard_remainder_of_line()) {
            fputs("Input error while discarding a long line.\n", stderr);
        } else {
            fputs("Input is too long.\n", stderr);
        }
        return 1;
    }

    if (!parse_lesson_count(input, &lesson_count)) {
        fputs("Enter one whole number from 1 to 100.\n", stderr);
        return 1;
    }

    if (lesson_count == 1) {
        if (printf("Plan: 1 lesson\n") < 0) {
            return 1;
        }
    } else {
        if (printf("Plan: %d lessons\n", lesson_count) < 0) {
            return 1;
        }
    }

    for (int day = 1; day <= lesson_count; ++day) {
        if (day % REVIEW_INTERVAL == 0) {
            ++checkpoint_count;
        }
    }

    if (printf("Review checkpoints: %d\n", checkpoint_count) < 0) {
        return 1;
    }

    if (lesson_count == 1) {
        if (fputs("Start with one focused session.\n", stdout) == EOF) {
            return 1;
        }
    } else {
        if (fputs("Make the plan in short, repeatable blocks.\n", stdout) == EOF) {
            return 1;
        }
    }

    return 0;
}
```

Compile and run the committed companion file exactly as follows:

```sh
cc -std=c17 -Wall -Wextra -Wpedantic \
  -o c-002-control-flow-and-simple-input \
  examples/c/c-002-control-flow-and-simple-input.c
printf '12\n' | ./c-002-control-flow-and-simple-input
```

Expected output:

```text
How many lessons (1-100)? Plan: 12 lessons
Review checkpoints: 2
Make the plan in short, repeatable blocks.
```

Try invalid input separately:

```sh
printf '12cats\n' | ./c-002-control-flow-and-simple-input
```

The program should exit with a nonzero status and write this diagnostic to standard error:

```text
Enter one whole number from 1 to 100.
```

## 7. Guided practice

Answer before reading the spoiler section.

1. What is the difference between `=` and `==`?
2. What value does `17 % 5` produce?
3. How many review checkpoints does the loop produce for `lesson_count == 20`?
4. Why does the program use `fgets` before `strtol` rather than converting directly into an `int`?
5. Which validation check rejects `12cats`?
6. Why is `value < MIN_LESSONS || value > MAX_LESSONS` checked before `(int)value`?
7. What happens when the input line is longer than the fixed input buffer?

## 8. Independent exercise

Create `exercise.c` beside the companion file. Use the same checked line-input and `strtol` pattern, but accept a number of practice sessions from `1` to `31`. Print `One session scheduled.` for `1`, otherwise print `N sessions scheduled.` Use a `for` loop to count every third session as a review session.

Your program must reject these inputs with a nonzero exit status:

```text
0
32
7days
<an empty line>
```

Test at least `1`, `3`, and `7` as valid inputs. Do not bypass validation by casting a raw `strtol` result directly to `int`.

## 9. Validate with sanitizers

Run the deterministic test in this repository:

```sh
./tests/c-002-control-flow-and-simple-input.sh
```

Then build with AddressSanitizer and UndefinedBehaviorSanitizer when supported:

```sh
cc -std=c17 -Wall -Wextra -Wpedantic \
  -fsanitize=address,undefined -fno-omit-frame-pointer -g \
  -o c-002-sanitized \
  examples/c/c-002-control-flow-and-simple-input.c
printf '12\n' | ./c-002-sanitized
printf '12cats\n' | ./c-002-sanitized
```

The valid input should produce the expected plan. The invalid input should produce the expected diagnostic and nonzero exit status, with no sanitizer report. Sanitizers are valuable checks, but they do not replace the explicit input validation in the source.

## 10. Final self-check

You are ready for the next lesson if you can answer each question without looking back.

1. Describe the three parts of a `for` statement.
2. Explain why a `for` loop with a visible bound is easier to review than an unstructured endless loop.
3. State the two different checks needed after `strtol` for “no digits” and “numeric value out of `long` range.”
4. Explain why the program checks the accepted range before narrowing from `long` to `int`.
5. Give one reason to reject a line that exceeds a fixed input buffer.
6. Write the formal Russian phrase for “Where are you from?” from Russian 002.

## Spoiler: answers and model solution

1. `=` assigns a value; `==` compares two values.
2. `17 % 5` is `2`.
3. `20` has review checkpoints on `5`, `10`, `15`, and `20`, so the result is `4`.
4. `fgets` gives the program a bounded, null-terminated line that it can inspect completely before conversion. It enables checks for extra text and a line that was too long for the buffer.
5. After `strtol`, the end pointer stops at the first character not belonging to the number. After trailing whitespace is skipped, `*end != '\0'` rejects `12cats`.
6. The accepted-range check makes sure the value fits the program’s requirements before it is narrowed to `int`. A successful `strtol` result can fit in `long` but still be unsuitable for the target type or application.
7. The companion program discards the remainder of that line, reports `Input is too long.`, and exits with failure. It does not treat the truncated prefix as a complete number.
8. From Russian 002: **Откуда вы?**

A model core for the independent exercise is:

```c
for (int session = 1; session <= session_count; ++session) {
    if (session % 3 == 0) {
        ++review_count;
    }
}
```

## Next lesson

The next scheduled lesson returns to Russian: **Russian 003 — grammatical gender, basic noun patterns, and familiar objects.**

## References

[1]: https://en.cppreference.com/w/c/io/fgets "fgets — cppreference.com"
[2]: https://en.cppreference.com/w/c/string/byte/strtol "strtol, strtoll — cppreference.com"
[3]: https://en.cppreference.com/w/c/language/for "for loop — cppreference.com"
