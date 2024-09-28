---
layout: post
title: "#12 Tackling DataLemur SQL Challenges for Data Science Interviews 🧠💡"
categories: [Data Science]
---

Data Science interviews often test your SQL skills, and being prepared can make all the difference. In this post, we’ll break down three SQL interview questions from Twitter, LinkedIn, and Facebook! Let’s dive into the problems and their solutions. 🚀

---

### 1. Histogram of Tweets [Twitter SQL Interview Question] 🐦

**Description**  
For this question, you need to calculate a histogram of tweets posted per user in 2022. This means you’ll group users by the number of tweets they posted and return the count of users in each group.

**Schema**:  
- **tweets** table:
  - `tweet_id` (integer)
  - `user_id` (integer)
  - `msg` (string)
  - `tweet_date` (timestamp)

**Example Input**:
| tweet_id | user_id | msg                                              | tweet_date        |
|----------|---------|--------------------------------------------------|-------------------|
| 214252   | 111     | Am considering taking Tesla private at $420...   | 12/30/2021 00:00:00|
| 739252   | 111     | Despite the constant negative press covfefe      | 01/01/2022 00:00:00|
| 846402   | 111     | Following @NickSinghTech changed my life!        | 02/14/2022 00:00:00|
| 241425   | 254     | If the salary is so competitive...               | 03/01/2022 00:00:00|

**Solution**:
```sql
SELECT tweet_count AS tweet_bucket, COUNT(user_id) AS users_num
FROM (
    SELECT user_id, COUNT(*) AS tweet_count
    FROM tweets
    WHERE tweet_date BETWEEN '2022-01-01' AND '2022-12-31'
    GROUP BY user_id
) AS tweet_histogram
GROUP BY tweet_count
ORDER BY tweet_count;
```

**Explanation**:  
We first filter tweets from 2022, group by `user_id` to get each user's tweet count, and then group by tweet count to get the number of users in each bucket. The final result gives the tweet histogram. 📊

---

### 2. Data Science Skills [LinkedIn SQL Interview Question] 🧑‍💻

**Description**  
Your task is to find candidates who are proficient in **Python**, **Tableau**, and **PostgreSQL**. Let’s identify those perfect candidates! 🔥

**Schema**:  
- **candidates** table:
  - `candidate_id` (integer)
  - `skill` (varchar)

**Example Input**:
| candidate_id | skill        |
|--------------|--------------|
| 123          | Python       |
| 123          | Tableau      |
| 123          | PostgreSQL   |
| 234          | R            |
| 345          | Python       |

**Solution**:
```sql
SELECT candidate_id
FROM candidates
WHERE skill IN ('Python', 'Tableau', 'PostgreSQL')
GROUP BY candidate_id
HAVING COUNT(DISTINCT skill) = 3
ORDER BY candidate_id;
```

**Explanation**:  
We use a `WHERE` clause to filter out the candidates who have the required skills. Then, we group by `candidate_id` and ensure each candidate has all three distinct skills by using `HAVING COUNT(DISTINCT skill) = 3`. 🎯

---

### 3. Page With No Likes [Facebook SQL Interview Question] 👍❌

**Description**  
In this task, you’ll query the pages that have **zero** likes. It’s time to figure out which pages need a little more love on Facebook! ❤️

**Schema**:  
- **pages** table:
  - `page_id` (integer)
  - `page_name` (varchar)

- **page_likes** table:
  - `user_id` (integer)
  - `page_id` (integer)
  - `liked_date` (datetime)

**Example Input**:
| page_id | page_name         |
|---------|-------------------|
| 20001   | SQL Solutions      |
| 20045   | Brain Exercises    |
| 20701   | Tips for Data Analysts|

**Solution**:
```sql
SELECT page_id
FROM pages
LEFT JOIN page_likes ON pages.page_id = page_likes.page_id
WHERE page_likes.page_id IS NULL
ORDER BY page_id;
```

**Explanation**:  
We use a `LEFT JOIN` to join the `pages` table with `page_likes`. Pages with no likes will have `NULL` values for `page_likes.page_id`, so we filter those out with the `WHERE` clause. The result is a list of page IDs with no likes! 🚫

---

### Wrapping Up 🎉

These SQL challenges test essential skills for data science interviews. Whether it's analyzing tweet patterns, matching candidates to job requirements, or identifying unpopular Facebook pages, understanding how to approach and solve these problems can give you an edge. Good luck on your data science journey! 🌟
