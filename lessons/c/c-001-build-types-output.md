# C 001 — Build, Types, and Output

**Estimated study time:** 25–35 minutes
**Prerequisites:** None
**Companion program:** [`examples/c/c-001-build-types-output.c`](../../examples/c/c-001-build-types-output.c)
**Author:** Manus AI

## What you will be able to do

By the end of this lesson, you should be able to create a small hosted C program, explain the roles of `#include`, `main`, statements, variables, and `return`, distinguish several fundamental C types, compile with strict diagnostics, and produce predictable text with `printf`.

## Retrieval warm-up

The preceding lesson was Russian 001. Without looking, recall one formal greeting, one informal farewell, and the Cyrillic sound of **В**. A model answer is: **Здравствуйте**, **Пока**, and **В** is approximately the *v* sound. This brief recall protects the alternating curriculum from becoming two unrelated courses.

There is no earlier C lesson to review. Before continuing, say this sentence aloud: **a C source file is plain text; a compiler translates it into a program that the operating system can run.**

## 1. The smallest useful C program

A hosted C program begins execution in a function named `main`.[1] We will use `int main(void)`: `int` says that the function returns an integer status to its environment, while `void` states that this version accepts no parameters.

```c
#include <stdio.h>

int main(void)
{
    printf("Hello, C!\n");
    return 0;
}
```

Read the program from top to bottom.

| Part | Purpose |
|---|---|
| `#include <stdio.h>` | Makes the declaration of `printf` available before the call is compiled. |
| `int main(void)` | Defines the program entry function for this lesson. |
| `{` and `}` | Mark the function body. |
| `printf("Hello, C!\n");` | Calls the formatted-output function and ends the line with the newline escape `\n`. |
| `return 0;` | Reports successful completion by convention; zero indicates success in a hosted environment.[1] |

> **Rule:** In C, a declaration tells the compiler what an identifier means before it is used. Including `<stdio.h>` is not optional decoration: calling `printf` without its declaration can result in incorrect diagnostics or undefined behavior.

## 2. Build the program with strict diagnostics

Save the source as `hello.c`. In a terminal, compile it with:

```sh
cc -std=c17 -Wall -Wextra -Wpedantic -o hello hello.c
```

The flags mean:

| Flag | Purpose |
|---|---|
| `-std=c17` | Ask the compiler to use the C17 language standard mode. |
| `-Wall` | Enable a useful baseline of warnings. |
| `-Wextra` | Enable additional warnings beyond `-Wall`. |
| `-Wpedantic` | Request diagnostics for constructs outside the selected standard. |
| `-o hello` | Name the output program `hello`. |

Run it with:

```sh
./hello
```

Expected output:

```text
Hello, C!
```

Treat warnings as work to investigate, not as decorative terminal noise. A clean build does not prove a program is correct, but it catches many mistakes before the program runs.

## 3. Fundamental types: choose the kind of value first

A **type** tells C how a value is represented and which operations are meaningful. Do not memorize every numeric limit today. The exact range of several basic types can differ across platforms; later lessons will use `<limits.h>`, fixed-width types such as `uint8_t` and `uint32_t`, and explicit range checks when an exact width is necessary.

| Type | First use | Example |
|---|---|---|
| `char` | A single character or a small integer code unit | `char initial = 'C';` |
| `int` | A general whole-number count or status | `int lesson_number = 1;` |
| `double` | A floating-point approximation, often for measured quantities | `double minutes = 30.0;` |
| `void` | No value, or an explicitly empty parameter list in `main(void)` | `int main(void)` |

Use a suffix-free integer literal such as `1` with an `int` when it fits. Use a decimal point, as in `30.0`, when you intend a floating-point literal. Later, when values represent bytes, file sizes, or protocol fields, selecting the correct type becomes a safety requirement rather than a style preference.

## 4. Output with `printf`

`printf` is declared in `<stdio.h>` and writes formatted output to the standard output stream.[2] Its first argument is a **format string**. Text inside the format string is emitted as text, except that conversion specifications beginning with `%` tell `printf` how to print additional arguments.

| Value type in this lesson | Conversion specification | Example |
|---|---|---|
| `int` | `%d` | `printf("Lesson %d\n", lesson_number);` |
| `double` | `%f` or a precision-controlled form such as `%.0f` | `printf("%.0f minutes\n", minutes);` |
| `char` | `%c` | `printf("%c\n", initial);` |

`%.0f` means “print a floating-point value with zero digits after the decimal point.” The `f` specification expects a `double` argument. Matching each format specification to the argument type matters: an incorrect match makes the behavior undefined.[2]

For the moment, use **literal** format strings that you write yourself. Never pass untrusted text as the format argument. The later I/O and security lessons will explain format-string vulnerabilities and safer ways to print arbitrary text.

