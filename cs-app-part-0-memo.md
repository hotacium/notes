

## Cプログラムから実行バイナリ

```
// GCC compilation system

 source program (*.c, text)
 │
 ▼
┌───────────────┐
│ pre-processor │
└┬──────────────┘
 │ modified source program (*.i, text)
 ▼
┌───────────────┐
│   compiler    │
└┬──────────────┘
 │ assembly program (*.x, text)
 ▼
┌───────────────┐
│   assembler   │
└┬──────────────┘
 │ relocatable object program (*.o, binary)
 ▼
┌───────────────┐
│    linker     │
└┬──────────────┘
 ▼
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
Running throught the system is a collection of electrical conduits called *buses* that **carry bytes of information and forth between the components**. Buses are typically designed to transfer fixed-sized chunks of bytes known as *words*. The number of bytes in a word (the *word size*) is a fundamental system parameter that **varies across systems**. 

#### I/O devices

Input/output (I/O) devices are the system's connection to the external world (e.g. a keyboard and mouse for user input, a display for user output, and a disk drive for long-term storage of data). Each I/O device is connected to the I/O bus by either a *controller* or an *adapter*. The distinction between the two is mainly one of packaging. Controllers are **chip sets** in the **device itself** or on **the system's main printed curcuit board** (often called the *motherboard*). An adapter is a **card** that plugs into a slot on the mother board. Regardless, the purpose of each is to **transfer information** back and forth between the I/O bus and an I/O device.

#### Main memory

The *main memory* is a **temporary storage device** that holds both a program and the data it manipulates while **the processor is executing the program**.  Phisically, main memory consists of a collection of *Dynamic Random Access Memory (DRAM)* chips. Logically, memory is organized as a **liner array of bytes**, each with its own unique address (array index) starting at zero. In general, each of the machine instructions that constitute a program can consist of a varable number of bytes. The sizes of data items that correspond to C program variables vary according to type. 

#### Processor

The *central processing unit (CPU)*, or simply *processor*, is the engine that interprets (or *executes*) instructions stored in main memory. At its core is a word-sized storage device (or register) called the *program counter* (PC). At any point in time, the PC points at (contains the address of) some machine-language instruction in main memory. 

CPU はメインメモリに保管された命令を解釈 (実行) するエンジン. その中心には word-sized なストレージがあり、プログラムカウンタ (PC) と呼ばれる. どんなときでも PC はメインメモリの機械語命令を指している (ポインタを保持). 

From the time that power is applied to the system, until the time that the power is shut off, the processor blindly and repeatedly performs the same basic task, over and over and over: It **reads the instruction** from memory pointed at by the program counter (PC), **interprets the bits** in the instruction, **performs some simple *operation*** dictated by the instruction, and then **updates the PC** to point to the *next* instruction, which may or may not be contiguous in memory to the instrction that was just executed.

1. PC が指すメモリから命令を読み出す
2. 命令を解釈する
3. 命令を実行する
4. PC を次の命令を指すように更新する

There are only a few of these simple operations, and they revolve around main memory, the *register file*, and the *arithmetic/logic unit* (ALU). The register file is a small storage device that **consists of a collection of word-sized registers**, each with its own unique name. The ALU computes new data and address values. Here are some examples of the simple operations that the CPU might carry out at the requerst of an instruction:  
- *Load*: Copy a byte or a word from main memory into a register, overwriting the previous contents of the register.
- *Store*: Copy a byte or a word from a register to a location in main memory, overwriting the previous contents of that location.
- *Update*: Copy The contents of two registers to the ALU, which adds the two words together and stores the result in a register, overwriting the previous contents of that register.
- *I/O Road*: Copy a byte or a word from an I/O device into a register.
- *I/O Write*: Copy a byte or a word from a register to an I/O device.
- *Jump*: Extract a word from the instruction itself and copy that word into the program counter (PC), overwriting the previous value of the PC.

## `hello` プログラムを実行する

```c
#include <stdio.h>

int main() {
	printf("hello, world\n");
}

```


Initially, the shell program is executing its instructions, waiting for us to type a command. As we type the characters `hello` at the keyboard, the shell program reads each one into a register, and then stores it in mamory,.

```
// from keyboard to main memory

