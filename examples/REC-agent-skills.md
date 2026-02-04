# Agent Skills with Anthropic - Learning Notes

> Started: 2026-01-31
> Source: DeepLearning.AI Course (Elie Schoppik + Andrew Ng)

---

## Big Picture

```
╔════════════════════════════════════════════════════════════════╗
║                    AGENT SKILLS COURSE MAP                     ║
╠════════════════════════════════════════════════════════════════╣
║                                                                ║
║   PART 1: Core Concepts        PART 2: Practical Skills        ║
║   ┌─────────────────┐          ┌─────────────────┐            ║
║   │ L01 Introduction│          │ L06 Pre-Built   │            ║
║   │ L02 Materials   │          │ L07 Custom      │            ║
║   │ L03 Why Part 1  │          └─────────────────┘            ║
║   │ L04 Why Part 2  │                                         ║
║   │ L05 Skills vs X │          PART 3: Environments            ║
║   └─────────────────┘          ┌─────────────────┐            ║
║                                │ L08 Claude API  │            ║
║                                │ L09 Claude Code │            ║
║                                │ L10 Agent SDK   │            ║
║                                └─────────────────┘            ║
║                                                                ║
║                                PART 4: Conclusion              ║
║                                ┌─────────────────┐            ║
║                                │ L11 Conclusion  │            ║
║                                └─────────────────┘            ║
╚════════════════════════════════════════════════════════════════╝

Core concept:
Prompts → Skills → Subagents → MCP
         ↑
    Package reusable instructions
```

---

## Progress

```
[Part 1: Core] ✅ → [Part 2: Practical] ✅ → [Part 3: Environments] ✅ → [Part 4: Conclusion] ✅
                                                                              🎉 COMPLETE!
```

---

## Prerequisites

- [x] What is an LLM and how prompts work
- [x] Basic understanding of agents (can follow instructions, use tools)
- [x] Familiar with Claude (web UI or API)
- [ ] MCP basics (optional, covered in L05)

---

## Part 1: Core Concepts (L01-L05)

### 1.1 What is a Skill? [P0]

**One sentence**: Skill = Package your repetitive prompts into a reusable folder

```
    ╔═══════════════════════════════════════════════════════════╗
    ║                    🎯 Without Skills                       ║
    ╠═══════════════════════════════════════════════════════════╣
    ║                                                           ║
    ║   Week 1        Week 2        Week 3        Week 4        ║
    ║     │             │             │             │           ║
    ║     ▼             ▼             ▼             ▼           ║
    ║   ┌───┐         ┌───┐         ┌───┐         ┌───┐        ║
    ║   │📝│ Type     │📝│ Type     │📝│ Type     │📝│ Type    ║
    ║   │📝│ 500-word │📝│ 500-word │📝│ 500-word │📝│ 500-word║
    ║   │📝│ prompt   │📝│ prompt   │📝│ prompt   │📝│ prompt  ║
    ║   └───┘         └───┘         └───┘         └───┘        ║
    ║                                                           ║
    ║   😩 Repetitive work × 52 weeks/year                      ║
    ╚═══════════════════════════════════════════════════════════╝


    ╔═══════════════════════════════════════════════════════════╗
    ║                    ✨ With Skills                          ║
    ╠═══════════════════════════════════════════════════════════╣
    ║                                                           ║
    ║        Package once                                       ║
    ║           │                                               ║
    ║           ▼                                               ║
    ║      ┌─────────┐                                          ║
    ║      │ 📦 Skill │                                         ║
    ║      └────┬────┘                                          ║
    ║           │                                               ║
    ║     ┌─────┼─────┬─────┬─────┐                             ║
    ║     ▼     ▼     ▼     ▼     ▼                             ║
    ║   Week1 Week2 Week3 Week4  ...                            ║
    ║   "analyze" "analyze" "analyze" "analyze"                 ║
    ║                                                           ║
    ║   ✨ One word triggers the entire workflow                 ║
    ╚═══════════════════════════════════════════════════════════╝
```

**Skill Structure = A Folder**

```
    📁 analyzing-marketing-campaign/
    │
    ├── 📄 SKILL.md          ◄── The brain: all instructions here
    │   │
    │   │  ┌─────────────────────────────────┐
    │   │  │ ---                             │
    │   │  │ name: analyzing-marketing...    │ ◄── Name
    │   │  │ description: when to trigger    │ ◄── Trigger condition
    │   │  │ ---                             │
    │   │  │                                 │
    │   │  │ # Step 1: Data Quality          │
    │   │  │ # Step 2: Funnel Analysis       │ ◄── What to do
    │   │  │ # Step 3: Efficiency            │
    │   │  │                                 │
    │   │  │ If asked about budget → read    │ ◄── Lazy loading
    │   │  │ references/                     │
    │   │  └─────────────────────────────────┘
    │
    └── 📁 references/
        │
        └── 📄 budget_rules.md   ◄── Large files, load only when needed
```

🍞 **Breadcrumb**: Quiz asked "What problem does Skill solve?" → Answered correctly (B: Makes repetitive prompt workflows reusable)

---

### 1.2 Progressive Disclosure [P0]

**One sentence**: Only load information when needed, protect the Context Window

**Why does Context Window matter?**

