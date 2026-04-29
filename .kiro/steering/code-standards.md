---
inclusion: always
---

# Code Standards

## Global Rules

- Every unit of code MUST have a single responsibility (SRP)
- No duplicated logic (DRY) — extract to function/utility
- Readable without excessive comments
- Use descriptive variable names, no magic numbers
- Avoid deeply nested logic
- Language: English (code & naming)
- Style: clean, minimal, readable — no over-engineering

## SOLID Principles

### SRP
- 1 class/function = 1 responsibility
- If a function does >1 action → split it
- Separate: Data → Service → Controller

### OCP
- Don't modify existing code to add new features
- Add behavior via abstraction/interface (Strategy Pattern, Plugin Pattern)

### LSP
- Subclasses must be compatible with their parent
- Don't change return types or core behavior in subclasses

### ISP
- Keep interfaces small and specific
- If an interface is too large → split it

### DIP
- Depend on abstractions, not implementations
- Use dependency injection

## Clean Code

### Naming
```js
// GOOD
getUserProfile()
calculateTotalPrice()

// BAD
doStuff()
dataProcess()
```

### Function Design
- Max 20–30 lines per function
- 1 function = 1 purpose
- If it needs a long comment → the function is wrong

### Error Handling
```js
// GOOD
try { ... } catch (error) { logError(error) }

// BAD — silent errors, empty catch
```

### Structure
```
controllers/
services/
repositories/
utils/
```

## AI Generation Rules

When generating code:
1. Problem → Breakdown → Implementation
2. Use modular structure and reusable functions
3. Avoid over-abstraction and premature optimization

## Output Checklist

Every generated file must be:
- [ ] Clean & formatted
- [ ] No unnecessary comments
- [ ] Follows SOLID
- [ ] Follows Clean Code
- [ ] Consistent naming

## Quick Reference

| Situation | Action |
|---|---|
| Function too long | SPLIT |
| Repeated logic | EXTRACT |
| Class too large | DECOMPOSE |
| Direct dependency | INJECT |
