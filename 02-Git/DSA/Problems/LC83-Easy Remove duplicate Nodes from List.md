# Dev Playbook
## DSA Session Log #003
### Problem: LeetCode 83 — Remove Duplicates from Sorted List

**Status:** ✅ Accepted

---

# Pattern

**In-place Linked List Modification**

Unlike LC21, this problem does **not** create a new list.

The existing list is modified by changing links.

---

# Core Idea

If two adjacent nodes have the same value:

```text
1 -> 2 -> 2 -> 3
```

Skip the duplicate node.

```java
temp.next = temp.next.next;
```

Result:

```text
1 -> 2 -> 3
```

---

# Biggest Learning

## Moving a Pointer ≠ Modifying a List

```java
temp = temp.next;
```

Only moves the traversal pointer.

---

```java
temp.next = temp.next.next;
```

Actually changes the Linked List.

Understanding this difference is the key concept of LC83.

---

# Pointer Rule

### Duplicate Found

```java
temp.next = temp.next.next;
```

✅ Remove duplicate

❌ Do NOT move `temp`.

Reason:

The next node may still be a duplicate.

Example:

```text
2 -> 2 -> 2
```

After removing one duplicate:

```text
2 -> 2
```

The current node must be checked again.

---

### Duplicate NOT Found

Move forward.

```java
temp = temp.next;
```

---

# Loop Condition

Safe traversal:

```java
while(temp != null && temp.next != null)
```

Always ensure `temp` exists before accessing `temp.next`.

---

# Important Assumption

The list is **already sorted**.

Therefore,

All duplicate values appear **consecutively**.

Without sorting, this approach would not work.

---

# Complexity

**Time:** `O(n)`

Each node is visited at most once.

**Space:** `O(1)`

Only one traversal pointer is used.

---

# Common Mistakes

❌ Moving `temp` immediately after deleting a duplicate.

❌ Confusing pointer movement with node deletion.

❌ Forgetting null safety while accessing `temp.next`.

❌ Thinking the last node needs to be "added" again.

The last node is already part of the list.

---

# Session Takeaway

Two fundamental Linked List operations are now mastered:

### LC21

> Connect existing nodes.

```java
current.next = temp1;
```

---

### LC83

> Remove existing nodes.

```java
temp.next = temp.next.next;
```

---

# Interview Takeaway

Before writing code, ask:

- Am I building a new list?
- Or modifying the existing one?

That answer determines the entire pointer strategy.

---

## Progress

- ✅ LC2 — Add Two Numbers
- ✅ LC21 — Merge Two Sorted Lists
- ✅ LC83 — Remove Duplicates from Sorted List

**Next Recommended:** LC206 — Reverse Linked List (Most Important Pointer Pattern)