# LC155 — Min Stack

## 🎯 Idea

Normal Stack gives:
- `push()` → O(1)
- `pop()` → O(1)
- `top()` → O(1)

But `getMin()` must also be **O(1)**.

### Trick: 2 arrays, 1 pointer

```text
stack     → actual values
minStack  → minimum till this position
```

Example:

```text
stack:     [ 9 ][ -6 ][ 0 ]
minStack:  [ 9 ][ -6 ][-6 ]
```

> **stack = what came in**  
> **minStack = smallest value till here**

---

## 🔥 Push

```java
stack[top] = value;

if(top == 0)
    minStack[top] = value;
else
    minStack[top] = Math.min(value, minStack[top - 1]);

top++;
```

---

## 📌 `top` Rule

`top` points to the **next empty position**.

```text
[9][-6][0][ ]
         ↑
        top
```

Therefore:

```java
push   → use top, then top++
pop    → top--
top()  → stack[top - 1]
getMin → minStack[top - 1]
```

### Why no `minTop`?

Both arrays use the **same position**.

> **2 arrays, 1 pointer.**

---

## ⚠️ Common Mistakes

### Don't recreate arrays in `push()`

❌
```java
stack = new int[100];
```

This destroys previous values.

✅ Initialize arrays **once in the constructor**.

### Don't scan the whole stack in `getMin()`

That makes `getMin()` **O(n)**.

Use:

```java
return minStack[top - 1];
```

---

## ⏱️ Complexity

| Operation | Time |
|---|---:|
| push | O(1) |
| pop | O(1) |
| top | O(1) |
| getMin | O(1) |

Space: **O(n)**

---

## 🧠 One-Line Memory

> **Min Stack = normal stack + minimum history.**

Every index stores:

`value + minimum till now`
