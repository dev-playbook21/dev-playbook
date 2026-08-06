# Dev Playbook
## DSA Session Log #010
### Problem: LeetCode 234 — Palindrome Linked List

**Status:** ✅ Accepted

---

# Pattern

**Pattern Composition**

This problem combines multiple previously learned patterns.

```
Fast & Slow Pointer
        +
Pointer Reversal
        +
Two Pointer Comparison
```

Unlike previous problems, no new algorithm is introduced.

Instead,

multiple patterns are composed together.

---

# Recognition Trigger

Whenever the question contains

- Palindrome
- Symmetric Linked List
- Compare First & Second Half
- O(1) Extra Space

Immediately think

```
Middle

↓

Reverse Second Half

↓

Compare
```

---

# Problem Statement

Determine whether a singly linked list is a palindrome.

Return

```java
true
```

or

```java
false
```

---

# Core Idea

Never reverse the entire list.

Instead,

```
Find Middle

↓

Reverse ONLY Second Half

↓

Compare both halves
```

---

# Mental Model

Imagine folding a paper.

```
1 2 3 2 1

↓

Fold from middle

↓

1 2

1 2
```

Both sides should become identical.

---

# Visual Intuition

Original List

```
1 → 2 → 3 → 2 → 1
```

Find Middle

```
1 → 2 → 3 → 2 → 1
          ↑
        slow
```

Reverse only

```
2 → 1
```

becomes

```
1 → 2
```

Now compare

```
1 → 2

↓

1 → 2
```

Every node matches.

Palindrome.

---

# Odd vs Even

## Odd Length

```
1 → 2 → 3 → 2 → 1
```

Middle

```
3
```

Ignore it.

Reverse starts from

```
slow.next
```

---

## Even Length

```
1 → 2 → 2 → 1
```

No single middle exists.

Reverse starts from

```
slow
```

---

# Memory Trick

Remember

```
Odd

↓

Skip Middle
```

```
Even

↓

No Middle To Skip
```

Easy rule

```
if (fast == null)

↓

Even

↓

Reverse from slow

----------------------

if (fast != null)

↓

Odd

↓

Reverse from slow.next
```

---

# Algorithm

### Phase 1

Find Middle

(LC876)

---

### Phase 2

Reverse Second Half

(LC206)

---

### Phase 3

Compare

First Half

vs

Reversed Second Half

---

### Phase 4 (Optional Interview Follow-Up)

Restore the original list.

(Not required by LeetCode.)

---

# Correct Code

```java
class Solution {

    public boolean isPalindrome(ListNode head) {

        ListNode list = head;
        ListNode prev = null;
        ListNode reserve = null;
        ListNode fast = head;
        ListNode slow = head;
        ListNode half = null;

        // Phase 1 : Find Middle
        while (fast != null && fast.next != null) {
            slow = slow.next;
            fast = fast.next.next;
        }

        // Phase 2 : Decide Reverse Start
        if (fast == null)
            prev = slow;
        else
            prev = slow.next;

        // Reverse Second Half
        while (prev != null) {
            reserve = prev.next;
            prev.next = half;
            half = prev;
            prev = reserve;
        }

        // Phase 3 : Compare
        while (half != null) {

            if (half.val != list.val)
                return false;

            half = half.next;
            list = list.next;
        }

        return true;
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

# Why It Works

The first half never changes.

Only the second half is reversed.

After reversal,

both halves move

```
Forward
```

making comparison straightforward.

---

# Common Mistakes

❌ Reverse the entire list.

Only reverse the second half.

---

❌ Forget odd/even handling.

Middle node must be skipped only for odd-length lists.

---

❌ Compare until first list becomes null.

Only compare until the reversed half ends.

---

❌ Count nodes to detect odd/even.

Fast pointer already tells you.

```
fast == null

↓

Even
```

```
fast != null

↓

Odd
```

---

# Interview Follow-Up

### Q.

Can we restore the original linked list?

Answer

```
Yes.
```

Reverse the second half again and reconnect.

Many production systems prefer restoring the input.

---

### Q.

Can we solve using Stack?

Yes.

```
Time : O(n)

Space : O(n)
```

Optimal solution is

```
O(1)
```

extra space.

---

# Pattern Connections

LC876

```
Find Middle
```

+

LC206

```
Reverse Second Half
```

+

Two Pointer Comparison

↓

LC234

---

# One Minute Revision

```
Find Middle

↓

Odd?

Skip Middle

↓

Even?

Start from Slow

↓

Reverse Second Half

↓

Compare

↓

Done
```

---

# Memory Hooks

🧠 Never reverse the whole list.

🧠 Reverse only the second half.

🧠 Fast tells Odd/Even.

🧠 Odd → Skip Middle.

🧠 Compare until reversed half ends.

---

# Progress

- ✅ LC2 — Add Two Numbers
- ✅ LC19 — Remove Nth Node From End
- ✅ LC21 — Merge Two Sorted Lists
- ✅ LC23 — Merge K Sorted Lists
- ✅ LC83 — Remove Duplicates
- ✅ LC141 — Linked List Cycle
- ✅ LC142 — Linked List Cycle II
- ✅ LC160 — Intersection of Two Linked Lists
- ✅ LC206 — Reverse Linked List
- ✅ LC234 — Palindrome Linked List
- ✅ LC876 — Middle of Linked List

---

# Pattern Learned

**Pattern Composition**

This problem is an excellent example that interview questions often combine previously learned techniques instead of introducing completely new ones.

```
Find Middle
        +
Reverse
        +
Compare
```

Master the building blocks,

and harder Linked List problems become compositions rather than new concepts.