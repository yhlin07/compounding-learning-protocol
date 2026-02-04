# Evolution: v1 → v4.1 in 4 Days

This document traces how the Grind protocol evolved through the Friction Flywheel methodology.

## Timeline

```
Jan 30 ──── Jan 31 ──── Jan 31 ──── Jan 31 ──── Jan 31
   │           │           │           │           │
  v1          v2          v3          v4         v4.1
   │           │           │           │           │
Initial    16 fixes    5 fixes     5 fixes    Bug fix
```

---

## v1 → v2: First Real Usage (16 Improvements)

**What happened**: Used v1 to learn a real course for the first time.

**Friction discovered**:
- No visual progress tracking
- Concepts dumped all at once (overwhelming)
- Notes recorded "right/wrong" instead of actual learning journey
- File naming was confusing

**Improvements made**:
- Added Gym Map at start of each Part (visual progress)
- Changed teaching rhythm: P0→Quiz→P1→Quiz→P2→Quiz (one at a time)
- Added cumulative connections (P0 → P0+P1 → P0+P1+P2)
- Breadcrumbs now record user's actual words + emotions + insights
- Renamed files from `LEARNING-NOTES.md` to `REC-{course-name}.md`
- Added Learning Objectives + Self-Check List
- Added final exam format (10-20 multiple choice)
- Visual-first became default behavior

---

## v2 → v3: Second Course (5 Improvements)

**What happened**: Used v2 to learn a different course.

**Friction discovered**:
- Part count was hardcoded (didn't fit all content types)
- Prerequisites were just labels (not helpful)
- Unclear when to save notes
- Too much text, not enough visuals

**Improvements made**:
- Part count now dynamic based on content structure
- Prerequisites must have icon + explanation + "what you need to know"
- Explicit SAVE Triggers (Quiz answered → save, Follow-up answered → save)
- Visual Checkpoint rule: if text > 3 sentences → stop and draw a diagram

---

## v3 → v4: Third Course (5 Improvements)

**What happened**: Used v3 to learn yet another course.

**Friction discovered**:
- Multiple choice quizzes let you guess right without understanding
- No cumulative visualization across Parts (only within Parts)
- Confused two types of cumulation

**Improvements made**:
- Feynman Check vs Quiz distinction:
  - Feynman Check (during learning): open-ended, "explain in your own words"
  - Quiz (final exam only): multiple choice, tests memory
- Part-level cumulative visualization: Part N = visualize Parts 1 through N
- Clarified two cumulation types:
  - P0→P1→P2 = within a Part
  - Part 1→2→3 = across Parts

---

## v4 → v4.1: Bug Fix

**What happened**: Noticed ASCII art breaking in markdown preview.

**Friction discovered**:
- ASCII diagrams rendered incorrectly (proportional font issues)

**Improvement made**:
- All ASCII art must be wrapped in code blocks (forces monospace font)

---

## Pattern Recognition

Looking back at all 5 versions, a clear pattern emerges:

| Version | Friction Source | Category |
|---------|-----------------|----------|
| v2 | First real usage | Overwhelming complexity |
| v3 | Different content type | Rigidity in structure |
| v4 | Deeper usage | Shallow understanding masked as learning |
| v4.1 | Edge case | Rendering bug |

**Key insight**: You can't design a good system in isolation. You have to use it, feel the friction, and iterate.

---

## The Flywheel in Action

```
Use Grind v1
    ↓
Friction: "This is overwhelming"
    ↓
Log: 16 specific problems
    ↓
Feed Back: Update to v2
    ↓
Use Grind v2
    ↓
Friction: "Prerequisites are useless labels"
    ↓
Log: 5 specific problems
    ↓
Feed Back: Update to v3
    ↓
... and so on
```

Each cycle made the system smarter.
Each friction point became fuel for improvement.

**The obstacle was the way.**
