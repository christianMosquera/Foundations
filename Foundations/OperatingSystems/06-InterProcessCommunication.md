# Inter-Process Communication (IPC)
Inter-Process Communication (IPC) allows multiple processes to communicate and share data with each other. Since processes are isolated from one another, IPC provides controlled methods for them to send messages, share memory, or signal each other.

## Why is IPC Important?
When processes need to work together, such as in client-server applications, multi-threaded programs, or privilege escalation exploits, IPC enables them to exchange data efficiently. IPC is also a critical aspect of reverse engineering and binary exploitation because many binaries interact with other processes or daemons (like network services).

## Pipes
Pipes are a simple way for a process to send data to another child process. Think of a one-way tunnel.

### <ins>How Pipes Work</ins>
- Data flows unidirectionally (one way only).
- They connect a parent process to its child.
- Processes read from the read end of the pipe and write to the write end.
### <ins>Example</ins>
```
#include <stdio.h>
#include <unistd.h>

int main() {
    int pipefd[2];
    char buffer[100];

    // Create a pipe
    pipe(pipefd);

    if (fork() == 0) { 
        // Child process
        close(pipefd[1]); // Close the write end
        read(pipefd[0], buffer, sizeof(buffer)); 
        printf("Child received: %s\n", buffer);
    } else {
        // Parent process
        close(pipefd[0]); // Close the read end
        write(pipefd[1], "Hello from parent", 17);
    }

    return 0;
}
```
### <ins>Explanation</ins>
- pipe(pipefd) creates a pipe. pipefd[0] is for reading, pipefd[1] is for writing.
- The parent writes to the pipe, and the child reads from it.

## Named Pipes (FIFOs)
Unlike anonymous pipes, named pipes (FIFOs) have names and exist as files in the filesystem. They allow communication between unrelated processes.

### <ins>How FIFOs Work</ins>
- Data flows unidirectionally (like regular pipes).
- Any process can access the FIFO file.
- FIFO files are created using mkfifo().
### <ins>Example</ins>
```
#include <stdio.h>
#include <fcntl.h>
#include <unistd.h>

int main() {
    char buffer[100];
    int fd;

    // Create a named pipe (FIFO)
    mkfifo("/tmp/my_fifo", 0666);

    if (fork() == 0) { 
        // Child process
        fd = open("/tmp/my_fifo", O_RDONLY); 
        read(fd, buffer, sizeof(buffer)); 
        printf("Child received: %s\n", buffer);
    } else {
        // Parent process
        fd = open("/tmp/my_fifo", O_WRONLY);
        write(fd, "Hello from parent", 17);
    }

    return 0;
}
```
### <ins>Explanation</ins>
- mkfifo("/tmp/my_fifo", 0666) creates a FIFO file.
- The parent writes to /tmp/my_fifo, and the child reads from it.
- The processes could be two unrelated processes. This is possible because the FIFO is stored as a file so all processes can open it with the open() call.


