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

Real-world recommendation systems often combine information about an item's content with signals from people's behavior, such as what they listen to, skip, save, or replay, and may learn from patterns across many users. This simulation is simpler: it compares each song's descriptive features with a manually specified user profile, then ranks songs by their match. Its current experimental scoring rule gives equal base weight to genre and mood, and twice as much influence to closeness to target energy. Other song attributes are available in the catalog but are not part of this scoring rule.

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

For this sensitivity experiment, each song's score starts at 0 and adds:

- **+1.0 point** when the song's genre matches the user's preferred genre (reduced from +2.0).
- **+1.0 point** when the song's mood matches the user's preferred mood.
- **Up to +2.0 weighted similarity points** for energy closeness:
  `2.0 * (1.0 - abs(song energy - target energy))`.

Energy and its target are both on a 0.0–1.0 scale, so the weighted energy feature awards 2 points for an exact match and decreases linearly to 0 for the largest possible difference. With genre and mood matches worth 1 point each, the maximum score remains 4.0 and the minimum is 0. The ranking rule sorts all songs by score from highest to lowest and recommends the top `k`; if scores tie, keep the songs in their original catalog order.

This experimental recipe prioritizes energy closeness more than the baseline did. Exact genre and mood matching can still miss adjacent styles or songs that fit the user's vibe but have different labels. The small catalog and manually assigned features may not represent every listener's taste.

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

High-Energy Pop (genre=pop, mood=happy, energy=0.95) — Top 5:

1. Sunrise City - Neon Echo (score: 3.74)
   - genre match (+1.0)
   - mood match (+1.0)
   - weighted energy similarity (+1.74)
2. Gym Hero - Max Pulse (score: 2.96)
   - genre match (+1.0)
   - mood mismatch (+0.0)
   - weighted energy similarity (+1.96)
3. Rooftop Lights - Indigo Parade (score: 2.62)
   - genre mismatch (+0.0)
   - mood match (+1.0)
   - weighted energy similarity (+1.62)
4. Storm Runner - Voltline (score: 1.92)
   - genre mismatch (+0.0)
   - mood mismatch (+0.0)
   - weighted energy similarity (+1.92)
5. Concrete Bloom - Static Youth (score: 1.88)
   - genre mismatch (+0.0)
   - mood mismatch (+0.0)
   - weighted energy similarity (+1.88)
```

**Screenshot or video** *(optional)*: <!-- Insert a screenshot or demo video link here -->

---

## Experiments You Tried

Sensitivity experiment: reduced the genre-match weight from +2.0 to +1.0 and
doubled energy similarity from up to +1.0 to up to +2.0, leaving mood at +1.0.
The score remains between 0 and 4 when feature and target energy are in the
documented 0.0–1.0 range: energy similarity is `2 * (1 - abs(song energy -
target energy))`, which ranges from 0 to 2. The profiles are still tested
against the same catalog; “sad” mood and “metal” genre are not represented.

### High-Energy Pop

```text
High-Energy Pop (genre=pop, mood=happy, energy=0.95) — Top 5:

1. Sunrise City - Neon Echo (score: 3.74)
   - genre match (+1.0)
   - mood match (+1.0)
   - weighted energy similarity (+1.74)
2. Gym Hero - Max Pulse (score: 2.96)
   - genre match (+1.0)
   - mood mismatch (+0.0)
   - weighted energy similarity (+1.96)
3. Rooftop Lights - Indigo Parade (score: 2.62)
   - genre mismatch (+0.0)
   - mood match (+1.0)
   - weighted energy similarity (+1.62)
4. Storm Runner - Voltline (score: 1.92)
   - genre mismatch (+0.0)
   - mood mismatch (+0.0)
   - weighted energy similarity (+1.92)
5. Concrete Bloom - Static Youth (score: 1.88)
   - genre mismatch (+0.0)
   - mood mismatch (+0.0)
   - weighted energy similarity (+1.88)
```

### Chill Lofi

```text
Chill Lofi (genre=lofi, mood=chill, energy=0.30) — Top 5:

1. Library Rain - Paper Lanterns (score: 3.90)
   - genre match (+1.0)
   - mood match (+1.0)
   - weighted energy similarity (+1.90)
2. Midnight Coding - LoRoom (score: 3.76)
   - genre match (+1.0)
   - mood match (+1.0)
   - weighted energy similarity (+1.76)
3. Spacewalk Thoughts - Orbit Bloom (score: 2.96)
   - genre mismatch (+0.0)
   - mood match (+1.0)
   - weighted energy similarity (+1.96)
4. Focus Flow - LoRoom (score: 2.80)
   - genre match (+1.0)
   - mood mismatch (+0.0)
   - weighted energy similarity (+1.80)
5. Blue Lanterns - Harborlight (score: 1.98)
   - genre mismatch (+0.0)
   - mood mismatch (+0.0)
   - weighted energy similarity (+1.98)
```

### Deep Intense Rock

```text
Deep Intense Rock (genre=rock, mood=intense, energy=0.90) — Top 5:

