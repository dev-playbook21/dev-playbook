# Dev Playbook
## DSA Session Log #011
### Problem: LeetCode 92 — Reverse Linked List II

**Status:** ✅ Accepted

---

# Pattern

**Pointer Reversal + Reconnection**

This problem extends **LC206 (Reverse Linked List)**.

Difference:

LC206

```
Reverse Entire List
```

LC92

```
Reverse Only A Sublist
```

The challenge is not reversing.

The challenge is reconnecting.

---

# Recognition Trigger

Whenever the question contains

- Reverse Between
- Reverse Range
- Reverse Portion
- Reverse Sublist
- Reverse k Nodes

Immediately think

```
Detach Mentally

↓

Reverse

↓

Reconnect
```

---

# Core Idea

Never think about the whole list.

Think only about

```
beforeLeft

↓

Reverse Segment

↓

afterRight
```

---

# Mental Model

Original

```
1 → 2 → 3 → 4 → 5
```

Reverse only

```
2 → 3 → 4
```

Imagine removing that section.

```
1

↓

2 → 3 → 4

↓

5
```

Reverse

```
4 → 3 → 2
```

Reconnect

```
1 → 4 → 3 → 2 → 5
```

---

# Visual Intuition

Original

```
Dummy

↓

0 → 1 → 2 → 3 → 4 → 5

      ↑
 beforeLeft

        ↑
      leftNode
```

Reverse

```
4 → 3 → 2
```

Reconnect

```
beforeLeft.next

↓

4
```

```
leftNode.next

↓

afterRight
```

Only

```
2
```

connections change.

Nothing else.

---

# Memory Trick

Remember only four pointers.

```
beforeLeft

↓

leftNode

↓

rightNode

↓

afterRight
```

Everything else is just LC206.

---

# Algorithm

## Phase 1

Move to

```
beforeLeft
```

---

## Phase 2

Reverse exactly

```
right-left+1
```

nodes.

---

## Phase 3

Reconnect

```
beforeLeft

↓

Head of reversed part
```

and

```
Tail of reversed part

↓

afterRight
```

Done.

---

# Why Dummy Node?

Edge Case

```
left = 1
```

Without Dummy

```
beforeLeft
```

doesn't exist.

Dummy guarantees

```
beforeLeft
```

always exists.

No special cases.

---

# Correct Code

```java
class Solution {

    public ListNode reverseBetween(ListNode head, int left, int right) {

        if (head == null || head.next == null || left == right)
            return head;

        ListNode result = new ListNode(0);
        result.next = head;

        ListNode list = result;

        for (int i = 1; i < left; i++) {
            list = list.next;
        }

        ListNode rev = list.next;
        ListNode saveRev = rev;

        ListNode temp = null;
        ListNode next = null;

        for (int i = 0; i < right - left + 1; i++) {

            next = rev.next;
            rev.next = temp;
            temp = rev;
            rev = next;
        }

        saveRev.next = rev;
        list.next = temp;

        return result.next;
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

❌ Reverse entire list.

Only reverse

```
right-left+1
```

nodes.

---

❌ Forget Dummy Node.

Fails when

```
left = 1
```

---

❌ Forget reconnecting tail.

```
leftNode.next

↓

afterRight
```

must always be restored.

---

❌ Forget reconnecting front.

```
beforeLeft.next

↓

reversedHead
```

---

# Interview Follow-Up

### Q.

Why not break the list first?

You don't need to.

Reverse only

```
k
```

nodes.

Reconnect afterwards.

Cleaner.

---

### Q.

Why use Dummy Node?

To remove

```
left == 1
```

as a special case.

---

# Relationship with Previous Problems

LC206

```
Reverse Entire List
```

↓

LC92

```
Reverse Fixed Range
```

↓

LC25

```
Reverse Multiple Fixed Ranges
```

---

# One Minute Revision

```
Dummy

↓

Find beforeLeft

↓

Reverse k Nodes

↓

Reconnect Front

↓

Reconnect Tail

↓

Done
```

---

# Memory Hooks

🧠 LC206 + Reconnection = LC92

🧠 Reverse only k nodes.

🧠 Dummy removes edge cases.

🧠 beforeLeft connects to new head.

🧠 Old head becomes new tail.

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
- ✅ LC160 — Intersection of Two Linked Lists
- ✅ LC206 — Reverse Linked List
- ✅ LC234 — Palindrome Linked List
- ✅ LC876 — Middle of Linked List

---

# Pattern Learned

**Partial Reversal**

Core Principle

```
Reverse

↓

Reconnect

↓

Restore Structure
```

Most advanced Linked List problems are based on this idea.

LC92 is the foundation for:

- LC25 — Reverse Nodes in k-Group
- LC143 — Reorder List
- Many interview-only linked list variations.