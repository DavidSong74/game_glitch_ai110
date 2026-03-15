# 💭 Reflection: Game Glitch Investigator

Answer each question in 3 to 5 sentences. Be specific and honest about what actually happened while you worked. This is about your process, not trying to sound perfect.

## 1. What was broken when you started?

When the game first ran, it appeared to work on the surface — it showed a number range, accepted guesses, and displayed hints — but nearly every mechanic was subtly wrong. The difficulty settings were inverted: Hard used the range 1–50 (smaller and easier than Normal's 1–100), and Easy was given fewer attempts (6) than Normal (8), which is backwards. The hints were broken in a hidden way: on every even-numbered attempt, the secret number was secretly converted to a string, causing comparisons like `"9" > "70"` to evaluate as `True` due to lexicographic ordering, which flipped the "Go higher/Go lower" messages at random. The scoring system deducted 5 points for every wrong guess, meaning a player could quickly reach a negative score just by playing normally.

---

## 2. How did you use AI as a teammate?

I used Claude Code (Claude Sonnet 4.6) as the AI tool on this project. One example of a correct AI suggestion was identifying the root cause of the inverted hints: the code was intentionally converting the secret to a string on even attempts (`secret = str(st.session_state.secret)`), and Claude correctly traced how Python's lexicographic string comparison (`"9" > "70"` is `True`) caused the flip — I verified this by mentally tracing a guess of 9 against a secret of 70. One example where I had to verify carefully was the new game reset: at first glance the new game handler looked correct (it did reset `history = []`), but the real issue was that the text input key (`guess_input_{difficulty}`) never changed between games of the same difficulty, so the old typed value persisted — Claude identified that adding a `game_count` counter to the key would force Streamlit to treat it as a fresh widget.

---

## 3. Debugging and testing your fixes

To decide whether a bug was really fixed, I traced through the code manually with concrete example values (e.g., guess = 9, secret = 70) before and after the fix, and checked that the output matched expectations. For the scoring fix, I verified that after removing the wrong-guess deductions, a player starting at 0 could never go negative — only win events add points, and they're capped at a minimum of 10. For the difficulty ranges, I confirmed the fix by checking that the ordering made logical sense: Easy (1–20, 10 attempts) is the most forgiving, Normal (1–50, 7 attempts) is medium, and Hard (1–100, 5 attempts) is the most challenging in both range and attempt count. Claude also helped explain why the `st.form` fix resolves the "Enter key doesn't submit" issue — Streamlit's `st.form_submit_button` hooks into the form's submit event, unlike a plain `st.button`.

---

## 4. What did you learn about Streamlit and state?

In the original app, the secret number was initialized with `if "secret" not in st.session_state`, which correctly runs only once — so the secret itself was stable. The real state problem was with `attempts`, which was initialized to `1` on fresh load but reset to `0` on new game, creating an off-by-one inconsistency that made the attempt counter display incorrectly from the very first guess. Streamlit "reruns" means the entire Python script executes from top to bottom every time the user interacts with anything — clicks a button, types in a field, changes a selectbox. `st.session_state` is a dictionary that persists across these reruns, so it's the only way to remember information (like the secret number or guess history) between interactions. The key fix for stable state was initializing `attempts` to `0` consistently and using `game_count` to ensure the text input widget gets a new key — and therefore a fresh empty value — whenever a new game starts.

---

## 5. Looking ahead: your developer habits

One habit I want to reuse is tracing through code with concrete example values before and after a fix — especially for logic bugs like the string comparison inversion, which would be very hard to spot just by reading the code without running a mental test case. Next time I work with AI on a coding task, I would ask the AI to explain *why* each suggested change fixes the problem, not just what to change, so I can verify the reasoning rather than taking the fix on faith. This project changed how I think about AI-generated code: it can look completely plausible and even run without errors while containing multiple subtle logic flaws, so treating AI output as a first draft that requires careful human review — not a finished product — is essential.
