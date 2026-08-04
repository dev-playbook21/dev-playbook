# Dev Playbook
## DSA Session Log #005
### Problem: LeetCode 23 — Merge k Sorted Lists

**Status:** ✅ Learned (Priority Queue Approach)

---

# Pattern

**Priority Queue (Min Heap) + Linked List**

This problem extends the idea of LC21.

- **LC21:** Compare 2 current nodes.
- **LC23:** Compare **k current nodes**.

Instead of manually comparing every list, a **Min Heap** always gives the smallest available node.

---

# Core Idea

At any moment, **only one active node from each list** is needed.

Initially:

```
L1 : 1 -> 4 -> 5
      ↑

L2 : 1 -> 3 -> 4
      ↑

L3 : 2 -> 6
      ↑
```

Heap contains only:

```
1(L1)
1(L2)
2(L3)
```

Not the complete lists.

---

# Comparator

```java
PriorityQueue<ListNode> minHeap =
    new PriorityQueue<>((a, b) -> a.val - b.val);
```

The comparator compares node values.

```
a.val - b.val < 0
```

→ `a` has higher priority (smaller value)

Therefore, the smallest node always stays at the top of the heap.

---

# Algorithm

### Step 1

Insert the head of every non-null list.

```
Heap

1
1
2
```

---

### Step 2

Remove the smallest node.

```
poll()

↓

1
```

Attach it to the answer.

---

### Step 3

Suppose the removed node belonged to:

```
1 -> 4 -> 5
```

The next available candidate from this list becomes:

```
4
```

Insert it into the heap.

Heap now becomes:

```
1
2
4
```

---

### Step 4

Repeat until the heap becomes empty.

---

# Most Important Observation

The heap **never stores all nodes**.

It stores **only one active candidate from each list**.

Maximum heap size is:

```
k
```

where `k` is the number of linked lists.

---

# Why Insert `smallest.next`?

Suppose:

```
1 -> 100
```

After removing:

```
1
```

The next candidate from that list is:

```
100
```

Without inserting it back,

```
100
```

would never participate in future comparisons.

---

# Visualization

Initially

```
Heap

1(L1)
1(L2)
2(L3)
```

↓

Poll

```
1(L1)
```

↓

Insert

```
4(L1)
```

Heap

```
1(L2)
2(L3)
4(L1)
```

↓

Poll

```
1(L2)
```

↓

Insert

```
3(L2)
```

Heap

```
2(L3)
3(L2)
4(L1)
```

The process continues until every node has been processed.

---

# Complexity

Let

- `N` = Total number of nodes
- `k` = Number of linked lists

Each node:

- enters the heap once
- leaves the heap once

Each heap operation costs:

```
O(log k)
```

Therefore,

**Time**

```
O(N log k)
```

**Space**

```
O(k)
```

because the heap stores at most one node from each list.

---

# Why Not Store All Nodes?

Storing every node would increase heap size to:

```
N
```

making every operation:

```
O(log N)
```

The sorted property of each list allows us to keep only one candidate per list, reducing complexity to:

```
O(N log k)
```

---

# Relationship with LC21

### LC21

```
Compare

L1.current

vs

L2.current
```

---

### LC23

```
Compare

Current node of

List1

List2

List3

...

ListK
```

The Min Heap automatically selects the smallest among all current candidates.

---

# Common Mistakes

❌ Inserting every node into the heap.

❌ Forgetting to insert `smallest.next`.

❌ Using an incorrect comparator.

❌ Assuming the heap stores complete lists.

❌ Ignoring null lists while initializing the heap.

---

# Interview Takeaway

The key insight is:

> **The heap does not store all nodes. It stores only the current head (active candidate) of each list. Whenever the smallest node is removed, its successor from the same list is inserted back into the heap, maintaining at most one active candidate per list.**

This is why the solution achieves **O(N log k)** instead of **O(N log N)**.

---

## Progress

- ✅ LC2 — Add Two Numbers
- ✅ LC21 — Merge Two Sorted Lists
- ✅ LC83 — Remove Duplicates from Sorted List
- ✅ LC19 — Remove Nth Node From End of List
- ✅ LC23 — Merge k Sorted Lists

**Patterns Learned So Far**

- Dummy Node
- Pointer Rewiring
- Two Pointer Gap
- Min Heap (Priority Queue)
- Multi-way Merge