# Tournament Branch Predictor

A synthesizable Verilog RTL implementation of a Tournament Branch Prediction Unit (BPU) for a 5-stage RISC-V pipelined processor, combining local and global prediction schemes with a dynamic choice predictor for adaptive branch prediction.

The design additionally integrates a Branch Target Buffer (BTB) with tag-based validation and anti-aliasing logic to improve prediction accuracy and target resolution.

---

## Features

- Tournament branch prediction architecture
- Local history predictor
- Global history predictor (Gshare-style)
- Dynamic choice/meta predictor
- Branch Target Buffer (BTB)
- XOR-based aliasing mitigation
- Full-tag BTB address validation
- Synthesizable Verilog RTL implementation
- Integrated testing with a public 5-stage pipelined processor core
- gem5-based architectural performance evaluation

---

## Architecture Overview

The predictor dynamically selects between:
- A Local Predictor optimized for loop-like branch behavior
- A Global Predictor optimized for correlated branch behavior

A Choice Predictor learns which predictor performs better for a given branch history and selects the final prediction dynamically.

---

## Tournament Predictor Flow

```text
               Program Counter
                      │
        ┌─────────────┴─────────────┐
        │                           │
 Local Predictor            Global Predictor
 (LHT + PHT)                (GHR + XOR PHT)
        │                           │
        └─────────────┬─────────────┘
                      │
              Choice Predictor
                (Meta Predictor)
                      │
               Final Prediction
                      │
                     BTB
                      │
                  next_pc
```

---

## Predictor Components

### Local Predictor

Implements a two-level adaptive predictor:
- Local History Table (LHT)
- Pattern History Table (PHT)
- 2-bit saturating counters

Optimized for:
- Loops
- Repetitive branch patterns
- Per-branch history correlation

---

### Global Predictor

Implements a Gshare-style predictor:
- Global History Register (GHR)
- XOR-folded indexing
- Global Pattern History Table

Optimized for:
- Cross-branch correlation
- Context-sensitive branch behavior
- Dynamic execution patterns

---

### Choice Predictor

A meta-predictor that dynamically selects between:
- Local prediction
- Global prediction

Uses:
- 2-bit saturating counters
- GHR-indexed Choice Pattern Table (CPT)

---

### Branch Target Buffer (BTB)

The BTB stores:
- Branch target addresses
- Valid bits
- Full PC tags

Features:
- Tag-based address validation
- Anti-aliasing protection
- Direct-mapped organization

---

## Aliasing Mitigation

The design implements two anti-aliasing techniques:

### XOR Folding

Global prediction tables use:

```text
PC XOR GHR
```

for context-sensitive indexing and reduced destructive aliasing.

### BTB Tag Validation

Full-PC tag comparison prevents incorrect target fetches caused by BTB collisions.

---

## Integration Testing

The predictor was additionally validated through integration with a public 5-stage pipelined processor core to evaluate:
- Realistic branch handling behavior
- Pipeline control-flow interaction
- Predictor update correctness
- Dynamic branch outcome tracking

---

## gem5 Evaluation

The predictor was benchmarked against:
- Baseline predictor
- Gshare predictor
- Perceptron predictor

using the gem5 architectural simulator.

### Observed Results

- Up to 5.6× speedup on highly predictable loop-heavy workloads
- Approximately 2× speedup on data-dependent branch patterns
- Improved IPC and branch prediction accuracy over baseline approaches

---

## Benchmark Summary

| Predictor | IPC | Direction Accuracy |
|---|---|---|
| Baseline | 1.282 | 92.00% |
| Gshare | 1.282 | 92.03% |
| Tournament | 1.34 | 94.13% |
| Perceptron | 1.548 | 90.53% |

---

## Waveforms

### Tournament Predictor Verification

Demonstrates predictor selection behavior, BTB interaction, and branch prediction updates.

![Waveform](waveforms/bpu_waveform.png)

---

## Tools Used

- Verilog HDL
- Vivado Design Suite
- gem5 Architectural Simulator

---

## Directory Structure

```text
├── src/             RTL source files
├── tb/       Verification testbenches
├── waveforms/       Simulation waveforms
├── images/          Architecture diagrams
├── docs/            Project documentation
└── README.md
```

---

## Future Improvements

- Perceptron-based neural branch predictor
- TAGE-style hybrid prediction
- Direct integration into custom RISC-V core
- RTL-to-GDSII physical implementation flow
- Extended benchmark evaluation using SPEC/CoreMark

---

## References

- Alpha 21264 Tournament Predictor
- gem5 Simulator
- RISC-V ISA
- Gshare Branch Prediction Architecture

---

## Authors

Pratyush L Daddi  Adityaa Mehra  K.S. Sethurathnam  Ridhi

Electronics Engineering  
IIT (BHU) Varanasi
