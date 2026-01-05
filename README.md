# Postflop-Poker-RL

A simplified **Heads-Up Texas Hold’em** environment + a **tabular Q-learning** agent trained to make post-flop decisions (Flop/Turn/River) under a **fixed-limit** betting structure. The project includes:

- A full poker hand simulator (deck, dealing, board reveal by street)
- Hand evaluation (best 5-card hand from 5/6/7 visible cards)
- Pre-flop “GTO-style” playable-hand filters (to avoid training on junk hands)
- A custom RL environment with capped raises and action masking
- Multiple scripted opponents to test exploitative learning
- A training loop with win-rate and reward tracking + visualization

---

## Project Structure (Logical)

While this repo may be in a notebook, the code is organized into these components:

1. **Card & Game Utilities**
   - Deck creation and shuffling
   - Dealing hole cards + board
   - Card/hand/board string formatting

2. **Poker Mechanics**
   - Streets: FLOP / TURN / RIVER and visible board slicing
   - Fixed-limit bet sizes by street
   - Action space: `FOLD`, `CHECK_CALL`, `BET`

3. **Hand Evaluation**
   - Evaluate 5-card hands (`evaluate_5`)
   - Evaluate best hand from hole + visible board (`evaluate_best_from_visible`)
   - Showdown comparison (`compare_players`)

4. **Environment**
   - `PokerEnv`: handles state, legal actions, betting logic, raise cap, street transitions, showdown reward

5. **Agent**
   - `QLearningAgent`: tabular Q-learning with discretized state representation

6. **Opponents**
   - `RandomOpponent`, `ConservativeOpponent`, `AggressiveOpponent`

7. **Training**
   - `train_and_plot(...)`: runs episodes, updates Q-table, decays epsilon, plots win rate and average reward

---

## Rules of the Game (Simplified Heads-Up Hold’em)

### Cards
- Standard 52-card deck represented as integers `0..51`
  - `rank = card % 13` (2..A mapped to `0..12`)
  - `suit = card // 13` (♣♦♥♠ mapped to `0..3`)

### Streets
- Only post-flop play is modeled:
  - **Flop**: 3 community cards revealed
  - **Turn**: 4th revealed
  - **River**: 5th revealed → showdown

### Betting (Fixed Limit)
- Fixed bet sizes:
  - Flop: `1`
  - Turn: `2`
  - River: `4`

### Actions
- `FOLD`: concede the pot
- `CHECK_CALL`: check if no bet, call if facing a bet
- `BET`: place a bet (amount determined by the street)

### Raise Cap
- `MAX_RAISES = 4` per street
- Action masking prevents betting when capped

---

## Pre-Flop Filter (GTO-Inspired)

To make training more realistic and reduce wasted learning on trash starting hands:

- The environment re-deals until:
  - Player 1 hand is “playable” under SB range (`is_sb_playable`)
  - Player 2 hand is “playable” under BB range (`is_bb_playable`)

This approximates pre-flop folding without explicitly simulating pre-flop action.

---

## Observation / State

The environment returns an observation dictionary for Player 1 (the agent):

```python
{
  "my_hand": (c1, c2),
  "board": [...],              # visible board cards for current street
  "pot": pot_size,
  "stacks": [p1_stack, p2_stack],
  "street": Street.FLOP|TURN|RIVER,
  "active_bets": {0: amt, 1: amt}   # current street contributions
}