```
    ╔═══════════════════════════════════════════════════════════════╗
    ║  Context Window = Claude's "working memory"                   ║
    ╠═══════════════════════════════════════════════════════════════╣
    ║                                                               ║
    ║      Imagine your desk:                                       ║
    ║                                                               ║
    ║      ┌─────────────────────────────────────────────┐         ║
    ║      │ 📄📄📄📄📄📄📄📄📄📄📄📄📄📄📄📄📄📄│  Fixed size    ║
    ║      │                                             │         ║
    ║      │   Too much stuff → Can't find what you need │         ║
    ║      │   Too little → Not enough information       │         ║
    ║      │                                             │         ║
    ║      └─────────────────────────────────────────────┘         ║
    ║                                                               ║
    ║      Context Window is the same:                              ║
    ║      • Too full → Quality drops, cost rises                   ║
    ║      • Too empty → Doesn't know what to do                    ║
    ║                                                               ║
    ╚═══════════════════════════════════════════════════════════════╝
```

**Progressive Disclosure = Layered Loading**

```
    ┌─────────────────────────────────────────────────────────────┐
    │                                                             │
    │   Level 1: Always in Context                                │
    │   ┌───────────────────────────────────────────────────┐    │
    │   │  📛 name: analyzing-marketing-campaign            │    │
    │   │  📝 description: trigger when analyzing campaigns │    │
    │   └───────────────────────────────────────────────────┘    │
    │         │                                                   │
    │         │  ← Tiny, almost no space                          │
    │         │                                                   │
    │ ════════════════════════════════════════════════════════   │
    │         │                                                   │
    │         ▼  User says "analyze campaign"                     │
    │                                                             │
    │   Level 2: Load after trigger                               │
    │   ┌───────────────────────────────────────────────────┐    │
    │   │  📄 Full SKILL.md content                         │    │
    │   │     - Step 1, 2, 3...                             │    │
    │   │     - Output format                               │    │
    │   └───────────────────────────────────────────────────┘    │
    │         │                                                   │
    │         │  ← Medium size                                    │
    │         │                                                   │
    │ ════════════════════════════════════════════════════════   │
    │         │                                                   │
    │         ▼  User asks "how to allocate budget"               │
    │                                                             │
    │   Level 3: Load only when needed                            │
    │   ┌───────────────────────────────────────────────────┐    │
    │   │  📁 references/budget_rules.md                    │    │
    │   │     - Detailed rules                              │    │
    │   │     - Could be large                              │    │
    │   └───────────────────────────────────────────────────┘    │
    │                                                             │
    └─────────────────────────────────────────────────────────────┘
```

**Analogy: Restaurant Menu**

```
    ╔═══════════════════════════════════════════════════════════╗
    ║                                                           ║
    ║   Level 1 (Street sign)    "Italian Restaurant"           ║
    ║         │                    ↑ Tells you what they sell   ║
    ║         ▼                                                 ║
    ║   Level 2 (Main menu)      Pizza / Pasta / Risotto        ║
    ║         │                    ↑ See it after entering      ║
    ║         ▼                                                 ║
    ║   Level 3 (Wine list)      Detailed red & white wines     ║
    ║                              ↑ Only shown when asked      ║
    ║                                                           ║
    ╚═══════════════════════════════════════════════════════════╝

    🎯 Progressive Disclosure = Don't dump the wine list on someone walking in
```

🍞 **Breadcrumb**: Both quiz questions correct ✅
- Q1: Why use Progressive Disclosure → B (Protect Context Window)
- Q2: When is name/description in Context → B (Always)

---

### 1.3 Skills vs Tools vs MCP vs Subagents [P1]

**One sentence**: Tools are low-level abilities, Skills are high-level workflows, MCP is external data, Subagents are independent workers

```
╔════════════════════════════════════════════════════════════════════╗
║                        🏗️ Building a House Analogy                 ║
╠════════════════════════════════════════════════════════════════════╣
║                                                                    ║
║   Tools (Instruments)                                              ║
║   ┌──────────────────────────────────────────────────────────┐    ║
║   │  🔨 Hammer    🪚 Saw    📏 Ruler                          │    ║
║   │                                                          │    ║
║   │  Lowest-level abilities: hit, cut, measure               │    ║
║   │  Claude built-in: Read, Write, Bash, Grep...             │    ║
║   └──────────────────────────────────────────────────────────┘    ║
║                              │                                     ║
║                              ▼                                     ║
║   Skills (Procedures/SOPs)                                         ║
║   ┌──────────────────────────────────────────────────────────┐    ║
║   │  📋 "How to build a table" SOP                           │    ║
║   │                                                          │    ║
║   │  1. Use saw to cut wood (uses Tool)                      │    ║
║   │  2. Use hammer to nail together (uses Tool)              │    ║
║   │  3. Use ruler to check if level (uses Tool)              │    ║
║   │                                                          │    ║
║   │  = High-level workflow, orchestrates Tools               │    ║
║   └──────────────────────────────────────────────────────────┘    ║
║                                                                    ║
╠════════════════════════════════════════════════════════════════════╣
║                                                                    ║
║   MCP (External Data Connections)                                  ║
║   ┌──────────────────────────────────────────────────────────┐    ║
║   │  🚚 Building Materials Supplier                          │    ║
║   │                                                          │    ║
║   │  Delivers: wood, nails, paint                            │    ║
║   │  = External data sources (DB, API, Google Drive...)      │    ║
║   │                                                          │    ║
║   │  MCP brings data in, Skill decides how to use it         │    ║
║   └──────────────────────────────────────────────────────────┘    ║
║                                                                    ║
╠════════════════════════════════════════════════════════════════════╣
║                                                                    ║
║   Subagents (Subcontractors)                                       ║
║   ┌──────────────────────────────────────────────────────────┐    ║
║   │  👷 Electrician    👷 Painter    👷 Carpenter             │    ║
║   │                                                          │    ║
║   │  Work independently, have their own tools and processes  │    ║
║   │  Report results back to general contractor               │    ║
║   │                                                          │    ║
║   │  = Independent context, can run in parallel              │    ║
║   └──────────────────────────────────────────────────────────┘    ║
║                                                                    ║
╚════════════════════════════════════════════════════════════════════╝
```

