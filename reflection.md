# 💭 Reflection: Game Glitch Investigator

Answer each question in 3 to 5 sentences. Be specific and honest about what actually happened while you worked. This is about your process, not trying to sound perfect.



## 1. What was broken when you started?

- What did the game look like the first time you ran it?
- List at least two concrete bugs you noticed at the start  
  (for example: "the secret number kept changing" or "the hints were backwards").

---

## Bugs Documented in app.py

| # | Location | Description |
|---|----------|-------------|
| 1 | `check_guess`, lines 37–40 | **Backwards hints** — when `guess > secret` the message says "Go HIGHER!" but the player should go LOWER. The two messages are swapped. |
| 2 | Submit block, lines 158–163 | **Type mismatch on even attempts** — on every even attempt, `secret` is cast to a `str`. Comparing an `int` guess to a `str` secret makes correct answers nearly impossible on those turns. |
| 3 | `get_range_for_difficulty`, line 10 | **Hard difficulty is easier than Normal** — Hard returns the range 1–50 while Normal returns 1–100, meaning Hard has a *narrower* (easier) range despite its name. |
| 4 | Session-state init, line 96 | **Attempts counter starts at 1 instead of 0** — `st.session_state.attempts = 1` means the first submit increments to 2, silently costing the player one full attempt. |
| 5 | New game handler, line 136 | **New game ignores selected difficulty** — the New Game button always calls `random.randint(1, 100)` instead of using the `low`/`high` values for the chosen difficulty. |
| 6 | Info banner, line 110 | **Range display is hardcoded** — the UI always shows "between 1 and 100" regardless of difficulty, so Easy and Hard players see wrong range information. |
| 7 | `update_score`, lines 57–60 | **Wrong guess awards points** — when the outcome is "Too High" and the attempt number is even, the score increases by +5, rewarding an incorrect guess. |

---

---

## 2. How did you use AI as a teammate?

- Which AI tools did you use on this project (for example: ChatGPT, Gemini, Copilot)?
- Give one example of an AI suggestion that was correct (including what the AI suggested and how you verified the result).
- Give one example of an AI suggestion that was incorrect or misleading (including what the AI suggested and how you verified the result).


Claude was used for this assignment. One Ai suggestion that the higher and lower messages were swapped were correct, and it was verified via testing after running the app. Ai was used here to debug and also clean up the writing and comments across all files, including this one! 

---

## 3. Debugging and testing your fixes

- How did you decide whether a bug was really fixed?
- Describe at least one test you ran (manual or using pytest)
  and what it showed you about your code.
- Did AI help you design or understand any tests? How?

A bug was considered fixed when the game behaved correctly across multiple manual test cases, instead of only one. For example, after swapping the hint messages, I tested guesses that were too high, too low, and exactly correct to confirm all three paths worked. I also tested the type mismatch bug by deliberately submitting a correct answer on an even attempt before the fix, which showed the game failing to recognize it as a win. A later bug surfaced through play-testing: after winning or losing, clicking New Game left `st.session_state.status` as `"won"` or `"lost"`, so the game immediately halted on every rerun and blocked further guesses — fixed by resetting `status` and `history` in the New Game handler. Claude helped identify which code paths to trace and explained why comparing an int to a str in Python returns False rather than raising an error, which made the bug harder to spot.

---

## 4. What did you learn about Streamlit and state?

- In your own words, explain why the secret number kept changing in the original app.
- How would you explain Streamlit "reruns" and session state to a friend who has never used Streamlit?
- What change did you make that finally gave the game a stable secret number?

The secret number kept changing because Streamlit reruns the entire Python script from top to bottom every time a user interacts with the page — clicking a button, typing in a field, or changing a dropdown all trigger a rerun. Without session state, `random.randint()` would be called fresh each rerun, generating a brand new number every time. Think of it like refreshing a webpage that regenerates its content on every load. The fix was wrapping the secret generation in a `if "secret" not in st.session_state` guard, so the number is only created once and then stored in `st.session_state` — a dictionary that Streamlit preserves across reruns.

---

## 5. Looking ahead: your developer habits

- What is one habit or strategy from this project that you want to reuse in future labs or projects?
  - This could be a testing habit, a prompting strategy, or a way you used Git.
- What is one thing you would do differently next time you work with AI on a coding task?
- In one or two sentences, describe how this project changed the way you think about AI generated code.

One habit I want to keep is reading code before trusting it,  eveen for AI-generated code. Documenting each bug in a table before fixing anything forced me to slow down and understand the full picture rather than patching one thing and missing three others. Next time, I would ask the AI to explain *why* a fix works rather than just accepting the suggested change, so I can verify the reasoning rather than just the output. This project made clear that AI gen code can have small, interacting bugs that look reasonable on the surface but break in specific edge cases, which means review and testing matter just as much as writing.
