# C 005 — Array Parameters, `size_t` Counts, and Bounded Traversal

**Estimated study time:** 25–35 minutes
**Prerequisites:** [C 001 — Build, Types, and Output](c-001-build-types-output.md), [C 002 — Expressions, Decisions, Loops, and Checked Input](c-002-control-flow-and-simple-input.md), [C 003 — Functions, Output Parameters, and Explicit Error Returns](c-003-functions-and-error-returns.md), and [C 004 — Fixed-Size Arrays, C Strings, and Index Bounds](c-004-arrays-strings-and-bounds.md)
**Companion program:** [`examples/c/c-005-array-parameters-and-bounded-iteration.c`](../../examples/c/c-005-array-parameters-and-bounded-iteration.c)
**Deterministic test:** [`tests/c-005-array-parameters-and-bounded-iteration.sh`](../../tests/c-005-array-parameters-and-bounded-iteration.sh)
**Author:** Manus AI

## What you will be able to do

By the end of this lesson, you should be able to pass an array to a function together with its explicit element count, explain why an array parameter is treated as a pointer, use `size_t` for element counts and forward indexes, and write a bounded loop that visits each supplied element exactly once without reading outside the array.

## Retrieval warm-up

Without opening earlier lessons, answer these questions.

1. What is the valid index range for an array with `N` elements?
2. Why does a visible C string need a terminating `\0`?
3. Why does C 004 compare an index with `strlen(label)` instead of the label array’s storage capacity?
4. In C 003, why does a helper return a status separately from an output value?
5. From Russian 005, complete: **я работаю**, **я читаю**, **я ___**.

The spoiler section contains model answers. Attempt recall before checking older lessons.

## 1. An array parameter is a pointer parameter

C arrays are contiguous sequences of elements.[1] When an array is used as a function argument, it converts to a pointer to its first element. In a function parameter list, these declarations describe the same parameter type:[1]

```c
static Status summarize(const int values[], size_t count, ...);
static Status summarize(const int *values, size_t count, ...);
```

The first spelling is useful because it signals that the pointer is expected to point at the first element of an integer array. The second spelling makes the underlying pointer type visible. Neither spelling transfers the array’s element count to the function.

> **Interface rule:** A pointer identifies where an array begins. It does not tell the function how many elements are valid. Pass the count explicitly and treat it as part of the function’s contract.

In the companion program, `main` owns a real array and can calculate its count. It passes both the pointer-like array argument and the count to each helper.

```c
const int session_minutes[] = {25, 30, 20, 45, 25};
const size_t session_count = sizeof session_minutes / sizeof session_minutes[0];

status = summarize_session_minutes(session_minutes, session_count,
                                   &total_minutes, &average_minutes);
```

The expression `sizeof session_minutes / sizeof session_minutes[0]` works here because `session_minutes` is still an actual array in `main`. It produces the element count, not a hard-coded guess.[3]

## 2. `sizeof` changes meaning after an array becomes a parameter

Inside this function, `values` is a pointer parameter, even though it is written with `[]`.

```c
static Status summarize_session_minutes(const int values[], size_t count,
                                        int *total_out, int *average_out)
{
    /* sizeof values is the size of a pointer here, not an array length. */
}
```

Using this inside the function is incorrect:

```c
size_t wrong_count = sizeof values / sizeof values[0];
```

It divides the size of a pointer by the size of an `int`. The result is unrelated to the caller’s actual array length and can vary by platform. C’s array-parameter adjustment is the reason the function must receive `count` explicitly.[1] [3]

The correct model is simple:

| Location | What the name represents | How to obtain the count |
|---|---|---|
| `main`, where the array is declared | The complete array object | `sizeof array / sizeof array[0]` |
| A helper parameter | A pointer to the first element | Receive an explicit `size_t count` parameter |

## 3. Use `size_t` for counts and forward indexes

`size_t` is the unsigned integer type returned by `sizeof`. It can represent the size of any theoretical object and is commonly used for array indexes and loop counts.[2]

```c
for (size_t index = 0; index < count; ++index) {
    /* values[index] is within the supplied range. */
}
```

This loop has a clear contract:

- It begins at the first valid index, `0`.
- It checks `index < count` before every element access.
- It increments after processing one element.
- It stops after the last valid index, `count - 1`.

Do not write a backward loop like `for (size_t index = count - 1; index >= 0; --index)`. Because `size_t` is unsigned, an index of `0` wraps to a very large value after decrementing. Reverse traversal is possible, but it needs a different carefully designed pattern. This lesson uses forward traversal because its bound is direct and easier to review.

## 4. The pointer identifies storage; the count establishes the boundary

For a non-null `values` pointer and a valid `count`, array indexing is a readable form of pointer access:

