# Dev Playbook
## DSA Session Log #006
### Problem: LeetCode 206 — Reverse Linked List

**Status:** ✅ Accepted

---

# Pattern

**Pointer Reversal (In-Place)**

Unlike previous Linked List problems, no new nodes are created.

The existing links are reversed by changing pointer directions.

---

# Core Idea

Initially

```
1 -> 2 -> 3 -> null
```

After reversing

```
3 -> 2 -> 1 -> null
```

The challenge is to reverse each link **without losing the remaining list**.

---

# The Three Pointer Technique

Maintain three pointers:

```java
prev
current
next
```

Visualization:

```
prev    current     next
 ↓         ↓          ↓
null <-    1   ->     2 -> 3
```

---

# Algorithm

For every node:

### Step 1

Save the remaining list.

```java
next = current.next;
```

---

### Step 2

Reverse the current link.

```java
current.next = prev;
```

---

### Step 3

Move `prev` forward.

```java
prev = current;
```

---

### Step 4

Move `current` forward.

```java
current = next;
```

Repeat until `current == null`.

Return:

```java
prev
```

because it becomes the new head.

---

# Why Save `next` First?

Suppose

```
1 -> 2 -> 3
```

If we directly execute

```java
current.next = prev;
```

the link to

```
2 -> 3
```

is lost forever.

Therefore,

always save

```java
next = current.next;
```

before modifying any pointer.

---

# Pointer Invariant

At the end of every iteration:

```
prev
 ↓
Reversed Part

current
 ↓
Remaining Part
```

Example:

```
2 -> 1 -> null

3 -> 4 -> null
```

- `prev` always points to the head of the reversed portion.
- `current` always points to the head of the unreversed portion.

Maintaining this invariant guarantees correctness.

---

# Correct Code

```java
class Solution {

    public ListNode reverseList(ListNode head) {

        if (head == null)
            return null;

        ListNode current = head;
        ListNode prev = null;
        ListNode next;

        while (current != null) {

            next = current.next;

            current.next = prev;

            prev = current;

            current = next;
        }

        return prev;
    }
}
```

---

# Complexity

**Time**

```
O(n)
```

Each node is visited exactly once.

---

**Space**

```
O(1)
```

Only three pointers are used.

---

# Common Mistakes

❌ Forgetting to save `current.next` before reversing.

❌ Moving `current` before updating `prev`.

❌ Returning the original `head`.

❌ Creating unnecessary dummy nodes.

---

# Interview Takeaway

The problem is **not about reversing values**.

It is about **reversing links** while preserving access to the remaining nodes.

The key insight is:

> **Never destroy a pointer before saving where it points.**

This is one of the most fundamental pointer manipulation techniques in Linked Lists.

---

# Relationship with Previous Problems

### LC21

Connect nodes.

```java
current.next = node;
```

---

### LC83

Remove nodes.

```java
current.next = current.next.next;
```

---

### LC206

Reverse nodes.

```java
current.next = prev;
```

---

# Pattern Learned

> **Pointer Reversal using Three Pointers**

This pattern is the foundation for several advanced Linked List problems, including:

- LC92 — Reverse Linked List II
- LC25 — Reverse Nodes in k-Group
- LC143 — Reorder List
- LC234 — Palindrome Linked List

---

## Progress

- ✅ LC2 — Add Two Numbers
- ✅ LC21 — Merge Two Sorted Lists
- ✅ LC83 — Remove Duplicates from Sorted List
- ✅ LC19 — Remove Nth Node From End of List
- ✅ LC23 — Merge k Sorted Lists
- ✅ LC206 — Reverse Linked List

**Patterns Mastered**

- Dummy Node
- Pointer Rewiring
- Two Pointer Gap
- Priority Queue (Min Heap)
- Multi-way Merge
- Pointer Reversal