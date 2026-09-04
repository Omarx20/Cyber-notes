# CS:APP — Chapter 1

## 1. Bits and Data Representation

All data is ultimately represented as a sequence of bits.

The same sequence of bits can represent different things depending on how it is interpreted. It could represent an integer, characters such as ASCII text, machine instructions, or other types of data.

---

## 2. From Source Code to Executable

Programs written in languages such as C are translated into machine-level instructions through several stages.

### 1. Preprocessing

The preprocessor modifies the source code, for example by expanding header files and macros.

The result is still a text-based source file.

### 2. Compilation

The compiler translates the preprocessed source code into assembly language.

### 3. Assembly

The assembler translates the assembly code into machine code, producing an object file.

### 4. Linking

The linker combines object files and required libraries, resolving references to functions and other symbols, producing the final executable program.

```text
Source Code
    ↓
Preprocessor
    ↓
Modified Source
    ↓
Compiler
    ↓
Assembly
    ↓
Assembler
    ↓
Object File
    ↓
Linker
    ↓
Executable
```

---

## 3. The Processor

When an executable program runs, the program and its required data are loaded into memory.

The **Program Counter (PC)** contains the address of the next instruction to be executed.

The processor fetches and executes instructions using components such as registers and the ALU.

---

## 4. Shell

A shell is a command-line program that allows the user to interact with the operating system by executing commands and programs.

---

## 5. Processes and Threads

A **process** is a running program together with its execution state and resources.

A process can contain multiple **threads**. Each thread represents an independent control flow, while threads within the same process share the process's address space and other resources.

---

## 6. Multi-Core Processors

A multi-core processor contains multiple CPU cores that can execute instructions independently and potentially in parallel.

The exact cache hierarchy depends on the processor architecture. Some caches may be private to individual cores while others may be shared.

---

## 7. I/O and Data

Input and output can be viewed as streams of data represented as bits.

Many I/O devices interact with the system through abstractions such as files and streams.

---

## 8. Buses

Buses provide communication paths between components of a computer system.

They transfer data in groups of bits, often referred to as words. The size of a word depends on the architecture, such as 32-bit or 64-bit systems.

---

## 9. The Kernel

The kernel is the core of the operating system.

It manages hardware resources and provides services such as:

* Process scheduling
* Memory management
* I/O
* Resource management
* Communication between applications and hardware

---

## 10. Operating System Abstractions

The operating system provides abstractions that hide the complexity of the underlying hardware.

```text
Processor        → Processes
Main Memory      → Virtual Memory
I/O Devices      → Files
```

These abstractions allow programmers to work with complex hardware through simpler interfaces.

---

## 11. Virtual Memory

A simplified view of a 32-bit process address space:

```text
0xFFFFFFFF
+-----------------------------+
|       Kernel Space          |
|     OS / privileged code    |
+-----------------------------+
|                             |
|       Unused / Guard        |
|                             |
+-----------------------------+
|        User Stack           |
|             ↓               |
+-----------------------------+
|   Shared Libraries /       |
|   Memory-Mapped Regions     |
|             ↑               |
+-----------------------------+
|        Runtime Heap         |
|             ↑               |
+-----------------------------+
|     Read/Write Data         |
+-----------------------------+
|     Read-Only Code/Text     |
+-----------------------------+
0x00000000
```

The exact layout and addresses depend on the architecture and operating system.

## Things I Want to Remember

* The same bits can have different meanings depending on their interpretation.
* The compiler does not directly produce the final executable; preprocessing, compilation, assembly, and linking are separate stages.
* The PC points to the next instruction to execute.
* Processes can contain multiple threads.
* The kernel manages resources and provides abstractions over hardware.
* Virtual memory provides each process with its own virtual address space.
