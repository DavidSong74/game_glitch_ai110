# 🎮 Game Glitch Investigator: The Impossible Guesser

## 🚨 The Situation

You asked an AI to build a simple "Number Guessing Game" using Streamlit.
It wrote the code, ran away, and now the game is unplayable. 

- You can't win.
- The hints lie to you.
- The secret number seems to have commitment issues.

## 🛠️ Setup

1. Install dependencies: `pip install -r requirements.txt`
2. Run the broken app: `python -m streamlit run app.py`

## 🕵️‍♂️ Your Mission

1. **Play the game.** Open the "Developer Debug Info" tab in the app to see the secret number. Try to win.
2. **Find the State Bug.** Why does the secret number change every time you click "Submit"? Ask ChatGPT: *"How do I keep a variable from resetting in Streamlit when I click a button?"*
3. **Fix the Logic.** The hints ("Higher/Lower") are wrong. Fix them.
4. **Refactor & Test.** - Move the logic into `logic_utils.py`.
   - Run `pytest` in your terminal.
   - Keep fixing until all tests pass!

## 📝 Document Your Experience

**Game's Purpose:**
A number-guessing game where the player selects a difficulty level and tries to guess a secret number within a limited number of attempts. Hints guide the player higher or lower, and points are awarded for winning — with more points the fewer attempts used.

**Bugs Found:**
- Difficulty ranges and attempt limits were inverted (Hard was easier than Easy/Normal)
- Hints ("Go higher/Go lower") flipped on even-numbered attempts because the secret was cast to a string, causing lexicographic string comparison instead of numeric
- Scoring deducted 5 points per wrong guess, allowing the score to go negative
- The text input retained its value across new games because the widget key never changed
- `attempts` was initialized to `1` instead of `0`, causing an off-by-one error in the attempt counter

**Fixes Applied:**
- Corrected difficulty ranges: Easy (1–20, 10 attempts), Normal (1–50, 7 attempts), Hard (1–100, 5 attempts)
- Removed the `str()` conversion on even attempts so comparisons remain numeric
- Removed wrong-guess point deductions so the score can only increase
- Added a `game_count` counter to the input widget key so it resets as a fresh widget on each new game
- Initialized `attempts` to `0` consistently across first load and new game resets

## 📸 Demo

- Dont know how to attach pics

## 🚀 Stretch Features

- [ ] [If you choose to complete Challenge 4, insert a screenshot of your Enhanced Game UI here]
