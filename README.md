## Which Character Talks the Most in The Big Bang Theory?

This whole thing started with a random thought while watching The Big Bang Theory. I was sitting there, rewatching an episode, and suddenly wondered that who actually talks the most in this show? Is it always Sheldon? Or does someone else take over sometimes?

That little question turned into a full data project. I found a dataset, cleaned it up, explored it with SQL, and brought it to life in Power BI. What started as a fun idea ended up as a visual story of dialogue across seasons.
 
 It’s simple, visual, and really fun to explore. Let me walk you through how I did it.

---

## Dataset

The data comes from the [Kaggle dataset](https://www.kaggle.com/datasets/pablovargas/the-big-bang-theory-series-dataset) which includes:

- 10 seasons
- Scene-by-scene character lines
- character names
- Season and Episode details

I focused only on the **main 7 characters**:

- Sheldon
- Leonard
- Penny
- Raj
- Howard
- Amy
- Bernadette

---
## What I Built

I created a matrix-style visual in Power BI that shows:

- Seasons as rows  
- Episodes as columns  
- The **top speaker per episode** shown in each box  
- The boxes are color-coded based on the character who spoke the most  
- Added a playful "BAZINGA!" icon to match the tone of the show  

Each square gives a quick snapshot of who dominated that episode. It’s like a heatmap but with names and numbers.

## 🔧 Steps I Followed

### 1. Cleaned the Data in Excel
- Separated the combined Season and Episode string into two columns
- Removed irrelevant rows and characters
- Made sure all character names were consistent

### 2. Wrote SQL to Find Top Speaker per Episode
I used a SQL query to count the number of lines per character in each episode, and used `ROW_NUMBER()` to rank them. This made sure there was only one "winner" per episode, even if there was a tie.

```sql
WITH character_lines AS (
  SELECT
    season,
    episode,
    person_scene AS character,
    COUNT(*) AS n_speak
  FROM sheldon_filtered
  WHERE person_scene IN (
    'Sheldon', 'Leonard', 'Penny', 'Raj', 'Howard', 'Amy', 'Bernadette'
  )
  GROUP BY season, episode, person_scene
),
ranked_lines AS (
  SELECT *,
         ROW_NUMBER() OVER (
           PARTITION BY season, episode
           ORDER BY n_speak DESC, character
         ) AS rnk
  FROM character_lines
)
SELECT
  season,
  episode,
  character,
  n_speak
FROM ranked_lines
WHERE rnk = 1
```

### Challenge faced

Sometimes two characters had the same number of lines in one episode. That caused problems when assigning colors in Power BI. To fix it, I used ROW_NUMBER() instead of RANK() and added a tiebreaker by character name. That way, each episode had only one top speaker.



## Tools Used

- Power BI  
- DAX  
- Power Query  
- Excel (for early checks)  
- Custom icons and hex color mapping
- SQL Server
---

###  Dashboard Highlights

- One square per episode showing the top speaker

- Each character has their own color

- Layout is clean, clear, and scroll-free

### What I Learned

-Sheldon does talk the most overall — but not always

-Some episodes are led by Amy, Raj, Penny, Leonard or Howard but not Bernadette

- Small tweaks in DAX and SQL can make a big difference

- Design matters just as much as data

## Final Thoughts

This project started with curiosity and turned into something fun and visual. It combines pop culture with data, and shows how even TV shows can lead to meaningful insights.

If you love The Big Bang Theory or just enjoy visual data storytelling, give this a try. Hope it gives you a few insights or maybe just a smile. 😊

---

## Coming Next

Thinking of doing a similar dashboard for *YOU* or *Brooklyn Nine-Nine*. Stay tuned!