**Summary Table:**

```
┌─────────────┬─────────────────────┬──────────────────────┐
│             │ What it is          │ Example              │
├─────────────┼─────────────────────┼──────────────────────┤
│ Tools       │ Low-level abilities │ Read, Write, Bash    │
├─────────────┼─────────────────────┼──────────────────────┤
│ Skills      │ High-level SOPs     │ Analyze campaign     │
├─────────────┼─────────────────────┼──────────────────────┤
│ MCP         │ External data conn. │ BigQuery, Notion     │
├─────────────┼─────────────────────┼──────────────────────┤
│ Subagents   │ Independent workers │ Analyze files in     │
│             │                     │ parallel             │
└─────────────┴─────────────────────┴──────────────────────┘
```

**How do they work together?**

```
         ┌─────────────────────────────────────────┐
         │           Main Agent (Contractor)       │
         │                                         │
         │   📋 Skill: "Analyze customer feedback" │
         └───────────────┬─────────────────────────┘
                         │
         ┌───────────────┼───────────────┐
         ▼               ▼               ▼
    ┌─────────┐    ┌─────────┐    ┌─────────┐
    │ MCP     │    │Subagent │    │Subagent │
    │ Notion  │    │ Analyze │    │ Analyze │
    │ ─────── │    │ interviews│   │ surveys │
    │ Get data│    │         │    │         │
    └────┬────┘    └────┬────┘    └────┬────┘
         │              │              │
         │         (run in parallel)   │
         │              │              │
         └──────────────┴──────────────┘
                        │
                        ▼
                  📊 Integrated Results
```

🍞 **Breadcrumb**: Quiz all correct ✅ but wanted to understand Subagent's "independent context" deeper

#### Deep Dive: Subagent's Independent Context

**Question: Why do we need "independent context"?**

```
╔═══════════════════════════════════════════════════════════════════╗
║  Scenario: You need to analyze 100 customer interviews             ║
╠═══════════════════════════════════════════════════════════════════╣
║                                                                   ║
║  ❌ Without Subagents (all in one context)                        ║
║                                                                   ║
║  ┌─────────────────────────────────────────────────────────────┐ ║
║  │  Main Agent's Context Window                               │ ║
║  │  ┌─────────────────────────────────────────────────────┐   │ ║
║  │  │ Interview1 Interview2 Interview3 ... Interview100   │   │ ║
║  │  │ 📄📄📄📄📄📄📄📄📄📄📄📄📄📄📄📄📄📄📄📄│   │ ║
║  │  │                                                     │   │ ║
║  │  │  😵 Overflow! Context can't fit everything          │   │ ║
║  │  │  😵 Even if it fits, quality degrades               │   │ ║
║  │  └─────────────────────────────────────────────────────┘   │ ║
║  └─────────────────────────────────────────────────────────────┘ ║
║                                                                   ║
╚═══════════════════════════════════════════════════════════════════╝
```

```
╔═══════════════════════════════════════════════════════════════════╗
║  ✅ With Subagents (each has independent context)                  ║
╠═══════════════════════════════════════════════════════════════════╣
║                                                                   ║
║         Main Agent                                                ║
║         ┌──────────────────┐                                     ║
║         │ Only responsible: │                                     ║
║         │ • Assign tasks    │                                     ║
║         │ • Collect results │                                     ║
║         │ • Compile report  │                                     ║
║         └────────┬─────────┘                                     ║
║                  │                                                ║
║    ┌─────────────┼─────────────┬─────────────┐                   ║
║    ▼             ▼             ▼             ▼                   ║
║ ┌──────┐    ┌──────┐     ┌──────┐     ┌──────┐                  ║
║ │Sub 1 │    │Sub 2 │     │Sub 3 │     │ ...  │                  ║
║ │──────│    │──────│     │──────│     │──────│                  ║
║ │Int.  │    │Int.  │     │Int.  │     │      │                  ║
║ │1-25  │    │26-50 │     │51-75 │     │76-100│                  ║
║ │      │    │      │     │      │     │      │                  ║
║ │ 📄📄 │    │ 📄📄 │     │ 📄📄 │     │ 📄📄 │   ← Each has    ║
║ │ 📄📄 │    │ 📄📄 │     │ 📄📄 │     │ 📄📄 │     own context ║
║ └──┬───┘    └──┬───┘     └──┬───┘     └──┬───┘                  ║
║    │           │            │            │                       ║
║    │      (Run in parallel! Simultaneously)                      ║
║    │           │            │            │                       ║
║    └───────────┴────────────┴────────────┘                       ║
║                       │                                           ║
║                       ▼                                           ║
║              ┌─────────────────┐                                 ║
║              │  4 mini reports │                                 ║
║              │  → Merge into 1 │                                 ║
║              └─────────────────┘                                 ║
║                                                                   ║
╚═══════════════════════════════════════════════════════════════════╝
```

