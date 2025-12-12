# Detection Logic — Connection State Correlation (FortiSIEM)

## Problem
Traditional alert logic triggered whenever a MISP malicious IP attempted a connection, even if the firewall blocked it immediately. This generated a large volume of false positives.

---

## Goal
Trigger alerts **only when a malicious IP successfully establishes a connection**, not when the firewall blocks it.

---

## Required Events
- `connection_open`
- `connection_established`
- `connection_closed` / `connection_dropped`

---

## High-Level Logic

### ✔ Trigger IF:
- `connection_open`  
**followed by:**  
- `connection_established` (within 30 seconds)

### ❌ Ignore IF:
- `connection_open` → `connection_closed/dropped` (within 10 seconds)

---

## Why This Reduces False Positives
- Eliminates noise from blocked attempts  
- Surfaces only REAL threats  
- Improves analyst triage  
- Increases SOC efficiency