## 5. Worked example: a small study-plan report

The companion program prints three values using the types above.

```c
#include <stdio.h>

int main(void)
{
    const int lesson_number = 1;
    const double planned_minutes = 30.0;
    const char track_initial = 'C';

    if (printf("Track: %c\n", track_initial) < 0) {
        return 1;
    }

    if (printf("Lesson: %d\n", lesson_number) < 0) {
        return 1;
    }

    if (printf("Planned study time: %.0f minutes\n", planned_minutes) < 0) {
        return 1;
    }

    return 0;
}
```

The `const` qualifier means this program will not modify the three named objects after initialization. Each `printf` call is checked because formatted output can fail. In this small example, `return 1;` gives the environment a nonzero status when output fails. The normal path reaches `return 0;`.

Compile and run the committed companion file exactly as follows:

```sh
cc -std=c17 -Wall -Wextra -Wpedantic \
  -o c-001-build-types-output \
  examples/c/c-001-build-types-output.c
./c-001-build-types-output
```

Expected output:

```text
Track: C
Lesson: 1
Planned study time: 30 minutes
```

## 6. Guided practice

Answer before reading the spoiler section.

1. Which header must appear before a program calls `printf`?
2. What does the `void` in `int main(void)` communicate in this lesson?
3. Choose the best type for each value: a lesson count of `12`, an estimate of `12.5` minutes, and the single letter `'R'`.
4. Write the `printf` call that prints `Lesson: 12` followed by a newline, assuming `lesson_number` is an `int` containing `12`.
5. Identify the defect: `printf("Minutes: %d\n", planned_minutes);` when `planned_minutes` has type `double`.

## 7. Independent exercise

Create a new file named `exercise.c` beside the companion program. Write a program that defines these three `const` variables:

```c
const int study_day = 2;
const double review_minutes = 15.5;
const char language_initial = 'R';
```

Print exactly this output, with the value of `review_minutes` shown to one decimal place:

```text
Language: R
Study day: 2
Review: 15.5 minutes
```

Compile it using the strict command from section 2. Then deliberately change the `%c` that prints `language_initial` to `%d`, rebuild, and observe the difference. Restore `%c` afterward. The type may still be passed in a way that produces some output, but the intended representation is a character, so `%c` is the correct specification.

## 8. Validate with sanitizers

For a small program without dynamic memory, sanitizers are not a substitute for careful review. They are still a useful habit. If your compiler supports them, build the companion program with:

```sh
cc -std=c17 -Wall -Wextra -Wpedantic \
  -fsanitize=address,undefined -fno-omit-frame-pointer -g \
  -o c-001-sanitized \
  examples/c/c-001-build-types-output.c
./c-001-sanitized
```

Expected output is the same three lines shown above, with no sanitizer diagnostic. If the compiler reports that a sanitizer option is unavailable, record that limitation and continue with the strict non-sanitized build. Do not remove warnings simply to make a build appear clean.

## 9. Final self-check

You are ready for the next lesson if you can answer each question without looking back.

1. Why does a program using `printf` include `<stdio.h>`?
2. What command compiles a file called `hello.c` as C17 with the baseline strict warnings?
3. Which type would you choose for a whole-number lesson count: `int` or `double`?
4. Which conversion specification matches an `int` in this lesson?
5. What status does `return 0;` conventionally signal from `main`?
6. Why is `printf("%d\n", some_double);` incorrect?

## Spoiler: answers and model solution

1. `<stdio.h>` declares `printf` before the function is called.
2. `cc -std=c17 -Wall -Wextra -Wpedantic -o hello hello.c`
3. `int`.
4. `%d`.
5. Successful completion.
6. `%d` expects an `int`, while `some_double` is a `double`. The correct choice here is a floating-point conversion such as `%f` or `%.1f`.

A model line for guided-practice item 4 is:

```c
printf("Lesson: %d\n", lesson_number);
```

A model solution for the independent exercise is:

```c
#include <stdio.h>

int main(void)
{
    const int study_day = 2;
    const double review_minutes = 15.5;
    const char language_initial = 'R';

    printf("Language: %c\n", language_initial);
    printf("Study day: %d\n", study_day);
    printf("Review: %.1f minutes\n", review_minutes);
    return 0;
}
```

## Next lesson

The next scheduled lesson returns to Russian: **Russian 002 — high-value Cyrillic sound patterns, stress awareness, and fuller introductions.**

## References

[1]: https://en.cppreference.com/w/c/language/main_function "Main function — cppreference.com"
[2]: https://en.cppreference.com/w/c/io/fprintf "printf, fprintf, sprintf, snprintf — cppreference.com"
