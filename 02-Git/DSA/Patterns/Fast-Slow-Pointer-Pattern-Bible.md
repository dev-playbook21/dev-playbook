# Fast & Slow Pointer (Floyd's Tortoise & Hare)

## 🎯 Pattern Summary

Two pointers move through the same structure at different speeds, and the *gap between them* — not their individual positions — is what carries the answer. One pointer measures distance, the other stands exactly where that measurement points. No extra memory, no second pass, just relative motion doing all the work.

---

## 🧠 Recognition Triggers

If a question contains any of these words, your hand should move toward Fast & Slow before you've even finished reading it:

- **"Middle"** — a position problem with unknown total length → speed ratio finds the midpoint without counting.
- **"Cycle" / "Loop"** — repeating structure with no natural end → only a pointer that can "lap" another can detect it.
- **"Meeting Point"** — two things converging inside a bounded space → classic fast/slow convergence signature.
- **"Nth from the end"** — a position relative to an *unknown* endpoint → fixed-gap two pointers.
- **"Duplicate number in an array using indices as pointers"** — array-as-linked-list disguise (LC287-style) → cycle detection in disguise.
- **"Without extra space" + "singly linked list"** — this combo almost always rules out hashing/counting and points straight at two pointers.

**Why these words work as triggers:** all of them describe a problem where you need **positional or structural information** (middle, entry point, distance from end) but only have **forward-only, single-pass access** (a singly linked list). Any time "I need a position, but I can't look backward or count first," Fast & Slow is the tool built for exactly that constraint.

---

## 🚫 When NOT To Use This Pattern

- **You need the value at a fixed index (e.g., "5th node")** — that's a simple counter walk, not a speed-ratio problem. Fast & Slow solves *relative* position (middle, end-relative), not *absolute* index lookup.
- **You need to compare elements from both ends simultaneously (e.g., palindrome check on an array)** — that's classic **Two Pointer (opposite ends)**, not Fast & Slow. Fast & Slow pointers move in the *same direction*; two-pointer-from-ends move toward each other.
- **You're searching a sorted structure for a target value** — that's **Binary Search**, not pointer racing. Beginners confuse "two pointers" broadly and reach for Fast & Slow when the real signal is "sorted + search," which wants divide-and-conquer instead.
- **You need to track a *window* of elements (e.g., longest substring)** — that's **Sliding Window**. The giveaway: Sliding Window cares about *everything inside* a range; Fast & Slow only cares about the *pointers themselves*, never a range between them.
- **You're doing multi-branch traversal (trees, graphs) needing to explore all paths** — that's **DFS/BFS**. Fast & Slow only works on linear, forward-only chains.

---

## 🎯 Core Objective

**What is Slow trying to achieve?**
Slow is trying to **arrive** — at the middle, at the cycle entrance, at the node just before the target, at the answer itself. Slow doesn't search; it paces itself so that when the right moment comes, it's already standing in the correct place.

**What is Fast trying to achieve?**
Fast is trying to **measure** — it either races to the end to define "how far is half," or races around a loop to define "how big is the cycle," or races ahead by a fixed gap to define "how far is N from the end." Fast never holds the answer. Fast exists purely to give Slow something to be relative *to*.

---

## 🧠 Mental Model

### The Running Track With One Lap Marker

Picture a circular running track (works for both cyclic *and* linear cases — a straight track is just a circle with one side "open").

```
                    🏁 START / FINISH
                         │
              ┌──────────┴──────────┐
              │                      │
         🐇 FAST (2x speed)     🐢 SLOW (1x speed)
              │                      │
              └──────────┬──────────┘
                         │
                    both begin together
```

- On an **open track** (a normal list): Fast sprints to the wall. The moment Fast hits the wall, Slow — having covered exactly half the ground — is standing at the **midpoint**. *(LC876)*
- On a **closed track** (a list with a cycle): Fast never hits a wall — it just keeps lapping. Because Fast gains exactly 1 unit of distance on Slow every tick, it is *mathematically guaranteed* to eventually catch up and **lap** Slow from behind. That collision is the **meeting point**. *(LC141, LC142)*
- With a **head start** instead of a speed difference: Fast doesn't run faster — it just starts **N steps ahead**. Now, whenever Fast reaches the finish line, Slow is automatically standing exactly N steps *before* it. *(LC19)*