keyboard -> I/O bus -> I/O bridge -> Memory Interface -> register -> memory interface -> I/O bridge -> memory bus -> main memory
```

When we hit the `enter` key on the keyboard, the shell knows that we have finished typing the command. The shell then loads the executable `hello` file by execting a sequence of instructions that copies the code and data in the `hello` object file from disk to main memory. The data include the string of characters `"hello, world\n"` that will eventually be printed out.

(Using a technique known as *direct memory access* (DMA), the data travels directly from disk to main memory, without passing through the processor.)

```
disk -> disk controller -> I/O bus -> I/O bridge -> memory bus -> main memory
```

Once the code and data in the `hello` object file are loaded into memory, the processor begins executing the machine-language instructions in the `hello` program's `main` routine. These instruction copy the bytes in the `"hello, world\n"` string from memory to the register file, and from there to the display device, where they are displayed on the screen. 

# キャッシュの話 Caches Matter

An important lesson from this simple example is that a system **spends a lot time moving information from one place to another**. The machine instructions in the `hello` program are originally stored on disk. When the program is loaded, they are **copied to main memory**. When the processor is loaded, they are **copied to main memory**. When the processor runs the programs, they are **copied from main memory into the processor**. Similarly, the data string `"hello, world\n"`, originally on disk, is **copied to main memory**, and then **copied from main memory to the disk device**. From a programmer's perspective, much of this copying is **overhead that slows down the "real work" of the program**. Thus, a major goal for system designers is make these copy operations run as fast as possible. 

プログラマにとってみれば、大量のコピーはプログラムの「本当の作業」を遅くするオーバーヘッドである.

Because of physical laws, larger storage devices are slower than smaller storage devices. And faster devices ar emove expensive to build than their slower counterparts. For example, the disk drive on a typical system might be 100 times larger than the main memory, but it might take the processor 10,000,000 times longer to read a word from disk than from memory. 

例えば、ディスクはメインメモリよりも 100 倍大きく、word をディスクからプロセッサに読み込むのは メインメモリからそうするよりも 10M 倍長い時間がかかるようなことがある.

Similarly, a typical register file stores only a few hundred of bytes of information, as opposed to millions of bytes in the main memory. However, the processor can read data from the register file almost 100 times faster than from memory. Even more toublesome, as semiconductor technology progresses over the years, this *processor-memory gap* countinues to increase. It is easier and cheaper to make processors run faster than it is to make main memory run faster.

レジスタはメインメモリよりも 100 倍速くプロセッサへの読み込みが可能. プロセッサとメモリ速度のギャップは拡大し続けており、メモリの速度向上よりもプロセッサの速度向上のほうが簡単で安い.

To deal with the processor-memory gap, system designers include smaller faster storage devices called *caches* that serve as termporary staging ares for information that the processor is likely to need in the near future.

キャッシュ: プロセッサが近い将来必要とするであろう情報のための一時的なステージングエリア. メモリよりも小さく高速 -> メモリ速度のボトルネックを改善.

```
               ┌───────────────────────────────────────┐
               │ CPU                                   │
               │                ┌──────────┐           │
               │ ┌──────────┐   │          │   ┌─────┐ │
               │ │          │   │ register ├───┤     │ │
               │ │ L1 cache │   │          │   │ ALU │ │
               │ │          │   │ file     ├───┤     │ │
               │ └─┬──┬─────┘   │          │   └─────┘ │
               │   │  │         └─┬──┬─────┘           │             memory bus
  cache bus ─┐ │   │  │           │  │                 │                    │
             │ │   │  │           │  │                 │                    │
┌──────────┐ ▼ │ ┌─┴──┴───────────┴──┴─────┐           │  ┌───────────────┐ ▼  ┌─────────────┐
│          ├───┴─┤                         ├───────────┴──┤               ├────┤             │
│ L2 cache │     │    memory interface     │ system bus   │ memory bridge │    │ main memory │
│          ├───┬─┤                         ├───────────┬──┤               ├────┤             │
└──────────┘   │ └─────────────────────────┘           │  └───────────────┘    └─────────────┘
               │                                       │
               └───────────────────────────────────────┘
