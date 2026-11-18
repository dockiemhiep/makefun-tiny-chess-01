# Chess fix round 3 — BLOCKED

Baseline `68f65de`: 8g CPU 479.980/513.962/481.590s, max 2,628ms, Elo 1,669/1,748/1,748. At 3.76–3.92x it times out.

## Implemented

Kept `M/A/X/qorder`; added incremental per-side non-pawn counts `PS`. `choose()` receives `game`; games 1–3 use 65k and `abs(previous root score)>650` caps the next move at 45k. `TN` replaces `Y>350`.

With `c=(len(moves)-8)//2`, `R=96*(8-game)+max(1,96-c)`, budgets use `max(35k,(42M-TN)//rem_est)` plus the cap `41,999,999-TN-35,001*(R-1)`. The reserve proves even 768 searched turns use at most 42M nodes including stop-node overruns. State is node/score/game data only.

## Screens

All use Docker `python:3.13-slim --cpus 1 --memory 512m --pids-limit 64`.

Screens (Elo, CPU/max): 180/150/115 generic t0 1,464, 290.033/1,467; 30-move estimate t0 1,326, 291.505/1,500; 210k/30 t0 1,464, 293.131/1,767; prioritize games 4–6 at 180k t0 1,326, 282.534/1,502; 210k priority stopped at 1,830ms; 195k priority t0 1,464, 285.636/1,604; original 210/150/115 generic t0 1,464, 291.378/1,542. Game-6 caps reached 5W1D practice at 247.837/1,797 but lost final strength.

### Complete tradeoff

Six-game values are measured first-six prefixes; the bot ignores total game count, so paths equal practice.

| Variant/seed | 8g bot/house s | 6g bot/house s | max ms | Elo | vs sunfish |
|---|---:|---:|---:|---:|---:|
| B/0 | 292.502/25.712 | 244.286/9.947 | 1,513 | 1,464 | +138 |
| B/1 | 297.766/42.613 | 249.815/10.517 | 1,501 | 1,530 | +134 |
| B/2 | 309.756/41.535 | 218.019/8.128 | 1,668 | 1,464 | +211 |
| J/0 | 284.066/24.178 | 238.805/8.517 | 1,747 | 1,598 | +272 |
| J/1 | 297.624/34.611 | 247.004/8.928 | 2,116 | 1,669 | +273 |
| J/2 | 303.943/50.303 | 228.984/9.407 | 1,916 | 1,598 | +345 |

B mean Elo **1,486**, margin **+161**, 90% CI **[+87.9,+234.1]**. J mean Elo **1,621.7**, margin **+296.7**, CI **[+226.1,+367.2]**. B passes timing but fails strength; J is best strength but fails both means and max-move.

## True-factor projections

For B, final wall `8g bot*3.76–3.92+135s` is 1,234.8–1,281.6s, 1,254.6–1,302.2s, 1,299.7–1,349.2s. Dev `6g bot*3.76–3.92+house*3.46` is 952.9–992.0s, 975.7–1,015.7s, 847.9–882.8s. Max: `1,668*3.92=6,538.6ms <10,000`.

J final is 1,203.1–1,248.5s, 1,254.1–1,301.7s, 1,277.8–1,326.5s; dev is 927.4–965.6s, 959.6–999.1s, 893.5–930.2s; max is `2,116*3.92=8,294.7ms`.

Packed perft passed 197,281; 97,862; 43,238; 9,467; 62,379 with full `B/S/C/E/H/Z/W/P/K/BS/PS` restoration. Because no screened variant passes every hard target, the conflict rule stopped SDK/check/replays and forbids commit/push. Best variant J is left uncommitted (`main.py` 4,057 bytes).
