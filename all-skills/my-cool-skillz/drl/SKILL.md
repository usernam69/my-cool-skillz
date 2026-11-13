---
name: drl
description: Use when the user types /drl or asks to build a new game for AI models to compete in, then train them with deep reinforcement learning and play against the trained AI
---

# DRL

## Overview
Make a new two-player game, have several AI models compete in it, train them with deep reinforcement learning on the user's PC, then let the user play against the best trained AI, shown as a ghost.

## Steps
1. **Ask setup questions in one round** (AskUserQuestion when available):
   - Which OpenRouter models to use. The user can type custom model names.
   - How many AIs per model. Default 5.
   - Spending cap for the AI that builds the game (OpenRouter calls). 
2. **Build the game with an AI builder.** Ask one OpenRouter model to write a small two-player game in Python, inside a scratch folder. It must follow one interface: `reset()`, `legal_actions()`, `step(action)`, `observation()`, and `reward()`. Run a quick check that random play finishes without errors before going on.
3. **Set up the training environment.** Make a venv in a scratch folder and install only into it (PyTorch and numpy, or the fewest libraries that work). Never install into the user's real system.
4. **Train with self-play.** Each model type gets its own AIs, and they play each other. Use a simple deep RL method such as DQN or PPO. Print progress every few hundred games: win rates and the average reward for each model. Save checkpoints in the scratch folder.
5. **Report the results.** Win rate per model, the game built, the number of training games, and the path to the best checkpoint.
6. **Let the user play.** Make a small playable version (terminal or a simple window) where the user plays the game against the best trained AI. Show the trained AI's past moves as a faint "ghost" overlay, so the user can see what it learned.

## Build plan
- **Project folder:** `C:\Users\Dovi\Downloads\my name is claude\drl`. Create it if missing.
- **Venv:** `drl\venv`. Install PyTorch (CPU build from `download.pytorch.org/whl/cpu`) and numpy into it only. Never install into the system Python.
- **Layout:** `game_builder.py` (OpenRouter call, $1 cap, sandboxed test run), `train.py` (self-play DQN or PPO, checkpoints in `drl\checkpoints`), `play.py` (terminal game against the best checkpoint, with the ghost overlay).
- **Core order:** game builder, then training, then play. Test training on a small built-in game before the builder is wired up, so training can be checked even without a key.
- **Key:** read `OPENROUTER_API_KEY` from the environment. If it's missing, stop and say so. Never ask for it in chat.

## Building the project from the mega idea list
When the user asks to build all the DRL ideas (the list is below, and no other chat has it):
1. **Build the core first** (steps 1–6 above). Nothing else starts until the core runs.
2. **Then go through the ideas in list order,** one at a time. Report each one in one line: `Idea N: <name> - built | partly built | not built - <why>`.
3. **Ideas that need outside services** (streaming, shared community pools, phone apps, scheduled overnight runs): build the local part instead, such as a saved file the user can upload, and say what's still missing.
4. **Read the OpenRouter key from the `OPENROUTER_API_KEY` environment variable.** Never ask the user to paste it into chat, and never write it into a file.
5. **Keep it to several rounds.** Stop after each batch of about 10 ideas, report progress, and wait for the user before going on.

### The ideas (in order)
Quick wins:
1. **Win-rate chart**: a live chart of each model's win rate as training runs.
2. **Game picker**: choose from a few game types (tic-tac-toe, Connect Four, a grid race) before building.
3. **Cost meter**: show the running OpenRouter spend next to the cap.
4. **Dry run**: play 10 random games first to check the built game before training.
5. **Model presets**: save a favorite list of OpenRouter models so you don't retype them.
6. **Resume training**: pick up from the last checkpoint instead of starting over.
7. **Seed control**: set a random seed so a run can be repeated exactly.
8. **Sound effects**: a beep on each win or loss during the playable ghost match.
9. **Scoreboard**: a simple table of wins, losses and draws for every model.
10. **Replay file**: save the best game's moves as text you can read later.
11. **Time limit**: stop training after N minutes, not just after N games.
12. **Progress bar**: a text bar showing how far training has gotten.
13. **Preflight check**: confirm the venv, libraries and API key work before starting.
14. **Keep the ghost**: save the ghost's moves so it still works after the run ends.

Bigger features:
15. **Game designer**: the builder AI proposes three game rules, and the user picks one.
16. **Tournament mode**: every trained model plays every other one in a round-robin.
17. **Head-to-head**: pit two saved checkpoints against each other, not just the latest.
18. **Curriculum**: start with a simple version of the game and make it harder as the AIs improve.
19. **Opponent pool**: keep old checkpoints and sometimes train against them, so the AIs don't forget older strategies.
20. **Explain mode**: after each game, the AI writes one sentence about its key move.
21. **Web viewer**: a local page that plays the ghost match in a browser.
22. **Two-player human mode**: two people play on the same keyboard against each other.
23. **Export**: package the trained model and game as a single file someone else can run.
24. **Hyperparameter sweep**: try several learning rates and report which one trained best.
25. **Leaderboard across runs**: keep results from past training runs and compare them.
26. **Auto-tune cap**: lower the builder's spending as the game gets closer to working.
27. **Multi-game mode**: train on several game types at once and see which one the AIs learn fastest.
28. **Replay of training**: a fast-forward replay of the AIs' games across training time.
29. **Mutation test**: change one game rule and check that the AIs notice and adapt.
30. **Reward shaping**: give partial credit for good moves, not just the final win.

Wild ideas:
31. **AI commentator**: a model narrates each game like a sports announcer.
32. **Evolve the game**: the builder rewrites the rules based on how the AIs play.
33. **Model draft**: the builder picks which OpenRouter models to field, based on early results.
34. **Blind ghost**: the ghost stays hidden until the user loses, then reveals its strategy.
35. **Tiny arcade**: wrap the whole thing in a retro arcade menu.
36. **Trash-talk mode**: the losing AI writes a short message to the winner.
37. **Dream match**: pit the trained AIs against a chess engine on a small board.
38. **Pixel art board**: draw the game board as a pixel-art grid the user can recolor.
39. **Stream it**: broadcast the AIs playing live to a stream.
40. **Gamble mode**: the user bets on which AI wins before each game.
41. **Mirror match**: the same AI plays both sides, to test whether it's just memorizing.
42. **Handicap**: the human gets a head start to make the match fair.
43. **Mood ghost**: the ghost's color changes with its win rate.
44. **Story mode**: each model is a character with a backstory that changes as it wins or loses.
45. **Phone version**: a version the user can play on a phone.
46. **Teach me**: the trained AI explains its strategy to the user in plain English.
47. **Overnight run**: schedule training to run while the user sleeps, and show the results in the morning.
48. **Cheating test**: check whether an AI learns to exploit a bug in the builder's game.
49. **Board size slider**: change the board size and watch how the AIs adapt.
50. **Community pool**: share checkpoints with other users and compete against theirs.
51. **Speedrun**: time how fast each model learns to beat a fixed target.
52. **Ghost race**: race the user's own past games against the ghost.
53. **Noise test**: add random mistakes to the game and see which AI handles them best.
54. **Meta probe**: have the AIs design a game rule for the user to test.

## Common Mistakes
- Going past the spending cap. Check the running cost after every builder call.
- Installing libraries into the user's system Python instead of the venv.
- Training with no progress output, so the user can't tell it's working.
- Claiming an AI is "smart" without win rates to back it up.
- Skipping the quick test of the built game before training.
