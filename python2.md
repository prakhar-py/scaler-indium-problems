
### **Problem: Meeting Room Conflict Detector**

**Problem Description**

You are tasked with building a function for a meeting room booking system. The function must determine if a newly proposed meeting time conflicts with any of the meetings already scheduled for that room.

A meeting is represented by a time interval `(start_time, end_time)`. A conflict occurs if the new meeting's time interval overlaps with any of the existing meeting intervals. An overlap exists between two intervals `(s1, e1)` and `(s2, e2)` if they have a common point in time.

**Constraints**

*   `0 <= len(scheduled_meetings) <= 1000`
*   `0 <= start_time < end_time <= 1440` (Times can be thought of as minutes in a 24-hour day).
*   All provided meeting intervals (both scheduled and new) are valid, meaning `start_time` is always less than `end_time`.

**Example**

**Input:**
```python
scheduled_meetings = [(1, 5), (10, 12), (6, 8)]
new_meeting = (7, 11)
```

**Output:**
```python
True
```

**Explanation:**

The function checks the `new_meeting` (7, 11) against each scheduled meeting:

1.  **Compare with (1, 5):** `max(7, 1)` is 7. `min(11, 5)` is 5. `7 < 5` is false. No conflict.
2.  **Compare with (10, 12):** `max(7, 10)` is 10. `min(11, 12)` is 11. `10 < 11` is true. A conflict is found.
3.  The function can stop and return `True` immediately.

The interval (7, 11) also conflicts with (6, 8), but the function finds the conflict with (10, 12) first and returns.

**Input Format**

The function will receive two arguments:

1.  `scheduled_meetings: List[Tuple[int, int]]` - A list of tuples. Each tuple contains:
    *   `start_time` (integer)
    *   `end_time` (integer)
2.  `new_meeting: Tuple[int, int]` - A single tuple representing the proposed new meeting.

**Output Format**

The function should return:

*   `bool` - `True` if the new meeting's time interval overlaps with any of the scheduled meeting intervals, `False` otherwise.

**Note**

*   The time intervals are "half-open," meaning a meeting `(start, end)` includes the `start` time but excludes the `end` time. For example, the meetings `(9, 10)` and `(10, 11)` are adjacent and do **not** conflict with each other.
