# Adding Zacas Instructions to ExceptionsZaamo Coverage

## Reviewer's Comment Explanation
Your reviewer is asking to add **Zacas atomic instructions** to the `ExceptionsZaamo_coverage.svh` file and the associated test generator to ensure proper coverage testing for illegal address alignments.

---

## What is Zacas?

**Zacas** is an atomic Compare-and-Swap (CAS) extension in RISC-V that complements the **Zaamo** (Atomic Memory Operations) extension. While Zaamo provides basic atomic operations (swap, add, and, or, xor, min, max), Zacas adds:

- **CAS instructions** (Compare-and-Swap operations)
- Operations on different data widths: word (32-bit), double-word (64-bit), half-word (16-bit), and byte (8-bit)

---

## Current Structure Analysis

### What You Have Now:

Your `ExceptionsZaamo_cg` covergroup in `ExceptionsZaamo_coverage.svh` includes:
- Basic AMO instructions: `amoswap`, `amoadd`, `amoand`, `amoor`, `amoxor`, `amomax`, `amomin`
- Support for different widths: `.w` (32-bit), `.d` (64-bit), `.h` (16-bit), `.b` (8-bit)
- Coverage for address misalignment
- Coverage for access faults

### What's Missing:

**Zacas instructions** are NOT covered. The Zacas extension provides:
- `cas.w` - Compare-and-swap word
- `cas.d` - Compare-and-swap doubleword (RV64 only)
- `cas.h` - Compare-and-swap half-word (ZABHA extension support)
- `cas.b` - Compare-and-swap byte (ZABHA extension support)

---

## Implementation Steps

### Step 1: Update the SystemVerilog Coverage File

Add Zacas instructions to the covergroup in `ExceptionsZaamo_coverage.svh`:

```systemverilog
`define COVER_EXCEPTIONSZAAMO
covergroup ExceptionsZaamo_cg with function sample(ins_t ins);
    option.per_instance = 0;

    // building blocks for the main coverpoints
    amo_instrs: coverpoint ins.current.insn {
        wildcard bins amoswap_w = {AMOSWAP_W};
        wildcard bins amoadd_w  = {AMOADD_W};
        wildcard bins amoand_w  = {AMOAND_W};
        wildcard bins amoor_w   = {AMOOR_W};
        wildcard bins amoxor_w  = {AMOXOR_W};
        wildcard bins amomax_w  = {AMOMAX_W};
        wildcard bins amomaxu_w = {AMOMAXU_W};
        wildcard bins amomin_w  = {AMOMIN_W};
        wildcard bins amominu_w = {AMOMINU_W};
        
        // ADD ZACAS INSTRUCTIONS HERE
        wildcard bins cas_w     = {CAS_W};
        
        `ifdef XLEN64
            wildcard bins amoswap_d = {AMOSWAP_D};
            wildcard bins amoadd_d  = {AMOADD_D};
            wildcard bins amoand_d  = {AMOAND_D};
            wildcard bins amoor_d   = {AMOOR_D};
            wildcard bins amoxor_d  = {AMOXOR_D};
            wildcard bins amomax_d  = {AMOMAX_D};
            wildcard bins amomaxu_d = {AMOMAXU_D};
            wildcard bins amomin_d  = {AMOMIN_D};
            wildcard bins amominu_d = {AMOMINU_D};
            
            // ADD 64-BIT ZACAS HERE
            wildcard bins cas_d     = {CAS_D};
        `endif
        
        `ifdef ZABHA_SUPPORTED
            wildcard bins amoswap_h = {AMOSWAP_H};
            wildcard bins amoadd_h  = {AMOADD_H};
            wildcard bins amoand_h  = {AMOAND_H};
            wildcard bins amoor_h   = {AMOOR_H};
            wildcard bins amoxor_h  = {AMOXOR_H};
            wildcard bins amomax_h  = {AMOMAX_H};
            wildcard bins amomaxu_h = {AMOMAXU_H};
            wildcard bins amomin_h  = {AMOMIN_H};
            wildcard bins amominu_h = {AMOMINU_H};
            wildcard bins amoswap_b = {AMOSWAP_B};
            wildcard bins amoadd_b  = {AMOADD_B};
            wildcard bins amoand_b  = {AMOAND_B};
            wildcard bins amoor_b   = {AMOOR_B};
            wildcard bins amoxor_b  = {AMOXOR_B};
            wildcard bins amomax_b  = {AMOMAX_B};
            wildcard bins amomaxu_b = {AMOMAXU_B};
            wildcard bins amomin_b  = {AMOMIN_B};
            wildcard bins amominu_b = {AMOMINU_B};
            
            // ADD ZABHA-BASED ZACAS HERE
            wildcard bins cas_h     = {CAS_H};
            wildcard bins cas_b     = {CAS_B};
        `endif
    }
    
    adr_LSBs: coverpoint ins.current.rs1_val[4:0]  {
        // auto fills 00000 through 11111
    }
    
    // main coverpoints
    cp_amo_address_misaligned:  cross amo_instrs, adr_LSBs;

    `ifdef RVMODEL_ACCESS_FAULT_ADDRESS
        illegal_address: coverpoint ins.current.rs1_val {
            bins illegal = {`RVMODEL_ACCESS_FAULT_ADDRESS};
        }
        cp_amo_access_fault:        cross amo_instrs, illegal_address;
    `endif

endgroup
```