**Two major benefits:**

```
┌─────────────────────────────────────────────────────────────┐
│                                                             │
│  1️⃣ Break through Context limits                            │
│                                                             │
│     One Agent can't fit it → Split into many, each handles  │
│                                                             │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  2️⃣ Parallel = Fast                                         │
│                                                             │
│     ❌ Sequential: Int1 → Int2 → Int3 → ... (100 steps)     │
│                    Total time = 100 × unit time             │
│                                                             │
│     ✅ Parallel:  ┌ Sub1 ─────┐                             │
│                   │ Sub2 ─────│  Run simultaneously         │
│                   │ Sub3 ─────│                             │
│                   └ Sub4 ─────┘                             │
│                    Total time ≈ 25 × unit time (4x faster)  │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

🍞 **Breadcrumb**: Follow-up "Is Subagent's context capacity different from Main Agent?"

**Answer: No difference, each is a full Claude (200K)**

```
┌─────────────────────────────────────────────────────────────┐
│                                                             │
│  Main Agent     Sub 1      Sub 2      Sub 3      Sub 4     │
│  ┌───────┐    ┌───────┐  ┌───────┐  ┌───────┐  ┌───────┐  │
│  │       │    │       │  │       │  │       │  │       │  │
│  │ 200K  │    │ 200K  │  │ 200K  │  │ 200K  │  │ 200K  │  │
│  │       │    │       │  │       │  │       │  │       │  │
│  └───────┘    └───────┘  └───────┘  └───────┘  └───────┘  │
│                                                             │
│  Key insight: Not "splitting context" but "opening new"     │
│  Total capacity = 200K × 5 = 1000K (5x!)                    │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## Part 2: Practical Skills (L06-L07)

### 2.1 Pre-Built Skills [P0]

**One sentence**: Skills Anthropic already made, use directly, no reinventing the wheel

**Why need Pre-Built?**

```
╔═══════════════════════════════════════════════════════════════════╗
║                                                                   ║
║   Problem: Everyone needs "make Excel" "make PPT" "make PDF"      ║
║                                                                   ║
║   Without Pre-Built:                                              ║
║   ┌─────────────────────────────────────────────────────────┐    ║
║   │   User A: Write own Excel Skill                         │    ║
║   │   User B: Write own Excel Skill                         │    ║
║   │   User C: Write own Excel Skill                         │    ║
║   │                                                         │    ║
║   │   😩 Reinventing wheels                                 │    ║
║   └─────────────────────────────────────────────────────────┘    ║
║                                                                   ║
║   With Pre-Built:                                                 ║
║   ┌─────────────────────────────────────────────────────────┐    ║
║   │   Anthropic makes it once → Everyone uses it directly   │    ║
║   │                                                         │    ║
║   │   ✨ Saves time, quality guaranteed                     │    ║
║   └─────────────────────────────────────────────────────────┘    ║
║                                                                   ║
╚═══════════════════════════════════════════════════════════════════╝
```

**What Pre-Built Skills exist?**

```
┌─────────────────────────────────────────────────────────────┐
│                                                             │
│   📌 Document Skills (Always on, you don't manage them)     │
│                                                             │
│      📊 Excel                                               │
│      📽️ PowerPoint                                          │
│      📄 Word                                                │
│      📕 PDF                                                 │
│                                                             │
│   You say "make me an Excel" → Claude auto-uses this Skill  │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

🍞 **Breadcrumb**: Quiz correct ✅ Follow-up "Does always-on take up context?"

**Answer: Almost none!**

```
┌─────────────────────────────────────────────────────────────┐
│                                                             │
│   What's always in Context:                                 │
│   ┌───────────────────────────────────────────────────┐    │
│   │  📛 name: excel                                   │    │
│   │  📝 description: use when generating Excel        │    │
│   └───────────────────────────────────────────────────┘    │
│                                                             │
│   ← Super small, just a few bytes                           │
│                                                             │
│   The full SKILL.md content?                                │
│   → Only loads when you say "make me an Excel"              │
│   → Takes no space when not used                            │
│                                                             │
│   🔗 This is the Progressive Disclosure we learned in Part 1!│
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

### 2.2 Skill-Creator [P0]

**One sentence**: A Skill that creates Skills — the "creator", auto-builds skeleton, validates, packages

**Why need skill-creator?**

