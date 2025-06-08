# 🛠️ Task 1: RISC-V Toolchain Setup and Verification Using WSL

## 🎯 Objective

Successfully install the RISC-V toolchain in WSL (Windows Subsystem for Linux), configure environment variables, and verify that the essential binaries (`gcc`, `objdump`) function correctly for cross-compilation development.

---

## 📋 Prerequisites

✅ WSL installed and configured  
✅ Basic Linux command line knowledge  

---

## 📥 Toolchain Download

We have first downloaded the RISC-V toolchain archive from:

👉 https://vsd-labs.sgp1.cdn.digitaloceanspaces.com/vsd-labs/riscv-toolchain-rv32imac-x86_64-ubuntu.tar.gz

Saved it to the Windows Downloads directory:

`/mnt/c/Users/nivet/Downloads`

---

## 🚀 Step-by-Step Implementation

---

### Step 1: Navigate to Downloads Directory and Create Installation Path

```bash
cd /mnt/c/Users/nivet/Downloads
sudo mkdir -p /opt/riscv
ls -la riscv-toolchain-rv32imac-x86_64-ubuntu.tar.gz
```


### Step 2: Extract the RISC-V Toolchain

```bash
sudo tar -xzf riscv-toolchain-rv32imac-x86_64-ubuntu.tar.gz -C /opt/riscv --strip-components=1
ls -la /opt/riscv/
ls -la /opt/riscv/riscv/
```
### Step 3: Configure PATH Environment Variable

```bash
echo 'export PATH=/opt/riscv/riscv/bin:$PATH' >> ~/.bashrc
source ~/.bashrc
echo $PATH | grep riscv
```

### Step 4: Verify Toolchain Installation

```bash
riscv32-unknown-elf-gcc --version
riscv32-unknown-elf-objdump --version
riscv32-unknown-elf-gcc -dumpmachine
ls -la /opt/riscv/riscv/bin/ | grep riscv32
```
---
---

## 📊 Expected Results

- **GCC version:** riscv32-unknown-elf-gcc (g04696df096) 14.2.0  
- **Objdump version:** GNU objdump (GNU Binutils) 2.43.1  
- **Target architecture:** riscv32-unknown-elf  
- **Binaries verified:** riscv32-unknown-elf-gcc, riscv32-unknown-elf-objdump, and other essential tools inside `/opt/riscv/riscv/bin/`

---

## 📸 Implementation Output

_(Include my terminal output screenshot below)_


![Task1_Output](screenshots/task1_1.png)
![Task1_Output](screenshots/task1_2.png)
![Task1_Output](screenshots/task1_3.png)
---
# 🏗️ Task 2: Cross-Compile "Hello, RISC-V"

## 🎯 Objective

Create a minimal C "Hello World" program and successfully cross-compile it for the RISC-V RV32 architecture, producing a valid 32-bit RISC-V ELF executable.

## 📋 Prerequisites

✅ RISC-V toolchain installed and configured (Task 1 completed)  

✅ PATH environment variable set to `/opt/riscv/riscv/bin`  

✅ Verified `riscv32-unknown-elf-gcc` functionality  

✅ Basic knowledge of C programming and cross-compilation  

## 🚀 Step-by-Step Implementation

### Step 1: Create the Hello World C Program

```bash
nano hello.c
```
### C Program
```
#include <stdio.h>

int main() {
    printf("Hello, RISC-V!\n");
    return 0;
}
```
### Step 2: Cross-Compile the Hello World C Program

Use the RISC-V GCC toolchain to compile the program into a RISC-V ELF executable:

```bash
riscv32-unknown-elf-gcc -o hello.elf hello.c
```
### Step 3: Verify the Compiled ELF Binary

Check the type and architecture of the compiled binary using the `file` command:

```bash
file hello.elf
```
### Step 4: Additional Verification Commands

1. List the compiled binary file with detailed info:

```bash
ls -la hello.elf
```
2. Verify target architecture of the toolchain used
```bash
riscv32-unknown-elf-gcc -dumpmachine
```
3.Inspect ELF section headers using objdump
```bash
riscv32-unknown-elf-objdump -h hello.elf
```
### 📊 Expected Result:
✅The hello.elf file is confirmed as a 32-bit RISC-V executable.
✅The toolchain target architecture is correctly set as riscv32-unknown-elf.
✅The ELF sections (.text, .rodata, .data, etc.) are visible and correctly formatted.
✅The executable is statically linked and supports RVC instructions.