---

### Step 2: Update the Python Test Generator

Add Zacas instructions to `ExceptionsZaamo.py`:

**In `_generate_amo_address_misaligned_tests()` function:**

```python
ops = [
    "amoswap.", "amoadd.", "amoxor.", "amoand.", "amoor.", 
    "amomin.", "amomax.", "amominu.", "amomaxu.",
    "cas."  # ADD THIS LINE FOR ZACAS
]
```

**After the `amomaxu.` line in both test generation functions:**

```python
# For 32-bit operations (always supported with Zacas)
ops = ["amoswap.", "amoadd.", "amoxor.", "amoand.", "amoor.", "amomin.", "amomax.", "amominu.", "amomaxu.", "cas."]
```

The CAS instruction will automatically be tested with:
- `.w` (word/32-bit)
- `.d` (double-word/64-bit) - RV64 only
- `.h` (half-word/16-bit) - ZABHA only
- `.b` (byte/8-bit) - ZABHA only

---

### Step 3: Update YAML Coverage Configuration

In `coverpoints/norm/Zacas.yaml`, ensure you have a coverage point entry:

**Before (old):**
```yaml
- name: Zacas_amocas_rs1_addr_alignment
  coverpoint: [""]
```

**After (new):**
```yaml
- name: Zacas_amocas_rs1_addr_alignment
  coverpoint: ["cp_align"]  # or the correct coverage point name
```

This tells the coverage tracking system to monitor the `cp_align` coverage point (or whatever it's named in your SystemVerilog).

---

## What "Address Misalignment" Coverage Means

The reviewer wants to test that CAS instructions properly handle **misaligned addresses**:

1. **Misaligned Access Testing**: Test CAS instructions with addresses that aren't naturally aligned
   - Word (32-bit): address LSBs = any value (0-31)
   - Double-word (64-bit): address LSBs = any value (0-31)
   - Half-word (16-bit): address LSBs = any value (0-31)
   - Byte (8-bit): address LSBs = any value (0-31)

2. **Expected Behavior**: 
   - Some alignments may cause address misalignment exceptions
   - Others may be handled by the implementation
   - The coverage tracks all 32 possible offset cases

---

## Checklist for PR Review Fix

- [ ] Add `cas.w`, `cas.d`, `cas.h`, `cas.b` bins to `amo_instrs` coverpoint in `ExceptionsZaamo_coverage.svh`
- [ ] Update the `ops` list in `_generate_amo_address_misaligned_tests()` to include `"cas."`
- [ ] Update the `ops` list in `_generate_amo_access_fault_tests()` to include `"cas."`
- [ ] Verify `ExceptionsZaamo.py` generates test cases for all CAS variants
- [ ] Update `coverpoints/norm/Zacas.yaml` to point to correct coverage point names
- [ ] Run tests to verify coverage collection works
- [ ] Check that all test cases execute without errors

---

## Why This Matters

- **Complete Coverage**: Ensures Zacas CAS instructions are tested for exception conditions
- **Compliance**: Meets RISC-V specification requirements for atomic operations
- **Regression Prevention**: Catches alignment-related bugs early
- **Documentation**: Makes it clear which exception scenarios are tested
