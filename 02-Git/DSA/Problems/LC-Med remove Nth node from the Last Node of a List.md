# LC19 — Remove Nth Node From End of List

**LeetCode:** 19  
**Difficulty:** Medium  
**Pattern:** Two Pointers + Dummy Node

---

# 💡 Core Idea

Instead of finding the length first, maintain a gap of **n nodes** between two pointers.

- `fast` moves `n` steps ahead.
- Then move both `slow` and `fast` together.
- When `fast` reaches the last node, `slow` will be at the **previous node** of the node to delete.

---

# ⭐ Why Dummy Node?

Without a dummy node:

```
1 -> 2 -> 3
```

Deleting the head has no previous node.

With a dummy node:

```
D -> 1 -> 2 -> 3
```

Now every real node has a predecessor.

This removes the special case for deleting the head.

---

# Algorithm

1. Create a dummy node.
2. Point `dummy.next` to `head`.
3. Initialize `slow` and `fast` to `dummy`.
4. Move `fast` ahead by `n` nodes.
5. Move both pointers until `fast.next == null`.
6. Delete the target node.
7. Return `dummy.next`.

---

# Correct Code (Java)

```java
class Solution {
    public ListNode removeNthFromEnd(ListNode head, int n) {

        ListNode dummy = new ListNode(0);
        dummy.next = head;

        ListNode slow = dummy;
        ListNode fast = dummy;

        for (int i = 0; i < n; i++) {
            fast = fast.next;
        }

        while (fast.next != null) {
            fast = fast.next;
            slow = slow.next;
        }

        slow.next = slow.next.next;

        return dummy.next;
    }
}
```

---

# Why `slow = fast = dummy`?

Starting from `dummy` ensures:

- Head also has a previous node.
- Head deletion becomes a normal deletion.
- No separate edge case is required.

---

# Why `while (fast.next != null)`?

After moving `fast` ahead by `n` nodes:

- We stop when `fast` reaches the **last node**.
- At that moment, `slow` is exactly at the **previous node** of the node to remove.

Then simply:

```java
slow.next = slow.next.next;
```

---

# Complexity

**Time:** `O(n)`

**Space:** `O(1)`

---

# Common Mistakes

❌ Starting pointers from `head` instead of `dummy`.

❌ Returning `new ListNode()` for an empty list.

❌ Using the wrong loop condition without adjusting the initial gap.

❌ Forgetting that the invariant depends on **both**:
- Initial gap (`n` vs `n+1`)
- Loop condition (`fast.next != null` vs `fast != null`)

These two must always be consistent.

---

# Interview Takeaway

This problem is **not about deleting a node**.

It is about maintaining a **fixed distance (gap)** between two pointers.

The dummy node simplifies the implementation by ensuring **every node, including the head, has a predecessor**, eliminating separate head-deletion logic.

---

# Related Problems

- LC21 — Merge Two Sorted Lists
- LC83 — Remove Duplicates from Sorted List
- LC876 — Middle of the Linked List
- LC141 — Linked List Cycle
- LC148 — Sort List
- LC23 — Merge k Sorted Lists

---

## ⭐ Pattern Learned

> **Dummy Node + Fixed Gap Two Pointers**

Whenever a linked list problem involves:
- deleting a node,
- especially the head,
- or finding the kth node from the end,

consider introducing a **dummy node** first. It often removes edge cases and leads to a cleaner, interview-friendly solution.