One track. Three ways to use the gap between two runners. That's the entire pattern.

---

## ⚡ Memory Hook

- **"Speed finds the middle. Laps find the loop. Head starts find the gap."**
- **"Fast never knows the answer — Fast only knows the distance."**
- **"If Fast can lap Slow, there's a cycle. If Fast can't, there's a wall."**
- **"Slow doesn't search. Slow arrives."**
- **"A head start turns a race into a ruler."**

---

## 🌳 Pattern Family

```
EASY
  LC876 — Middle of Linked List
  (pure speed ratio, no cycle, no gap — the "hello world" of this pattern)
        │
        ▼
MEDIUM
  LC19 — Remove Nth Node From End
  (same engine, but Fast gets a HEAD START instead of double speed)
        │
        ▼
MEDIUM
  LC141 — Linked List Cycle
  (introduces the LAPPING idea — meeting = proof of a cycle)
        │
        ▼
HARD (conceptually)
  LC142 — Linked List Cycle II
  (LC141 + Floyd's second phase: reset one pointer to head,
   walk both at equal speed — meeting point #2 = cycle entrance)
```

Each problem doesn't introduce a new pattern — it **removes a training wheel** from the last one. Master LC876's ratio, add LC141's lapping, add LC142's second phase, and LC19's head-start variant becomes almost obvious in comparison.

---

## 🧩 Recognition Decision Tree

```
                         "Linked List Problem"
                                  │
                                  ▼
                   Do I need a POSITION or a MEETING?
                                  │
                ┌─────────────────┴─────────────────┐
                ▼                                     ▼
           POSITION                                MEETING
                │                                     │
     ┌──────────┴──────────┐                          ▼
     ▼                      ▼                  Does a cycle exist?
   Middle of            Nth node                       │
   the list             from the end          ┌────────┴────────┐
     │                      │                  ▼                 ▼
     ▼                      ▼                YES               UNKNOWN
Fast & Slow           Fast starts N        (find entry)      (must detect first)
(equal speed,         steps ahead,               │                 │
 same start)          then equal speed           ▼                 ▼
     │                      │              Fast & Slow        Fast & Slow
     ▼                      ▼              + 2nd phase        (equal speed,
   LC876                  LC19             (reset to head)     detect meeting)
                                                  │                 │
                                                  ▼                 ▼
                                                LC142              LC141
```

---

## ⚙️ Pattern DNA