```
╔═══════════════════════════════════════════════════════════════════╗
║                                                                   ║
║   Problem: Building a Skill from scratch is tedious               ║
║                                                                   ║
║   ┌─────────────────────────────────────────────────────────┐    ║
║   │   1. Create folder                                      │    ║
║   │   2. Create SKILL.md                                    │    ║
║   │   3. Write YAML frontmatter (format must be correct)    │    ║
║   │   4. Create references/ folder                          │    ║
║   │   5. Package into .zip                                  │    ║
║   │   6. Validate format is correct                         │    ║
║   │                                                         │    ║
║   │   😩 Many steps, easy to make mistakes                  │    ║
║   └─────────────────────────────────────────────────────────┘    ║
║                                                                   ║
║   With skill-creator:                                             ║
║   ┌─────────────────────────────────────────────────────────┐    ║
║   │   You: "Make me a skill for analyzing campaigns"        │    ║
║   │                                                         │    ║
║   │   skill-creator: Auto-completes all 6 steps above       │    ║
║   │                                                         │    ║
║   │   ✨ One sentence, Skill is born                        │    ║
║   └─────────────────────────────────────────────────────────┘    ║
║                                                                   ║
╚═══════════════════════════════════════════════════════════════════╝
```

**What does skill-creator do behind the scenes?**

```
╔═══════════════════════════════════════════════════════════════════╗
║                    🌟 skill-creator 3 Steps                       ║
╠═══════════════════════════════════════════════════════════════════╣
║                                                                   ║
║   You say: "Make me a skill for analyzing marketing campaigns"    ║
║                                                                   ║
║         │                                                         ║
║         ▼                                                         ║
║   ┌─────────────────────────────────────────────────────────┐    ║
║   │  Step 1: init_skill.py (Initialize - build skeleton)    │    ║
║   │  ─────────────────────                                  │    ║
║   │                                                         │    ║
║   │  ┌─────────────────────────────────────────────────┐   │    ║
║   │  │  📁 analyzing-marketing-campaign/               │   │    ║
║   │  │  ├── 📄 SKILL.md      ← Auto-generate skeleton │   │    ║
║   │  │  │   │                                          │   │    ║
║   │  │  │   │  ---                                     │   │    ║
║   │  │  │   │  name: analyzing-marketing-campaign      │   │    ║
║   │  │  │   │  description: [auto-filled]              │   │    ║
║   │  │  │   │  ---                                     │   │    ║
║   │  │  │   │  # Instructions                          │   │    ║
║   │  │  │   │  [auto-written based on your needs]      │   │    ║
║   │  │  │                                              │   │    ║
║   │  │  ├── 📁 references/    ← Auto-created          │   │    ║
║   │  │  └── 📁 assets/        ← Auto-created          │   │    ║
║   │  └─────────────────────────────────────────────────┘   │    ║
║   └─────────────────────────────────────────────────────────┘    ║
║                                                                   ║
║         │                                                         ║
║         ▼                                                         ║
║   ┌─────────────────────────────────────────────────────────┐    ║
║   │  Step 2: validate_skill.py (Validate - check format)    │    ║
║   │  ──────────────────────────                             │    ║
║   │                                                         │    ║
║   │  Checklist:                                             │    ║
║   │  ┌─────────────────────────────────────────────────┐   │    ║
║   │  │  ✅ Does SKILL.md exist?                        │   │    ║
║   │  │  ✅ Is YAML frontmatter format correct?         │   │    ║
║   │  │  ✅ Is name filled in?                          │   │    ║
║   │  │  ✅ Is description filled in?                   │   │    ║
║   │  │  ✅ Is folder structure correct?                │   │    ║
║   │  └─────────────────────────────────────────────────┘   │    ║
║   │                                                         │    ║
║   │  ❌ Has errors → Go back and fix                        │    ║
║   │  ✅ All good → Next step                                │    ║
║   └─────────────────────────────────────────────────────────┘    ║
║                                                                   ║
║         │                                                         ║
║         ▼                                                         ║
║   ┌─────────────────────────────────────────────────────────┐    ║
║   │  Step 3: package_skill.py (Package - zip it up)         │    ║
║   │  ─────────────────────────                              │    ║
║   │                                                         │    ║
║   │  ┌─────────────────┐         ┌─────────────────┐       │    ║
║   │  │ 📁 Folder       │   →→→   │ 📦 .zip file    │       │    ║
║   │  │ analyzing-...   │ Package │ analyzing-...   │       │    ║
║   │  └─────────────────┘         └─────────────────┘       │    ║
║   │                                                         │    ║
║   │  This .zip can be:                                      │    ║
║   │  • Uploaded to Claude AI                                │    ║
║   │  • Shared with others                                   │    ║
║   │  • Put in Claude Code                                   │    ║
║   └─────────────────────────────────────────────────────────┘    ║
║                                                                   ║
║         │                                                         ║
║         ▼                                                         ║
║                                                                   ║
║   🎉 Done! Your Skill is ready to use                             ║
║                                                                   ║
╚═══════════════════════════════════════════════════════════════════╝
```

🍞 **Breadcrumb**: Quiz correct ✅ Explained three steps in my own words: Initialize → Validate → Package

---

### 2.3 Skills + MCP Integration [P1]

**One sentence**: Skill handles "how to do it", MCP handles "data source", combine them and you don't need to manually feed data

**Why combine them?**

