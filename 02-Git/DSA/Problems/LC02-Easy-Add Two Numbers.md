# LeetCode #2 - Add Two Numbers

> **Difficulty:** Medium  
> **Pattern:** Dummy Node + Carry Propagation + Simultaneous Linked List Traversal

---

# Problem Statement

Two non-empty linked lists represent two non-negative integers.

- Digits are stored in **reverse order**.
- Each node contains **one digit**.
- Return the sum as a linked list.

Example:

```
l1 : 2 -> 4 -> 3

l2 : 5 -> 6 -> 4

342 + 465 = 807

Result

7 -> 0 -> 8
```

---

# Key Observation

The numbers are already stored in reverse order.

```
342

↓

2 -> 4 -> 3
```

This means addition can be performed exactly like elementary addition from right to left without reversing the list.

---

# Core Idea

Instead of converting the linked lists into integers:

1. Traverse both linked lists simultaneously.
2. Keep track of carry.
3. Create a new node for every computed digit.
4. Build the answer using a Dummy Node.

---

# Algorithm

## Step 1

Create a Dummy Node.

```
dummy

↓

0
```

Also create another pointer.

```
current = dummy
```

---

## Step 2

Initialize carry.

```java
carry = 0;
```

---

## Step 3

Repeat while:

- First list still has nodes
- OR second list still has nodes
- OR carry still exists

```java
while(l1 != null || l2 != null || carry != 0)
```

---

## Step 4

Read values safely.

```java
x = (l1 != null) ? l1.val : 0;
y = (l2 != null) ? l2.val : 0;
```

Reason:

The linked lists may have different lengths.

---

## Step 5

Calculate current sum.

```java
sum = x + y + carry;
```

---

## Step 6

Separate digit and carry.

```java
digit = sum % 10;
carry = sum / 10;
```

Example

```
18

digit = 8

carry = 1
```

---

## Step 7

Append digit into the answer list.

```java
current.next = new ListNode(digit);
current = current.next;
```

Result grows as:

```
dummy

↓

0 -> 7

↓

0 -> 7 -> 0

↓

0 -> 7 -> 0 -> 8
```

---

## Step 8

Move input pointers.

```java
if(l1 != null)
    l1 = l1.next;

if(l2 != null)
    l2 = l2.next;
```

---

## Step 9

Return the answer.

```java
return dummy.next;
```

The dummy node itself is ignored.

---

# Dry Run

Input

```
2 -> 4 -> 3

5 -> 6 -> 4
```

---

### Iteration 1

```
2 + 5 + 0

sum = 7

digit = 7

carry = 0
```

Result

```
7
```

---

### Iteration 2

```
4 + 6 + 0

sum = 10

digit = 0

carry = 1
```

Result

```
7 -> 0
```

---

### Iteration 3

```
3 + 4 + 1

sum = 8

digit = 8

carry = 0
```

Result

```
7 -> 0 -> 8
```

---

# Why Dummy Node?

Without Dummy Node

```java
if(head == null)
    head = newNode;
else
    current.next = newNode;
```

Every insertion needs a special case.

---

With Dummy Node

```java
current.next = newNode;
current = current.next;
```

No special case.

Same logic for every insertion.

---

# Carry Flow

```
9 + 8

↓

17

digit = 7

carry = 1
```

Next iteration

```
5 + 4 + carry

↓

10

digit = 0

carry = 1
```

**Carry is NOT stored inside any node.**

Carry only exists as an integer variable.

---

# Complexity Analysis

### Time Complexity

```
O(max(n,m))
```

where

- n = length of first list
- m = length of second list

---

### Auxiliary Space

```
O(1)
```

(Result list excluded.)

---

# Common Mistakes

### ❌ Using

```java
while(l1.next != null)
```

instead of

```java
while(l1 != null)
```

---

### ❌ Resetting carry every iteration

```java
carry = 0;
```

---

### ❌ Returning current instead of dummy.next

Wrong

```java
return current;
```

Correct

```java
return dummy.next;
```

---

### ❌ Forgetting the final carry

Example

```
9 + 1

↓

10

Output

0 -> 1
```

---

### ❌ Accessing node value before checking null

Wrong

```java
x = node.val;
```

Correct

```java
x = (node != null) ? node.val : 0;
```

---

# Pattern Recognition

Whenever a question contains:

- Traverse two linked lists
- Build a new linked list
- Carry / Borrow
- Digit-by-digit computation

This pattern is a strong candidate.

---

# Related Problems

- LeetCode 445 - Add Two Numbers II
- LeetCode 21 - Merge Two Sorted Lists
- LeetCode 23 - Merge k Sorted Lists
- LeetCode 19 - Remove Nth Node From End
- LeetCode 24 - Swap Nodes in Pairs
- LeetCode 328 - Odd Even Linked List

---

# Personal Learning Notes

## Biggest Insight

Carry is **not transferred to the next node**.

Instead:

```
carry = sum / 10;
```

The node only stores

```
digit = sum % 10;
```

The linked list stores the **answer digits**, while the `carry` variable stores the **temporary state** required for the next iteration.

This separation between **Data Structure** and **Algorithm State** is the key idea behind this problem.

---

# Tags

`Linked List` `Dummy Node` `Carry` `Simulation` `Math` `Traversal` `Pattern`