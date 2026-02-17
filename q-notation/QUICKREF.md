# Q-notation Quick Reference

**One-page guide to automation level encoding**

---

## The Rule

```
Hard C/K → Q = Automation indicator

WORK → WORQ    ✓
WEEK → WEEQ    ✓
QUICK → QUIQQ  ✓
```

---

## Q-Count = Automation Level

| Q's | Level | Control Mode | Example |
|-----|-------|--------------|---------|
| 0 | 0-2 | Manual | AGENTIC_WORK_WEEK |
| 1 | 3-5 | Agent-assisted | AGENTIQ_WORK_WEEK |
| 2 | 6-7 | Blended ⚠️ | AGENTIQ_WORQ_WEEK |
| 3+ | 8-10 | Automated | AGENTIQ_WORQ_WEEQ |

⚠️ **Level 6-7**: The "hard break" - humans lose proactive control

---

## Visual Guide

```
Automation Spectrum:

0 ←──────────────────────────────────────────────────────────→ 10
│                    │                    │                    │
Manual          Agent-assisted       Blended           Automated
WORK               WORQ               WORQ                WORQ
                                      WEEK                WEEQ
                                                         QUICK
                                                         
Human decides      Agent suggests     Agent decides      Agent full control
Human executes     Human executes     Human intervenes   Human rescue only
```

---

## Common Transformations

### Single Words

| Base | Q-form | Sound | Meaning |
|------|--------|-------|---------|
| WORK | WORQ | /wɜrk/ | Automated work |
| WEEK | WEEQ | /wiːk/ | Agent timeframe |
| TASK | TASQ | /tæsk/ | Automated task |
| CHECK | CHEQQ | /tʃɛk/ | Auto-validation |
| QUICK | QUIQQ | /kwɪk/ | Rapid automation |
| AGENTIC | AGENTIQ | /əˈdʒɛntɪk/ | Agent-driven |

### Compounds

```
WORK_WEEK        → WORQ_WEEQ         (2 Q's, Level ~6)
AGENTIC_WORK     → AGENTIQ_WORQ      (2 Q's, Level ~6)
QUICK_TASK       → QUIQQ_TASQ        (2 Q's, Level ~6)
AGENTIC_WORK_WEEK → AGENTIQ_WORQ_WEEQ (3 Q's, Level ~9)
```

---

## Practical Examples

### Code Configuration

```python
# Manual (0 Q's)
MODE = "WORK_CYCLE"
automation_level = 0

# Agent-assisted (1 Q)
MODE = "WORQ_CYCLE"
automation_level = 3

# Blended (2 Q's)
MODE = "WORQ_QUIQQ_CYCLE"
automation_level = 6

# Automated (3 Q's)
MODE = "AGENTIQ_WORQ_WEEQ"
automation_level = 9
```

### API Endpoints

```
/api/work/v1        → Manual processing
/api/worq/v1        → Automated processing
/api/worq/v2/quiqq  → Rapid automation
```

### CI/CD Stages

```
build.work          → Manual build
build.worq          → Automated build
deploy.worq.cheqq   → Auto-deploy with checks
monitor.worq.quiqq  → Auto-monitor with quick response
```

---

## Decision Tree

```
Does your process involve C/K sounds?
│
├─ YES → Can it be automated?
│        │
│        ├─ NO → Keep base form (WORK)
│        │
│        └─ YES → How much automation?
│                 │
│                 ├─ Agent suggests → +1 Q (WORQ)
│                 │
│                 ├─ Agent decides → +2 Q's (AGENTIQ_WORQ)
│                 │
│                 └─ Full automation → +3 Q's (AGENTIQ_WORQ_WEEQ)
│
└─ NO → Q-notation may not apply
         (Consider other domain terms)
```

---

## Rules of Thumb

### When to Add Q

✓ **DO** transform when:
- Automation level changes
- Agent involvement increases
- Manual → automated transition

✗ **DON'T** transform when:
- Word already has Q (QUARTER, QUEUE)
- Pronunciation would be unclear
- No automation context

### Counting Q's

```
Count Q's that represent C/K → Q transformations:

QUIQQ_WORQ_WEEQ
  ^      ^    ^
  1      2    3  = 3 Q's total

QUARTER → Still has Q, but it's original
          (not a transformation, doesn't count)
```

---

## Common Patterns

### Progressive Automation

```
Stage 1: WORK              (manual)
Stage 2: WORQ              (+1 Q)
Stage 3: WORQ_WEEQ         (+2 Q's)
Stage 4: AGENTIQ_WORQ_WEEQ (+3 Q's)
```

### Domain Naming

```
# DevOps
deploy.manual.check
deploy.tasq.cheqq
deploy.tasq.cheqq.quiqq

# Data Science
model.training.work
model.training.worq
model.agentiq.training.worq

# API
/v1/work
/v1/worq
/v2/worq/quiqq
```

---

## Automation Level Calculation

```
Formula: Level ≈ 3.33 × Q_count

0 Q's → Level 0   (3.33 × 0 = 0)
1 Q  → Level ~3  (3.33 × 1 = 3.33)
2 Q's → Level ~6  (3.33 × 2 = 6.66)
3 Q's → Level ~9  (3.33 × 3 = 9.99)

Max: Level 10
```

---

## Phonetic Preservation

**Key principle**: Q-forms sound the same as originals

```
WORK  /wɜrk/  → WORQ  /wɜrk/  ✓ Identical
WEEK  /wiːk/  → WEEQ  /wiːk/  ✓ Identical
QUICK /kwɪk/  → QUIQQ /kwɪk/  ✓ Identical

Spoken: No difference
Written: Clear automation indicator
```

---

## Anti-Patterns

### ❌ Avoid

```
WORQ → WORQQ        (don't double-Q)
QUARTER → QUARQER   (already has Q)
WORK_WORK → WORQ_WORQ_WORQ  (over-transforming)
```

### ✓ Prefer

```
WORK → WORQ                    (single transformation)
QUARTER → QUARTER              (leave as-is)
WORK_WEEK → WORQ_WEEQ          (compound transformation)
```

---

## Integration Checklist

Using Q-notation in your project?

- [ ] Identify C/K terms in your domain
- [ ] Define automation levels (0-10)
- [ ] Map Q-counts to levels
- [ ] Create Q-transformed versions
- [ ] Document automation thresholds
- [ ] Implement in code/config
- [ ] Test pronounceability
- [ ] Update documentation

---

## Resources

- **Full docs**: [README.md](README.md)
- **Theory**: [THEORY.md](THEORY.md) - Hofstadter, Levenshtein, etc.
- **Examples**: [EXAMPLES.md](EXAMPLES.md) - Code, use cases
- **Contributing**: [CONTRIBUTING.md](CONTRIBUTING.md)
- **Changelog**: [CHANGELOG.md](CHANGELOG.md)

---

## Remember

1. **Hard C/K → Q** = Automation indicator
2. **Count Q's** = Automation level
3. **Pronounce the same** = /k/ sound preserved
4. **Write differently** = Visual distinction
5. **Mean more** = Encode automation metadata

---

**Q-notation v0.1.0** | MIT License | [GitHub](../)
