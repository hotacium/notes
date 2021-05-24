manipulating_process_memo.md
# Listing and Manipulating Processes

## `ps` command
**process** is a runnning program. Each process on the system has a numeric **process ID** (PID).

For a quick listing of running processes, just run `ps` on the command line.

```
$ ps
    PID TTY          TIME CMD
 167968 pts/0    00:00:04 zsh
 181940 pts/0    00:00:00 ps
```
* PID: The process ID
* TTY: The terminal device where the process is running.
* STAT: The process status, that is, what the process is doing and where its memory resides. For example, `S` means sleeping and `R` means runnning.
* TIME: The amount of CPU time in minutes and seconds that the process has used so far
* COMMAND (CMD)


#### Command Options
* `ps x`: Show all of your running processes.
* `ps ax`: Show all processes on the system, not just the ones you own.
* `ps u`: Include more detailed information on processes.
* `ps w`: Show full command names, not just what fits on one line.

## `kill` command
To terminate a process, send it a **signal** with the `kill` command. A signal is a message to a process from the kernel. When you run `kill`, you're asking the kernel to send a signal to another process.
```
# terminate (TERM)
$ kill [pid]

# stop (STOP)
$ kill -STOP [pid]

# continue (CONT)
$ kill -CONT [pid]
```

Other signals give the process a chance to clean up after itself, but `KILL` does not.

## Job Control
Shells also support **job control**, which is a way to send `TSTP` (similar to `STOP`) and `CONT` signals to programs.  
For example:
* `TSTP` signal with CTRL-Z
* `fg` to start the process again in **foreground**
* `bg` to start the process again in **backgournd**

## Backgournd Processes
You can detach a process from the shell and put it in the "background" with the **ampersand** (`&`).  
e.g.
```
$ gunzip file.gz &
```
The shell should respond by printing the **PID** of the new background process so that you can continue working.

If a program tries to **read** something from the standard input when it's in the **background**, it can **freeze** (try `fg` to bring it back) or terminate.  
Also, if the program writes to the **standard output** or **standard error**, the output can appear in the terminal window with **no** regard for anything else running there. (-> Redirect stdout and stderr with `>` and `2>` (append: `>>`), if you want)







