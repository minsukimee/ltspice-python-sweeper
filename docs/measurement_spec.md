# Measurement Spec (Phase 2) — Periodic Steady-State Window

## 1. Purpose
This document defines exactly how we measure metrics from LTspice waveforms so that:
- manual LTspice measurements and Python automation match
- results are repeatable and not affected by startup transients

This project uses a pulsed input, so the circuit approaches **periodic steady state** (repeatable cycles),
not a single DC final value.

---

## 2. Circuit + stimulus assumptions
- Circuit: RC low-pass (R in series, C to ground) with output at node `out`
- Input: PULSE source with period `T` and ON-time `Ton`
- Simulation: transient (`.tran`)

---

## 3. Definitions
### 3.1 Periodic steady state
Periodic steady state means the waveform repeats from cycle to cycle.
Practical check: the last two cycles of `V(out)` look identical.

### 3.2 Metrics
We measure during the ON-time of the **last** cycle, excluding edge margins.

**Metric A — Vpp_on (V)**
Ripple-like variation during the ON window:
Vpp_on = max(V(out)) - min(V(out)) measured over the defined ON measurement window.

**Metric B — Vend_on (V)**
Output voltage near the end of the ON window (useful for RC behavior):
Vend_on = V(out) sampled at the end of the ON measurement window.

---

## 4. Simulation stop-time rule (to reach periodic steady state)
Let:
- tau = R * C
- T = input period

Choose:
Tstop = max(10 * tau, 5 * T)

This ensures startup transient effects decay and we have multiple cycles to verify repeatability.

---

## 5. Measurement window rule (last-cycle ON window)
Let:
- Tstop = transient stop time
- T = pulse period
- Ton = pulse ON-time
- t_margin = edge-exclusion margin

Compute the start of the last cycle:
t_cycle_start = Tstop - T

Define the ON measurement window:
t_start = t_cycle_start + t_margin  
t_end   = t_cycle_start + Ton - t_margin

All measurements for Vpp_on and Vend_on must be computed using only data in [t_start, t_end].

### 5.1 Edge-exclusion margin
Edges (rise/fall) can create spikes and measurement artifacts.
We exclude them using a fixed margin.

Recommended default:
t_margin = 50 us

(We can adjust this if timestep or rise time changes.)

---

## 6. Example (reference case)
Given:
- R = 98 ohm
- C = 10 uF
- tau = R*C = 0.98 ms
- Pulse: T = 20 ms, Ton = 10 ms

Stop-time rule:
- 10*tau = 9.8 ms
- 5*T = 100 ms
=> Tstop = 100 ms (or higher)

Window:
- t_cycle_start = Tstop - T
- t_start = (Tstop - 20ms) + 50us
- t_end   = (Tstop - 20ms) + 10ms - 50us

Metrics:
- Vpp_on = max(V(out)) - min(V(out)) over [t_start, t_end]
- Vend_on = V(out) at t_end

---

## 7. Notes
- If the last two cycles are not identical, increase Tstop and rerun.
- If using a different waveform name, always reference it as V(out) where `out` is the node name.
