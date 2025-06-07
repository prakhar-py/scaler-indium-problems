### Question 4: Consecutive Login Days (Medium)

**Problem Statement:**
You are given a `UserLogins` table that records each time a user logs into a system. Write an SQL query to find the maximum number of consecutive days each user has logged in. A login on a new day breaks the previous streak if there was no login on the immediately preceding day.

**Schema:**

**Table: `UserLogins`**
| Column Name | Data Type | Constraints/Notes |
|-------------|-----------|-------------------|
| LogID       | INT       | PRIMARY KEY       |
| UserID      | INT       |                   |
| LoginDate   | DATE      |                   |

---

**Sample Input:**

**UserLogins Table:**
| LogID | UserID | LoginDate  |
|-------|--------|------------|
| 1     | 101    | 2023-10-01 |
| 2     | 101    | 2023-10-02 |
| 3     | 101    | 2023-10-03 |
| 4     | 102    | 2023-10-01 |
| 5     | 101    | 2023-10-05 | -- Streak broken for User 101
| 6     | 101    | 2023-10-06 |
| 7     | 102    | 2023-10-02 |
| 8     | 102    | 2023-10-04 | -- Streak broken for User 102
| 9     | 102    | 2023-10-05 |
| 10    | 101    | 2023-10-02 | -- Duplicate login, doesn't extend streak, handled by distinct login dates
| 11    | 103    | 2023-11-01 |
| 12    | 103    | 2023-11-02 |
| 13    | 103    | 2023-11-03 |
| 14    | 103    | 2023-11-04 |
| 15    | 103    | 2023-11-05 |

**Sample Output:**

| UserID | MaxConsecutiveDays |
|--------|--------------------|
| 101    | 3                  |
| 102    | 2                  |
| 103    | 5                  |

*Calculation Hints:*
*   **User 101:**
    *   Logs: 2023-10-01, 2023-10-02, 2023-10-03 (Streak 1: 3 days)
    *   Log: 2023-10-05, 2023-10-06 (Streak 2: 2 days)
    *   Max for User 101 is 3. (Note: 2023-10-02 duplicate login doesn't change the distinct login days)
*   **User 102:**
    *   Logs: 2023-10-01, 2023-10-02 (Streak 1: 2 days)
    *   Logs: 2023-10-04, 2023-10-05 (Streak 2: 2 days)
    *   Max for User 102 is 2.
*   **User 103:**
    *   Logs: 2023-11-01, 2023-11-02, 2023-11-03, 2023-11-04, 2023-11-05 (Streak 1: 5 days)
    *   Max for User 103 is 5.

---

**Solutions:**

```sql
WITH UserDistinctLogins AS (
    SELECT DISTINCT
        UserID,
        LoginDate
    FROM
        UserLogins
),
LoginGroups AS (
    SELECT
        UserID,
        LoginDate,
        JULIANDAY(LoginDate) - ROW_NUMBER() OVER (PARTITION BY UserID ORDER BY LoginDate) AS GroupIdentifier
    FROM
        UserDistinctLogins
),
StreakLengths AS (
    SELECT
        UserID,
        GroupIdentifier,
        COUNT(*) AS CurrentStreak
    FROM
        LoginGroups
    GROUP BY
        UserID,
        GroupIdentifier
)
SELECT
    UserID,
    MAX(CurrentStreak) AS MaxConsecutiveDays
FROM
    StreakLengths
GROUP BY
    UserID
ORDER BY
    UserID;
```


**Hints:**
Hint 1:
The first step is to ensure you're working with unique login dates per user. Then, for each user, you need a way to identify "gaps" in their login dates to determine the start of a new consecutive sequence.

Hint 2:
Window functions are key here. Think about assigning a row number to each login day (ordered by date) for each user. If you subtract this row number (or a value derived from it, like days) from the actual login date, consecutive dates will produce a constant value, effectively grouping them.
