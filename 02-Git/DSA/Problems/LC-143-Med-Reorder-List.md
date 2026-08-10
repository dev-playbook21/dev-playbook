# Dev Playbook
## DSA Session Log #012
### Problem: LeetCode 143 — Reorder List

**Status:** ✅ Accepted

---

# Pattern

## Split → Reverse → Weave

LC143 is a **pattern-composition problem**.

It combines:

```text
LC876
Find Middle

        +

LC206
Reverse Linked List

        +

Pointer Weaving
```

The real difficulty is the final weaving step.

---

# Problem

Given:

```text
1 → 2 → 3 → 4 → 5
```

Reorder it as:

```text
1 → 5 → 2 → 4 → 3
```

For even length:

```text
1 → 2 → 3 → 4
```

becomes:

```text
1 → 4 → 2 → 3
```

---

# Recognition Trigger

Whenever the required order looks like:

```text
First
Last
Second
Second Last
Third
Third Last
...
```

Think:

```text
First Half
     +
Reversed Second Half
     +
Alternate Weaving
```

---

# Core Idea

Do NOT try to reorder the entire list directly.

Break the problem into three independent phases.

```text
Phase 1
Find Middle

↓

Phase 2
Reverse Second Half

↓

Phase 3
Weave Both Halves
```

---

# Example

Original:

```text
1 → 2 → 3 → 4 → 5
```

---

## Phase 1 — Find Middle

Using Fast & Slow:

```text
slow = head
fast = head
```

After traversal:

```text
1 → 2 → 3 → 4 → 5
          ↑
        slow
```

For this setup:

```text
slow = 3
```

Important:

For an odd-length list, the middle node remains part of the first half.

So split at:

```text
1 → 2 → 3

4 → 5
```

---

# Phase 2 — Split

Save the second half:

```java
ListNode second = slow.next;
```

Then terminate the first half:

```java
slow.next = null;
```

Now:

```text
First Half:

1 → 2 → 3 → null
```

```text
Second Half:

4 → 5 → null
```

This split is IMPORTANT.

Without:

```java
slow.next = null;
```

the old connection can interfere with weaving and create an incorrect structure/cycle.

---

# Phase 3 — Reverse Second Half

Second half:

```text
4 → 5
```

Reverse using the LC206 pattern:

```text
5 → 4
```

Now we have:

```text
First Half:

1 → 2 → 3
```

```text
Reversed Second Half:

5 → 4
```

---

# Phase 4 — Weave

Target:

```text
1 → 5 → 2 → 4 → 3
```

The weaving rule is:

```text
SAVE first
LINK first → second
MOVE first

SAVE second
LINK second → first
MOVE second
```

---

# Weaving Dry Run

Initially:

```text
list = 1 → 2 → 3
halfRev = 5 → 4
```

### Step 1

Save first:

```java
temp = list.next;
```

So:

```text
temp = 2
```

Connect first to second:

```java
list.next = halfRev;
```

Now:

```text
1 → 5
```

Move first:

```java
list = temp;
```

Now:

```text
list = 2
```

---

### Step 2

Save second:

```java
temp2 = halfRev.next;
```

So:

```text
temp2 = 4
```

Connect second to first:

```java
halfRev.next = list;
```

Now:

```text
1 → 5 → 2
```

Move second:

```java
halfRev = temp2;
```

Now:

```text
halfRev = 4
```

---

### Next iteration

Same process:

```text
1 → 5 → 2 → 4 → 3
```

`halfRev` becomes `null`.

Done.

---

# Clean Weaving Code

```java
while (halfRev != null) {

    ListNode temp = list.next;
    list.next = halfRev;
    list = temp;

    ListNode temp2 = halfRev.next;
    halfRev.next = list;
    halfRev = temp2;
}
```

---

# The Weaving Memory Trick

Remember:

```text
SAVE
ATTACH
MOVE

SAVE
ATTACH
MOVE
```

Or:

```text
First → Second
Second → First
```

Repeated.

---

# Correct Code

```java
class Solution {

    public void reorderList(ListNode head) {

        if (head == null || head.next == null)
            return;

        ListNode slow = head;
        ListNode fast = head;

        // Phase 1: Find middle
        while (fast != null && fast.next != null) {
            slow = slow.next;
            fast = fast.next.next;
        }

        // Phase 2: Split
        ListNode second = slow.next;
        slow.next = null;

        // Phase 3: Reverse second half
        ListNode halfRev = null;

        while (second != null) {
            ListNode next = second.next;
            second.next = halfRev;
            halfRev = second;
            second = next;
        }

        // Phase 4: Weave
        ListNode list = head;

        while (halfRev != null) {

            ListNode temp = list.next;
            list.next = halfRev;
            list = temp;

            ListNode temp2 = halfRev.next;
            halfRev.next = list;
            halfRev = temp2;
        }
    }
}
```

