# Tinker: Playlist Chaos

> **Time:** ~45 minutes | **Type:** Debugging + AI Collaboration

---

## Overview

You tried to use AI to help automate your playlists. It wrote a Streamlit app that groups songs into mood-based playlists — **"Hype," "Chill," and "Mixed"** — based on song details and a simple user profile.

Unfortunately, it is not behaving as expected:

- Some songs land in the **wrong playlist**
- The **search** feels unreliable
- The **"Lucky Pick"** feature can misbehave
- The **playlist stats** do not always make sense

**Your mission:** Use your AI coding assistant in VS Code to debug, test, and improve this AI-written app until it behaves more reliably. You will explore weird behavior through the UI, trace it back to the code, and make targeted fixes.

---

## Intended Behavior (Your Reference Guide)

Use these rules as your benchmark for how the app should work in a perfect state.

### Song Classification — The "Mood" Engine

| Mood | Condition |
|------|-----------|
| **Hype** | Energy >= `hype_min_energy` (default: 7), OR genre matches user's `favorite_genre`, OR genre contains: `rock`, `punk`, `party` |
| **Chill** | Energy <= `chill_max_energy` (default: 3), OR title contains: `lofi`, `ambient`, `sleep` |
| **Mixed** | Any song that does NOT strictly meet Hype or Chill criteria |

### Search Functionality
- Case-insensitive
- Partial match against the selected field (e.g., searching `"AC"` should find `"AC/DC"`)
- Returns all songs where the query string is contained within the song's attribute

### Playlist Statistics
- **Total Songs:** Unique count of all songs across all categories
- **Average Energy:** Mathematical average of energy levels of all songs in the system
- **Hype Ratio:** Percentage of "Hype" songs relative to total songs

### Lucky Pick
- Selecting **"Hype"** or **"Chill"** → must only pick from that specific playlist
- Selecting **"Any"** → random song from the combined pool of Hype + Chill + Mixed

### Data Normalization
- All user input (titles, artists, genres) must be **trimmed** of leading/trailing whitespace
- Artists and genres converted to **lowercase** for comparison
- Prevents duplicate or mismatched entries

---

## Goals

By completing this activity, you will be able to:

- [ ] Identify and explain logic and state issues in AI-generated Python code across multiple files
- [ ] Use your AI coding assistant in VS Code to reason about debugging steps
- [ ] Test behavior through the app interface and simple scenarios to verify that fixes work
- [ ] Use Git and VS Code to version and document code changes
- [ ] Critically evaluate AI debugging suggestions and refine them for correctness

---

## Tinker Instructions

### Part 1: Meet the Chaos
> Explore the app, break things intentionally, and document what you observe.

- [ ] Run the app and explore the UI
- [ ] Test song classification — does each song land in the right mood playlist?
- [ ] Try the search bar — is it case-insensitive? Does partial match work?
- [ ] Test "Lucky Pick" for Hype, Chill, and Any
- [ ] Check the playlist statistics — do Total Songs, Average Energy, and Hype Ratio look correct?
- [ ] Write down (or note) at least **3 bugs** you observe

---

### Part 2: AI Debugging Collaboration
> Use your AI assistant to trace bugs back to the code and fix them.

- [ ] Share your bug observations with your AI assistant
- [ ] Ask the AI to explain the root cause of each bug
- [ ] Work with the AI to write targeted fixes — one bug at a time
- [ ] Test each fix in the app before moving to the next
- [ ] Critically evaluate the AI's suggestions — do they fully solve the problem?

---

### Part 3: AI-Assisted Refactor and Version Control
> Clean up the code and save your progress with Git.

- [ ] Ask the AI to suggest any improvements or refactors to the fixed code
- [ ] Apply reasonable refactors (don't over-engineer)
- [ ] Stage and commit your changes with a descriptive commit message
- [ ] Verify the app still works correctly after refactoring

---

### Part 4: Reflect and Discuss
> Step back and think about what you learned.

- [ ] What bugs surprised you the most and why?
- [ ] How useful was the AI assistant? Where did it help vs. where did it fall short?
- [ ] What would you do differently when working with AI-generated code next time?
- [ ] Share your reflections with your group or in the discussion board

---

## Project Files

| File | Purpose |
|------|---------|
| [app.py](app.py) | Streamlit UI — handles display and user interaction |
| [playlist_logic.py](playlist_logic.py) | Core logic — song classification, search, stats, lucky pick |
| [requirements.txt](requirements.txt) | Python dependencies |
