# get_next_line

A C function that reads a file line by line, one call at a time, from any file descriptor — including standard input, regular files, and pipes.

## About

`get_next_line` returns a single line (including the trailing `\n`, except possibly on the last line of the file) each time it's called, remembering its position between calls via static/persistent state. This avoids reading the whole file into memory at once and works efficiently regardless of file size.

The **bonus** version extends this to handle **multiple file descriptors at the same time**, each maintaining its own independent read state, using a single static variable.

## How It Works

1. Data is read from the file descriptor in chunks of `BUFFER_SIZE` bytes at a time.
2. Chunks are appended to a persistent buffer until a newline is found or the end of the file is reached.
3. The line up to (and including) the newline is extracted and returned.
4. Any leftover data is kept for the next call.

`BUFFER_SIZE` is configurable at compile time and defaults to `3` if not defined, which is useful for stress-testing edge cases with a small buffer.

## Project Structure

```
.
├── get_next_line.h          # Mandatory version header
├── get_next_line_bonus.h    # Bonus version header (multiple fd support)
├── get_next_line.c          # Mandatory implementation
├── get_next_line_bonus.c    # Bonus implementation
├── get_next_line_utils.c    # Helper functions (mandatory)
└── get_next_line_utils_bonus.c
```

## Functions

| Function       | Description                                             |
|----------------|-----------------------------------------------------------|
| `get_next_line`| Returns the next line from the given file descriptor      |
| `read_file`    | Reads from the fd into the working buffer until a newline or EOF |
| `get_my_line`  | Extracts the current line from the buffer                 |
| `next_line`    | Preserves the remainder of the buffer after the current line |
| `new_line`     | Checks a string for a newline character                   |
| `ft_strlen`    | Returns the length of a string                             |
| `ft_strjoin`   | Concatenates two strings into a newly allocated string     |
| `ft_substr`    | Returns a newly allocated substring                         |

## Building & Usage

Compile with a custom `BUFFER_SIZE` if desired:

```bash
cc -Wall -Wextra -Werror -D BUFFER_SIZE=42 get_next_line.c get_next_line_utils.c main.c -o gnl
```

Example usage:

```c
#include "get_next_line.h"

int main(void)
{
    int fd;
    char *line;

    fd = open("file.txt", O_RDONLY);
    while ((line = get_next_line(fd)) != NULL)
    {
        printf("%s", line);
        free(line);
    }
    close(fd);
    return (0);
}
```

For the bonus version (multiple file descriptors), include `get_next_line_bonus.h` and compile the `_bonus.c` files instead — `get_next_line` can then be called with different `fd`s interchangeably without losing track of each one's position.

## Notes

- This is a learning project (42 School curriculum) focused on static variables, memory management, and buffered I/O in C.
- Returns `NULL` when the file has been fully read or on error.

## License

No license specified.
