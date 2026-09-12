# LC328 — Odd Even Linked List

## 🧠 One-Line Memory

> **Separate positions by rewiring → Odd chain + Even chain → Join.**

```text
1 → 2 → 3 → 4 → 5

ODD:  1 → 3 → 5
EVEN: 2 → 4

JOIN:
1 → 3 → 5 → 2 → 4
```

## 🔑 Setup

```java
ListNode odd = head;
ListNode even = head.next;
ListNode evenHead = even;
```

`evenHead` save karo — end mein even chain ko odd chain ke end par attach karna hai.

## 🔄 Rewire

```java
while (even != null && even.next != null) {

    odd.next = even.next;
    odd = odd.next;

    even.next = odd.next;
    even = even.next;
}
```

### 4 Moves — बस याद रख

```text
odd.next  = even.next
odd       = odd.next

even.next = odd.next
even      = even.next
```

**Odd → skip Even**  
**Even → skip Odd**

## 🔗 Final Join

```java
odd.next = evenHead;
return head;
```

## ⚡ 5-Second Revision

```text
odd  = 1
even = 2
save evenHead

ODD  → 1 → 3 → 5
EVEN → 2 → 4

odd.next = evenHead
```

### 🔥 Golden Line

> **Odd/even means POSITION, not value. Don't create new nodes — rewire existing `next` pointers.**

**Time:** O(n)  
**Space:** O(1)
