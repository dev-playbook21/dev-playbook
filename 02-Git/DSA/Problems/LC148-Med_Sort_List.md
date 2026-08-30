# LC148 — Sort List

## 🧠 One-Line Memory

> **SPLIT → SORT LEFT → SORT RIGHT → MERGE**

```text
4 → 2 → 1 → 3
      ↓
4 → 2 | 1 → 3
 ↓         ↓
2 → 4     1 → 3
      ↓
1 → 2 → 3 → 4
```

## 1️⃣ Base Case

```java
if (head == null || head.next == null)
    return head;
```

**1 node = already sorted.**

## 2️⃣ Split

```java
ListNode slow = head;
ListNode fast = head.next;

while (fast != null && fast.next != null) {
    slow = slow.next;
    fast = fast.next.next;
}

ListNode right = slow.next;
slow.next = null;
```

Split phase mein **comparison nahi hota**.

## 3️⃣ Recursively Sort

```java
ListNode leftSorted = sortList(head);
ListNode rightSorted = sortList(right);
```

`sortList()` = **break + recurse + merge**.

Eventually:

```text
4 | 2 | 1 | 3
```

Single nodes are already sorted, then recursion returns sorted halves.

## 4️⃣ Merge

Both lists are now sorted.

```java
while (left != null && right != null) {

    if (left.val < right.val) {
        result.next = left;
        left = left.next;
    } else {
        result.next = right;
        right = right.next;
    }

    result = result.next;
}

result.next = (right != null) ? right : left;
```

### 🔥 Merge Rule

> **Compare `.val` → smaller node attach → move that pointer.**

**One comparison = one node chosen.**

## ⚡ 5-Second Mental Map

```text
LC148
 ↓
MIDDLE
 ↓
SPLIT
 ↓
sortList(left)
sortList(right)
 ↓
MERGE
 ↓
compare .val
```

### Pattern Connection

```text
LC876 → Find Middle
LC21  → Merge Sorted Lists
LC148 → Middle + Recursion + Merge
```

## 🎯 Complexity

```text
Time  = O(n log n)
Space = O(log n) recursion stack
```

## 🔒 Golden Lines

```text
sortList() = BREAK + RECURSE + MERGE
merge()    = COMPARE VALUES + ATTACH SMALLER
```

> **Merge Sort doesn't sort while splitting. It sorts while merging.**