```c
int value = values[index];
/* Equivalent address expression: *(values + index) */
```

Use `values[index]` for ordinary traversal. It keeps the index and the bound visible together. Do not attempt to “discover” an integer array’s end by scanning memory. Unlike C strings, general `int` arrays do not have a built-in terminator. Their valid range comes from an explicit count.

The companion helper rejects these interface failures before traversal:

```c
if (values == NULL || count == 0 || count > (size_t)INT_MAX ||
    total_out == NULL || average_out == NULL) {
    return STATUS_INVALID_ARGUMENT;
}
```

A zero count is invalid for this small session-summary interface because an average would be undefined. The `count > INT_MAX` check makes the later conversion for integer division safe. These are application-specific preconditions, but every array-processing function needs clear rules for null pointers, counts, and any later arithmetic.

## 5. Validate each element and protect the accumulated total

A bounded index prevents an out-of-bounds read. It does not prove that every stored value is appropriate for the program. The example accepts session durations from `0` through `180` minutes and checks the sum before adding another value.

```c
for (size_t index = 0; index < count; ++index) {
    int value = values[index];

    if (value < 0 || value > MAX_SESSION_MINUTES) {
        return STATUS_INVALID_VALUE;
    }

    if (total > INT_MAX - value) {
        return STATUS_SUM_OVERFLOW;
    }

    total += value;
}
```

The subtraction is safe because `value` was first required to be nonnegative. The check proves that `total + value` fits in `int` before the addition occurs. Only after the entire traversal succeeds does the helper assign `*total_out` and `*average_out`. This keeps the output-parameter rule from C 003: callers should not use an output value when the status is not successful.

## 6. Worked example: report a bounded session array

The companion program holds a fixed array of session durations. `main` calculates the count while it still has an array object. The helpers receive the pointer-plus-count pair, traverse only the stated range, and report errors through `Status`.

```c
#include <limits.h>
#include <stddef.h>
#include <stdio.h>
#include <stdlib.h>

typedef enum {
    STATUS_OK = 0,
    STATUS_INVALID_ARGUMENT = 1,
    STATUS_INVALID_VALUE = 2,
    STATUS_SUM_OVERFLOW = 3,
    STATUS_OUTPUT_FAILURE = 4
} Status;

enum {
    MAX_SESSION_MINUTES = 180
};

static Status summarize_session_minutes(const int values[], size_t count,
                                        int *total_out, int *average_out)
{
    int total = 0;

    if (values == NULL || count == 0 || count > (size_t)INT_MAX ||
        total_out == NULL || average_out == NULL) {
        return STATUS_INVALID_ARGUMENT;
    }

    for (size_t index = 0; index < count; ++index) {
        int value = values[index];

        if (value < 0 || value > MAX_SESSION_MINUTES) {
            return STATUS_INVALID_VALUE;
        }

        if (total > INT_MAX - value) {
            return STATUS_SUM_OVERFLOW;
        }

        total += value;
    }

    *total_out = total;
    *average_out = total / (int)count;
    return STATUS_OK;
}

static Status write_session_report(const int values[], size_t count,
                                   int total, int average)
{
    if (values == NULL || count == 0) {
        return STATUS_INVALID_ARGUMENT;
    }

    if (printf("Session count: %zu\n", count) < 0) {
        return STATUS_OUTPUT_FAILURE;
    }

    for (size_t index = 0; index < count; ++index) {
        if (printf("Session %zu: %d minutes\n", index, values[index]) < 0) {
            return STATUS_OUTPUT_FAILURE;
        }
    }

    if (printf("Total minutes: %d\n", total) < 0 ||
        printf("Average whole minutes: %d\n", average) < 0) {
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

int main(void)
{
    const int session_minutes[] = {25, 30, 20, 45, 25};
    const size_t session_count =
        sizeof session_minutes / sizeof session_minutes[0];
    int total_minutes;
    int average_minutes;
    Status status;

    status = summarize_session_minutes(session_minutes, session_count,
                                       &total_minutes, &average_minutes);
    if (status == STATUS_INVALID_VALUE) {
        (void)write_error("Session minutes must be between 0 and 180.\n");
        return EXIT_FAILURE;
    }
    if (status == STATUS_SUM_OVERFLOW) {
        (void)write_error("Session total is too large.\n");
        return EXIT_FAILURE;
    }
    if (status != STATUS_OK) {
        (void)write_error("Cannot summarize the session array.\n");
        return EXIT_FAILURE;
    }

    status = write_session_report(session_minutes, session_count,
                                  total_minutes, average_minutes);
    if (status != STATUS_OK) {
        return EXIT_FAILURE;
    }

    return EXIT_SUCCESS;
}
```

Compile and run the committed companion file exactly as follows:

