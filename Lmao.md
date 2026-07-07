```C
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <fcntl.h>
#include <sys/types.h>

int main() {
    int p[2];
    pipe(p);

    pid_t pid = fork();
    if (pid < 0) {
        perror("fork failed");
        return 1;
    }

    if (pid) {
        // Parent process
        close(p[1]); // Close unused write end
        char c;
        read(p[0], &c, 1);
        write(STDOUT_FILENO, &c, 1);
        close(p[0]);
    } else {
        // Child process
        int fd = open("foo", O_RDONLY);
        if (fd < 0) {
            perror("Error opening file foo");
            return 1;
        }

        size_t size = 1024 * 1024 * 1024; /* 1 GB */
        while (size > 0) {
            char c;
            read(fd, &c, 1);
            write(p[1], &c, 1);
            size--;
        }
        close(fd);
        close(p[1]);
    }

    return 0;
}
```