```

An *L1 cache* on the processor chip holds tens of thousands of bytes and can be accessed nearly as fast as the register file. A larger *L2 cache* with hundreds of thousands to millions of bytes is connected to the processor by a special bus It might take 5 times longer for the process to access the L2 cache than the L1 cache, but this still 5 to 10 times faster than accessing the main memory. The L1 and L2 caches are implemented with a hardware technology known as *Static Random Access Memory* (SRAM).

- L1 キャッシュ: 10K~ bytes の容量. レジスタとほぼ同速でのアクセス
- L2 キャッシュ: 100K~1M~ bytes の容量. L1 キャッシュよりも 5 倍程度遅いが、メモリアクセスよりも 5-10 倍高速.

L1, L2 キャッシュは SRAM (Static Random Access Memory) で実装される.


#### 脱線: 使用 CPU のキャッシュ
参考: 使用中の CPU (AMD Ryzen 5 3400G) のカタログスペック ([here](https://www.amd.com/en/products/apu/amd-ryzen-5-3400g))
- Total L1 Cache: 384KB
- Total L2 Cache: 2MB
- Total L3 Cache: 4MB

実際に確認
```
zsh 1779 % getconf -a | grep CACHE
LEVEL1_ICACHE_SIZE                 65536 # <- 64KB
LEVEL1_ICACHE_ASSOC                4
LEVEL1_ICACHE_LINESIZE             64
LEVEL1_DCACHE_SIZE                 32768 # <- 32KB
LEVEL1_DCACHE_ASSOC                8
LEVEL1_DCACHE_LINESIZE             64
LEVEL2_CACHE_SIZE                  524288 # <- 500KB
LEVEL2_CACHE_ASSOC                 8
LEVEL2_CACHE_LINESIZE              64
LEVEL3_CACHE_SIZE                  4194304 # <- 4MB
LEVEL3_CACHE_ASSOC                 16
LEVEL3_CACHE_LINESIZE              64
LEVEL4_CACHE_SIZE                  0
LEVEL4_CACHE_ASSOC                 0
LEVEL4_CACHE_LINESIZE              0
```

L1, L2 キャッシュは プロセッサ数 (3400G の場合は 4 コア) だけあるっぽい
- L1 Cache: (64KB + 32KB) * 4 = 384KB
- L2 Cache: 500KB * 4 = 2MB
- L3 Cache: 4MB

CS: APP における L1 キャッシュ (プロセッサ 内部に存在するキャッシュ) に相当するのが 3400G では L1, L2 キャッシュ. L2 キャッシュ (プロセッサ 外部に存在するキャッシュ) に相当するのが L3 キャッシュということだと思われる. 


#### アクセス速度 2020
[参照](https://qiita.com/zacky1972/items/e0faf71aa0469141dede)

- レジスタ: 0.33~1ns
- L1 キャッシュ: 1.33~4 ns
- L2 キャッシュ: 4~15 ns
- L3 キャッシュ: 16~50 ns
- メインメモリ: 100 ns
- NVMe SSD: 50 ms (= 50000 ns)
- SATA SSD: 100ms (= 100000ns)

メモリはレジスタの 100倍  
SSD は速いものでメモリの 500倍  

# The Operating System Manages the Hardware OS のハードウェア制御

A program does not access the keyboard, display, or main memory directly. Rather, it relies on the services provided by the *operating system*. We can think of the operating system as a layer of software **interposed between the application program and the hardware**. All attempts by an application program to manipulate the hardware must **go through the operating system**.

プログラムはハードウェアに直接アクセスするわけではなく OS に依頼する. OS はアプリケーションプログラムとハードウェアの間に介在するレイヤーとして捉えることができる.

```
// Layers of computer system

