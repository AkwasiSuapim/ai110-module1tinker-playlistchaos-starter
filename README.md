# Playlist Chaos

Your AI assistant tried to build a smart playlist generator. The app runs, but some of the behavior is unpredictable. Your task is to explore the app, investigate the code, and use an AI assistant to debug and improve it.

This activity is your first chance to practice AI-assisted debugging on a codebase that is slightly messy, slightly mysterious, and intentionally imperfect.

You do not need to understand everything at once. Approach the app as a curious investigator, work with an AI assistant to explain what you find, and make targeted improvements.

---

## How the code is organized

### `app.py`  

The Streamlit user interface. It handles things like:

- Showing and updating the mood profile  
- Adding songs  
- Displaying playlists  
- Lucky pick  
- Stats and history

### `playlist_logic.py`  

The logic behind the app, including:

- Normalizing and classifying songs  
- Building playlists  
- Merging playlist data  
- Searching  
- Computing statistics  
- Lucky pick mechanics

You will need to look at both files to understand how the app behaves.

---

## What you will do

### 1. Explore the app  

Run the app and try things out:

- Add several songs with different titles, artists, genres, and energy levels  
- Change the mood profile  
- Use the search box  
- Try the lucky pick  
- Inspect the playlist tabs and stats  
- Look at the history  

As you explore, write down at least five things that feel confusing, inconsistent, or strange. These might be bugs, quirks, or unexpected design decisions.

### 2. Ask AI for help understanding the code  

Pick one issue from your list. Use an AI coding assistant to:

- Explain the relevant code sections  
- Walk through what the code is supposed to do  
- Suggest reasons the behavior might not match expectations  

For example:

> "Here is the function that classifies songs. The app is mislabeling some songs. Help me understand what the function is doing and where the logic might need adjustment."

Before making changes, summarize in your own words what you think is happening.

### 3. Fix at least four issues  

Make improvements based on your investigation.

For each fix:

- Identify the source of the issue  
- Decide whether to accept or adjust the AI assistant's suggestions  
- Update the code  
- Add a short comment describing the fix  

Your fixes may involve logic, calculations, search behavior, playlist grouping, lucky pick behavior, or anything else you discover.

### 4. Test your changes  

After each fix, try interacting with the app again:

- Add new songs  
- Change the profile  
- Try search and stats  
- Check whether playlists behave more consistently  

Confirm that the behavior matches your expectations.

### 5. Optional stretch goals  

If you finish early or want an extra challenge, try one of these:

- Improve search behavior  
- Add a "Recently added" view  
- Add sorting controls  
- Improve how Mixed songs are handled  
- Add new features to the history view  
- Introduce better error handling for empty playlists  
- Add a new playlist category of your own design  

---

## Tips for success

- You do not need to solve everything. Focus on exploring and learning.  
- When confused, ask an AI assistant to explain the code or summarize behavior.  
- Test the app often. Small experiments reveal useful clues.  
- Treat surprising behavior as something worth investigating.  
- Stay curious. The unpredictability is intentional and part of the experience.

When you finish, Playlist Chaos will feel more predictable, and you will have taken your first steps into AI-assisted debugging.

---

## Reflection

### The Bugs I Found and Fixed

**Bug 1: Case-sensitive search**

When I first ran the app I searched for "AC/DC" in the artist field and got zero results. Then I tried "ac/dc" and Thunderstruck showed up immediately. I opened `search_songs` in `playlist_logic.py` and traced the comparison. The query was being lowercased with `q = query.lower()`, but the song field value was being compared directly — no `.lower()` call on it. So "AC/DC" never matched "ac/dc" even though they're the same artist. The fix was one line: add `.lower()` when reading the song's field value. After that I tested "AC", "ac", "AC/DC", and "ac/dc" and all of them returned the right result.

**Bug 2: Lucky Pick mode mismatch**

Lucky Pick was ignoring my selection. I'd pick "Hype" and it would return a Chill song, or pick "Chill" and get a Mixed song. I looked at `lucky_pick` in the logic file and the comparisons were checking against `"Hype"` and `"Chill"` with capital letters. Then I checked the selectbox options in `app.py` — they were `["any", "hype", "chill"]`, all lowercase. The mode string was always lowercase coming in, so `mode == "Hype"` never matched and the function always fell through to the `else` branch which picks from everything. I made both sides consistent (all lowercase) and the behavior immediately matched what I expected.

**Bug 3: Songs landing in the wrong playlist**

I noticed a few songs that felt like they should be Chill ending up in Mixed. I walked through `classify_song` manually with a few examples and eventually spotted the issue: the variable named `chill_keywords` was being checked against the song **title**, not the genre. So a song with genre "ambient" and energy 5 (above chill max of 3) would land in Mixed unless the word "ambient" literally appeared in the title. I re-read the assignment spec: "title contains: lofi, ambient, sleep" — it does say title, not genre. The real issue was I had test songs with mid-range energy and no matching title keywords. Once I understood the rule, I verified that songs with low energy (≤ 3) are always correctly caught as Chill by the energy check, and songs that need the title keyword match work correctly too.

---

### How AI Helped (and Where I Didn't Trust It)

My AI assistant was useful for explaining what each function was doing and pointing out the mode mismatch in Lucky Pick quickly. For the search bug it spotted the missing `.lower()` in one shot and I agreed with it.

For the classification issue, the AI initially suggested also checking the **genre** for chill keywords like "lofi" and "ambient." That seemed reasonable in isolation — it makes intuitive sense that a song with genre "ambient" should be Chill. But I went back and re-read the spec, which specifically says "title contains," not genre. I did not apply that suggestion.

That was the most important moment in the whole exercise. The AI was reasoning about what made logical sense in general, not about what the spec actually required. If I had accepted that fix blindly, the behavior would have changed in a way that didn't match the assignment. Reading the spec myself — not just trusting the AI — was the step that made the fix reliable.

---

### The Refactor

After fixing the bugs I looked at `classify_song` and noticed a naming problem. The variables `hype_keywords` and `chill_keywords` had parallel names but were doing different things: hype keywords matched against the **genre**, and chill keywords matched against the **title**. That inconsistency wasn't obvious from the names alone.

I renamed them to `hype_genres` and `chill_title_words` to make the difference explicit. I also combined all the conditions for each mood into single boolean variables (`is_hype` and `is_chill`) so the final `if` statements are clean one-liners instead of compound expressions you have to mentally parse. The logic is identical — it's just easier to read at a glance.

I also fixed a subtle mutation bug in `merge_playlists`. The original code set `merged[key]` to a direct reference from playlist `a`, then called `.extend()` on it — which was silently modifying `a`'s original list. Since the second argument is always an empty dict in the current app, the extend never adds anything and the bug never shows up. But it would break the moment someone passed real data. The fix was one word: wrap `a.get(key, [])` in `list(...)` to copy it first.

I tested the refactor by verifying that all 22 default songs still landed in the same playlists as before, then added a few edge cases by hand:
- A rock song with energy 2 → still Hype (genre keyword beats energy threshold)
- A song with "ambient" in the title and energy 9 → Hype (Hype is checked first)
- An ambient-genre song with energy 5 and no matching title keyword → Mixed (correct per spec)

---

### One Lesson

The biggest thing I took from this: AI tools are fast at pattern matching and explanation, but they do not know your spec. Every suggestion the AI made was reasonable in some context, but a couple of them would have contradicted what the assignment actually required. The habit worth keeping is: AI explains, I verify against the spec, then I decide. Not the other way around.
