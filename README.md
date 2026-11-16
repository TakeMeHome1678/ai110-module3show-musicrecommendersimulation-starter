# 🎵 Music Recommender Simulation

## Project Summary

In this project you will build and explain a small music recommender system.

Your goal is to:

- Represent songs and a user "taste profile" as data
- Design a scoring rule that turns that data into recommendations
- Evaluate what your system gets right and wrong
- Reflect on how this mirrors real world AI recommenders

Replace this paragraph with your own summary of what your version does.

---

## How The System Works

Real-world recommendation systems often combine information about an item's content with signals from people's behavior, such as what they listen to, skip, save, or replay, and may learn from patterns across many users. This simulation is simpler: it compares each song's descriptive features with a manually specified user profile, then ranks songs by their match. Its scoring rule prioritizes the user's favorite genre, then mood, and then closeness to their target energy. Other song attributes are available in the catalog but are not part of this initial scoring rule.

The simulation uses these fields:

- `Song`: `id`, `title`, `artist`, `genre`, `mood`, `energy`, `tempo_bpm`, `valence`, `danceability`, and `acousticness`.
- `UserProfile`: `favorite_genre`, `favorite_mood`, `target_energy`, and `likes_acoustic`.

### Scoring Logic Design

The planned data flow is:

```text
Input: user preferences (genre="rock", mood="intense", energy=0.85)
  -> Load songs from data/songs.csv
  -> Loop over every song and calculate its score
  -> Store each song with its score
  -> Sort by score, highest first
Output: the top K recommendations
```

For each song, the scoring rule starts at 0 and adds:

- **+2.0 points** when the song's genre matches the user's preferred genre.
- **+1.0 point** when the song's mood matches the user's preferred mood.
- **Up to +1.0 similarity point** for energy closeness:
  `1.0 - abs(song energy - target energy)`.

Energy and its target are both on a 0.0–1.0 scale, so this awards 1 point for an exact match and decreases linearly to 0 points for the largest possible difference. The maximum score is 4.0. The ranking rule sorts all songs by score from highest to lowest and recommends the top `k`; if scores tie, keep the songs in their original catalog order.

This recipe may over-prioritize genre: a song in the preferred genre can earn more points than a song with a better mood or energy match. Exact genre and mood matching can also miss adjacent styles or songs that fit the user's vibe but have different labels. The small catalog and manually assigned features may not represent every listener's taste.

The functional recommender uses this example taste profile as a dictionary:

```python
user_prefs = {
    "genre": "rock",
    "mood": "intense",
    "energy": 0.85,
}
```

This profile should clearly distinguish an intense rock song such as *Storm Runner* from a chill lofi song such as *Library Rain*. However, it is intentionally narrow: exact genre and mood preferences may miss adjacent choices such as punk or post-rock, even when their energy is a good fit. Adding preferences for features such as valence, tempo, danceability, or acousticness—or allowing multiple preferred genres and moods—could make the profile more expressive.

**Prompt for a profile critique:**

> Critique this recommender taste profile: `{"genre": "rock", "mood": "intense", "energy": 0.85}`. Would it let a content-based recommender clearly differentiate an intense rock song like *Storm Runner* from a chill lofi song like *Library Rain*? Is it too narrow to recommend related or unexpected music? Explain what it captures, what it may exclude, and which one or two additional preferences from valence, tempo, danceability, or acousticness would improve it without making the profile unnecessarily complicated.

---

## Getting Started

### Setup

1. Create a virtual environment (optional but recommended):

   ```bash
   python -m venv .venv
   source .venv/bin/activate      # Mac or Linux
   .venv\Scripts\activate         # Windows

2. Install dependencies

```bash
pip install -r requirements.txt
```

3. Run the app:

```bash
python -m src.main
```

### Running Tests

Run the starter tests with:

```bash
pytest
```

You can add more tests in `tests/test_recommender.py`.

---

## Sample Recommendation Output

```
Loaded songs: 18

Top recommendations for genre=pop, mood=happy, energy=0.80:

1. Sunrise City - Neon Echo (score: 3.98)
   - genre match (+2.0)
   - mood match (+1.0)
   - energy similarity (+0.98)
2. Gym Hero - Max Pulse (score: 2.87)
   - genre match (+2.0)
   - mood mismatch (+0.0)
   - energy similarity (+0.87)
3. Rooftop Lights - Indigo Parade (score: 1.96)
   - genre mismatch (+0.0)
   - mood match (+1.0)
   - energy similarity (+0.96)
4. Night Drive Loop - Neon Echo (score: 0.95)
   - genre mismatch (+0.0)
   - mood mismatch (+0.0)
   - energy similarity (+0.95)
5. Afterimage - Glass Circuit (score: 0.94)
   - genre mismatch (+0.0)
   - mood mismatch (+0.0)
   - energy similarity (+0.94)
```

**Screenshot or video** *(optional)*: <!-- Insert a screenshot or demo video link here -->

---

## Experiments You Tried

Use this section to document the experiments you ran. For example:

- What happened when you changed the weight on genre from 2.0 to 0.5
- What happened when you added tempo or valence to the score
- How did your system behave for different types of users

---

## Limitations and Risks

Summarize some limitations of your recommender.

Examples:

- It only works on a tiny catalog
- It does not understand lyrics or language
- It might over favor one genre or mood

You will go deeper on this in your model card.

---

## Reflection

Read and complete `model_card.md`:

[**Model Card**](model_card.md)

Write 1 to 2 paragraphs here about what you learned:

- about how recommenders turn data into predictions
- about where bias or unfairness could show up in systems like this
