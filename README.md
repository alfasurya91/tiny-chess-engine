[![Competition](https://make.fun/badge/tiny-chess-01.svg)](https://make.fun/tiny-chess-01) [![Rank](https://make.fun/badge/tiny-chess-01/alfasurya91/tiny-chess-engine/rank.svg)](https://make.fun/tiny-chess-01/alfasurya91/tiny-chess-engine) [![Reward](https://make.fun/badge/tiny-chess-01/alfasurya91/tiny-chess-engine/reward.svg)](https://make.fun/tiny-chess-01/alfasurya91/tiny-chess-engine)
Bounty: [make.fun/bounty/tiny-chess-01](https://make.fun/bounty/tiny-chess-01)

# tiny-chess-01

A 4,096-byte chess engine. `main.py` (3,540 bytes) is the whole engine:
an LZMA + base85 payload unpacked and executed at startup.

## Engine

- Board: 0x88, move generation with make/unmake (castling, en passant,
  promotion). Verified by perft (startpos d1–d4: 20/400/8902/197281).
- Search: negamax with alpha-beta pruning, MVV-LVA move ordering,
  captures-only quiescence with stand-pat.
- Evaluation: material (P 100, N 320, B 330, R 500, Q 900) + middlegame
  piece-square tables, from the side-to-move perspective.
- Budget: iterative deepening with a FIXED node cap of 20,000 nodes/move
  (deterministic — no clock, no unseeded randomness). The best move of
  the last fully completed depth is played. ~13k–27k nodes/sec, so a
  move takes under 2 seconds on one CPU.
- Protocol: talks to the env alone over HTTP (`/game`, `/move`, `/resign`
  at `MAKEFUN_ENV_URL`), standard library only, no other files read.

## Size rule

- `main.py` is the whole engine: at most 4,096 bytes as committed,
  Python 3.13, standard library only. The compressed payload and the
  unpacker both count toward the 4,096 bytes.
- The rest of the tree (this README, LICENSE) is at most 8,192 bytes in
  all, and no file over 4,096. MIT licence.
- Search to a fixed node count, never a clock: a final contender's
  trials are replayed and must play the same moves.

## Grade it locally

From inside the competition's dev kit:

    makefun grade-local --kit . --bot <your repository> --driver docker

It plays a practice trial against the house engines, replays it, and
prints the Elo the scorer gives it. Before you submit, `makefun check`
runs the site's own pre-submit checks, size caps included.