---

# Complexity

Time:

```text
O(n)
```

Why?

```text
Find Middle      O(n)
Reverse Half     O(n)
Weave             O(n)
```

Overall:

```text
O(n)
```

Space:

```text
O(1)
```

Only pointers are used.

---

# Common Mistakes

## ❌ Mistake 1 — Reverse Entire List

Wrong:

```text
1 → 2 → 3 → 4 → 5

↓

5 → 4 → 3 → 2 → 1
```

LC143 needs:

```text
First Half
```

+

```text
Reversed Second Half
```

---

## ❌ Mistake 2 — Reverse From `slow`

For the standard setup:

```text
1 → 2 → 3 → 4 → 5
          ↑
        slow
```

Do:

```java
second = slow.next;
```

Not:

```java
second = slow;
```

Because `3` remains the final node of the first half.

---

## ❌ Mistake 3 — Forget the Split

Always:

```java
ListNode second = slow.next;
slow.next = null;
```

Otherwise the original connection remains.

---

## ❌ Mistake 4 — Lose `next`

Before changing:

```java
node.next
```

always save it.

```java
ListNode next = node.next;
```

Same principle as LC206.

---

## ❌ Mistake 5 — Wrong Weaving Order

Never do:

```text
Change pointer
↓
Try to recover old pointer
```

Instead:

```text
SAVE
↓
CHANGE
↓
MOVE
```

---

# Pattern Connection

## LC876

```text
Find Middle
```

↓

## LC206

```text
Reverse Linked List
```

↓

## LC143

```text
Find Middle
+
Reverse Second Half
+
Weave
```

This is an important lesson:

> Harder interview problems are often compositions of patterns you already know.

---

# One-Minute Revision

When you see:

```text
1 → 2 → 3 → 4 → 5

↓

1 → 5 → 2 → 4 → 3
```

Immediately think:

```text
MIDDLE
   ↓
SPLIT
   ↓
REVERSE SECOND HALF
   ↓
WEAVE
```

---

# Mental Diagram

```text
Original

1 → 2 → 3 → 4 → 5
          ↑
        slow

        SPLIT

1 → 2 → 3

4 → 5

        REVERSE

1 → 2 → 3

5 → 4

        WEAVE

1 → 5 → 2 → 4 → 3
```

---

# Memory Hooks

🧠 **Reorder = First + Last + Second + Second Last**

🧠 Find middle first.

🧠 Odd length → middle stays in first half.

🧠 Reverse only the second half.

🧠 Split before weaving.

🧠 Weave = SAVE → ATTACH → MOVE.

🧠 Never lose `next` before changing `next`.

---

# Interview Explanation

If asked to explain the solution:

> "I first use fast and slow pointers to find the middle of the list. I split the list into two halves and reverse the second half using the standard iterative reversal technique. Finally, I merge the two halves alternately by taking one node from the first half and one from the reversed second half. This gives the required first-last-second-second-last ordering. The solution runs in O(n) time and uses O(1) extra space."

---

# Progress

- ✅ LC2 — Add Two Numbers
- ✅ LC19 — Remove Nth Node From End
- ✅ LC21 — Merge Two Sorted Lists
- ✅ LC23 — Merge K Sorted Lists
- ✅ LC83 — Remove Duplicates
- ✅ LC92 — Reverse Linked List II
- ✅ LC141 — Linked List Cycle
- ✅ LC142 — Linked List Cycle II
- ✅ LC143 — Reorder List
- ✅ LC160 — Intersection of Two Linked Lists
- ✅ LC206 — Reverse Linked List
- ✅ LC234 — Palindrome Linked List
- ✅ LC876 — Middle of Linked List

---

# Pattern Learned

## Split → Reverse → Weave

This is one of the most important Linked List composition patterns.

```text
Find Middle
      ↓
Split
      ↓
Reverse
      ↓
Weave
```

Once these four operations are comfortable, many advanced Linked List problems stop looking like completely new problems.

---

# Next Target

🔥 **LC25 — Reverse Nodes in k-Group**

Expected composition:

```text
LC206 Reverse
        +
LC92 Partial Reverse
        +
Reconnect
        +
Repeat for every k nodes
```

The main new challenge will be:

```text
"How do I know whether k nodes actually exist before reversing?"
```

Do not memorize the final code.

Understand the pointer structure first.