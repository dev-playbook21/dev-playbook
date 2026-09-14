# LC24 — Swap Nodes in Pairs

## 🧠 One-Line Memory

> **Every 2 nodes → swap → connect with previous pair.**

```text
1 → 2 → 3 → 4
↓
2 → 1 → 4 → 3
```

## 🔑 Pair Logic

```text
first → second → next
        ↓
second → first → next
```

Core links:

```java
temp = second.next;

second.next = first;
first.next = temp;
```

## 🔗 Connect Pairs

`prev` = **last node of previous swapped pair**.

```java
if (prev != null)
    prev.next = second;

prev = first;
```

Then move to next pair:

```java
first = temp;

if (first == null || first.next == null)
    break;

second = first.next;
```

## 👑 Without Dummy

First pair's `second` becomes the new head:

```java
ListNode newHead = head.next;
```

Return:

```java
return newHead;
```

## ⚡ 5-Second Revision

```text
Swap 2
  ↓
Connect previous pair
  ↓
Move to next pair
```

> **Don't memorize the whole code — remember: `first → second → next` becomes `second → first → next`.**

**Time:** O(n)  
**Space:** O(1)