| Attribute | Details |
|---|---|
| **Pattern** | Fast & Slow Pointer (Floyd's Tortoise & Hare) |
| **Purpose** | Extract positional/structural info from a forward-only linear structure in one pass |
| **Time Complexity** | O(n) |
| **Space Complexity** | O(1) |
| **Pointer Movement** | Slow: +1 step/tick · Fast: +2 steps/tick (or +1 with a head-start offset) |
| **Common Initialization** | `slow = head, fast = head` (or `fast = head.next`, or `fast` offset by N, or both from a `dummy` node) |
| **Loop Style** | `while (fast != null && fast.next != null)` for ratio-based; `while (fast != null)` for head-start-based |
| **Interview Frequency** | Very High — a top-5 linked list pattern across FAANG-tier interviews |
| **Difficulty Range** | Easy → Medium (conceptually simple, execution details trip people up) |
| **Recognition Keywords** | middle, cycle, loop, meeting point, Nth from end, duplicate via indices |

---

## 🧠 Invariants

This is the section that matters more than any line of code.

**For the ratio-based version (LC876, LC141):**
> **At every tick, `distance(Slow) = distance(Fast) / 2`.**
This never breaks. Not "usually true" — *always* true, at every single iteration, from tick zero to the last. It's this unbreakable ratio that lets you reason about the algorithm without ever tracing through the whole list in your head.

**For the cycle-detection version (LC141):**
> **If a cycle exists, the gap between Fast and Slow shrinks by exactly 1 every tick once both are inside the cycle.**
A shrinking gap in a bounded space *must* hit zero eventually — that's not an assumption, it's a guarantee. This is why "will they ever meet" is never in question once you see the gap-shrinking framing.

**For the cycle-entry version (LC142):**
> **After the first meeting, the distance from the meeting point to the cycle entrance equals the distance from `head` to the cycle entrance.**
This is the "OHHHH" invariant — it's not obvious from staring at code, but becomes obvious once you draw the distances (see the next section).

**For the head-start version (LC19):**
> **At every tick after the initial offset, the gap between Fast and Slow stays exactly N nodes.**
A constant gap means: when Fast reaches the end, Slow is *automatically* N nodes from the end — no counting required.

---

## 🔬 Why This Pattern Works

### Why the 2:1 ratio finds the middle (LC876)

```
Distance covered when Fast finishes = full length (L)
Distance covered by Slow at that same moment = L / 2   (because Slow moves half as often)

That's it. That's the whole proof. No induction needed —
just "half of Fast's total journey" by definition of moving half as fast.
```

### Why a faster pointer MUST catch a slower one in a cycle (LC141)

```
Think of the gap between them, not their absolute positions.

Tick 0:  gap = G
Tick 1:  gap = G - 1     (Fast gains 1 step on Slow every tick)
Tick 2:  gap = G - 2
  ...
Tick G:  gap = 0         ← COLLISION

A shrinking integer gap inside a FINITE loop cannot shrink forever —
it must hit zero. There's no way for Fast to "skip over" Slow,
because it only gains 1 unit of ground per tick.
```

### Why the second phase finds the cycle entrance (LC142) — the real "OHHHH" moment

```
Let:
  a = distance from HEAD to the cycle's entrance
  b = distance from the entrance to the MEETING POINT
  c = distance from the meeting point back around to the entrance

           a                    b
  HEAD ────────▶ ENTRANCE ────────▶ MEETING POINT
                     ▲                    │
                     └────────── c ───────┘
                          (rest of the loop)

When they meet:
  Slow traveled:  a + b
  Fast traveled:  a + b + b + c     (Fast lapped the loop one extra time)

Since Fast moves exactly 2x Slow's speed:
  2(a + b) = a + b + b + c
  → a = c

"a" (head to entrance) EQUALS "c" (meeting point to entrance, going forward).

That's why resetting one pointer to HEAD and moving both
one step at a time makes them meet EXACTLY at the entrance —
they're walking two paths of provably equal length.
```

### Why a head start creates a fixed gap (LC19)

```
If Fast starts N steps ahead and both then move at the SAME speed,
the gap between them never changes — it started at N, and moving
both by 1 step doesn't change the difference between two positions.

Fast reaches the end  →  Slow is still exactly N steps behind  →
Slow is sitting on the Nth-from-last node. No counting the list length needed.
```

---

## 🚶 Step-by-Step Animation

**Example (LC876 — Middle of Linked List):** `1 → 2 → 3 → 4 → 5 → 6`

```
TICK 0 (start)
1 → 2 → 3 → 4 → 5 → 6 → null
S,F

TICK 1
1 → 2 → 3 → 4 → 5 → 6 → null
    S       F

TICK 2
1 → 2 → 3 → 4 → 5 → 6 → null
        S           F

CHECK: fast.next == null → STOP

RESULT
1 → 2 → 3 → 4 → 5 → 6 → null
        S
        ↑
   Slow = 4  →  THE ANSWER
```

**Example (LC141 — Cycle Detection):** `1 → 2 → 3 → 4 → 5 → (back to 3)`

```
TICK 0
1 → 2 → 3 → 4 → 5
S,F              ↑___________|
                  (5 points back to 3)

TICK 1
1 → 2 → 3 → 4 → 5
    S   F        ↑___________|

TICK 2
1 → 2 → 3 → 4 → 5
        S       F
                ↑___________|
        (fast wraps: 5 → 3)

TICK 3
1 → 2 → 3 → 4 → 5
    F   S
   (fast now at 3, slow at 4 — gap closing)

TICK 4
1 → 2 → 3 → 4 → 5
        F,S
        ↑
   MEETING POINT — cycle confirmed
```

---

## 🧠 Initialization Cheat Sheet

| Initialization | Used When | Why |
|---|---|---|
| `slow = head, fast = head` | LC876 (want the **second** middle), LC141 | Starts the 2:1 ratio from zero — the cleanest baseline |
| `slow = head, fast = head.next` | LC876 variant (want the **first** middle) | Gives Fast a 1-node head start, shifting which middle you land on |
| `slow = fast = dummy` where `dummy.next = head` | LC19 | A dummy node lets Slow end up *before* the target node — essential when you might need to delete the head itself |
| `fast` offset by N steps first, then both move together | LC19 | Builds the fixed gap *before* the synchronized walk begins |
| `slow = head` (reset), `fast = meetingPoint` | LC142 phase 2 | Exploits the `a = c` invariant — both are now equidistant from the entrance |

**The pattern behind the pattern:** every initialization choice is really answering *"where do I want Slow standing when the loop ends?"* — one node early (for deletion), on the middle, or at the cycle's entrance. Initialization is the whole strategy in disguise.

---

## 🔁 Loop Condition Cheat Sheet

| Condition | Used When | What It's Really Asking |
|---|---|---|
| `while (fast != null && fast.next != null)` | LC876 | "Can Fast safely take its next TWO steps without crashing?" |
| `while (fast != null)` | LC19 (after the head-start phase) | "Has Fast reached the end yet?" — only one step per tick here, so only one null check needed |
| `while (fast != null && fast.next != null)` with a meet-check inside | LC141 | Same safety check as LC876, PLUS an equality check (`slow == fast`) each tick to detect the lap |
| `while (slow != fast)` (phase 2) | LC142 | "Have the two equally-paced pointers converged yet?" — no null checks needed here because a cycle guarantees they'll always have somewhere to go |

**The rule of thumb:** if Fast moves 2 steps per tick, you need **two** null checks (both landings must be safe). If Fast moves 1 step per tick, you only need **one**.

---

## 🧠 Common Beginner Mistakes

- **Thinking Fast holds the answer.** This happens because Fast is the pointer doing all the "work" (moving more), so it feels important — but Fast is disposable scaffolding. Slow is always the answer.
- **Believing two passes are required.** This comes from thinking procedurally ("first find the length, then walk to the middle") instead of relationally ("what does the *gap* tell me"). Once you think in gaps, one pass becomes obvious.
- **Moving Slow more than one step accidentally.** Usually a copy-paste error from Fast's movement line — breaks the entire 2:1 invariant silently, giving a wrong-but-plausible-looking answer.
- **Forgetting the second null check (`fast.next != null`).** Beginners mentally simulate Fast moving one step at a time, forgetting it's actually jumping two — so they miss that BOTH intermediate nodes need to exist.
- **Not resetting a pointer to `head` for LC142's second phase.** This mistake comes from not internalizing the `a = c` invariant — without understanding *why* resetting works, it looks like an arbitrary extra step instead of the natural continuation it is.
- **Using a head-start pointer AND doubling its speed at the same time (mixing LC19's technique with LC876's).** These are two different tools solving two different questions (gap vs. ratio) — combining them without realizing it produces meaningless results.

---

## 🎤 Interview Questions

1. **Why not just compute the list's length first, then walk to the target?**
   Because that requires two full passes; Fast & Slow encodes the same information in one pass by using relative speed instead of a counted total.

2. **Can Fast & Slow be done recursively?**
   Yes, but it requires threading extra state (like a counter or shared reference) through the call stack, which costs O(n) space — defeating the O(1) space advantage that makes this pattern attractive in the first place.

3. **Why does Fast moving exactly 2x (not 3x or 1.5x) matter?**
   2x gives the clean `distance(Slow) = distance(Fast)/2` relationship. Other ratios still work mathematically but complicate the arithmetic for no benefit — 2x is the simplest ratio that guarantees a lap in a cycle every single tick (gap shrinks by exactly 1, never skips over).

4. **How do you prove Fast will always catch Slow in a cycle, rather than looping past it forever?**
   Frame it as a shrinking integer gap (decreasing by 1 each tick) inside a finite space — an integer counting down by 1 inside a bounded range must hit zero; it can't skip past zero.

5. **Why does resetting one pointer to `head` find the cycle's entrance?**
   Because of the algebraic identity `a = c` (distance from head to entrance equals distance from meeting point to entrance) — walking both at equal speed from equidistant starting points guarantees they meet exactly at the entrance.

6. **What breaks if you use this pattern on a doubly linked list?**
   Nothing breaks it, but it becomes pointless — a doubly linked list can be walked backward and its length obtained trivially via other means, removing the core constraint (forward-only access) that makes this pattern valuable.

7. **How would you find the length of the cycle once detected?**
   Keep one pointer fixed at the meeting point, advance a second pointer around the loop counting steps until it returns to the same node.

8. **What's the difference in approach between LC19 and LC876?**
   LC876 uses a **speed difference** (2x) to encode a ratio; LC19 uses a **starting offset** (N steps) to encode a fixed gap. Different relationships, same two-pointer skeleton.

9. **Why use a dummy node for LC19 but not for LC876?**
   LC19 might require removing the head node itself, and a dummy node gives you a stable "previous" reference even in that edge case; LC876 never modifies the list, so no such safety net is needed.

10. **Can this pattern detect a cycle in an array (not a linked list)?**
    Yes — treat each value as a "pointer" to the next index (`nums[i]` points to index `nums[i]`), and the exact same Fast & Slow logic detects a cycle, which is the basis of LC287 (Find the Duplicate Number).

---

## 🔗 Related Problems

**Easy**
- **LC876 — Middle of Linked List:** the foundational ratio-based version, no cycle, no gap, pure speed difference.

**Medium**
- **LC19 — Remove Nth Node From End:** swaps "speed ratio" for "starting offset" — same skeleton, different relationship being encoded.
- **LC141 — Linked List Cycle:** introduces the "gap shrinks by 1 per tick" idea and the concept of a guaranteed lap.
- **LC142 — Linked List Cycle II:** LC141 plus a second phase exploiting the `a = c` distance identity to locate the cycle's entrance, not just confirm its existence.
- **LC287 — Find the Duplicate Number:** the *exact same* cycle-detection logic as LC141/LC142, but on an array instead of a linked list — recognizing this transferability is a strong interview signal.

**Hard (conceptually, via combination)**
- **LC143 — Reorder List:** combines "find the middle" (LC876) with list reversal and merging — a compound problem built from this pattern as one of three steps.
- **LC234 — Palindrome Linked List:** also leans on "find the middle" as a setup step before reversing the second half and comparing.

---

## ⚖️ Compare With Similar Patterns

| Pattern | When To Choose It Instead |
|---|---|
| **Dummy Node** | Not a competing pattern — often used *alongside* Fast & Slow (e.g., LC19) specifically to simplify edge cases involving the head node. |
| **Two Pointer (opposite ends)** | When comparing or converging from both ends of a *bounded, indexable* structure (e.g., palindrome check on an array) — Fast & Slow only ever moves in one direction. |
| **Sliding Window** | When the problem cares about the *contents between* two pointers (subarray sums, substrings) — Fast & Slow never cares what's between the pointers, only their positions. |
| **Binary Search** | When the structure is sorted and you're narrowing a search range — a fundamentally different "eliminate half the space" strategy, not a racing strategy. |
| **DFS/BFS** | When the structure branches (trees, graphs) — Fast & Slow assumes a single forward path, which breaks the moment there's more than one "next." |

---

## ⚡ One Minute Revision Card

```
FAST & SLOW POINTER — 60 SECOND CARD

Core idea:     Relative speed/offset encodes position.
Slow's job:    ARRIVE at the answer.
Fast's job:    MEASURE the distance.

Three flavors:
  Ratio (2x speed)     → middle           (LC876)
  Lapping (2x speed)   → cycle detection  (LC141)
  Head start (N steps) → Nth from end     (LC19)

Cycle entrance trick (LC142):
  distance(head → entrance) == distance(meeting → entrance)
  → reset one pointer to head, walk both 1 step, they meet at entrance.

Loop condition:
  2 steps/tick → check fast AND fast.next
  1 step/tick  → check fast only

Time: O(n)   Space: O(1)   Passes: 1

Golden line: "Fast never knows the answer — only the distance."
```

---

## 🏆 Golden Rules

✓ Understand the objective before touching code — ask "what is Slow trying to reach?" first.

✓ Fast is disposable. Slow (or the meeting point) is always where the answer lives.

✓ The invariant is the algorithm — if you can state what stays true every tick, you already understand the solution.

✓ Speed ratio and starting offset are two *different* tools solving two *different* questions — never mix them by accident.

✓ A shrinking gap inside a bounded space is a mathematical guarantee of a meeting, not a coincidence.

✓ Two steps per tick means two null checks. One step per tick means one.

✓ Initialization is strategy, not syntax — where pointers start determines what they can reach.

✓ The meeting point is not always the final answer — sometimes it's just the key that unlocks the next phase (LC142).

✓ If the structure branches, this pattern doesn't apply — it only trusts a single forward path.

✓ Recognize the pattern by its *question shape* (middle, cycle, Nth-from-end), not by memorized code.
