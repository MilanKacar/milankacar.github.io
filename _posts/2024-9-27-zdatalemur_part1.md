---
layout: post
title: "#12 Tackling DataLemur SQL Challenges for Data Science Interviews 🧠💡"
categories: [Data Science]
---

Mastering SQL is a crucial skill for data science interviews. Today, we’re diving into three SQL challenges from **DataLemur**. These problems not only help you sharpen your SQL skills but also give you practical insights into real-world data science scenarios. Let’s explore the solutions! 🚀

---

### 1. Histogram of Tweets [Twitter SQL Interview Question] 🐦

**Description**  
In this challenge, we need to create a histogram that shows how many tweets users posted in 2022. The goal is to group users based on their tweet count and return the number of users in each group.

**Schema**:  
- **tweets** table:
  - `tweet_id` (integer)
  - `user_id` (integer)
  - `msg` (string)
  - `tweet_date` (timestamp)

**Example Input**:

<table style="border-collapse: collapse; width: 100%; border-radius: 8px; overflow: hidden; box-shadow: 0 4px 10px rgba(0, 0, 0, 0.15);">
  <thead style="background-color: #c4c2bb; color: black;">
    <tr style="background-color: #f2f2f2;">
      <th style="border: 1px solid black; padding: 8px;">tweet_id</th>
      <th style="border: 1px solid black; padding: 8px;">user_id</th>
      <th style="border: 1px solid black; padding: 8px;">msg</th>
      <th style="border: 1px solid black; padding: 8px;">tweet_date</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="border: 1px solid black; padding: 8px;">214252</td>
      <td style="border: 1px solid black; padding: 8px;">111</td>
      <td style="border: 1px solid black; padding: 8px;">Am considering taking Tesla private at $420...</td>
      <td style="border: 1px solid black; padding: 8px;">12/30/2021 00:00:00</td>
    </tr>
    <tr>
      <td style="border: 1px solid black; padding: 8px;">739252</td>
      <td style="border: 1px solid black; padding: 8px;">111</td>
      <td style="border: 1px solid black; padding: 8px;">Despite the constant negative press covfefe</td>
      <td style="border: 1px solid black; padding: 8px;">01/01/2022 00:00:00</td>
    </tr>
    <tr>
      <td style="border: 1px solid black; padding: 8px;">846402</td>
      <td style="border: 1px solid black; padding: 8px;">111</td>
      <td style="border: 1px solid black; padding: 8px;">Following @NickSinghTech changed my life!</td>
      <td style="border: 1px solid black; padding: 8px;">02/14/2022 00:00:00</td>
    </tr>
    <tr>
      <td style="border: 1px solid black; padding: 8px;">241425</td>
      <td style="border: 1px solid black; padding: 8px;">254</td>
      <td style="border: 1px solid black; padding: 8px;">If the salary is so competitive...</td>
      <td style="border: 1px solid black; padding: 8px;">03/01/2022 00:00:00</td>
    </tr>
  </tbody>
</table>


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

---

### 2. Data Science Skills [LinkedIn SQL Interview Question] 🧑‍💻

**Description**  
This challenge involves finding candidates who are proficient in **Python**, **Tableau**, and **PostgreSQL**. We're tasked with listing candidates who have all these skills and ordering them by candidate ID.

**Schema**:  
- **candidates** table:
  - `candidate_id` (integer)
  - `skill` (varchar)

**Example Input**:


<table style="border-collapse: collapse; width: 100%; border-radius: 8px; overflow: hidden; box-shadow: 0 4px 10px rgba(0, 0, 0, 0.15);">
  <thead style="background-color: #c4c2bb; color: black;">
    <tr style="background-color: #f2f2f2;">
      <th style="border: 1px solid black; padding: 8px;">candidate_id</th>
      <th style="border: 1px solid black; padding: 8px;">skill</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="border: 1px solid black; padding: 8px;">123</td>
      <td style="border: 1px solid black; padding: 8px;">Python</td>
    </tr>
    <tr>
      <td style="border: 1px solid black; padding: 8px;">123</td>
      <td style="border: 1px solid black; padding: 8px;">Tableau</td>
    </tr>
    <tr>
      <td style="border: 1px solid black; padding: 8px;">123</td>
      <td style="border: 1px solid black; padding: 8px;">PostgreSQL</td>
    </tr>
    <tr>
      <td style="border: 1px solid black; padding: 8px;">234</td>
      <td style="border: 1px solid black; padding: 8px;">R</td>
    </tr>
    <tr>
      <td style="border: 1px solid black; padding: 8px;">345</td>
      <td style="border: 1px solid black; padding: 8px;">Python</td>
    </tr>
  </tbody>
</table>


**Solution**:
```sql
SELECT candidate_id
FROM candidates
WHERE skill IN ('Python', 'Tableau', 'PostgreSQL')
GROUP BY candidate_id
HAVING COUNT(DISTINCT skill) = 3
ORDER BY candidate_id;
```

---

### 3. Page With No Likes [Facebook SQL Interview Question] 👍❌

**Description**  
Here, we need to find Facebook pages that have received **zero** likes. The task is to return the IDs of these pages, sorted in ascending order.

**Schema**:  
- **pages** table:
  - `page_id` (integer)
  - `page_name` (varchar)

- **page_likes** table:
  - `user_id` (integer)
  - `page_id` (integer)
  - `liked_date` (datetime)

**Example Input**:


<table style="border-collapse: collapse; width: 100%; border-radius: 8px; overflow: hidden; box-shadow: 0 4px 10px rgba(0, 0, 0, 0.15);">
  <thead style="background-color: #c4c2bb; color: black;">
    <tr style="background-color: #f2f2f2;">
      <th style="border: 1px solid black; padding: 8px;">page_id</th>
      <th style="border: 1px solid black; padding: 8px;">page_name</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="border: 1px solid black; padding: 8px;">20001</td>
      <td style="border: 1px solid black; padding: 8px;">SQL Solutions</td>
    </tr>
    <tr>
      <td style="border: 1px solid black; padding: 8px;">20045</td>
      <td style="border: 1px solid black; padding: 8px;">Brain Exercises</td>
    </tr>
    <tr>
      <td style="border: 1px solid black; padding: 8px;">20701</td>
      <td style="border: 1px solid black; padding: 8px;">Tips for Data Analysts</td>
    </tr>
  </tbody>
</table>


**Solution**:
```sql
SELECT page_id
FROM pages
LEFT JOIN page_likes ON pages.page_id = page_likes.page_id
WHERE page_likes.page_id IS NULL
ORDER BY page_id;
```

---

### Wrapping Up 🎉

These DataLemur SQL challenges are a great way to test your skills and prepare for data science interviews. Whether it’s creating histograms of user activity, identifying top candidates, or filtering data with joins, these exercises cover key SQL concepts. Stay tuned for more data science insights! 💪
