# C 004 — Fixed-Size Arrays, C Strings, and Index Bounds

**Estimated study time:** 25–35 minutes
**Prerequisites:** [C 001 — Build, Types, and Output](c-001-build-types-output.md), [C 002 — Expressions, Decisions, Loops, and Checked Input](c-002-control-flow-and-simple-input.md), and [C 003 — Functions, Output Parameters, and Explicit Error Returns](c-003-functions-and-error-returns.md)
**Companion program:** [`examples/c/c-004-arrays-strings-and-bounds.c`](../../examples/c/c-004-arrays-strings-and-bounds.c)
**Deterministic test:** [`tests/c-004-arrays-strings-and-bounds.sh`](../../tests/c-004-arrays-strings-and-bounds.sh)
**Author:** Manus AI

## What you will be able to do

By the end of this lesson, you should be able to declare a fixed-size array, count its elements with `sizeof` while it is still an array, explain the difference between a general `char` array and a null-terminated C string, reserve space for `\0`, obtain a string length only after termination is known, and validate a user-supplied index before reading an array element.

## Retrieval warm-up

Without opening the earlier C lessons, answer these questions.

1. Why does C 002 place text into a bounded buffer before converting it with `strtol`?
2. What does `end == text` mean after a `strtol` call?
3. What does `&total_minutes` pass to the C 003 calculation function?
4. Why does C 003 check a status before using an output value?
5. From Russian 004, complete: **новый — новая — ___**.

The spoiler section has model answers. Attempt recall before reviewing the older lesson.

## 1. Arrays have a fixed number of elements

An array stores a fixed number of elements of the same type. Array indexes begin at `0`, so an array with `N` elements has valid indexes from `0` through `N - 1`.

```c
char label[17];
```

This reserves 17 `char` elements, numbered `label[0]` through `label[16]`. Accessing `label[17]`, or any negative conceptual index, is outside the array and has undefined behavior. C does not perform a runtime bounds check for ordinary array access; the program must establish the bound before indexing.

For a true array in the same scope, this expression calculates its element count:

```c
size_t capacity = sizeof label / sizeof label[0];
```

Because `label` has elements of type `char`, `sizeof label[0]` is `1`, so `sizeof label` is also its element count here. The division form is still a good general habit because it works for arrays of larger element types such as `int scores[10]`.

> **Array rule:** If an index comes from input or a calculation, prove `index < element_count` before using `array[index]`.

## 2. A C string is more than a `char` array

A `char` array is simply an array of character-sized objects. It becomes a **C string** only when it contains a null character, written `\0`, after its text. The first `\0` marks the end of the string.

```c
char word[] = "cat";
```

This initializer creates four elements:

| Index | Stored value |
|---|---|
| `0` | `'c'` |
| `1` | `'a'` |
| `2` | `'t'` |
| `3` | `\0` |

The visible word has three characters, but the array requires four elements. String functions such as `strlen` look for the first null character and report the number of characters before it.[2] Calling `strlen` on an array that is not known to be null-terminated is undefined behavior.[2]

A byte array is not automatically a C string. Later lessons will make this distinction crucial: a byte buffer may contain zero-valued bytes as ordinary data, while a C string treats the first zero byte as its end. Do not apply string functions to an arbitrary byte buffer.

## 3. Reserve a terminator and reject truncation

The companion program accepts a label of **1–15 visible characters**. It declares a 17-element input array:

```c
enum {
    MAX_LABEL_LENGTH = 15,
    LABEL_CAPACITY = MAX_LABEL_LENGTH + 2
};

char label[LABEL_CAPACITY];
```

Why 17 elements? A complete terminal-entered line may contain:

| Space purpose | Maximum count |
|---|---:|
| Visible label characters | 15 |
| Newline read by `fgets` | 1 |
| Null terminator written by `fgets` | 1 |
| Total array elements | 17 |

`fgets` reads at most `count - 1` characters and writes a null terminator after successful input.[1] The program passes `sizeof label` rather than manually repeating `17`. After a successful read, it verifies that a newline was present. If it was absent, the line did not fit the program’s accepted line format, so the program discards the remainder and reports failure rather than treating a truncated prefix as complete input.

```c
if (strchr(label, '\n') == NULL) {
    /* Discard the rest of the input line, then reject it. */
}

label[strcspn(label, "\n")] = '\0';
```

This replacement removes the newline only after the program has established that one was read. The array stays null-terminated, so `strlen(label)` is safe afterward.

Do not treat `strncpy` as a generic repair for oversized input. If its copy count is reached before the source terminator, `strncpy` does **not** append a null terminator.[3] This lesson rejects an oversized line at the input boundary instead of copying a shortened prefix and silently changing the user’s data.

## 4. `sizeof` and `strlen` answer different questions

These operations answer different questions and use different types.

| Expression | Meaning | Type |
|---|---|---|
| `sizeof label` | Storage capacity of this array in bytes | `size_t` |
| `sizeof label / sizeof label[0]` | Number of elements in this array | `size_t` |
| `strlen(label)` | Visible string length before the first `\0` | `size_t` |

