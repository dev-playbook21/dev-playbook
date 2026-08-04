# Dev Playbook
## DSA Session Log #002
### Problem: LeetCode 21 — Merge Two Sorted Lists

**Date:** 25 July 2026

---

# Status

- ✅ Solved
- ✅ Accepted (208 / 208)
- ✅ Runtime: 0 ms
- ⚠️ Session Duration: Longer than expected due to implementation confusion
- ⭐ Biggest Gain: Learned Linked List pointer thinking

---

# Initial Mistakes

## 1. Thinking Like Arrays

Initially I tried copying values instead of connecting nodes.

Wrong mindset:

```java
current.next.val = x;
```

Reality:

`current.next` doesn't even exist until it points to a node.

Linked Lists are **connected by references**, not by assigning values into nonexistent nodes.

---

## 2. Creating Nodes Mentally

I was unconsciously thinking:

```
take value

↓

create new node

↓

attach
```

Actual optimal approach:

```
existing node

↓

attach directly

↓

move pointer
```

Example:

```java
current.next = temp1;
```

NOT

```java
current.next = new ListNode(temp1.val);
```

---

## 3. Confusing Tail Pointer

Initially I didn't understand why:

```java
current = current.next;
```

is required.

Understanding:

```
dummy

↓

1

↓

4

↓

7
```

After attaching `1`, the tail becomes `1`.

So the next node must be attached after `1`, not after `dummy`.

Hence:

```java
current = current.next;
```

---

## 4. While Condition

Wrong:

```java
while(temp1 != null || temp2 != null)
```

Correct:

```java
while(temp1 != null && temp2 != null)
```

Reason:

Comparison is only possible while **both** lists still exist.

---

## 5. Remaining List Handling

Initially I tried handling remaining nodes inside the comparison loop.

Wrong structure:

```
while
    compare

    if list1 finished
        attach

    if list2 finished
        attach
```

Correct structure:

```
Phase 1

while(both exist)

↓

compare

↓

attach smaller

↓

repeat

----------------

Phase 2

attach remaining list
```

Implementation:

```java
if(temp1 != null)
    current.next = temp1;

if(temp2 != null)
    current.next = temp2;
```

---

# Biggest Concept Learned

## Existing Nodes Are Already Connected

Suppose

```
temp2

↓

4 -> 7 -> 9
```

Doing

```java
current.next = temp2;
```

does NOT attach only `4`.

It automatically attaches

```
4 -> 7 -> 9
```

because

```
4.next

↓

7

↓

9
```

already exists.

This was the biggest Linked List realization of this session.

---

# Final Algorithm

```
Create dummy node

↓

current = dummy

↓

while(list1 != null && list2 != null)

↓

compare values

↓

attach smaller node

↓

move attached pointer

↓

move current

↓

attach remaining list

↓

return dummy.next
```

---

# Complexity

Time:

```
O(n + m)
```

Space:

```
O(1)
```

---

# Interview Takeaways

### What Interviewer Wants

Not this:

```java
current.next.val = x;
```

They want this:

```java
current.next = temp1;
```

Understanding pointer manipulation is more important than memorizing code.

---

# Session Retrospective

## What Went Wrong

Both me and ChatGPT spent too much time patching the same implementation.

Repeated patching increased frustration.

---

## New Rule (Permanent)

If:

- Same bug appears 3–4 times
- Implementation becomes messy
- Frustration starts increasing

DO NOT continue patching.

Instead:

```
Delete code

↓

Blank screen

↓

Rewrite algorithm

↓

Fresh implementation
```

This is the same strategy used by experienced engineers when a solution becomes tangled.

---

# Lessons for Future Linked List Problems

Whenever solving any Linked List question, ask:

### 1.

Am I copying values?

or

Am I attaching nodes?

---

### 2.

Where is my tail pointer?

---

### 3.

What happens after attaching?

Did I move:

```java
current = current.next;
```

---

### 4.

Is my loop responsible only for comparison?

Or am I mixing comparison with leftover handling?

---

# Final Thoughts

This session was mentally exhausting but highly valuable.

The biggest achievement was **changing the mental model**:

From:

```
Store values
```

To:

```
Connect nodes
```

That single shift will make future Linked List problems significantly easier.

---

# Session Rating

Understanding Gained:
⭐⭐⭐⭐⭐ (5/5)

Implementation Quality:
⭐⭐⭐⭐☆ (4/5)

Efficiency of Session:
⭐⭐⭐☆☆ (3/5)

Overall Value:
⭐⭐⭐⭐⭐ (5/5)

---

## ✅ Ready for Next Pattern

Next Recommended Problems:

- LC83 — Remove Duplicates from Sorted List
- LC203 — Remove Linked List Elements
- LC206 — Reverse Linked List
- LC876 — Middle of the Linked List

These continue building the same pointer manipulation foundation learned in this session.