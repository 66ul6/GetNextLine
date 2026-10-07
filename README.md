*This activity has been created as part of the 42 curriculum by <your_login>.*

# Get Next Line (GNL)

## Description
The `get_next_line` project is an implementation of a sequential file reader written in [C](https://en.wikipedia.org/wiki/C_(programming_language)) that reads from a file descriptor and returns one complete line at a time. The project explores static variables, low-level file I/O operations, and dynamic memory allocation. The entire codebase is written for the [42 Network](https://42.fr/) curriculum and strictly complies with the [Norminette](https://github.com/42School/norminette) coding standard.

## Instructions
To integrate and compile `get_next_line` with your program:

1. Include the header in your C source file:
    #include "get_next_line.h"

2. Compile your source files with the `BUFFER_SIZE` macro defined:
    cc -Wall -Wextra -Werror -D BUFFER_SIZE=42 get_next_line.c get_next_line_utils.c main.c

3. Execution:
    Repeatedly call `get_next_line(fd)` in a loop to retrieve each line until the function returns `NULL` upon reaching EOF or encountering an error.

## Resources
* **Programming Language**: [C (Programming Language) - Wikipedia](https://en.wikipedia.org/wiki/C_(programming_language))
* **Coding Standard**: [42 Norminette Repository](https://github.com/42School/norminette)
* **System Documentation**: Standard man pages for `read()`, `malloc()`, and `free()`.
* **AI Usage**: Artificial Intelligence (Google Gemini) was used as an interactive technical assistant for logic tracing, buffer boundary management, and code formatting to conform to the 42 Norminette standard. All logic and code assembly were verified and written manually.

## Algorithm Explanation and Justification
Because the `read()` system call retrieves fixed-size byte chunks according to `BUFFER_SIZE`, reading operations often pull data past the first encountered newline (`\n`). Since standard file descriptors cannot be rewound, unread data must be stored persistently across successive function calls.

### Algorithm Stages:
1. **Data Accumulation**: Reads raw chunks into a temporary buffer and appends them to a static string pointer. The loop terminates when a newline character is found in the static buffer or when `read()` returns `0` (EOF).
2. **Line Extraction**: Scans the static string up to and including the newline character, allocates the exact required memory, and copies that slice into a return string.
3. **Leftover Preservation**: Extracts all characters following the newline character into a newly allocated string, frees the previous static buffer to prevent memory leaks, and stores the leftover slice in the static pointer for the next call.

### Justification:
This three-stage modular approach decouples file reading, string parsing, and memory cleanup. It guarantees strict memory safety, handles variable buffer sizes (from 1 byte to several megabytes) without buffer overflow, and ensures minimal memory footprint by only caching unread leftovers between calls.
