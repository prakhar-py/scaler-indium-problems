

### Question 2: Meeting Room Conflict Detector (Easy-Medium)

**Problem Description:**

You are working on a booking system for meeting rooms. You need to implement a function that checks if a newly proposed meeting time conflicts with any already scheduled meetings for a particular room.

Meetings are represented by tuples `(start_time: int, end_time: int)`, where times are integers (e.g., hours from 0 to 23, or minutes in a day). A meeting `(s1, e1)` is considered to run from `s1` up to (but not including) `e1`. So, `(9, 10)` is a meeting from 9:00 to 9:59.
A conflict occurs if the new meeting's time interval overlaps with any of the existing meetings' time intervals. Two intervals `(s1, e1)` and `(s2, e2)` overlap if `max(s1, s2) < min(e1, e2)`.

Your function will take a list of already scheduled meetings and a new proposed meeting (both as tuples of start and end times). It should return `True` if there is a conflict, and `False` otherwise. Assume meeting times are valid (i.e., `start_time < end_time`).

**Function to Complete:**
```python
from typing import List, Tuple

def has_meeting_conflict(
    scheduled_meetings: List[Tuple[int, int]], 
    new_meeting: Tuple[int, int]
) -> bool:

    new_start, new_end = new_meeting

    if not (new_start < new_end): # Basic validation for the new meeting itself
        # This scenario might indicate invalid input, but for conflict checking with valid existing ones:
        # if new_start >= new_end, it cannot logically overlap in a meaningful way if intervals are [start, end)
        # However, problem statement says "Assume meeting times are valid (i.e., start_time < end_time)"
        # which likely applies more to the `scheduled_meetings` input.
        # For robustness, if new_meeting is invalid, it might be considered non-conflicting or an error.
        # Let's stick to the overlap definition assuming new_meeting is also valid per problem spirit.
        pass


    for existing_start, existing_end in scheduled_meetings:
        # Check for overlap: max(start1, start2) < min(end1, end2)
        # This condition means there's a common time slot.
        if max(existing_start, new_start) < min(existing_end, new_end):
            return True  # Conflict found
            
    return False # No conflicts found

```
