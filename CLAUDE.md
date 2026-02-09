# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Folded cascode OTA design in a generic 180 nm CMOS process (level-1 MOSFET models). The entire flow — specifications, hand calculations, SPICE simulation, and result analysis — lives in a single Jupyter notebook.

## Repository Structure

- `folded_cascode_opamp.ipynb` — main design notebook (specs → hand calc → ngspice sim)
- `sim/` — generated SPICE netlists and simulation output files (created at runtime)

## Running

```bash
# Requires: Python 3, numpy, matplotlib, ngspice
jupyter notebook folded_cascode_opamp.ipynb
```

Run all cells top-to-bottom. The notebook calls `ngspice -b` via subprocess; ngspice must be on `$PATH`.

## Circuit Topology

PMOS differential input pair with NMOS + PMOS folded cascode loads. 11 transistors (M0–M10), single-ended output, ideal bias voltage sources.

- **M0**: PMOS tail current source (Iss = 100 uA)
- **M1, M2**: PMOS differential pair
- **M3, M4**: NMOS cascodes; **M5, M6**: NMOS current sources (In = 100 uA)
- **M7, M8**: PMOS cascodes; **M9, M10**: PMOS current sources (Ip = 50 uA)
- Current balance: Iss/2 + Ip = In at each branch

## Key Design Parameters

- VDD = 1.8 V, CL = 2 pF, L = 0.5 um (all transistors)
- Target: DC gain > 60 dB, GBW > 50 MHz, PM > 60°, SR > 20 V/us, Power < 1 mW

## Simulations (ngspice)

1. **Operating Point (.op)** — verify all transistors in saturation
2. **AC Analysis** — open-loop Bode plot (gain, phase, GBW, phase margin)
3. **Transient** — unity-gain feedback step response (slew rate, settling)

Netlists are generated programmatically from the hand-calculated sizing and written to `sim/`. Simulation data is saved via ngspice `wrdata` and parsed with numpy.
