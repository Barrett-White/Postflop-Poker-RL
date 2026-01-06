# Postflop Poker RL

This project implements a simplified heads-up Texas Hold’em environment and a tabular Q-learning agent that learns post-flop decision making. The game uses a fixed-limit betting structure and only models play on the flop, turn, and river.

The goal was to study how a basic reinforcement learning agent behaves in a noisy, imperfect-information setting without relying on deep learning.

---

## What This Includes

- A poker hand simulator (deck, dealing, board reveal)
- Hand evaluation for 5, 6, or 7 visible cards
- A custom RL environment with betting rules and raise caps
- A tabular Q-learning agent
- Several simple opponent strategies for testing
- Training loops with win-rate and reward tracking

---

## Game Setup

- Two-player (heads-up) Texas Hold’em
- Standard 52-card deck
- Only post-flop play is modeled
- Streets: Flop, Turn, River
- Fixed-limit betting:
  - Flop bet size: 1
  - Turn bet size: 2
  - River bet size: 4
- Maximum of 4 raises per street

Actions available to the agent:
- Fold
- Check/Call

---

## Pre-Flop Handling

Pre-flop betting is not explicitly modeled.  
Instead, hands are filtered before dealing:

- Player 1 must have a playable small-blind hand
- Player 2 must have a playable big-blind hand

This avoids training on obviously bad hands and keeps the focus on post-flop decisions.

---

## State Representation

The agent uses a discretized state for tabular Q-learning.  
Each state includes:

- Hand category (high card, pair, two pair, etc.)
- Simple board texture flags (paired, monotone)
- Current street (flop, turn, river)
- Whether the agent is facing a bet

This keeps the state space small enough for tabular learning.

---

## Reward

Rewards are based on net profit for Player 1:

- Winning returns pot minus invested chips
- Losing returns negative invested chips
- Ties split the pot

Rewards are only finalized at fold or showdown.

---

## Opponents

Three opponent types are included:

- Random: chooses valid actions uniformly
- Conservative: only bets with pair or better
- Aggressive: frequently bets and bluffs

These are used to test whether the agent learns exploitative behavior.

---

## Training

The training loop:
- Runs repeated hands
- Updates the Q-table
- Uses epsilon-greedy exploration
- Tracks win rate and average reward over time

Plots are generated to visualize learning progress.

---

## Limitations

This is a simplified environment by design.

- No pre-flop betting
- Fixed bet sizes only
- Coarse state abstraction
- No equity calculation
- Tabular Q-learning instead of neural networks

The goal is clarity and control, not realism.

---

## Possible Extensions

- Add pre-flop actions
- Add pot odds or stack-to-pot ratio features
- Detect draws (flush, straight)
- Replace Q-learning with DQN or actor-critic
- Add self-play training
