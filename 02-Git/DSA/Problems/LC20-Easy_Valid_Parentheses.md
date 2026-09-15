# LC20 — Valid Parentheses

## 🧠 One-Line Memory

> **Opening → PUSH | Closing → POP + MATCH**

```text
OPEN  → PUSH
CLOSE → POP → CHECK
END   → stack empty
```

### 🔑 Core Logic

```java
if (open)
    stack[top++] = ch;
else {
    if (top == 0) return false;

    char pop = stack[--top];

    if (mismatch(pop, ch))
        return false;
}

return top == 0;
```

### Valid Pairs

```text
( )    [ ]    { }
```

> **Closing bracket must match the TOP.**

**Time:** O(n)  
**Space:** O(n)
