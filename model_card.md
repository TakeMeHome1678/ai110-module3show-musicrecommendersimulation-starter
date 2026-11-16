# 🎧 Model Card: VibeScout 1.0

## 1. Model Name

**VibeScout 1.0** recommends songs by comparing their listed features with a user's stated taste.

## 2. Goal / Task

The recommender ranks songs that best match a user's preferred genre, mood, and energy. It returns the top five songs with short reasons for their scores.

## 3. Data Used

The catalog contains 18 songs. Each song has a genre, mood, energy, tempo, valence, danceability, and acousticness, along with its title and artist. The catalog is small and uneven: lofi appears three times, pop twice, and most genres only once. The data does not include lyrics, listening history, or user ratings.

## 4. Algorithm Summary

The system gives 1 point for a matching genre and 1 point for a matching mood. It adds up to 2 points for energy that is close to the user's target. It scores every song, sorts from highest score to lowest, and shows the top five with reasons.

## 5. Observed Behavior / Biases

Exact genre and mood matches can keep recommendations inside familiar labels and miss related music from other genres. Energy can outweigh a genre or mood mismatch. For example, Spacewalk Thoughts moved ahead of Focus Flow for the Chill Lofi profile because its energy was closer to the target, even though its genre did not match. Users with preferences absent from the catalog, such as metal or sad, receive no points for those preferences.

## 6. Evaluation Process

I tested High-Energy Pop, Chill Lofi, Deep Intense Rock, High-Energy Sad Rock, and an unrepresented Metal/Sad profile. I compared their top-five lists and checked that songs matching the profile tended to score higher. Gym Hero surprised me by ranking near the top for Happy Pop: it matches pop and has high energy, even though its mood is intense.

I also changed the weights: genre went from 2 points to 1, and energy similarity doubled from a maximum of 1 point to 2. Spacewalk Thoughts then moved ahead of Focus Flow for Chill Lofi because it was closer to the target energy. This confirms the weights change rankings, but without listener feedback I cannot say the new ranking is more accurate.

## 7. Intended Use and Non-Intended Use

This is a classroom demo for learning how content-based scoring and ranking work. It can show how changing preferences or weights affects a small catalog. It is not designed to understand a person's full taste or make dependable recommendations for a real music service.

## 8. Ideas for Improvement

- Add more songs and balance the number of examples in each genre and mood.
- Use more song features, such as valence, tempo, and acousticness.
- Collect listener feedback and use it to tune weights and improve recommendation variety.

## 9. Personal Reflection

My biggest learning moment was changing the feature weights and seeing Spacewalk Thoughts move ahead of Focus Flow for the Chill Lofi profile. It showed me that small scoring choices can noticeably change a ranked list. AI tools helped me draft code, explanations, and test profiles quickly, but I still needed to check the math, run the program, and compare the actual output with the documented recipe. I was surprised that a simple combination of genre, mood, and energy can feel like a recommendation because it produces an understandable reason for each result. If I extend the project, I would add a larger, more balanced catalog, include more audio features, and use listener feedback to check whether the rankings are genuinely useful.
