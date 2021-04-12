

## Cプログラムから実行バイナリ

```
// GCC compilation system

+ source program (*.c, text)
|
1. pre-processor
|
+ modified source program (*.i, text)
|
2. compiler
|
+ assembly program (*.s, text)
|
3. assembler
|
+ relocatable object programs (*.o, binary)
|
4. linker
|
executable object program (binary)

```

- *Preprocessing phase*. The preprocessor modifies the orignal C program according to directives that begin with `#` character. For example, the `#include <stdio.h>` command tells the preprocessor to **read the content of the system header file `stdio.h` and insert it directly into the program text**. 

- *Compilation phase*. The compiler traslates the text file `*.i`  into the text file `*.s`, which contains an *assembly-language program*. Each statement in an assemly-language program exactly describes one low-level machine-language instruction in a standard text form. Assembly language is useful because it provides a **common** output language for **different** compilers for **different** high-level languages. 

- *Assembly phase*. Next, the assembler translates `*.s` file into machine-language instructions, packages them in a form known as a *relocatable object program*, and stores the result in the object file `*.o`. The `*.o` file is a binary file whose bytes encode machine language instructions rather than characters.

- *Linking phase*. The linker merge `*.o` file and functions of *standard C library* provided by every C compiler.


## プログラムの実行
#### Shell 
If the first word of the commnad line does not corrspond to a built-in shell command, a shell assumes that it is the name of an executable file. The shell loads and runs a program and then **waits** for it to terminate. When the program terminates, the shell then prints a prompt and waits for the next input commnad line.

#### Hardware Organization of a System
```
┌────────────────────────────────┐
│ CPU                            │                       ┌───────────────┐
│        ┌───────────┐           │          memory bus   │               │
│        │ register  │   ┌─────┐ │          ┌────────────┤  main memory  │
│ ┌────┐ │           └───►     │ │          │            │               │
│ │ PC │ │           ◄───┐ ALU │ │          │ ┌──────────►               │
│ └────┘ └─▲┌────────┘   │     │ │          │ │          │               │
│          ││            └─────┘ │    ┌─────▼─┴───────┐  │               │
│  ┌───────┘▼─────────┐  system bus   │               │  └───────────────┘
│  │ Memory Interface └───────────────► I/O bridge    │
│  │                  ◄──────────┼────┐               │
│  └──────────────────┘          │    │               │
│                                │    │               │
└────────────────────────────────┘    └─────┬──┬──────┘
                                            │  │
────────────────────────────────────────────┴──┴───────────────────────────

          I/O bus

────────────────┬──┬─────────────────────┬───┬──────────────────┬──┬───────
                │  │                     │   │                  │  │
           ┌────┴──┴───────────┐   ┌─────┴───┴───────┐   ┌──────┴──┴─────┐
           │  USB controller   │   │  graphics       │   │ disk          │
           │                   │   │                 │   │               │
           │                   │   │   adapter       │   │   controller  │
           └──▲─────────▲──────┘   └─────────────────┘   └───────────────┘
              │         │               ▼                    ▼    ▼
            mouse   keyboard          display               HDD  SSD
```
[drawn by asciiflow](https://asciiflow.com)


#### Buses
Running throught the system is a collection of electrical conduits called *buses* that **carry bytes of information and forth between the components**. Buses are typically designed to transfer fixed-sized chunks of bytes known as *words*. The number of bytes in a word (the *word size*) is a fundamental sstem parameter that varies across systems.
