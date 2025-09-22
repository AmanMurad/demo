# RISC-V Fibonacci Sequence – Array Computation

- Author:  Aman Murad  
- Company: 10xEngineers Technologies (Pvt.) Ltd.  
- Date:    2025-09-14  
- Module:  R2: Intro to RISC-V Programming – v2  
- Section: RISC-V Unprivileged ISA  
- Task 4:  Assembly program that prints the first N Fibonacci numbers  

---

## Description

Write a **RISC-V assembly program** that computes the first 12 numbers in the Fibonacci sequence and stores the result in a finite vector (i.e., array) `V` of length `N = 12`.  

The Fibonacci sequence is defined as:

V(0) = 0
V(1) = 1
V(i) = V(i-1) + V(i-2), for i = 2, 3, ...

- The program **statically defines** the dimension of the vector (`N`) in the `.data` section.  
- First two Fibonacci numbers (`0` and `1`) are initialized in memory.  
- Remaining numbers are computed in a loop and stored in `V`.  
- The sequence is printed one number per line using the helper function `print_unsigned_ln`.  

---

## Structure

project/
│
├─ src/
│ ├─ main.s # Main program computing Fibonacci sequence
│ ├─ lib.s # Utility functions (print, string output)
│ ├─ syscalls.s # System call implementations (tohost_exit, printing)
│ ├─ regs.s # Register definitions and constants
│ ├─ macro.s # Reusable macros
│ ├─ startup.s # Startup and reset code
│ ├─ trap.s # Trap/exception handlers
│ └─ riscv.ld # Linker script
│
├─ build/ # Output directory for compiled objects and binary
│
├─ Makefile # Build automation
└─ README.md # This file

## Register Usage

| Register | Type           | Purpose                                  | Preserved by |
|----------|----------------|------------------------------------------|--------------|
| `s0`     | Callee-saved   | Base address of Fibonacci array `V`      | Callee       |
| `s1`     | Callee-saved   | Length of Fibonacci sequence `N`         | Callee       |
| `s2`     | Callee-saved   | Loop index                               | Callee       |
| `t0-t6`  | Temporary      | Used for address and value computations  | Caller       |
| `a0`     | Argument/Return| Argument to `print_unsigned_ln` / exit code | N/A      |
| `ra`     | Return address | Saved/restored across main calls         | Callee       |
| `sp`     | Stack pointer  | Stack frame management                    | N/A          |

---

## Code Working

1. Load base address of array `V` and sequence length `N` from memory.  
2. Initialize loop index `i = 2`.  
3. Compute Fibonacci numbers using the formula `V(i) = V(i-1) + V(i-2)` and store in `V[i]`.  
4. Repeat until `i >= N`.  
5. Reset loop index for printing.  
6. Print each number of the sequence using `print_unsigned_ln`.  
7. Exit the program via `tohost_exit`.  

---

### Build the program
```bash
make build
```
- Assembles `main.s` into `main.o`  
- Links into the binary `build/main`  
- Produces disassembly `build/main.dis`

### Run on Spike (bare-metal)
```bash
make run
```
- Executes `build/main` on Spike using RV32IM Zicsr ISA  
- Prints the computed GCD to console

### Clean build artifacts
```bash
make clean
```
- Removes `build/` directory and all compiled files

### Help
```bash
make help
```
- Displays the target menu and usage instructions

---
