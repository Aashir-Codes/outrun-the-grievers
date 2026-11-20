# Outrun the Grievers — Multi-Agent Adversarial Search

An Artificial Intelligence mini-project implementing multi-agent search algorithms and state evaluation functions for **Thomas** to collect all escape code pieces (`K`) and reach the Griever Hole (`E`) before sunset while avoiding moving Grievers.

## Project Structure

- `multi_agents.py` — Implementations of all agents and evaluation functions (Q1–Q5):
  - **Q1 (`ReflexAgent`)**: One-step lookahead reflex agent using maze distances to keys/exit and proximity penalties for Grievers.
  - **Q2 (`MinimaxAgent`)**: Multi-agent minimax search supporting any number of Grievers.
  - **Q3 (`AlphaBetaAgent`)**: Minimax with alpha-beta pruning (strict inequality pruning, root alpha updates, multi-Griever pruning).
  - **Q4 (`ExpectimaxAgent`)**: Expectimax search modeling Grievers as uniform random chance nodes.
  - **Q5 (`betterEvaluationFunction`)**: State evaluation function combining score, nearest-target maze distance, MST distance across remaining keys and exit, key count penalty, and close-range Griever danger penalty.
- `game.py` — Core game rules, scoring, and `GameState` interface.
- `maze.py` — Maze parser, movement directions, and cached BFS `mazeDistance`.
- `grievers.py` — Griever agents (`random` and `hunter`).
- `gametrees.py` — Explicit small game trees for verifying search agents by hand.
- `play.py` — Command-line runner for graphical, text, and batch simulations.
- `display.py` — Text and Tkinter graphical displays.
- `util.py` — Utility helpers and data structures.
- `mazes/` — Maze layout files (`duel`, `dead_end`, `arena_small`, `arena_medium`, `arena_big`, `open_glade`).

## Game Rules & Scoring

- **Agents**: Thomas is agent `0`; Grievers are agents `1, 2, ...` moving in turn. One full round by all agents is one **ply**.
- **Scoring**:
  - Each move by Thomas (including `Stop`): `-1`
  - Picking up a code piece (`K`): `+50`
  - Reaching the exit (`E`) after collecting all code pieces (win): `+500`
  - Caught by a Griever or running out of moves at sunset (lose): `-500`

## Usage

### Play Manually
```bash
python3 play.py
```
Use arrow keys or `W A S D` to move, and `Space` to wait.

### Run AI Agents (GUI)
```bash
# Q1: Reflex Agent
python3 play.py -a reflex -l arena_small
python3 play.py -a reflex -l arena_medium
python3 play.py -a reflex -l arena_small -g hunter

# Q2: Minimax
python3 play.py -a minimax -l duel -d 3
python3 play.py -a minimax -l dead_end -d 4

# Q3: Alpha-Beta Pruning
python3 play.py -a alphabeta -l duel -d 3
python3 play.py -a alphabeta -l arena_medium -d 3

# Q4: Expectimax
python3 play.py -a expectimax -l dead_end -d 4

# Q5: Better Evaluation Function
python3 play.py -a expectimax -e better -l arena_big
python3 play.py -a alphabeta -e better -l arena_big
```

### Useful CLI Flags
- `-a <agent>` — Agent to run (`beeline`, `reflex`, `minimax`, `alphabeta`, `expectimax`)
- `-l <maze>` — Maze name (run `python3 play.py --list` to see all mazes)
- `-d <depth>` — Search depth in plies (default: `2`)
- `-e <eval>` — Evaluation function (`score` or `better`)
- `-g <griever>` — Griever behavior (`random` or `hunter`)
- `-n <num>` — Number of games to run (uses seeds `0` to `n-1`)
- `-s <seed>` — Replay a specific random seed
- `-q` — Quiet mode (summary results only, no window)
- `-t` — Text-only display in the terminal (no Tkinter required)
- `--first-move` — Compute only the first move and print states generated

## Verification & Tests

Run the explicit game tree tests:
```bash
python3 gametrees.py
```

Check first-move action and generated state counts:
```bash
python3 play.py -a minimax -l duel -d 2 --first-move        # North, 142 states
python3 play.py -a minimax -l duel -d 3 --first-move        # North, 1161 states
python3 play.py -a minimax -l dead_end -d 4 --first-move    # West, 3653 states
python3 play.py -a alphabeta -l duel -d 2 --first-move      # North, 47 states
python3 play.py -a alphabeta -l duel -d 3 --first-move      # North, 348 states
python3 play.py -a alphabeta -l dead_end -d 4 --first-move  # West, 635 states
python3 play.py -a expectimax -l duel -d 3 --first-move     # North, 1161 states
python3 play.py -a expectimax -l dead_end -d 4 --first-move # East, 3653 states
```

Compare Minimax vs. Expectimax on `dead_end` (20 games, depth 4):
```bash
python3 play.py -a minimax -l dead_end -d 4 -n 20 -q     # 0/20 escapes, avg -502.4
python3 play.py -a expectimax -l dead_end -d 4 -n 20 -q  # 19/20 escapes, avg 444.8
```

Benchmark Q1 and Q5 across mazes:
```bash
python3 play.py -a reflex -l arena_medium -n 10 -q
python3 play.py -a expectimax -e better -l arena_medium -n 10 -q
python3 play.py -a expectimax -e better -l arena_big -n 10 -q
```

## Requirements

- Python 3
- `python3-tk` (optional, only required for the graphical window; `-t` and `-q` work without it)
