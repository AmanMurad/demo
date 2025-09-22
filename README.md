# RISC-V Caller-Callee Register Convention Demonstration

- Author:  Aman Murad
- Company: 10xEngineers Technologies (Pvt.) Ltd.
- Date:    2025-09-12 
- Module:  R2: Intro to RISC-V Programming  
- Section: RISC-V Unprivileged ISA  
- Task 2:  RISC-V Caller-Callee saved registers

---

## Description

This project demonstrates the RISC-V calling convention and register preservation rules between caller and callee functions. It showcases proper stack management, register spilling, and the distinction between caller-saved and callee-saved registers.

- **Caller-saved registers** (t0-t6): Preserved by the caller if needed across function calls
- **Callee-saved registers** (s0-s11): Preserved by the callee across function boundaries  
- **Stack frame management**: Proper allocation/deallocation and register spilling
- **Function calling convention**: Argument passing (a0-a7) and return values (a0, a1)

---

## Structure

```
project/
│
├─ src/
│   ├─ main.s          # Main program with caller/callee demonstration
│   ├─ lib.s           # Utility functions (print, string operations)
│   ├─ syscalls.s      # System call implementations
│   ├─ regs.s          # Register definitions and constants
│   └─ riscv.ld        # Linker script
│
├─ build/              # Output directory for compiled objects
│
├─ Makefile            # Build automation
└─ README.md           # This file
```

---

## Register Usage

| Register | Type          | Purpose                          | Preserved by |
|----------|---------------|----------------------------------|--------------|
| `s1`     | Callee-saved  | Holds value 190                  | Callee       |
| `t0`     | Caller-saved  | Holds value 405                  | Caller       |
| `a0`     | Argument/Return | Function argument (150) → Return (160) | N/A        |
| `ra`     | Special       | Return address                   | Caller       |

---

## Makefile Usage

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
- Prints the output of our program

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