## 📸 Implementation Output

_(Include my terminal output screenshot below)_


![Task2_Output](screenshots/Task2_1.png)
![Task2_Output](screenshots/Task2_2.png)
---
# 🏗️ Task 3: From C to Assembly
## 🎯 Objective
Generate the .s file (assembly output) from the C source code and analyze the function prologue and epilogue in the main function to understand how the compiler sets up and tears down a stack frame in RISC-V.

## 📋 Prerequisites
✅ RISC-V toolchain installed and configured
✅ PATH environment variable set
✅ Completed Task 2 (have a working hello.c file)

## 🚀 Step-by-Step Implementation
### Step 1: Generate the Assembly .s file
```bash
riscv32-unknown-elf-gcc -S -O0 hello.c
```
✅ This command generates the file hello.s in the current directory.
### Step 2: Verify the Generated Assembly File
```bash
ls -la hello.s
cat hello.s
nl hello.s
```
✅ The hello.s file contains human-readable RISC-V assembly code.
✅The nl hello.s will print the hello.s file with line numbers
### 📝 Explanation of Prologue and Epilogue
 #### Function Prologue (at the start of main):
```
addi    sp,sp,-16      # Adjust stack pointer to allocate stack space
sw      ra,12(sp)      # Save return address
sw      s0,8(sp)       # Save frame pointer (s0)
addi    s0,sp,16       # Set up new frame pointer
```
##### Purpose:
👉 Allocates stack space
👉 Saves the return address and previous frame pointer
👉 Sets up new frame pointer

#### Function Epilogue (at the end of main):
```
lw      ra,12(sp)      # Restore return address
lw      s0,8(sp)       # Restore previous frame pointer
addi    sp,sp,16       # Deallocate stack space
ret                    # Return to caller
```
##### Purpose:
👉 Restores saved registers
👉 Cleans up stack frame
👉 Returns to caller (ret)

## 📸 Implementation Output

_(Include my terminal output screenshot below)_


![Task3_Output](screenshots/task3_1.png)
![Task3_Output](screenshots/task3_2.png)
---
# 🏗️ Task 4: Hex Dump & Disassembly
## 🎯 Objective

✅ Disassemble the ELF binary using objdump
✅ Convert the ELF binary into a raw Intel HEX format
✅ Understand the meaning of each column in the disassembly output

## 🚀 Step-by-Step Implementation
### Step 1: Generate Disassembly Dump
```bash
riscv32-unknown-elf-objdump -d hello.elf > hello.dump
```
✅ This command generates hello.dump, which contains the full disassembly of hello.elf.

### Step 2: Convert ELF to Raw Intel HEX
```bash
riscv32-unknown-elf-objcopy -O ihex hello.elf hello.hex
```
✅ This command converts hello.elf into a raw Intel HEX format (hello.hex) which can be used for loading into flash memory of embedded boards.

### Step 3: Verify Files
``` bash
ls -la hello.dump hello.hex
```
✅ output:
```
-rw-r--r-- 1 nivetha nivetha  2345 Jun  8 08:15 hello.dump
-rw-r--r-- 1 nivetha nivetha   890 Jun  8 08:15 hello.hex
```
#### 📖 Understanding the Disassembly Output
###### Example part of hello.dump:
```
100b4:   1141                addi    sp,sp,-16
100b6:   4581                li      a1,0
100b8:   c422                sw      s0,8(sp)
```
Column	Meaning  
📌 100b4:	Memory Address of instruction  
📌 1141	Machine code / raw instruction encoding  
📌 addi sp,sp,-16	Instruction mnemonic and operands

✅  breakdown:  
📌 100b4: → The address where this instruction will execute in memory.  
📌1141 → Hex representation of the instruction (opcode and operands encoded).  
📌addi sp,sp,-16 → Human-readable instruction:  
📌addi = Add Immediate  
📌sp,sp,-16 → Subtract 16 from the stack pointer → creates space on stack.  

## 📸 Implementation Output

_(Include my terminal output screenshot below)_


![Task4_Output](screenshots/task4_1.png)
![Task4_Output](screenshots/task4_2.png)
---
# 🏗️ Task 5: ABI & Register Cheat-Sheet