---------------------+-----------+
application programs |           |
---------------------+ software  |
operating system     |           |
---------------------+-----------+
hardware (e.g. processor, main memory, I/O devices)
---------------------------------+
```

The operating system has two primary purposes: (1) To **protect the hardware** from misuse by runaway applications, and (2) To **provide applications with simple and uniform mechanisms** for manipulating complicated and often wildly different low-level hardware devices. The operating system achieves both goals via the fundamental abstractions: ***processes*, *virtual memory*, and *files***. files are abstractions for **I/O devices**. Virtual memory is ab abstraction for both the **main memory and disk I/O devices**. And processes are abstractions for the **processor, main memory, and I/O devices**.

- プロセスはプロセッサ、メインメモリ、入出力機器を抽象化する.
- 仮想メモリはメインメモリとディスク入出力を抽象化する.
- ファイルは 入出力機器を抽象化する.

- Processes: processor + main memory + I/O devices
- Virtual memory: main memory + disk I/O devices
- File system: I/O devices



## Processes プロセス

A *process* is the operating system's abstraction for a running program. Multiple processes can run concurrently on the same system, and each process appears to have exclusive use of the hardware. By *concurrently*, we mean that the instruction of one process are interleaved with the instructions of another process. The operating system performs this interleaving with a mechanism known as *context switching*.

プロセスは実行中のプログラムの抽象化. 複数のプロセスがに走り、かつそれらのプログラムはハードウェアを専有しているように見える. 

The operating system keeps track of all the state information that the process needs in order to run. This state, which is known as the *context*, includes information such as the current value of the PC, the register file, and the contents of main memory. At any point in time, exactly one process is running on the system. When the operating system decides to transfer control from the current process to a some new process, it performs a *context switch* by saving the context of the current process, restoring the context of the new process, and then passing control to the new process. The new process picks up exactly where it left off.

OS はプロセスの実行に必要な全ての状態情報 (state information) を追跡し続けている. この状態 (state) は *context* として知られ、現在の PC やレジスタの値やメインメモリの内容を含む. OS は実行コントロールを現在のプロセスから新しいプロセスへ移動させようと決めたときに *context switch* を実行して、現在のプロセスの context を保存し 新しいプロセスの context を復帰させ、コントロールを新しいプロセスへ渡す. 

```
shell process
-> context switch
-> command process
-> context switch
-> shell process
```


## Threads スレッド

In modern system a process can actually consist of multiple execution units, called *threads*, each **running in the context of the process** and **sharing the same code and global data**. 

一つの context 内で実行され、同じコードとグローバルデータを共有する.   
-> プロセスよりもコンテキスト情報が最小で済むので切り替えが速くなる. 


## Virtual Memory 仮想メモリ

*Virtual memory* is an abstraction that provides each process with the illusion that it has exclusive use of the main memory. Each process the same uniform view of memory, which is known as its *virtual address space*. 


```
// Linux process virtual address space.

------------------------- 0xffffffff
kernel virtual memory
-------------------------
user stack


-------------------------


memory mapped region for shared libraries
-------------------------


run-time heap
-------------------------+
read/write data          |
-------------------------+ program code and data
read-only code and data  |
-------------------------+
unused
------------------------- 0x0
```


In Linux, the topmost 1/4 of the address space is reserved for **code and data in the operating system** that is common to all process. The bottommost 3/4 of the address space holds **the code and data defined by the user's processes**. 

The virtual address space seen by each process consists of a number of well-defined areas, each with a specific purpose.

- *Program code and data*. Code begins at the same fixed address, followed by data locations that correspond to global C variables. The code and data areas are initialized directly from the contents of an executable object file.

実行ファイルの内容で直接初期化される. 

- *Heap*. The code and data ares are followed immediately by the run-time *heap*. Unlike the code and data areas, which are fixed in size once the process begins running, the heap expands and contracts dynamically at runtime as a result of calls to C standard library routines such as `malloc` and `free`.

C では `malloc` / `free` で操作される. Rust では `Box` など. 

- *Shared libraries*. The area holds the code and data for *shared libraries* such as the C standard library and the math library.


- *Stack*. the space is the *user stack* that the compiler uses to implement function calls. Like the heap, the user stack expands and contracts dynamically during the execution of the program. In particular, each time we call a function, the stack grows. Each time we return from a function, it contracts. 

コンパイラが関数呼び出しを実装するために使用する. プログラムの実行中に動的に構築される. 特に関数を呼び出すたびにスタックは大きくなる.

- *Kernel virtual memory*. The *kernel* is the part of the operating system that is always resident in memory. The top 1/4 of the address space is reserved for the kernel. Application programs are **not allowed to read or write** the contents of this area or to directly call functions defined in the kernel code.

ユーザースペースのプログラムには 読み書き権限がない. 


## Files ファイル

A Unix file is a **sequence of bytes**, nothing more and nothing less. Every I/O device, including disks, keyboards, displays, and even networks, is modeled as a file. All input and output in the system  is performed by reading and writing files, using a set of operating system function known as *system calls*.

Unix のファイルはバイト列. すべての I/O 機器 (ディスク、キーボード、ディスプレイ、ネットワーク) はファイルをモデルにしている. すべての入出力はファイルの読み書きとして実行され、それにはシステムコールという OS 機能群を用いる.

File system provides applications with a uniform view of all of the varied I/O devices that might be contained in the system. 
