For `char label[17]` containing `"cat"`, `sizeof label` is `17`, but `strlen(label)` is `3`. It would be incorrect to display all 17 positions as text; only the three characters before `\0` are part of the C string.

A common trap occurs when an array is passed to a function. In a parameter list such as `const char *label`, the function receives a pointer, not the original array object. It cannot use `sizeof label` to recover the caller’s array capacity. Pass the length or capacity explicitly when the function needs it.

## 5. Validate the logical bound, not only the allocation bound

The companion program calculates the length after safely reading a string:

```c
size_t length = strlen(label);
```

If the label is `"cat"`, the physical array has 17 elements, but valid character indexes are only `0`, `1`, and `2`. Index `3` holds the terminating `\0`; indexes after it are not part of the input string. Therefore the program must check against `length`, not only against the array capacity.

```c
if (index >= length) {
    return STATUS_INVALID_INDEX;
}

character = label[index];
```

The index has type `size_t`, an unsigned type used for sizes and counts. The parser rejects negative input before converting from `long` to `size_t`, then rejects any index that is greater than or equal to the actual string length. This order prevents an unsafe conversion and an out-of-bounds read.

## 6. Worked example: inspect one label character safely

The companion program reads a short label and an index. It prints the requested character only after it has established all of the following:

1. The label line fits the fixed input array and contains a newline.
2. The label is not empty after the newline is removed.
3. The index text converts completely to a nonnegative number.
4. The index is smaller than the label’s visible length.

```c
#include <ctype.h>
#include <errno.h>
#include <limits.h>
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

typedef enum {
    STATUS_OK = 0,
    STATUS_INVALID_ARGUMENT = 1,
    STATUS_INPUT_FAILURE = 2,
    STATUS_LINE_TOO_LONG = 3,
    STATUS_EMPTY_LABEL = 4,
    STATUS_INVALID_INDEX = 5,
    STATUS_OUTPUT_FAILURE = 6
} Status;

enum {
    MAX_LABEL_LENGTH = 15,
    LABEL_CAPACITY = MAX_LABEL_LENGTH + 2,
    INDEX_LINE_CAPACITY = 32
};

static int discard_remainder_of_line(void)
{
    int character;

    do {
        character = getchar();
    } while (character != '\n' && character != EOF);

    return character == EOF && ferror(stdin) ? 0 : 1;
}

static Status read_line(char *buffer, size_t capacity)
{
    if (buffer == NULL || capacity < 2 || capacity > INT_MAX) {
        return STATUS_INVALID_ARGUMENT;
    }

    if (fgets(buffer, (int)capacity, stdin) == NULL) {
        return STATUS_INPUT_FAILURE;
    }

    if (strchr(buffer, '\n') == NULL) {
        if (!discard_remainder_of_line()) {
            return STATUS_INPUT_FAILURE;
        }
        return STATUS_LINE_TOO_LONG;
    }

    buffer[strcspn(buffer, "\n")] = '\0';
    return STATUS_OK;
}

static Status parse_index(const char *text, size_t limit, size_t *result)
{
    char *end = NULL;
    long value;

    if (text == NULL || result == NULL || limit == 0 ||
        limit > (size_t)LONG_MAX) {
        return STATUS_INVALID_ARGUMENT;
    }

    errno = 0;
    value = strtol(text, &end, 10);

    if (end == text || errno == ERANGE || value < 0) {
        return STATUS_INVALID_INDEX;
    }

    while (isspace((unsigned char)*end)) {
        ++end;
    }

    if (*end != '\0' || value >= (long)limit) {
        return STATUS_INVALID_INDEX;
    }

    *result = (size_t)value;
    return STATUS_OK;
}

static Status write_indexed_character(const char *label, size_t length,
                                      size_t index)
{
    if (label == NULL || length == 0 || index >= length) {
        return STATUS_INVALID_ARGUMENT;
    }

    if (printf("Label: %s\n", label) < 0 ||
        printf("Stored characters: %zu\n", length) < 0 ||
        printf("Character at index %zu: %c\n", index, label[index]) < 0) {
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
    char label[LABEL_CAPACITY];
    char index_line[INDEX_LINE_CAPACITY];
    size_t label_length;
    size_t index;
    Status status;

    if (fputs("Label (1-15 characters): ", stdout) == EOF) {
        return EXIT_FAILURE;
    }

    status = read_line(label, sizeof label);
    if (status == STATUS_LINE_TOO_LONG) {
        (void)write_error("Label is too long. Use at most 15 characters.\n");
        return EXIT_FAILURE;
    }
    if (status != STATUS_OK) {
        (void)write_error("Could not read the label.\n");
        return EXIT_FAILURE;
    }

    label_length = strlen(label);
    if (label_length == 0) {
        (void)write_error("Label must not be empty.\n");
        return EXIT_FAILURE;
    }

    if (printf("Index (0-%zu): ", label_length - 1) < 0) {
        return EXIT_FAILURE;
    }

    status = read_line(index_line, sizeof index_line);
    if (status != STATUS_OK) {
        (void)write_error("Could not read the index.\n");
        return EXIT_FAILURE;
    }

    status = parse_index(index_line, label_length, &index);
    if (status != STATUS_OK) {
        (void)write_error("Index must select an existing label character.\n");
        return EXIT_FAILURE;
    }

    status = write_indexed_character(label, label_length, index);
    if (status != STATUS_OK) {
        return EXIT_FAILURE;
    }

    return EXIT_SUCCESS;
}
```

