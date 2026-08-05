# Dev Playbook
## DSA Session Log #009
### Problem: LeetCode 160 — Intersection of Two Linked Lists

**Status:** ✅ Accepted

---

# Pattern

**Length Equalization + Two Pointers**

Unlike LC141/142, this is **NOT** a Fast & Slow Pointer problem.

Both pointers move at the **same speed**.

The trick is to **equalize their remaining distance** before comparing them.

---

# Recognition Trigger

Whenever the question contains words like:

- Intersection
- Common Tail
- Same Node
- Merge Point
- Shared Nodes

Immediately think:

```
Equalize remaining distance
```

Not

```
Compare values
```

---

# Problem Statement

Return the node where two linked lists intersect.

If no intersection exists,

```java
return null;
```

Important:

Intersection means

```
nodeA == nodeB
```

NOT

```
nodeA.val == nodeB.val
```

---

# Core Idea

Imagine

```
List A

1 → 2 → 3
         \
          7 → 8 → 9
         /
4 → 5 ---
```

The moment two linked lists intersect,

their remaining path becomes identical.

So instead of finding the intersection,

equalize the remaining distance.

---

# Memory Trick

Imagine two runners.

```
Runner A

8 km left
```

```
Runner B

5 km left
```

Can they finish together?

❌ No.

First,

make both runners start with

```
5 km
```

Now they run together.

The first place where they stand together

=

Intersection.

---

# Visual Intuition

Suppose

```
Length A = 8

Length B = 5
```

Difference

```
3
```

Skip first

```
3 nodes
```

of the longer list.

Now

```
Remaining Length A = 5

Remaining Length B = 5
```

Move both pointers together.

The first common node

=

Answer.

---

# Step-by-Step Example

```
List A

1 → 2 → 3 → 7 → 8 → 9

Length = 6
```

```
List B

4 → 5 → 7 → 8 → 9

Length = 5
```

Difference

```
1
```

Skip

```
1
```

node in List A.

Now

```
A

2 → 3 → 7 → 8 → 9
```

```
B

4 → 5 → 7 → 8 → 9
```

Move together.

```
2   4

↓

3   5

↓

7   7
```

Pointers become equal.

Return

```
7
```

---

# Why It Works

Initially,

the longer list reaches the shared tail later.

By skipping the extra nodes,

both pointers have exactly the same distance remaining.

Therefore,

they either

```
Meet

or

Reach null together.
```

---

# Algorithm

### Step 1

Calculate

```
Length A

Length B
```

---

### Step 2

Find

```
Difference
```

---

### Step 3

Move the pointer of the longer list

```
Difference
```

steps.

---

### Step 4

Move both pointers

```
One Step
```

together.

---

### Step 5

If

```
ptrA == ptrB
```

Return that node.

Otherwise,

return

```
null
```

---

# Correct Code

```java
public class Solution {

    public ListNode getIntersectionNode(ListNode headA, ListNode headB) {

        if (headA == null || headB == null)
            return null;

        ListNode list1 = headA;
        ListNode list2 = headB;

        int length1 = 0;
        int length2 = 0;

        while (list1 != null) {
            length1++;
            list1 = list1.next;
        }

        while (list2 != null) {
            length2++;
            list2 = list2.next;
        }

        ListNode ptrA = headA;
        ListNode ptrB = headB;

        int diff = Math.abs(length1 - length2);

        if (length1 > length2) {

            while (diff-- > 0)
                ptrA = ptrA.next;

        } else {

            while (diff-- > 0)
                ptrB = ptrB.next;
        }

        while (ptrA != null) {

            if (ptrA == ptrB)
                return ptrA;

            ptrA = ptrA.next;
            ptrB = ptrB.next;
        }

        return null;
    }
}
```

---

# Complexity

Time

```
O(m+n)
```

Space

```
O(1)
```

---

# Common Mistakes

❌ Comparing

```
value
```

instead of

```
node reference
```

Correct

```java
ptrA == ptrB
```

---

❌ Forgetting to equalize lengths.

Pointers will never align correctly.

---

❌ Creating a HashSet.

Works,

but

```
Space = O(n)
```

Optimal solution requires

```
O(1)
```

space.

---

# Interview Follow-Up

### Q.

Can this be solved

without calculating lengths?

Answer

```
Yes.
```

There exists another elegant

```
O(m+n)

O(1)
```

solution.

It switches pointers between the two lists.

(No length calculation.)

---

# Relationship with Previous Problems

LC19

```
Maintain Gap
```

↓

LC160

```
Remove Gap
```

Both problems rely on

```
Distance Equalization.
```

---

# One Minute Revision

```
Find Lengths

↓

Difference

↓

Skip Longer List

↓

Equal Remaining Distance

↓

Move Together

↓

First Common Node

=

Intersection
```

---

# Memory Hooks

🧠 Compare Nodes, Not Values.

🧠 Equal Distance → Equal Chance.

🧠 Skip First. Compare Later.

🧠 Same Tail means Same Answer.

🧠 Don't find the intersection.

Equalize the journey.

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
- ✅ LC876 — Middle of Linked List

---

# Pattern Learned

**Distance Equalization**

Core Principle:

```
If two pointers must arrive together,

first make them travel

the same remaining distance.
```

This idea appears in:

- LC19 (Maintain Gap)
- LC160 (Remove Gap)
- LC142 (Distance Relationship)



#### Alternative: Pointer Switching (Elegant Variant) — revisit later after more pointer problems.