1. Storm Runner - Voltline (score: 3.98)
   - genre match (+1.0)
   - mood match (+1.0)
   - weighted energy similarity (+1.98)
2. Gym Hero - Max Pulse (score: 2.94)
   - genre mismatch (+0.0)
   - mood match (+1.0)
   - weighted energy similarity (+1.94)
3. Concrete Bloom - Static Youth (score: 1.98)
   - genre mismatch (+0.0)
   - mood mismatch (+0.0)
   - weighted energy similarity (+1.98)
4. Afterimage - Glass Circuit (score: 1.92)
   - genre mismatch (+0.0)
   - mood mismatch (+0.0)
   - weighted energy similarity (+1.92)
5. Sunrise City - Neon Echo (score: 1.84)
   - genre mismatch (+0.0)
   - mood mismatch (+0.0)
   - weighted energy similarity (+1.84)
```

### Edge Case: High Energy, Sad Mood

```text
Edge Case - High Energy, Sad Mood (genre=rock, mood=sad, energy=0.95) — Top 5:

1. Storm Runner - Voltline (score: 2.92)
   - genre match (+1.0)
   - mood mismatch (+0.0)
   - weighted energy similarity (+1.92)
2. Gym Hero - Max Pulse (score: 1.96)
   - genre mismatch (+0.0)
   - mood mismatch (+0.0)
   - weighted energy similarity (+1.96)
3. Concrete Bloom - Static Youth (score: 1.88)
   - genre mismatch (+0.0)
   - mood mismatch (+0.0)
   - weighted energy similarity (+1.88)
4. Afterimage - Glass Circuit (score: 1.82)
   - genre mismatch (+0.0)
   - mood mismatch (+0.0)
   - weighted energy similarity (+1.82)
5. Sunrise City - Neon Echo (score: 1.74)
   - genre mismatch (+0.0)
   - mood mismatch (+0.0)
   - weighted energy similarity (+1.74)
```

### Edge Case: Unrepresented Genre and Mood

```text
Edge Case - Unrepresented Genre and Mood (genre=metal, mood=sad, energy=1.00) — Top 5:

1. Gym Hero - Max Pulse (score: 1.86)
   - genre mismatch (+0.0)
   - mood mismatch (+0.0)
   - weighted energy similarity (+1.86)
2. Storm Runner - Voltline (score: 1.82)
   - genre mismatch (+0.0)
   - mood mismatch (+0.0)
   - weighted energy similarity (+1.82)
3. Concrete Bloom - Static Youth (score: 1.78)
   - genre mismatch (+0.0)
   - mood mismatch (+0.0)
   - weighted energy similarity (+1.78)
4. Afterimage - Glass Circuit (score: 1.72)
   - genre mismatch (+0.0)
   - mood mismatch (+0.0)
   - weighted energy similarity (+1.72)
5. Sunrise City - Neon Echo (score: 1.64)
   - genre mismatch (+0.0)
   - mood mismatch (+0.0)
   - weighted energy similarity (+1.64)
```

### Intuition Check: High-Energy Pop

For the High-Energy Pop profile (pop, happy, target energy 0.95), **Sunrise City** ranks first with 3.74 points: +1.0 for matching pop, +1.0 for matching happy, and +1.74 for weighted energy similarity (`2 * (1 - |0.82 - 0.95|)`). The result seems plausible from the catalog features because it matches the requested genre and mood and is reasonably energetic. **Gym Hero** is closer in energy (1.96 weighted points) and matches pop, but its intense mood does not match happy, so it scores 2.96. I don’t have personal listening experiences, but the result may feel right to someone who values the full vibe match; the weight shift makes energy differences more influential than before.

The recommendations changed, but there are no listener ratings or feedback to show whether they became more accurate. The visible ranking change is in Chill Lofi, where **Spacewalk Thoughts** moves ahead of **Focus Flow** because its energy is closer to the 0.30 target, even though its genre does not match lofi. This is the intended sensitivity effect. Other top results largely remain similar, and the small catalog limits variety.

---

## Limitations and Risks

The catalog has only 18 songs and is uneven: most genres appear once, so users looking for an uncommon or missing genre may get poor matches. The system uses exact genre and mood labels, which can miss songs with a similar sound or feeling. Energy can outweigh a genre or mood mismatch, and songs with energy close to the target may rank highly even when another preference does not fit. The recommender does not use lyrics, listening history, or listener feedback, so its scores do not prove that someone will enjoy a recommendation.

---

## Reflection

This project showed me how a recommender turns a few preferences into scores and a ranked list. Changing the weights changed which songs appeared higher: when energy counted more, Spacewalk Thoughts moved ahead of Focus Flow for the Chill Lofi profile because its energy was closer to the target, even though its genre did not match. This showed me that the scoring rules shape the recommendations and do not guarantee that they are the best songs for a person.

The catalog and exact-label rules can also create bias. Genres that appear only once have fewer chances to match, and preferences missing from the catalog receive no match points. Energy can compensate for a mood mismatch, as with Gym Hero appearing high for Happy Pop because it matches pop and has high energy despite being labeled intense. A larger, more balanced catalog and real listener feedback would help test whether these recommendations are useful.