```
╔═══════════════════════════════════════════════════════════════════╗
║                                                                   ║
║   Previous problem: marketing campaign skill needs manual CSV     ║
║                                                                   ║
║   ┌─────────────────────────────────────────────────────────┐    ║
║   │                                                         │    ║
║   │   Every week:                                           │    ║
║   │   1. Go to database to pull data   ┐                    │    ║
║   │   2. Export as CSV                 ├── 😩 Tedious       │    ║
║   │   3. Upload to Claude              ┘                    │    ║
║   │   4. Say "analyze this"                                 │    ║
║   │                                                         │    ║
║   └─────────────────────────────────────────────────────────┘    ║
║                                                                   ║
║   ─────────────────────────────────────────────────────────────   ║
║                                                                   ║
║   Solution: Skill + MCP                                           ║
║                                                                   ║
║   ┌─────────────────────────────────────────────────────────┐    ║
║   │                                                         │    ║
║   │   Every week:                                           │    ║
║   │   1. Say "analyze last week's campaign" ← One sentence! │    ║
║   │                                                         │    ║
║   │      MCP auto-fetches data from BigQuery                │    ║
║   │      Skill auto-analyzes                                │    ║
║   │                                                         │    ║
║   └─────────────────────────────────────────────────────────┘    ║
║                                                                   ║
╚═══════════════════════════════════════════════════════════════════╝
```

**What does each handle?**

```
┌─────────────────────────────────────────────────────────────┐
│                                                             │
│   ┌─────────────┐              ┌─────────────┐             │
│   │   Skill     │              │    MCP      │             │
│   │  ─────────  │     ←──→     │  ─────────  │             │
│   │             │   Collaborate │             │             │
│   │ "How to do" │              │"Data source"│             │
│   │             │              │             │             │
│   │  • Calc CTR │              │  BigQuery   │             │
│   │  • Calc ROAS│              │  Notion     │             │
│   │  • Output   │              │  Google     │             │
│   │    format   │              │  Drive...   │             │
│   └─────────────┘              └─────────────┘             │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

🍞 **Breadcrumb**: Quiz correct ✅ My own words: "Skill handles how to do things, MCP handles getting data"

---

### 🔗 P0 + P1 + P2 Complete Connection

```
╔═══════════════════════════════════════════════════════════════════╗
║                    Part 2 Complete Connection                     ║
╠═══════════════════════════════════════════════════════════════════╣
║                                                                   ║
║   P0: How to "get" a Skill                                        ║
║   ┌─────────────────────────────────────────────────────────┐    ║
║   │  Pre-Built (ready-made)  or  Skill-Creator (DIY)        │    ║
║   └──────────────────────────┬──────────────────────────────┘    ║
║                              │                                    ║
║                              ▼                                    ║
║   P1: How to "enhance" a Skill                                    ║
║   ┌─────────────────────────────────────────────────────────┐    ║
║   │  Skill + MCP = Auto-fetch data, no manual feeding       │    ║
║   └──────────────────────────┬──────────────────────────────┘    ║
║                              │                                    ║
║                              ▼                                    ║
║   P2: How to "combine" Skills                                     ║
║   ┌─────────────────────────────────────────────────────────┐    ║
║   │  Multi-Skill collaboration = Pro team, complex task     │    ║
║   │  done with one sentence                                 │    ║
║   └─────────────────────────────────────────────────────────┘    ║
║                                                                   ║
╚═══════════════════════════════════════════════════════════════════╝
```

---

## Part 3: Environments (L08-L10)

### 🗺️ Gym Map

```
╔═══════════════════════════════════════════════════════════════════╗
║                    🗺️ AGENT SKILLS GYM CHALLENGE                  ║
╠═══════════════════════════════════════════════════════════════════╣
║                                                                   ║
║   🏆 Part 1        🏆 Part 2        🏆 Part 3        🏆 Part 4     ║
║   Core Gym         Practical Gym    Environment Gym  Conclusion   ║
║   ──────────      ──────────      ──────────      ──────────    ║
║   [Conquered ✅]   [Conquered ✅]   [Conquered ✅]   [Conquered ✅]║
║                                                                   ║
║   Badges:          Badges:          Badges:          Badges:      ║
║   🥇 Skill        🥇 Pre-Built    🥇 Claude Code   🥇 Perfect 10 ║
║   🥇 Progressive  🥇 Creator      🥇 Agent SDK                   ║
║   🥇 Four Roles   🥇 MCP Integration                             ║
║                   🥇 Combo Skills                                 ║
║                                                                   ║
╚═══════════════════════════════════════════════════════════════════╝
```

---

### 3.1 Skills with Claude Code [P0]

**One sentence**: Skills go in ~/.claude/skills/ (global) or ./.claude/skills/ (project)

**Where do Skills go?**

```
┌─────────────────────────────────────────────────────────────────┐
│                                                                 │
│   Two locations:                                                │
│                                                                 │
│   1️⃣ Global Skills (available to all projects)                  │
│   ┌─────────────────────────────────────────────────────────┐  │
│   │                                                         │  │
│   │   ~/.claude/skills/                                     │  │
│   │   ├── grind/           ← The one you're using now!      │  │
│   │   ├── code-review/                                      │  │
│   │   └── ...                                               │  │
│   │                                                         │  │
│   └─────────────────────────────────────────────────────────┘  │
│                                                                 │
│   2️⃣ Project Skills (only this project can use)                 │
│   ┌─────────────────────────────────────────────────────────┐  │
│   │                                                         │  │
│   │   ./your-project/.claude/skills/                        │  │
│   │   └── project-specific-skill/                           │  │
│   │                                                         │  │
│   └─────────────────────────────────────────────────────────┘  │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

