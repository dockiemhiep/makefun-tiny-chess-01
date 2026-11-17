# Chess fix round

Baseline: `6cbad16`, `main.py` 3,366 bytes. No submission was made.

## Changes

- **P0 trial wall:** use 64-square iteration, incremental evaluation caches, and deterministic 220k/160k/125k node budgets (wide/late positions get less). Every clean 8-game M4 trial is now below 800 bot-CPU seconds without using a clock.
- **P1 LMR:** a quiet move is reduced only when the mover was not in check and the move does not give check. The reported position now chooses `d1e1+`, not `e3e2`.
- **P1 repetition:** rebuild two-sided history from `start_fen` plus every game move before the forced-move path. Root detection is live; the regression cycle avoids repeating with `f6g8`.
- **P1 dead positions:** recognize the server's exact insufficient-material cases. A corrected capturable-rook repro chooses `d7e7`, not drawing `d7d8`. (The debate's first FEN did not permit `Kxd8`.)
- **Cheap P2s:** check mate before the 100-halfmove draw (`c2c8#`), disable consecutive null moves, and add king-shield/mop-up evaluation. Optional TT normalization was left unchanged.

## Strength

| Seed | Before W-D-L / Elo | After W-D-L / Elo |
|---|---:|---:|
| t=0 | 6-1-1 / 1,748 | 6-1-1 / 1,748 |
| t=1 | 6-1-1 / 1,748 | 7-0-1 / 1,845 |
| t=2 | 5-0-3 / 1,530 | 6-1-1 / 1,748 |
| mean | 1,675.3 | **1,780.3** |

The after scores lead sunfish-4k by +422/+449/+495 Elo: mean **+455.3**, 90% paired t-interval **[+393.1, +517.6]**.

## Timing

Clean full 8-game CPython 3.13 M4 passes:

| Seed | moves | bot CPU s | mean ms | p95 ms | max ms |
|---|---:|---:|---:|---:|---:|
| t=0 | 402 | 698.034 | 1,736 | 2,669 | 3,042 |
| t=1 | 404 | 739.057 | 1,829 | 2,565 | 2,968 |
| t=2 | 383 | 607.668 | 1,587 | 2,193 | 2,432 |

Before: full t=0 used 1,148.2 bot-CPU seconds; t=2 used 912.5s with a 3,899ms max; custom eight used 1,222.5s. No full t=1 timing survived (six games: 549s). After: no timeout or move over 10s. Fresh replays produced byte-identical state on all seeds (`39c3e0...`, `aa0e56...`, `4fc1ec...`).

## Gates and size

- Official SDK: 15/15. Packed perft: 197,281; 97,862; 43,238; 9,467; 62,379, with full state restoration.
- Packed payload equals its 8,521-byte source (`dca901b8...`).
- `main.py`: **3,366 -> 3,751 bytes**. Repository snapshot: **6,898 -> 7,903 bytes**.
