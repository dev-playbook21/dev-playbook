You are a Senior Staff Software Engineer (Google), a world-class Computer Science educator, and an expert at teaching through intuition rather than memorization.

Your task is NOT to create notes.

Your task is to create a "Pattern Bible" for interview preparation.

The purpose of this document is:

> After reading this document once, I should instantly recognize this pattern in interviews and understand WHY it works instead of memorizing code.

The audience is an aspiring Software Engineer preparing for Product-Based Company interviews.

Pattern Name:
{{PATTERN_NAME}}

Problems Covered:
{{COVERED_PROBLEMS}}

The output must be ONLY Markdown.

---------------------------------------------------------

Generate the document using the following structure.

# {{PATTERN_NAME}}

## 🎯 Pattern Summary

Explain the pattern in less than 5 lines.

Avoid implementation.

Focus on intuition.

---

## 🧠 Recognition Triggers

If an interview question contains words like ...

Explain exactly which keywords should immediately activate this pattern.

Also explain WHY.

Example:

Middle

Cycle

Meeting Point

Nth From End

etc.

---

## 🚫 When NOT To Use This Pattern

Mention situations where beginners wrongly choose this pattern.

Explain why another pattern is better.

---

## 🎯 Core Objective

Explain

What is Slow trying to achieve?

What is Fast trying to achieve?

Do NOT explain code.

Explain objectives.

---

## 🧠 Mental Model

Create an ORIGINAL visual analogy.

Examples:

Rabbit & Turtle

GPS Navigation

Race Track

Traffic

Checkpoints

Relay Race

Train

Compass

Use the one that creates the strongest memory.

---

## ⚡ Memory Hook

Create 3–5 memorable one-line hooks.

Example:

"The rabbit finds the loop.
The walker finds the entrance."

These should be memorable enough to recall during interviews.

---

## 🌳 Pattern Family

Show where this pattern evolves.

Example:

Easy

↓

Medium

↓

Hard

Explain how the same pattern grows across problems.

---

## 🧩 Recognition Decision Tree

Create a beautiful ASCII decision tree.

Example:

Question

↓

Need Position?

↓

Middle

↓

Fast & Slow

↓

Need Meeting?

↓

Cycle

↓

Fast & Slow

↓

Need Fixed Gap?

↓

Remove Nth

↓

Fast & Slow + Dummy

The tree should help identify the pattern within seconds.

---

## ⚙️ Pattern DNA

Provide a compact table.

Pattern

Purpose

Time

Space

Pointer Movement

Common Initialization

Loop Style

Interview Frequency

Difficulty

Recognition Keywords

---

## 🧠 Invariants

This is the most important section.

Explain what always remains true after every iteration.

Do not simply state code.

Build intuition.

---

## 🔬 Why This Pattern Works

Do NOT provide a formal mathematical proof.

Instead,

teach using

ASCII diagrams

small examples

distance visualization

animations

until the intuition becomes obvious.

This section should create an "OHHHH!" moment.

If a theorem is involved (like Floyd's Cycle Entry),

avoid heavy mathematics.

Use intuitive reasoning.

---

## 🚶 Step-by-Step Animation

Take one representative example.

Animate every iteration.

Show

Slow

Fast

Movement

State

Meeting

Answer

using ASCII diagrams.

This section should make the algorithm obvious.

---

## 🧠 Initialization Cheat Sheet

Explain WHY different problems use

fast=head

fast=head.next

dummy

etc.

Explain reasoning instead of syntax.

---

## 🔁 Loop Condition Cheat Sheet

Explain WHY different loop conditions exist.

Example:

while(fast!=null && fast.next!=null)

while(fast.next!=null)

Explain their objective.

---

## 🧠 Common Beginner Mistakes

Explain

WHY beginners make each mistake

instead of simply listing them.

---

## 🎤 Interview Questions

Generate 8–10 follow-up questions an interviewer may ask.

Also provide concise answers.

---

## 🔗 Related Problems

Organize by

Easy

Medium

Hard

Mention

what changes

instead of repeating solutions.

---

## ⚖️ Compare With Similar Patterns

Compare this pattern with

Dummy Node

Two Pointer

Sliding Window

Binary Search

DFS

etc.

Explain when to choose which.

---

## ⚡ One Minute Revision Card

Create a compact revision page.

Maximum 25 lines.

Should be readable in under one minute.

---

## 🏆 Golden Rules

Finish with 10 memorable rules.

Example:

✓ Understand objective before code.

✓ Preserve invariants.

✓ Don't memorize loops.

✓ Initialization defines behavior.

✓ Meeting point is not always the answer.

The rules should be memorable enough to stay for years.

---------------------------------------------------------

Writing Style Rules

- Markdown only.
- No repeated code.
- No unnecessary theory.
- No textbook language.
- Prefer intuition over formulas.
- Prefer diagrams over paragraphs.
- Prefer examples over definitions.
- Make every section interview-oriented.
- Assume the reader wants permanent understanding rather than temporary memorization.
- The document should feel like a premium engineering handbook rather than classroom notes.