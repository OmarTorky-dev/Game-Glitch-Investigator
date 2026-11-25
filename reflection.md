# 💭 Reflection: Game Glitch Investigator

Answer each question in 3 to 5 sentences. Be specific and honest about what actually happened while you worked. This is about your process, not trying to sound perfect.

## 1. What was broken when you started?

The first time I ran `python -m streamlit run app.py`, the game loaded without any crash: a title, a sidebar with a difficulty picker, a text box, "Submit Guess" and "New Game" buttons, and a "Developer Debug Info" expander that shows the secret number. Everything *looked* fine, but once I started guessing with the debug panel open, the hints, the attempt counter and the New Game button were all misbehaving. I played two games (Normal with secret 50, and Hard with secret 30) and wrote down what I expected versus what actually happened.

**Bugs I noticed (expected vs. actual):**

1. **The hints lie on every other guess.** With the secret at 50, I expected a guess of 60 to say "Go LOWER!", but it said "📈 Go HIGHER!". A guess of 40 on the next turn gave the correct hint, so the bug only shows up on even-numbered attempts. On those turns the app turns the secret into a string, so the numbers get compared alphabetically (`"9" > "50"`) and the hint messages are swapped. *Note:* in the original starter code the messages were backwards on **every** guess ("Too High" → "Go HIGHER!"). I had already flipped the messages in the main branch of `check_guess` before logging this, which is why my playthrough only showed the bad hint on even attempts. The string-comparison fallback still had them backwards.
2. **New Game doesn't start a new game.** After losing, I expected "New Game" to let me play again. Instead the app kept showing "Game over. Start a new game to try again." and ignored every guess, because `status` is never reset to `"playing"`. New Game also sets attempts to 0 instead of 1, always picks the secret from 1–100 regardless of difficulty, and doesn't clear the history.
3. **You get fewer attempts than promised.** On Normal the sidebar says 8 attempts allowed, but the game ended with "Out of attempts!" after my 7th submission. `attempts` starts at 1 instead of 0. The "Attempts left" banner also shows the count from *before* your last guess (it still said 7 after my first guess).
4. **Difficulty and range don't line up.** Hard uses 1–50, which is *easier* than Normal's 1–100, and the main banner always says "Guess a number between 1 and 100" even on Easy or Hard.
5. **Score goes haywire.** A wrong "Too High" guess *adds* +5 points on even attempts and subtracts 5 on odd attempts, so the score bounced between 0 and 5 while I was guessing wrong.

**Bug Reproduction Log**

| Input | Expected Behavior | Actual Behavior | Console Output / Error |
|-------|-------------------|-----------------|------------------------|
| Normal, secret = 50, 1st guess `60` (attempt #2 internally) | "Too High" → "📉 Go LOWER!" | "📈 Go HIGHER!" shown; score went **up** from 0 to 5 | none |
| Normal, secret = 50, guess `9` on an even attempt | "Too Low" → "Go HIGHER!" | Outcome recorded as "Too High" (string compare `"9" > "50"`); score +5 | none |
| Normal, secret = 50, guesses `60, 40, 9, 70, abc, 45, 55` | 8 attempts allowed, game still running after 7 guesses | "Out of attempts! The secret was 50. Score: 0" after the 7th guess | none |
| After losing, click **New Game 🔁**, then guess `10` | Fresh game: status reset, history cleared, guesses accepted | Still shows "Game over. Start a new game to try again."; guess ignored; history still holds old guesses | none |
| Select **Hard** in sidebar | Larger range than Normal; banner shows the Hard range | Sidebar says "Range: 1 to 50" (smaller than Normal's 1–100); banner still says "between 1 and 100" | none |
| Fresh Normal game, first guess submitted | "Attempts left" drops from 8 to 7 | Starts at 7 before any guess, still shows 7 after the first guess | none |

None of these bugs crash the app or print anything in the terminal. They are all silent logic bugs, which is why they're easy to miss. Separately, running `pytest` at the start failed all 3 tests with `NotImplementedError: Refactor this function from app.py into logic_utils.py`, because `logic_utils.py` is still a stub.

---

## 2. How did you use AI as a teammate?

- Which AI tools did you use on this project (for example: ChatGPT, Gemini, Copilot)?
- Give one example of an AI suggestion that was correct (including what the AI suggested and how you verified the result).
- Give one example of an AI suggestion you did not accept as written (including what the AI suggested, why you rejected or changed it, and how you verified your version). It does not have to be a suggestion that was wrong: over-engineered, out of scope, harder to read, or a poor fit for this codebase all count.

---

## 3. Debugging and testing your fixes

- How did you decide whether a bug was really fixed?
- Describe at least one test you ran (manual or using pytest)  
  and what it showed you about your code.
- Did AI help you design or understand any tests? How?

---

## 4. What did you learn about Streamlit and state?

- How would you explain Streamlit "reruns" and session state to a friend who has never used Streamlit?

---

## 5. Looking ahead: your developer habits

- What is one habit or strategy from this project that you want to reuse in future labs or projects?
  - This could be a testing habit, a prompting strategy, or a way you used Git.
- What is one thing you would do differently next time you work with AI on a coding task?
- In one or two sentences, describe how this project changed the way you think about AI generated code.
