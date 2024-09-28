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
    <tr>
      <th style="border: 1px solid #ddd; padding: 12px; text-align: left;">tweet_id</th>
      <th style="border: 1px solid #ddd; padding: 12px; text-align: left;">user_id</th>
      <th style="border: 1px solid #ddd; padding: 12px; text-align: left;">msg</th>
      <th style="border: 1px solid #ddd; padding: 12px; text-align: left;">tweet_date</th>
    </tr>
  </thead>
  <tbody>
    <tr style="background-color: #f9f9f9;">
      <td style="border: 1px solid #ddd; padding: 12px;">214252</td>
      <td style="border: 1px solid #ddd; padding: 12px;">111</td>
      <td style="border: 1px solid #ddd; padding: 12px;">Am considering taking Tesla private at $420...</td>
      <td style="border: 1px solid #ddd; padding: 12px;">12/30/2021 00:00:00</td>
    </tr>
    <tr>
      <td style="border: 1px solid #ddd; padding: 12px;">739252</td>
      <td style="border: 1px solid #ddd; padding: 12px;">111</td>
      <td style="border: 1px solid #ddd; padding: 12px;">Despite the constant negative press covfefe</td>
      <td style="border: 1px solid #ddd; padding: 12px;">01/01/2022 00:00:00</td>
    </tr>
    <tr style="background-color: #f9f9f9;">
      <td style="border: 1px solid #ddd; padding: 12px;">846402</td>
      <td style="border: 1px solid #ddd; padding: 12px;">111</td>
      <td style="border: 1px solid #ddd; padding: 12px;">Following @NickSinghTech changed my life!</td>
      <td style="border: 1px solid #ddd; padding: 12px;">02/14/2022 00:00:00</td>
    </tr>
    <tr>
      <td style="border: 1px solid #ddd; padding: 12px;">241425</td>
      <td style="border: 1px solid #ddd; padding: 12px;">254</td>
      <td style="border: 1px solid #ddd; padding: 12px;">If the salary is so competitive...</td>
      <td style="border: 1px solid #ddd; padding: 12px;">03/01/2022 00:00:00</td>
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
    <tr>
      <th style="border: 1px solid #ddd; padding: 12px; text-align: left;">candidate_id</th>
      <th style="border: 1px solid #ddd; padding: 12px; text-align: left;">skill</th>
    </tr>
  </thead>
  <tbody>
    <tr style="background-color: #f9f9f9;">
      <td style="border: 1px solid #ddd; padding: 12px;">123</td>
      <td style="border: 1px solid #ddd; padding: 12px;">Python</td>
    </tr>
    <tr>
      <td style="border: 1px solid #ddd; padding: 12px;">123</td>
      <td style="border: 1px solid #ddd; padding: 12px;">Tableau</td>
    </tr>
    <tr style="background-color: #f9f9f9;">
      <td style="border: 1px solid #ddd; padding: 12px;">123</td>
      <td style="border: 1px solid #ddd; padding: 12px;">PostgreSQL</td>
    </tr>
    <tr>
      <td style="border: 1px solid #ddd; padding: 12px;">234</td>
      <td style="border: 1px solid #ddd; padding: 12px;">R</td>
    </tr>
    <tr style="background-color: #f9f9f9;">
      <td style="border: 1px solid #ddd; padding: 12px;">345</td>
      <td style="border: 1px solid #ddd; padding: 12px;">Python</td>
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
    <tr>
      <th style="border: 1px solid #ddd; padding: 12px; text-align: left;">page_id</th>
      <th style="border: 1px solid #ddd; padding: 12px; text-align: left;">page_name</th>
    </tr>
  </thead>
  <tbody>
    <tr style="background-color: #f9f9f9;">
      <td style="border: 1px solid #ddd; padding: 12px;">20001</td>
      <td style="border: 1px solid #ddd; padding: 12px;">SQL Solutions</td>
    </tr>
    <tr>
      <td style="border: 1px solid #ddd; padding: 12px;">20045</td>
      <td style="border: 1px solid #ddd; padding: 12px;">Brain Exercises</td>
    </tr>
    <tr style="background-color: #f9f9f9;">
      <td style="border: 1px solid #ddd; padding: 12px;">20701</td>
      <td style="border: 1px solid #ddd; padding: 12px;">Tips for Data Analysts</td>
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
