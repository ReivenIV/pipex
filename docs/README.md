# Understanding Key Concepts for Pipex in C


## 🏁 Conclusion

- `fork()` creates a child process.
- `pipe()` allows two processes to communicate.
- `stdin` and `stdout` handle input/output.
- `execve()` runs new programs.
- `env` contains system variables, including `PATH`, which helps find executables.
- `struct t_env` organizes environment variables.


## 1. `fork()`

### 📝 What is it?

`fork()` is a function that creates a new process by duplicating the current process.

### 🛠 How does it work?

When you call `fork()`, the operating system creates a **child process**, which is a copy of the **parent process**.

- The **child process** has a different Process ID (PID).
- Both processes run the same code after the `fork()`.
- `fork()` returns:
  - `0` in the **child process**
  - The PID of the child in the **parent process**
  - `-1` if an error occurs

### 💡 Analogy

Imagine a magician cloning themselves. The clone (child) looks exactly like the original (parent), but they are two separate people now.

### 📌 Code Example

```c
#include <stdio.h>
#include <unistd.h>

int main() {
    pid_t pid = fork();
    
    if (pid < 0) {
        perror("fork failed");
        return 1;
    }
    else if (pid == 0) {
        printf("This is the child process. PID: %d\n", getpid());
    }
    else {
        printf("This is the parent process. Child PID: %d\n", pid);
    }
    return 0;
}
```








---


## 2. A **pipe** (`pipe()`)

### 📝 What is it?

A pipe is a way for two processes to communicate. It creates a **one-way** data channel where one process writes, and the other reads.

### 🛠 How does it work?

- `pipe(int fds[2])` creates a pipe and fills `fds` with two file descriptors:
  - `fds[0]` → Read end
  - `fds[1]` → Write end
- One process writes to `fds[1]`, and the other reads from `fds[0]`.

### 💡 Analogy

Think of a pipe as a **straw**:

- One person pours liquid (writes data).
- The other person drinks (reads data).

### 📌 Code Example

```c
#include <stdio.h>
#include <unistd.h>
#include <string.h>

int main() {
    int fd[2];
    char buffer[50];
    pipe(fd);

    if (fork() == 0) {
        close(fd[0]); // Close read end in child
        write(fd[1], "Hello, parent!", 14);
        close(fd[1]);
    } else {
        close(fd[1]); // Close write end in parent
        read(fd[0], buffer, 14);
        printf("Parent received: %s\n", buffer);
        close(fd[0]);
    }
    return 0;
}
```





---


## 3. `stdin`, `stdout`

### 📝 What are they?

- `stdin` (Standard Input): Where input comes from (default: keyboard).
- `stdout` (Standard Output): Where output goes (default: terminal).

### 💡 Analogy

Think of `stdin` as a **microphone** and `stdout` as a **speaker**:

- What you say into the microphone (`stdin`) can be played on a speaker (`stdout`).

### 📌 Code Example

```c
#include <stdio.h>

int main() {
    char name[50];
    printf("Enter your name: ");
    scanf("%s", name);
    printf("Hello, %s!\n", name);
    return 0;
}
```




---


## 4. `execve()`

### 📝 What is it?

`execve()` replaces the current process with a new program. It runs an external program inside the current process.

### 🛠 How does it work?

- `pathname`: The path to the executable file.
- `argv`: The arguments (like `./ls -l` → `{"ls", "-l", NULL}`).
- `envp`: The environment variables (like `PATH`).

### 📌 Code Example

```c
#include <unistd.h>
#include <stdio.h>

int main() {
    char *args[] = {"/bin/ls", "-l", NULL};
    execve(args[0], args, NULL);
    perror("execve failed");
    return 1;
}
```

---

## 5. `env` in `main(int ac, char **av, char **env)`

### 📝 What is `env`?

- `env` is an array of **environment variables**.
- It contains system settings like `PATH`, `HOME`, etc.

### 📌 Example: Finding an Executable

The `PATH` variable stores directories where executables are found.

```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

int main(int argc, char **argv, char **envp) {
    while (*envp) {
        if (strncmp(*envp, "PATH=", 5) == 0)
            printf("%s\n", *envp);
        envp++;
    }
    return 0;
}
```

---

## 6. `struct t_env`

### 📝 What is it?

A structure to store environment variables in a structured way.

### 📌 Example Struct

```c
typedef struct s_env {
    char *key;
    char *value;
} t_env;
```

This can help manage environment variables easily.

### 💡 How is it used in Pipex?

In `pipex`, you need to extract `PATH`, find the right executable, and pass it to `execve()`.

```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

typedef struct s_env {
    char *key;
    char *value;
} t_env;

int main(int argc, char **argv, char **envp) {
    t_env env;
    while (*envp) {
        if (strncmp(*envp, "PATH=", 5) == 0) {
            env.key = "PATH";
            env.value = *envp + 5;
            printf("%s = %s\n", env.key, env.value);
        }
        envp++;
    }
    return 0;
}
```

---


These concepts are the foundation for `pipex`! 🚀

