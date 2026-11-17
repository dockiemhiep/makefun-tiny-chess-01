# Engine log

- Starter copied verbatim from the kit: practice trial 0 scored 0-0-6,
  Elo 0; live 2.8 s; replay and L1 regrade matched byte for byte.
- v1 (3,245 B): independent 0x88 alpha-beta/PVS engine, 60k fixed nodes.
  Trial 0: W depth-2/depth-3/depth-4, L 100k/250k/500k; 3-0-3,
  Elo 1,279, 326 spare plies, live 228.1 s; replay and L1 matched.
- v2 (3,260 B): 180k nodes. Trial 0: W through nodes-100k,
  L 250k/500k; 4-0-2, Elo 1,451, 400 spare plies, live 575.0 s;
  replay and L1 matched. Keeper (+172 Elo vs v1).
- v3 (3,347 B): null-move at 180k. Trial 0: same 4-0-2 and Elo
  1,451, but 418 spare plies and live 509.6 s; replay/L1 matched.
  Beat v2 1.5-0.5 in a colour-swapped 60k self-match. Keeper.
- v4 (3,357 B): 300k nodes. Trial 0 swept 6-0, Elo 2,112 cap,
  509 spare plies, live 952.2 s, replay/L1 matched. Custom 8-game
  stress: 6-0-2 (lost 1M/2M), 1,251 s. Actual-budget match vs v3: 1-1.
  Practice instance 1: 4-1-1, but one 300k move lost on time; reject.
- v5 (3,380 B pre-golf): 300k nodes below 36 root moves, 200k otherwise.
  Trial 0 W depth-2/depth-3/depth-4/100k/500k,
  D 250k; 5-1-0, Elo 1,809, 496 spare plies, live 842.0 s; replay and
  L1 matched. Practice instance 1 live: 6-0-0 in 549 s, no timeout.
  Final-shape custom stress: v4 was 6-0-2 in 1,251 s; v5 is safer in
  wide positions. Keep v5.
- Golf (3,366 B): removed only the always-on null-move experiment flag.
  Same move in all 4 fixed positions; colour-swapped 40k match 1-1
  (Black won both otherwise identical 160-ply games). Final trial 0:
  5-1-0, Elo 1,809, 496 spare plies, live 846.1 s; replay and L1 regrade
  matched byte for byte, with no timeout.
