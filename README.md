*This activity has been created as part of the 42 curriculum by kmaghair.*

<div align="center">

# 📑 GET_NEXT_LINE | Reading a Line from a FD

![Language](https://img.shields.io/badge/Language-C-00599C?logo=c&logoColor=white)
![Norminette](https://img.shields.io/badge/Norminette-OK-success)
![Unit Tests](https://img.shields.io/badge/Unit_Tests-PASSING-brightgreen)
![Score](https://img.shields.io/badge/Score-125%2F100-brightgreen)

</div>

---

## 📌 Description
The `get_next_line` project is a function written in [C](https://en.wikipedia.org/wiki/C_(programming_language)) that reads a file descriptor and returns one complete line at a time until reaching the End of File (EOF)[cite: 2]. 

This project explores the mechanics of static variables, low-level I/O operations using system calls, and strict dynamic memory allocation (`malloc` and `free`)[cite: 2]. Developed as part of the [42 Network](https://42.fr/) curriculum, the entire codebase strictly conforms to the [Norminette](https://github.com/42School/norminette) coding standard.

---

## 🛠️ Instructions

### 1. Integration
Include the header file in your C source files:
```c
#include "get_next_line.h"
```

### 2. Compilation
Compile `get_next_line` alongside your source files and define the `BUFFER_SIZE` macro flag (e.g., `42`)[cite: 1]:
```bash
cc -Wall -Wextra -Werror -D BUFFER_SIZE=42 get_next_line.c get_next_line_utils.c main.c -o gnl
```

### 3. Execution
Call `get_next_line(fd)` repeatedly in a loop until it returns `NULL` (indicating EOF or an error occurred)[cite: 2]:
```c
int     fd = open("test.txt", O_RDONLY);
char    *line;

while ((line = get_next_line(fd)) != NULL)
{
    printf("%s", line);
    free(line);
}
close(fd);
```

---

## 📚 Resources
* **Language & Standard**: [C Programming Language](https://en.wikipedia.org/wiki/C_(programming_language)) | [42 Norminette Standard](https://github.com/42School/norminette)
* **System Calls & Docs**: `read(2)`, `malloc(3)`, and `free(3)` standard Unix man pages[cite: 2].
* **AI Usage**: 
  * **Brainstorming & Planning**: Used Google Gemini to map out buffer boundaries and understand static variable persistence[cite: 2].
  * **Debugging & Memory Leaks**: Used AI to analyze edge cases (like `BUFFER_SIZE=1`, empty files, and lines without terminating `\n`)[cite: 1, 2].
  * **Norminette Refactoring**: Assisted in restructuring the helper functions into modular 25-line blocks while avoiding forbidden functions[cite: 1].
  * *All final logic, tests, and function implementations were verified and assembled manually.*

---

## 🧠 Algorithm Explanation & Justification

Because the `read()` system call fetches arbitrary chunk sizes determined by `BUFFER_SIZE`, reading operations frequently capture bytes belonging to the next line[cite: 1]. Since file descriptors cannot be rewound using `lseek` (which is strictly forbidden in this project), excess characters must be preserved between successive function calls[cite: 1].

### The 3-Stage Pipeline:
1. **Data Accumulation (`helper`)**:
   Reads chunks from the file descriptor into a temporary buffer of size `BUFFER_SIZE + 1` and appends them to a `static char *` pointer[cite: 1, 2]. The loop halts immediately once a newline (`\n`) is detected in the accumulated buffer or when `read()` returns `0` (EOF).
2. **Line Extraction (`extract_line`)**:
   Measures the exact distance to the first newline character, dynamically allocates memory, and extracts only the current line (including the terminating `\n`) to return to the caller[cite: 2].
3. **State Cleanup (`update_static`)**:
   Calculates the leftover characters remaining *after* the extracted newline, moves them to a newly allocated string, frees the previous static buffer to prevent memory leaks, and points the static variable to the new leftovers for the next call.

### Justification:
* **Separation of Concerns**: Splitting reading, extraction, and cleanup keeps every function clean, memory-safe, and well under the 25-line limit imposed by Norminette.
* **Dynamic Resilience**: Handles edge cases seamlessly, from single-byte buffers (`BUFFER_SIZE=1`) to massive reads (`BUFFER_SIZE=10000000`), without memory corruption or buffer overflows[cite: 1].
* **Minimal Memory Overhead**: Caches only unread leftover characters in memory between function calls instead of loading unnecessary file data[cite: 1].
