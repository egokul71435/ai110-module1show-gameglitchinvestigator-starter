# 🎮 Game Glitch Investigator: The Impossible Guesser

## 🚨 The Situation

You asked an AI to build a simple "Number Guessing Game" using Streamlit.
It wrote the code, ran away, and now the game is unplayable. 

- You can't win.
- The hints lie to you.
- The secret number seems to have commitment issues.

## 🛠️ Setup

1. Install dependencies: `pip install -r requirements.txt`
2. Run the app: `python -m streamlit run app.py`

## 🕵️‍♂️ Your Mission

1. **Play the game.** Open the "Developer Debug Info" tab in the app to see the secret number. Try to win.
2. **Find the State Bug.** Why does the secret number change every time you click "Submit"? Ask ChatGPT: *"How do I keep a variable from resetting in Streamlit when I click a button?"*
3. **Fix the Logic.** The hints ("Higher/Lower") are wrong. Fix them.
4. **Refactor & Test.** - Move the logic into `logic_utils.py`.
   - Run `pytest` in your terminal.
   - Keep fixing until all tests pass!

## 📝 Document Your Experience

**Game purpose:** A number guessing game where the player tries to guess a randomly chosen secret number within a set number of attempts. The difficulty setting controls the range of possible numbers and the attempt limit.

**Bugs found:**

| # | Location | Bug |
|---|----------|-----|
| 1 | `check_guess` | Hint messages were backwards — "Go HIGHER!" when guess was too high |
| 2 | Submit handler | Secret was cast to `str` on even attempts, breaking integer comparison |
| 3 | `get_range_for_difficulty` | Hard difficulty returned 1–50, a narrower (easier) range than Normal (1–100) |
| 4 | Session state init | `attempts` started at `1` instead of `0`, costing the player one attempt silently |
| 5 | New Game handler | Always called `randint(1, 100)` regardless of selected difficulty |
| 6 | Info banner | Hardcoded "between 1 and 100" regardless of difficulty |
| 7 | `update_score` | "Too High" outcome added +5 points on even attempts instead of subtracting |
| 8 | New Game handler | Never reset `status` or `history`, so the game stayed locked after a win/loss |

**Fixes applied:**

- Swapped the "Go HIGHER!" / "Go LOWER!" messages in `check_guess`
- Removed the `str()` cast; always compare guess to the integer secret
- Changed Hard range to 1–500
- Changed `attempts` initialization to `0`
- Updated New Game handler to use `randint(low, high)`
- Updated info banner to use `{low}` and `{high}` variables
- Removed the even-attempt score bonus; wrong guesses always subtract 5
- Added `status = "playing"` and `history = []` resets to the New Game handler

## 📸 Demo

![Fixed winning game](Screenshot%202026-03-01%20at%202.26.08%20PM.png)

## 🚀 Stretch Features

- [ ] [If you choose to complete Challenge 4, insert a screenshot of your Enhanced Game UI here]
