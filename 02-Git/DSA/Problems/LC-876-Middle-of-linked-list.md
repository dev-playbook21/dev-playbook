# Dev Playbook
## DSA Session Log #006
### Problem: LeetCode 876 — Middle of the Linked List

**Status:** ✅ Accepted

---

# Pattern

Fast & Slow Pointer

---

# Recognition Trigger

Question contains:

- Middle
- Half
- Center

↓

Immediately think

```
Fast & Slow Pointer
```

---

# Core Idea

Fast moves

```
2 steps
```

Slow moves

```
1 step
```

When Fast reaches the end,

Slow automatically reaches the middle.

---

# Memory Hook

🏃 Runner reaches Finish.

🚶 Walker reaches Middle.

Remember:

> **Runner finishes. Walker answers.**

---

# Visual Intuition

```
1 → 2 → 3 → 4 → 5 → 6

S
F
```

Move 1

```
1 → 2 → 3 → 4 → 5 → 6

    S
        F
```

Move 2

```
1 → 2 → 3 → 4 → 5 → 6

        S
                F
```

Move 3

```
1 → 2 → 3 → 4 → 5 → 6

            S

                  F = null
```

Return

```
Slow
```

---

# Why It Works

Fast travels twice as fast.

So when Fast covers the whole list,

Slow covers only half.

Hence,

```
Slow = Middle
```

---

# Algorithm

1. Slow = Head
2. Fast = Head
3. Move

```
Slow → 1

Fast → 2
```

4. When Fast stops

```
Return Slow
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

❌ Counting nodes first.

Two passes are unnecessary.

---

❌ Wrong initialization.

```
fast = head.next
```

changes the invariant.

---

❌ Wrong loop condition.

Always use

```
while(fast != null && fast.next != null)
```

---

# Interview Follow-Up

If interviewer asks

> Return the **first middle** instead of second.

Only initialization / loop condition changes.

---

# Memory Hooks

🏃 Runner reaches finish.

🚶 Walker reaches middle.

Fast decides when to stop.

Slow decides the answer.

---

# Pattern Connection

LC876

↓

Position Variant

↓

Fast & Slow Pointer