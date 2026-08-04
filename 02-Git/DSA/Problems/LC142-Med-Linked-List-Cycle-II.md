# Dev Playbook
## DSA Session Log #008
### Problem: LeetCode 142 — Linked List Cycle II

**Status:** ✅ Accepted

---

# Pattern

**Fast & Slow Pointer (Floyd's Algorithm)**

This problem extends **LC141 (Cycle Detection).**

LC141 asks:

> Is there a cycle?

LC142 asks:

> Where does the cycle begin?

---

# Recognition Trigger

Whenever a Linked List question contains words like:

- Cycle
- Loop
- Circular
- Find Entry
- Detect Start
- Meeting Point

Immediately think:

```
Fast & Slow Pointer
```

---

# Problem Statement

Return the node where the cycle starts.

If no cycle exists,

return

```java
null
```

---

# Core Idea

The algorithm works in **two phases**.

```
Phase 1

Detect Cycle

↓

Phase 2

Locate Cycle Entry
```

Never try to find the entry directly.

First detect.

Then locate.

---

# Phase 1 — Detect Cycle

Use Floyd's Algorithm.

```
Slow

1 step

Fast

2 steps
```

If

```
slow == fast
```

Cycle exists.

If

```
fast == null

or

fast.next == null
```

No cycle.

Return

```
null
```

---

# Phase 2 — Find Cycle Entry

This is the magic.

Suppose

```
1 → 2 → 3 → 4 → 5
    ↑         ↓
    └─────────┘
```

Meeting happens at

```
5
```

Cycle actually starts at

```
2
```

Algorithm:

```
Pointer A = Head

Pointer B = Meeting Point
```

Move both

```
1 step

1 step

1 step
```

The first node where they meet is

```
Cycle Entry
```

---

# Memory Trick

Imagine two people.

```
🏃 Runner

Finds the loop.
```

```
🚶 Walker

Finds the entrance.
```

Remember:

> **Runner detects. Walker locates.**

---

# Visual Intuition

Initially

```
Head

↓

1 → 2 → 3 → 4 → 5
    ↑         ↓
    └─────────┘
```

Meeting

```
Head

↓

1 → 2 → 3 → 4 → 5
    ↑         ↓
    └─────────┘
              ↑
          Meeting
```

Now

```
Pointer A = Head

Pointer B = Meeting
```

Move together.

```
A

↓

1 → 2 → 3 → 4 → 5
    ↑         ↓
    └─────────┘
              ↑
              B
```

Move 1

```
A → 2

B → 2
```

They meet at

```
Cycle Entry
```

---

# Why Does This Work?

You do **NOT** need to memorize the mathematical proof.

Just remember this fact.

```
Distance

Head → Entry

=

Meeting → Entry
```

So,

if one pointer starts from

```
Head
```

and another starts from

```
Meeting Point
```

and both move

```
one step
```

they will reach

```
Entry
```

at the same time.

---

# Algorithm

### Step 1

Detect cycle.

```
Slow = 1 step

Fast = 2 steps
```

---

### Step 2

If no cycle

```
return null
```

---

### Step 3

Reset one pointer to

```
Head
```

---

### Step 4

Keep another pointer at

```
Meeting Point
```

---

### Step 5

Move both

```
One Step
```

until

```
ptr1 == ptr2
```

---

### Step 6

Return

```
ptr1
```

---

# Correct Code

```java
public class Solution {

    public ListNode detectCycle(ListNode head) {

        if (head == null || head.next == null)
            return null;

        ListNode slow = head;
        ListNode fast = head;

        while (fast != null && fast.next != null) {

            slow = slow.next;
            fast = fast.next.next;

            if (slow == fast) {

                ListNode ptr1 = head;
                ListNode ptr2 = slow;

                while (ptr1 != ptr2) {
                    ptr1 = ptr1.next;
                    ptr2 = ptr2.next;
                }

                return ptr1;
            }
        }

        return null;
    }
}
```

---

# Complexity

Time

```
O(n)
```

Space

```
O(1)
```

---

# Common Mistakes

❌ Returning the meeting point.

Meeting Point ≠ Cycle Entry.

---

❌ Starting Fast from

```
head.next
```

Use the standard Floyd initialization.

```
slow = head

fast = head
```

---

❌ Forgetting

```
fast != null

&&

fast.next != null
```

Results in NullPointerException.

---

❌ Moving one pointer faster than the other in Phase 2.

Both must move

```
exactly one step.
```

---

# Interview Follow-Up

### Q. Why not use HashSet?

HashSet

```
Time : O(n)

Space : O(n)
```

Floyd

```
Time : O(n)

Space : O(1)
```

---

### Q. Why does resetting one pointer work?

Because

```
Head → Entry

=

Meeting → Entry
```

So moving both one step guarantees they meet at the cycle entry.

---

# Relationship with Previous Problems

LC141

```
Detect Cycle
```

↓

LC142

```
Find Cycle Entry
```

LC141 stops at

```
Meeting Point
```

LC142 continues from there.

---

# One Minute Revision

```
Cycle?

↓

Fast & Slow

↓

Meet?

↓

No

↓

Return null

↓

Yes

↓

Pointer1 = Head

Pointer2 = Meeting

↓

Move both one step

↓

Where they meet

=

Cycle Entry
```

---

# Memory Hooks

🧠 **Runner finds the loop. Walker finds the entrance.**

🧠 **Meeting Point is NOT the answer.**

🧠 **Detect first. Locate second.**

🧠 **Head → Entry = Meeting → Entry**

🧠 **One speed after meeting.**

---

# Progress

- ✅ LC2 — Add Two Numbers
- ✅ LC19 — Remove Nth Node From End
- ✅ LC21 — Merge Two Sorted Lists
- ✅ LC23 — Merge K Sorted Lists
- ✅ LC83 — Remove Duplicates
- ✅ LC141 — Linked List Cycle
- ✅ LC142 — Linked List Cycle II
- ✅ LC206 — Reverse Linked List
- ✅ LC876 — Middle of Linked List

---

# Pattern Learned

**Fast & Slow Pointer (Advanced)**

You now know all three major variants:

- Position (LC876)
- Gap (LC19)
- Meeting & Entry (LC141 + LC142)

This completes the core Fast & Slow Pointer toolkit used in Linked List interviews.
