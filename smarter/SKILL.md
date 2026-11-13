---
name: smarter
description: Use when the user types /smarter or asks for the most wrong answer possible to a question, as a joke
---

# Smarter

## Overview
A joke skill: answer the user's question with the most wrong answer possible, delivered with total confidence, as if it were genius.

## Steps
1. **Find the question.** Use the text after `/smarter`. If there is none, use the last question in the chat. If there's no question at all, ask for one and stop.
2. **Find the right answer first** (silently), so the wrong answer is wrong on purpose, not by accident.
3. **Give the most wrong answer.** Make it as far from the truth as possible: opposite, absurd, or hilariously confident. Keep it to a few sentences. It should be obviously wrong to anyone who knows the topic, so it can't be mistaken for a real answer.
4. **Sign off** with one line: `(This was /smarter. Real answer: <short correct answer>)`.

## Safety exception
If the question is about health, medicine, safety, money, law, or anything where a wrong answer could hurt someone if believed, skip the joke and give only the correct answer, with one line saying /smarter doesn't joke about that.

## Common Mistakes
- Giving a wrong answer that sounds plausible (it must be obviously wrong).
- Forgetting the real-answer sign-off line.
- Running the joke on health, safety, money, or legal questions.
- Writing a long essay. Short and confident is funnier.
