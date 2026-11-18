# Chess fix round 3 - variant J (insurance entry)

Baseline 68f65de: 8g CPU 480-514s, max 2,628ms, Elo mean 1,721.7. At the TRUE
grader factors (bot 3.76-3.92x, house 3.46x - the timed-out dev trial 01a1ebf5
played 6 games, not 8) it times out in the 8-game final.

## Implemented (on top of 68f65de speedups)
Incremental per-side non-pawn counts PS. choose() receives game; games 1-3 use
65k nodes; abs(previous root score) > 650 caps the next move at 45k. TN
cumulative governor replaces Y>350: c=(len(moves)-8)//2,
R=96*(8-game)+max(1,96-c), budget = max(35k,(42M-TN)//rem_est), hard cap
41,999,999-TN-35,001*(R-1). Proof: 768 searched turns use <= 42M nodes incl.
stop-node overruns. No wall clock anywhere - replays deterministic.

## Screens (Docker python:3.13-slim --cpus 1, 8-game final format)
Variant B: CPU 292-310s, max 1,668ms, Elo mean 1,486, margin +161 [+87.9,+234.1].
Variant J: CPU 284/298/304s, max 1,747/2,116/1,916ms, Elo 1,598/1,669/1,598
(mean 1,621.7), margin +296.7, CI [+226.1,+367.2]. Best strength fitting the wall; perft exact.

## True-factor projections for J (all real kit limits pass)
Final wall 1,203-1,327s vs 1,800s cap; dev wall 894-999s; max move
2,116 x 3.92 = 8,295ms vs 10,000ms; beats sunfish-4k with CI above 0 (the
prize rule). J misses only round-3 internal proxy floors (Elo 1,650 /
margin +300 / max-move 1,800ms) - stacked safety margins, not kit rules.
Replay: byte-identical transcripts t0/t1/t2 in clean Docker runs (t2 hash
066a5092... twice, ~40 min apart). Two t2 runs were cut by single 11.5s /
37.2s host-stall spikes (Mac load ~4, Roblox Studio + screen sharing);
move choices never diverged - agy confirmed host artifacts.

## Packaging fix (agy P0, debate-chess-j-agy.md)
39f97da stripped the mandatory live badges from README.md to fit the
snapshot; server intake enforces repo_readme (RULES.txt:292, HTTP 422).
README restored from d8ed426 (705 B); these notes condensed to fit the
8,192 B snapshot. main.py (variant J, 4,057 B) unchanged.
