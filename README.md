# LTspice Python Parameter Sweeper

## Overview
This project automates LTspice simulations using Python.
It sweeps a component value in a parameterized circuit, runs LTspice in batch mode,
extracts waveform data, and generates engineering results automatically.

Instead of manually changing component values and rerunning simulations,
this tool performs the entire process programmatically.

## What the tool does
- Sweeps a single component parameter (e.g., capacitor value)
- Runs LTspice automatically from the command line
- Extracts waveform data from simulation output
- Computes a design metric (e.g., output ripple Vpp)
- Generates:
  - CSV summary of results
  - Plot of sweep variable vs metric
  - Organized run folders with raw simulation outputs

## Inputs
- LTspice circuit file (`.asc`) with parameterized component
- List of sweep values defined in Python

## Outputs
- `results/sweep_results.csv`
- `results/sweep_plot.png`
- `results/runs/` (one folder per sweep point)

## How to run
1. Install Python dependencies
2. Ensure LTspice is installed and accessible from command line
3. Run:
   ```bash
   python scripts/run_sweep.py
