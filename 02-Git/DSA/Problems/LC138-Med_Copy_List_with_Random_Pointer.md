# LC138 — Copy List with Random Pointer

## 🧠 Core Idea

> **Original Node → Copy Node map karo → Copies ke connections bithao → `map.get(head)` return karo.**

```text
Original List              Copy List

A → B → C                  A' → B' → C'
↘   ↘   ↘                  ↘    ↘    ↘
 C   A   B                  C'   A'   B'
```

**A != A'** — values same ho sakte hain, nodes new hain.

---

## 1️⃣ Create Copies + Map

```java
Map<ListNode, ListNode> map = new HashMap<>();

ListNode curr = head;

while (curr != null) {
    map.put(curr, new ListNode(curr.val));
    curr = curr.next;
}
```

### Why HashMap?

Duplicates values ho sakte hain.

```text
❌ Map<Integer, ListNode>
✅ Map<ListNode, ListNode>
```

Because hume **exact original NODE** identify karna hai.

```text
Original Node → Copy Node
A → A'
B → B'
C → C'
```

---

## 2️⃣ Copies ke Connections Banao

```java
curr = head;

while (curr != null) {
    ListNode copy = map.get(curr);

    copy.next = map.get(curr.next);
    copy.random = map.get(curr.random);

    curr = curr.next;
}
```

### 🧠 Remember

> **Original jis node ko point kare → Map se uski COPY nikaal → Copy ko point kara.**

Example:

```text
A.random → C

A'.random = map.get(C)
          = C'
```

---

## 3️⃣ Return Copy Head

```java
return map.get(head);
```

Because:

```text
head          → Original A
map.get(head) → Copy A'
```

So `map.get(head)` = **copied list ka head**.

---

# ⚡ 5-Second Revision

```text
HEAD
 ↓
Original Nodes
 ↓
Create Copy + Map
 ↓
Original → Copy
 ↓
Set copy.next
Set copy.random
 ↓
return map.get(head)
```

## 🔥 Golden Line

> **Map original NODE to its COPY; then redirect every relationship to the corresponding copy.**