```sh
cc -std=c17 -Wall -Wextra -Wpedantic \
  -o c-005-array-parameters-and-bounded-iteration \
  examples/c/c-005-array-parameters-and-bounded-iteration.c
./c-005-array-parameters-and-bounded-iteration
```

Expected output:

```text
Session count: 5
Session 0: 25 minutes
Session 1: 30 minutes
Session 2: 20 minutes
Session 3: 45 minutes
Session 4: 25 minutes
Total minutes: 145
Average whole minutes: 29
```

## 7. Guided practice

Answer before reading the spoiler section.

1. Why must `summarize_session_minutes` receive `count` when it already receives `values[]`?
2. What is the valid index range when `count` is `5`?
3. Why does the loop condition use `index < count` rather than `index <= count`?
4. What does `const` mean in `const int values[]` for this helper?
5. Why does the program use `size_t` for `count` and `index`?
6. What happens if an element is `200` in this program’s session array?
7. Why does the program check `total > INT_MAX - value` before adding?
8. From Russian 005, say “I study in the evening.”

## 8. Independent exercise

Create `exercise.c` beside the companion program. Define a fixed array of five nonnegative review scores. Write a `summarize_scores` function that receives `const int scores[]`, a `size_t count`, and output parameters for the total and highest score.

Your function must reject a null pointer, a zero count, and any score outside the range `0` through `100`. Traverse with a forward loop that uses `size_t index` and `index < count`. Check the total before each addition so it cannot overflow. Calculate the count in `main` with `sizeof scores / sizeof scores[0]`, then pass it explicitly to the helper.

Write a second helper that prints each `scores[index]` value. Do not calculate an array length with `sizeof` inside either helper. Compile with strict flags, then test a valid five-score array and deliberately change one score to `101` to confirm the error path is exercised.

## 9. Validate with sanitizers

Run the repository’s deterministic test:

```sh
./tests/c-005-array-parameters-and-bounded-iteration.sh
```

Then build and run with AddressSanitizer and UndefinedBehaviorSanitizer when supported:

```sh
cc -std=c17 -Wall -Wextra -Wpedantic \
  -fsanitize=address,undefined -fno-omit-frame-pointer -g \
  -o c-005-sanitized \
  examples/c/c-005-array-parameters-and-bounded-iteration.c
./c-005-sanitized
```

The program should print the five bounded entries, the total, and the average with no sanitizer report. Sanitizers help find some memory and undefined-behavior errors during testing. They do not replace the explicit pointer checks, count checks, element validation, and loop bounds in the source.

## 10. Final self-check

You are ready for the next lesson if you can do all of the following without looking back.

1. Explain why `int values[]` and `int *values` describe the same ordinary function parameter type.
2. State why the called function cannot recover an array length with `sizeof values`.
3. Write the safe forward traversal condition for an array with `count` elements.
4. Explain why a general integer array needs an explicit count while a C string has a null terminator convention.
5. State why `size_t` fits array counts and forward indexes.
6. Explain why the helper assigns output parameters only after successful traversal.
7. Recall the Russian phrase **Вечером я учусь.**

## Spoiler: answers and model response

1. An array with `N` elements has valid indexes from `0` through `N - 1`.
2. A C string needs `\0` to mark where the visible string ends.
3. `strlen(label)` measures the logical visible-string length. Extra array capacity is not part of the input string.
4. A separate status makes success/failure unambiguous and prevents callers from treating an invalid output value as valid data.
5. **учусь**.
6. The parameter is adjusted to a pointer, and a pointer does not retain the caller’s element count. Pass `count` explicitly.
7. For `count == 5`, the valid indexes are `0`, `1`, `2`, `3`, and `4`.
8. `index <= count` permits `index == count`, which is one past the final element and therefore invalid.
9. `const` prevents this helper from modifying elements through the `values` parameter.
10. `size_t` is the type returned by `sizeof` and is designed to represent object sizes; it is appropriate for counts and forward indexes.
11. A value of `200` returns `STATUS_INVALID_VALUE`; `main` reports that session minutes must be between `0` and `180` and exits with failure.
12. The check proves that `total + value` fits in `int` before the addition occurs.
13. “I study in the evening” is **Вечером я учусь.**

A safe bounded traversal is:

```c
for (size_t index = 0; index < count; ++index) {
    use(values[index]);
}
```

## Next lesson

The next scheduled lesson returns to Russian: **Russian 006 — common places, `где?` questions, and basic location phrases.**

## References

[1]: https://en.cppreference.com/w/c/language/array "Array declaration — cppreference.com"
[2]: https://en.cppreference.com/w/c/types/size_t "size_t — cppreference.com"
[3]: https://en.cppreference.com/w/c/language/sizeof "sizeof operator — cppreference.com"
