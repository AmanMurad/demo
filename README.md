#  Demonstrating Const Pointers in C (Introduction to C Programming)  

This task demonstrates the **differences between**:  
- **Pointer to const** (`const int *ptr`)  
- **Const pointer** (`int * const ptr`)  
- **Const pointer to const** (`const int * const ptr`)  

The program also uses a **global array of integers** representing configuration parameters. It provides functionality to **display**, **update**, and **audit** these parameters, showcasing how different const pointer types behave in C.  

---

## Program Structure
- `src/main.c` → Contains the main program logic.  
- `src/other.c` → Contains function implementations (`displayConfig`, `updateConfig`, `auditConfig`).  
- `include/header.h` → Contains macro definitions (e.g., `CONFIG_SIZE`) and function declarations.  
- `Makefile` → Automates building, running, cleaning, and showing help.  

---

## Usage

### 1. Build
Compile the program into an executable named `main`:
```bash
make build
```

### 2. Run
Run the program using the executable generated:
```bash
make run
```

### 3. Clean
Remove the compiled executable:
```bash
make clean
```

### 4. Help
Display the list of available targets:
```bash
make help
```

### Shortcuts
To build and run in one step
```bash
make
```

---