Compile and run the committed companion file exactly as follows:

```sh
cc -std=c17 -Wall -Wextra -Wpedantic \
  -o c-004-arrays-strings-and-bounds \
  examples/c/c-004-arrays-strings-and-bounds.c
printf 'cat\n1\n' | ./c-004-arrays-strings-and-bounds
```

Expected output:

```text
Label (1-15 characters): Index (0-2): Label: cat
Stored characters: 3
Character at index 1: a
```

Try an out-of-range index:

```sh
printf 'cat\n3\n' | ./c-004-arrays-strings-and-bounds
```

The program should exit with failure and write this diagnostic to standard error:

```text
Index must select an existing label character.
```

## 7. Guided practice

Answer before reading the spoiler section.

1. For `char word[] = "cat";`, how many array elements exist and why?
2. What is the largest valid index for `char scores[10];`?
3. Why is `strlen` unsafe on a `char` array that might not contain `\0`?
4. For `char label[17]` containing `"cat"`, what are `sizeof label` and `strlen(label)`?
5. Why does the example reject index `3` for the label `cat` even though the array capacity is larger than 3?
6. Why does `read_line` reject a line when `strchr(buffer, '\n')` finds no newline?
7. Why should a function receive a string length or capacity explicitly rather than calculate `sizeof` on a pointer parameter?

## 8. Independent exercise

Create `exercise.c` beside the companion program. Declare a fixed-size character array that accepts a one-word course tag of at most 12 visible characters. Reuse the checked line-reading pattern from this lesson and print the tag’s first and last characters.

Your program must:

1. Reserve storage for the newline and `\0` as well as the visible tag.
2. Reject an empty tag and an overlong line instead of silently truncating it.
3. Call `strlen` only after successful input has established null termination.
4. Reject any requested index for which `index >= strlen(tag)`.
5. Use `size_t` for the capacity, length, and index variables.

Test a one-character tag, a 12-character tag, an empty line, a 13-character tag, and an index equal to the string length.

## 9. Validate with sanitizers

Run the repository’s deterministic test:

```sh
./tests/c-004-arrays-strings-and-bounds.sh
```

Then build and run with AddressSanitizer and UndefinedBehaviorSanitizer when supported:

```sh
cc -std=c17 -Wall -Wextra -Wpedantic \
  -fsanitize=address,undefined -fno-omit-frame-pointer -g \
  -o c-004-sanitized \
  examples/c/c-004-arrays-strings-and-bounds.c
printf 'cat\n1\n' | ./c-004-sanitized
printf 'cat\n3\n' | ./c-004-sanitized
```

The valid input should print the indexed character. The invalid input should return nonzero with the expected diagnostic and no sanitizer report. Sanitizers can reveal some memory mistakes during testing, but they do not replace the explicit termination and bounds checks in the source.

## 10. Final self-check

You are ready for the next lesson if you can do all of the following without looking back.

1. State the valid index range for an array with `N` elements.
2. Explain the difference between a `char` array and a C string.
3. Explain why a visible 15-character line needs additional array space for newline input and `\0`.
4. State the difference between `sizeof label` and `strlen(label)`.
5. Explain why index validation uses `index < label_length` instead of `index < sizeof label`.
6. Recall the Russian neuter adjective form in **новое окно**.

## Spoiler: answers and model response

1. C 002 uses a bounded buffer so the program can inspect complete input before conversion and can reject an overlong or malformed line.
2. `end == text` means `strtol` converted no digits.
3. `&total_minutes` passes the address of the caller’s `int` storage.
4. A non-success status means the output might not be valid; the caller must stop or handle the failure before using it.
5. **новое**.
6. `char word[] = "cat";` has four elements: three visible characters and one terminating `\0`.
7. The largest valid index in `char scores[10];` is `9`.
8. `strlen` searches for a null terminator; if no terminator exists within the real array, it reads beyond the array and behavior is undefined.
9. For a 17-element array holding `"cat"`, `sizeof label` is `17` and `strlen(label)` is `3`.
10. Index `3` is the null terminator, not a visible label character. A larger index is not part of the logical string either.
11. If no newline was read, the program cannot safely treat the buffer as a complete accepted line; it rejects the input after consuming the rest of that line.
12. In a function parameter, an array has decayed to a pointer, so `sizeof` describes the pointer rather than the caller’s original array.
13. **новое окно** means “new window.”

## Next lesson

The next scheduled lesson returns to Russian: **Russian 005 — high-frequency present-tense verbs and short daily-routine statements.**

## References

[1]: https://en.cppreference.com/w/c/io/fgets "fgets — cppreference.com"
[2]: https://en.cppreference.com/w/c/string/byte/strlen "strlen, strnlen_s — cppreference.com"
[3]: https://en.cppreference.com/w/c/string/byte/strncpy "strncpy, strncpy_s — cppreference.com"
