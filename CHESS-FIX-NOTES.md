# Chess fix round 2

Baseline: `d8ed426` (the dev grader timed out at 1,800.012s). No submission was made.

## Change

- Kept search deterministic: no wall clock or randomness. Full budgets are 210k nodes normally, 150k in wide positions, and 115k after ply 120; after 350 bot moves in a long trial they become 60% of those values.
- Precomputed slider rays, piece-square scores, and piece values; reused them in move generation, attack tests, incremental evaluation, play, and ordering. Evaluation and capture ordering keep their prior integer semantics.
- Uniform 110k/85k/65k screening scored 1,530 and 1,464 Elo on clean t=0/1 passes; 160k/120k/90k scored 1,396 on t=0; 190k/140k/105k scored 1,464 on clean t=2. These strength losses ruled out a uniform cut.

## Final 8-game trials

Exact CPython 3.13 Docker runtime: `--cpus 1 --memory 512m --pids-limit 64`.

| Seed | before Elo | after W-D-L / Elo | before CPU s | after CPU s | mean / p95 / max ms |
|---|---:|---:|---:|---:|---:|
| t=0 | 1,748 | 6-0-2 / 1,669 | 698.034 | 479.980 | 1,250 / 1,821 / 2,131 |
| t=1 | 1,845 | 6-1-1 / 1,748 | 739.057 | 513.962 | 1,192 / 1,769 / 2,628 |
| t=2 | 1,748 | 6-1-1 / 1,748 | 607.668 | 481.590 | 1,183 / 1,694 / 2,063 |
| mean | **1,780.3** | **1,721.7** | 681.586 | **491.844** | 1,208 / 1,761 / 2,274 |

After scores lead sunfish-4k by +343/+352/+495 Elo: mean **+396.7**, 90% paired t-interval **[+252.9, +540.4]**.

Grader projections with 160s house allowance:

- t=0: `479.980 * 2.5 + 160 = 1,359.950s < 1,800s`.
- t=1: `513.962 * 2.5 + 160 = 1,444.905s < 1,800s`.
- t=2: `481.590 * 2.5 + 160 = 1,363.975s < 1,800s`.

Fresh replays were byte-identical (`9e373bf3...`, `8450492d...`, `ffba0e0b...`). Clean replay CPU/max ms: 481.303/2,137; 517.036/2,148; 486.983/1,898. No timeout or move over 3s.

## Gates and size

- Official SDK: 15/15. Packed perft: 197,281; 97,862; 43,238; 9,467; 62,379, with full state restoration.
- Packed payload equals its 8,973-byte source (`7f32f2fb...`).
- `main.py`: 3,751 -> **3,908 bytes**. Snapshot: 7,903 -> **7,841 bytes**.