🍞 **Breadcrumb**:
- Quiz: Where do global Skills go?
- My answer: "~/.claude/skills/"
- Follow-up: "What does ~ mean?" → Explained Home directory

---

### 3.2 Skills with Agent SDK [P1]

**One sentence**: Agent SDK lets you write complex Agents in Python/TypeScript, load Skills via system prompt

**When to use which?**

```
┌─────────────────────────────────────────────────────────────┐
│                                                             │
│   Claude Code                   Agent SDK                   │
│   ──────────                   ──────────                   │
│                                                             │
│   ✅ Interactive dialogue       ✅ Automated pipelines       │
│   ✅ Quick prototypes           ✅ Multi-agent collaboration │
│   ✅ One-off tasks              ✅ Production services       │
│   ✅ No coding required         ✅ Fine-grained control      │
│                                                             │
│   🎯 Decision key:                                          │
│   Human watching alongside? → Claude Code                   │
│   Needs to run automatically, Production? → Agent SDK       │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

### 🔗 P0 + P1 Connection

```
╔═══════════════════════════════════════════════════════════════════╗
║                    Part 3: P0 + P1 Connection                     ║
╠═══════════════════════════════════════════════════════════════════╣
║                                                                   ║
║   P0: Claude Code                                                 ║
║   ┌─────────────────────────────────────────────────────────┐    ║
║   │  Skills in ~/.claude/skills/ (global) or in project     │    ║
║   │  Best for: Interactive, one-off, quick prototypes       │    ║
║   └──────────────────────────┬──────────────────────────────┘    ║
║                              │                                    ║
║                              ▼                                    ║
║   P1: Agent SDK                                                   ║
║   ┌─────────────────────────────────────────────────────────┐    ║
║   │  SKILL.md content goes into system prompt               │    ║
║   │  Best for: Automation, Production, multi-agent          │    ║
║   │  Advantage: Fine error handling + Observability (3rd    │    ║
║   │  party tools)                                           │    ║
║   └─────────────────────────────────────────────────────────┘    ║
║                                                                   ║
╚═══════════════════════════════════════════════════════════════════╝
```

---

## 🔗 Part 1 → Part 2 → Part 3 Complete Concept Map

```
╔═══════════════════════════════════════════════════════════════════════════╗
║                         COMPLETE COURSE FLOW                              ║
╠═══════════════════════════════════════════════════════════════════════════╣
║                                                                           ║
║   Part 1: What is it?                                                     ║
║   ─────────────────                                                       ║
║                                                                           ║
║   ┌─────────────────────────────────────────────────────────────────┐    ║
║   │                                                                 │    ║
║   │   Skill = Package reusable prompt workflows                     │    ║
║   │                                                                 │    ║
║   │   📁 skill-folder/                                              │    ║
║   │   ├── SKILL.md      ← Brain (name + description + instructions) │    ║
║   │   └── references/   ← Large files, load when needed             │    ║
║   │                                                                 │    ║
║   │   Progressive Disclosure = Layered loading, protect Context     │    ║
║   │   • Level 1: name + description (always there)                  │    ║
║   │   • Level 2: Full SKILL.md content (after trigger)              │    ║
║   │   • Level 3: references/ (when asked)                           │    ║
║   │                                                                 │    ║
║   │   Four Roles:                                                   │    ║
║   │   • Tools = Low-level abilities (Read, Write, Bash)             │    ║
║   │   • Skills = High-level workflow SOPs                           │    ║
║   │   • MCP = External data connections                             │    ║
║   │   • Subagents = Independent context, parallel work              │    ║
║   │                                                                 │    ║
║   └─────────────────────────────────────────────────────────────────┘    ║
║                                         │                                 ║
║                                         ▼                                 ║
║   Part 2: How to get / enhance?                                           ║
║   ────────────────────────────────                                        ║
║                                                                           ║
║   ┌─────────────────────────────────────────────────────────────────┐    ║
║   │                                                                 │    ║
║   │   Two ways to get Skills:                                       │    ║
║   │                                                                 │    ║
║   │   1️⃣ Pre-Built (ready-made)                                     │    ║
║   │      Excel, PPT, Word, PDF → Always on, use directly            │    ║
║   │                                                                 │    ║
║   │   2️⃣ Skill-Creator (DIY)                                        │    ║
║   │      init_skill.py → validate_skill.py → package_skill.py       │    ║
║   │      (skeleton)       (check format)      (package .zip)        │    ║
║   │                                                                 │    ║
║   │   ─────────────────────────────────────────────────────────     │    ║
║   │                                                                 │    ║
║   │   Enhance Skills:                                               │    ║
║   │                                                                 │    ║
║   │   3️⃣ Skill + MCP                                                │    ║
║   │      Skill = how to do    MCP = data source                     │    ║
║   │      Combined = auto-fetch data, no manual feeding              │    ║
║   │                                                                 │    ║
║   │   4️⃣ Multi-Skill Combo                                          │    ║
║   │      Like a pro team: Accounting + Design + Product + Growth    │    ║
║   │      One sentence triggers multi-Skill collaboration            │    ║
║   │                                                                 │    ║
║   └─────────────────────────────────────────────────────────────────┘    ║
║                                         │                                 ║
║                                         ▼                                 ║
║   Part 3: Where to use?                                                   ║
║   ──────────────────                                                      ║
║                                                                           ║
║   ┌─────────────────────────────────────────────────────────────────┐    ║
║   │                                                                 │    ║
║   │   Claude Code (Interactive)                                     │    ║
║   │   ┌─────────────────────────────────────────────────────────┐  │    ║
║   │   │  Where to put Skills:                                   │  │    ║
║   │   │  • ~/.claude/skills/   ← Global                         │  │    ║
║   │   │  • ./.claude/skills/   ← Project                        │  │    ║
║   │   │                                                         │  │    ║
║   │   │  Best for: Human alongside, one-off, quick prototypes   │  │    ║
║   │   └─────────────────────────────────────────────────────────┘  │    ║
║   │                                                                 │    ║
║   │   Agent SDK (Programmatic control)                              │    ║
║   │   ┌─────────────────────────────────────────────────────────┐  │    ║
║   │   │  How to load: SKILL.md goes into system prompt          │  │    ║
║   │   │                                                         │  │    ║
║   │   │  Best for: Automation, Production, multi-agent          │  │    ║
║   │   │                                                         │  │    ║
║   │   │  Advantages:                                            │  │    ║
║   │   │  • Fine-grained error handling (try/except)             │  │    ║
║   │   │  • Observability (need MLflow/Langfuse etc.)            │  │    ║
║   │   └─────────────────────────────────────────────────────────┘  │    ║
║   │                                                                 │    ║
║   └─────────────────────────────────────────────────────────────────┘    ║
║                                                                           ║
╚═══════════════════════════════════════════════════════════════════════════╝
```

---

## 🎯 One Sentence Summary

```
┌─────────────────────────────────────────────────────────────────────────┐
│                                                                         │
│   Part 1        →        Part 2        →        Part 3                  │
│                                                                         │
│   What is Skill      How to get/enhance     Where to use                │
│   ─────────────     ──────────────        ──────────                    │
│                                                                         │
│   Package prompts    Pre-Built ready-made   Claude Code                 │
│   + Progressive      Skill-Creator DIY      (interactive)               │
│     Disclosure       + MCP for data                                     │
│   + Four Roles       + Multi-Skill combo    Agent SDK                   │
│                                             (automation)                │
│                                                                         │
│   ─────────────────────────────────────────────────────────────────     │
│                                                                         │
│   🎯 Complete flow:                                                      │
│                                                                         │
│   1. Use Skill-Creator to make a Skill (or use Pre-Built)               │
│   2. Connect MCP so Skill can auto-fetch data                           │
│   3. Can combine multiple Skills for complex tasks                      │
│   4. Deploy to Claude Code (interactive) or Agent SDK (automation)      │
│                                                                         │
└─────────────────────────────────────────────────────────────────────────┘
```

---

## Part 4: Conclusion (L11)

### 🎓 Course Complete!

```
╔═══════════════════════════════════════════════════════════════════╗
║                                                                   ║
║                    🎉 Agent Skills Course Complete! 🎉            ║
║                                                                   ║
║   ┌─────────────────────────────────────────────────────────┐    ║
║   │                                                         │    ║
║   │   📊 Report Card                                        │    ║
║   │                                                         │    ║
║   │   Part 1 Quiz: 4/4 ✅                                   │    ║
║   │   Part 2 Quiz: All correct ✅                           │    ║
║   │   Part 3 Quiz: All correct ✅                           │    ║
║   │   Part 4 Final: 10/10 💯                                │    ║
║   │                                                         │    ║
║   │   ─────────────────────────────────────────────────     │    ║
║   │                                                         │    ║
║   │   🏆 Badges Collected: 9/9                              │    ║
║   │                                                         │    ║
║   │   🥇 Skill           🥇 Pre-Built      🥇 Claude Code   │    ║
║   │   🥇 Progressive     🥇 Creator        🥇 Agent SDK     │    ║
║   │   🥇 Four Roles      🥇 MCP Integration 🥇 Perfect 10   │    ║
║   │                      🥇 Combo Skills                    │    ║
║   │                                                         │    ║
║   └─────────────────────────────────────────────────────────┘    ║
║                                                                   ║
║   Completion Date: 2026-01-31                                     ║
║                                                                   ║
╚═══════════════════════════════════════════════════════════════════╝
```

---

## Quiz Results

| Part | Score | Date |
|------|-------|------|
| 1    | 4/4 ✅ | 2026-01-31 |
| 2    | All correct ✅ | 2026-01-31 |
| 3    | All correct ✅ | 2026-01-31 |
| 4    | 10/10 💯 | 2026-01-31 |

---

## 🎓 Certificate of Completion

```
╔═══════════════════════════════════════════════════════════════════╗
║                                                                   ║
║                    CERTIFICATE OF COMPLETION                      ║
║                                                                   ║
║                    Agent Skills with Anthropic                    ║
║                                                                   ║
║                    Completed: 2026-01-31                          ║
║                    Score: 100%                                    ║
║                                                                   ║
║                    🏆 All badges collected                        ║
║                                                                   ║
╚═══════════════════════════════════════════════════════════════════╝
```

---

*Last updated: 2026-01-31*