## 🎯 Objective  
List all 32 RV32 integer registers with their ABI names and typical calling-convention roles.

---

## 📋 ABI Register Table

| Register | ABI Name | Typical Role / Calling Convention                |
|----------|----------|-------------------------------------------------|
| x0       | zero     | Hardwired zero (always 0)                        |
| x1       | ra       | Return address (used to store return PC)        |
| x2       | sp       | Stack pointer                                   |
| x3       | gp       | Global pointer                                 |
| x4       | tp       | Thread pointer                                |
| x5       | t0       | Temporary register (caller-saved)               |
| x6       | t1       | Temporary register (caller-saved)               |
| x7       | t2       | Temporary register (caller-saved)               |
| x8       | s0/fp    | Saved register / frame pointer (callee-saved)  |
| x9       | s1       | Saved register (callee-saved)                   |
| x10      | a0       | Function argument 0 / return value 0            |
| x11      | a1       | Function argument 1 / return value 1            |
| x12      | a2       | Function argument 2                             |
| x13      | a3       | Function argument 3                             |
| x14      | a4       | Function argument 4                             |
| x15      | a5       | Function argument 5                             |
| x16      | a6       | Function argument 6                             |
| x17      | a7       | Function argument 7                             |
| x18      | s2       | Saved register (callee-saved)                   |
| x19      | s3       | Saved register (callee-saved)                   |
| x20      | s4       | Saved register (callee-saved)                   |
| x21      | s5       | Saved register (callee-saved)                   |
| x22      | s6       | Saved register (callee-saved)                   |
| x23      | s7       | Saved register (callee-saved)                   |
| x24      | s8       | Saved register (callee-saved)                   |
| x25      | s9       | Saved register (callee-saved)                   |
| x26      | s10      | Saved register (callee-saved)                   |
| x27      | s11      | Saved register (callee-saved)                   |
| x28      | t3       | Temporary register (caller-saved)               |
| x29      | t4       | Temporary register (caller-saved)               |
| x30      | t5       | Temporary register (caller-saved)               |
| x31      | t6       | Temporary register (caller-saved)               |

---

## 📝 Calling Convention Summary

- **Argument/Return registers:** `a0`–`a7` (x10–x17) — Used to pass function arguments and return values.  
- **Callee-saved registers:** `s0`–`s11` (x8, x9, x18–x27) — Preserved by the called function across calls.  
- **Caller-saved registers:** `t0`–`t6` (x5–x7, x28–x31) — May be overwritten by the called function; caller must save if needed.  
- **Special purpose registers:**  
  - `zero` (x0): Constant zero value  
  - `ra` (x1): Return address  
  - `sp` (x2): Stack pointer  
  - `gp` (x3): Global pointer  
  - `tp` (x4): Thread pointer  
  - `s0` (x8) also serves as frame pointer (`fp`)  

✅ **Task 5 complete:** ABI register list and calling conventions summarized.
---
# Task 6: Stepping with GDB

## Objective
Learn how to start `riscv32-unknown-elf-gdb` on the ELF file, set a breakpoint at `main`, step through the instructions, and inspect registers.

## Commands and Steps

1. **Start GDB for the RISC-V binary:**

   ```bash
   gdb-multiarch hello.elf
   ```
2. **Disassemble the main function to view assembly instructions:**
```gdb
disassemble main
```
3. **Check which symbol corresponds to a specific address inside main:**
```gdb
info symbol 0x10170
```

4. **Examine 10 instructions starting at the main address:**
```gdb
x/10i 0x10162
```

5. **Inspect the program entry point _start:**
```gdb
info symbol 0x100e2
x/5i 0x100e2
```
6. **Examine the string stored at address 0x1245c used in the program:**

```gdb
x/s 0x1245c
```
7. **Exit the gdb:**
   ```gdb
   quit
   ```
## Observations:  
📌The main function sets up the stack frame, saves registers, loads the address of a string literal, and calls puts.  
📌The string "Hello, RISC-V World!" is stored at address 0x1245c.  
📌No debugging symbols are available in the ELF file, so source-level debugging is not possible.  
📌The program starts at _start, which sets up the environment before calling main.
## 📸 Implementation Output

_(Include my terminal output screenshot below)_


![Task6_Output](screenshots/task6_1.png)
![Task6_Output](screenshots/task6_2.png)
---













