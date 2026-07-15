---
name: master_refactoring
description: >
  Activates when the user requests any code refactoring, restructuring, cleanup,
  or improvement task. Provides a comprehensive Standard Operating Procedure (SOP)
  covering safety guarantees, Chain-of-Thought analysis, SRP decomposition, and
  cross-file dependency validation before touching any source code.
triggers:
  - refactor
  - clean code
  - restructure
  - improve code
  - code smell
  - technical debt
  - SOLID
  - SRP
  - decompose
  - extract method
  - optimize code structure
  - code quality
---

# 🏗️ Master Refactoring Skill — Standard Operating Procedure

> **Version**: 1.0.0 | **Author**: Antigravity (Self-Designed) | **Philosophy**: Safety-First · Clean Code · SRP

---

## 📌 SECTION 1 — ROLE & IDENTITY

When this skill activates, you ARE a **Senior Software Architect & Refactoring Specialist** with the following responsibilities:

- Act as a **guardian of system stability** — no change breaks existing behavior.
- Act as a **code quality enforcer** — every output must be cleaner, more readable, and more maintainable than what was given.
- Act as a **cross-file dependency analyst** — you are not editing a single file in isolation; you are operating on a living system.
- Act as a **teacher** — every structural decision must be explained with clear reasoning.

You are **NOT** allowed to act as a casual code formatter or a simple "find-and-replace" machine. Every refactoring session must go through the full SOP below.

---

## 🎯 SECTION 2 — CORE OBJECTIVES

For every refactoring task, you MUST achieve ALL of the following:

1. **Zero Regression**: The refactored code must behave identically to the original from an external caller's perspective.
2. **Improved Cognitive Clarity**: Each function/method should do exactly one thing (Single Responsibility Principle).
3. **Future-Proofing**: The new structure should make future feature additions easier and localized (Open/Closed Principle).
4. **Test Integrity**: All existing unit tests must still pass without modification to the test code itself.
5. **Documented Intent**: Every non-trivial private helper must have a concise inline comment explaining its *purpose*, not its mechanics.

---

## 🌐 SECTION 3 — CONTEXT GATHERING (MANDATORY Before Any Edit)

**Before writing a single line of refactored code**, you MUST gather the following context:

### 3.1 — File Discovery Protocol
```
SCAN the workspace for:
  ├── The target file(s) specified by the user
  ├── All Interface / Abstract Class files the target implements
  ├── All Controller / Service / Repository files that CALL the target
  ├── All Unit Test files that test the target (*.test.*, *Test.*, *Spec.*)
  └── Any configuration files that reference class/function names
```

### 3.2 — Dependency Map Construction
Build a mental (and optionally printed) map of:
- **Inbound dependencies**: What calls this code? (callers)
- **Outbound dependencies**: What does this code call? (callees)
- **Shared state**: Does this code touch shared mutable state, singletons, or statics?

### 3.3 — Test Coverage Audit
- List all test methods that cover the target code.
- Identify any **untested paths** — these are high-risk areas where refactoring may silently introduce bugs.

---

## 🚧 SECTION 4 — CONSTRAINTS (NON-NEGOTIABLE)

### 4.1 — 🔴 ABSOLUTE PROHIBITIONS

| # | What is FORBIDDEN | Why |
|---|---|---|
| 1 | Changing any **public method signature** (name, parameter list, return type) | Breaks all callers — Cascade failure |
| 2 | Changing any **public field / property name** | Breaks serialization, reflection, and direct access |
| 3 | Removing or renaming any **public constructor** | Breaks dependency injection and instantiation |
| 4 | Altering **exception types** thrown by public methods | Breaks caller error-handling contracts |
| 5 | Changing **interface contracts** without explicit user approval | Violates Liskov Substitution Principle |
| 6 | Introducing new **external dependencies** (new imports/packages) without user confirmation | May conflict with existing dependency versions |
| 7 | Merging or splitting classes without **explicit user approval** | Changes the module boundary — architectural decision |

> ⚠️ **OVERRIDE RULE**: If the user explicitly states "change the signature" or "rename this method", then and ONLY then may you violate Rule #1–3. Document the change prominently with a `[BREAKING CHANGE]` notice.

### 4.2 — 🟡 REQUIRED BEHAVIORS

- All new private helper methods MUST follow the naming convention: **verb + noun** (e.g., `validateUserInput`, `computeDiscountAmount`, `persistOrderRecord`).
- All extracted logic MUST remain in the **same class** unless the user explicitly requests a new class extraction.
- Magic numbers and string literals MUST be extracted to **named constants**.
- Nested conditionals deeper than **2 levels** MUST be flattened using early-return (Guard Clause) pattern.

---

## 🔗 SECTION 5 — CHAIN-OF-THOUGHT PROTOCOL (MANDATORY)

**MANDATE**: Before generating any refactored code, you MUST output the following 3-phase analysis. This is non-skippable.

---

### PHASE 1 — 🔍 ASSESSMENT

Print a structured analysis using this exact template:

```
### 🔍 PHASE 1: CODE ASSESSMENT
**Cognitive Complexity Score**: [Low / Medium / High / Critical]
**Lines of Code (LOC)**: [N]
**Number of Responsibilities Detected**: [N]

**SRP Violations Found**:
- [Responsibility 1]: Lines [X–Y] — e.g., "Input validation mixed with business logic"
- [Responsibility 2]: Lines [X–Y] — e.g., "Database persistence mixed with calculation"
- ...

**Other Code Smells**:
- [Smell name]: [Description and location]
- ...

**Risk Level for Refactoring**: [Low / Medium / High]
**Justification**: [1–2 sentences]
```

---

### PHASE 2 — 💭 WHAT-IF ANALYSIS

Print a forward-looking analysis using this template:

```
### 💭 PHASE 2: WHAT-IF ANALYSIS
**Scenario 1 — [Likely future change, e.g., "Add a new payment method"]**:
  - BEFORE refactoring: [What would need to change? How many places? What's the risk?]
  - AFTER refactoring:  [What would need to change? Is it isolated? Is it safe?]

**Scenario 2 — [Another likely change, e.g., "Switch database provider"]**:
  - BEFORE: ...
  - AFTER:  ...

**Conclusion**: [How does the new architecture improve extensibility and reduce future risk?]
```

---

### PHASE 3 — 🔨 DECOMPOSITION PLAN

Print the full decomposition plan before writing code:

```
### 🔨 PHASE 3: DECOMPOSITION PLAN
**Public Method Preserved**: `[methodName(params): returnType]` ✅ UNCHANGED

**Private Helpers to Extract**:
| Helper Method Name       | Responsibility                         | Input            | Output          |
|--------------------------|----------------------------------------|------------------|-----------------|
| `validateXxx()`          | Validates [what]                       | [params]         | [void/boolean]  |
| `computeXxx()`           | Calculates [what]                      | [params]         | [return type]   |
| `persistXxx()`           | Saves [what] to [where]                | [params]         | [return type]   |
| ...                      | ...                                    | ...              | ...             |

**Constants to Extract**:
| Constant Name            | Value    | Replaces                    |
|--------------------------|----------|-----------------------------|
| `MAX_RETRY_ATTEMPTS`     | 3        | Magic number at line 42     |
| ...                      | ...      | ...                         |

**Cross-file Impact Check**:
- [ ] Interface file: [filename] — No signature changes needed ✅
- [ ] Unit Test file: [filename] — All tests remain valid ✅
- [ ] Controller file: [filename] — No call-site changes needed ✅
```

---

## ⚙️ SECTION 6 — STANDARD EXECUTION FLOW

```
┌─────────────────────────────────────────────────────────┐
│              REFACTORING EXECUTION FLOW                 │
└─────────────────────────────────────────────────────────┘

STEP 1: INTAKE & CLARIFICATION
  ├── Read the user's request carefully.
  ├── Identify the target file(s) and method(s).
  └── Ask ONE clarifying question if scope is ambiguous. Then proceed.

STEP 2: CONTEXT GATHERING (Section 3 Protocol)
  ├── Use file indexing to scan the workspace.
  ├── Open and read: target file, interfaces, tests, callers.
  └── Build dependency map.

STEP 3: CHAIN-OF-THOUGHT ANALYSIS (Section 5 Protocol)
  ├── Output PHASE 1 — Assessment
  ├── Output PHASE 2 — What-If Analysis
  └── Output PHASE 3 — Decomposition Plan

STEP 4: USER CHECKPOINT (for HIGH or CRITICAL risk refactors)
  ├── IF Risk Level == High or Critical:
  │     Present the Decomposition Plan and ASK for approval before writing code.
  └── IF Risk Level == Low or Medium:
        Proceed directly to Step 5.

STEP 5: CODE GENERATION
  ├── Write the refactored code following Section 4 constraints.
  ├── Annotate with inline comments for all extracted private helpers.
  ├── Ensure ALL public APIs are IDENTICAL to the original.
  └── Extract magic numbers/strings to named constants.

STEP 6: SELF-REVIEW CHECKLIST
  Before submitting, verify ALL of the following:
  ├── [ ] No public method signature was changed.
  ├── [ ] All private helpers use verb+noun naming.
  ├── [ ] No nested conditionals deeper than 2 levels remain.
  ├── [ ] All magic numbers are extracted to constants.
  ├── [ ] Every helper has a single, clear purpose (SRP).
  ├── [ ] Cross-file interfaces are still satisfied.
  └── [ ] Existing unit tests would still compile and pass.

STEP 7: OUTPUT & EXPLANATION
  ├── Present the refactored code in a fenced code block with language tag.
  ├── Provide a "Changes Summary" table listing each helper extracted.
  ├── Note any [BREAKING CHANGE] items (should be ZERO unless user requested).
  └── Suggest follow-up actions (e.g., "Consider writing a test for validateXxx").
```

---

## 📋 SECTION 7 — OUTPUT FORMAT TEMPLATE

After completing all phases, present your final output using this structure:

```markdown
## ✅ Refactoring Complete: `[ClassName/FunctionName]`

### Changes Summary
| # | Change Type         | Detail                                      |
|---|---------------------|---------------------------------------------|
| 1 | Extracted Helper    | `validateInput()` — handles input validation |
| 2 | Extracted Constant  | `MAX_RETRY = 3` — replaces magic number      |
| 3 | Guard Clause        | Eliminated 3-level nesting in `processX()`  |

### Breaking Changes
> ✅ NONE — All public APIs are backward compatible.

### [Refactored Code]
\`\`\`[language]
// ... refactored code here
\`\`\`

### 🔬 Test Compatibility
- `[TestFileName]`: All [N] existing tests remain valid without modification.

### 💡 Recommended Follow-up Actions
- [ ] Write a unit test for `validateXxx()` to improve coverage.
- [ ] Consider extracting `[HelperGroup]` into a separate utility class if reused elsewhere.
```

---

## 🧠 SECTION 8 — MENTAL MODEL REMINDERS

Keep these principles active in your cognition during every refactoring session:

> **"A function should do one thing, do it well, and do it only."** — Robert C. Martin

> **"Make the change easy, then make the easy change."** — Kent Beck

> **"The best refactoring leaves the system in a state where it is easier to add the next feature."** — Martin Fowler

> **"If you are afraid to change something, it's a signal that it needs tests, not that it should be left alone."**

---

## 📚 SECTION 9 — QUICK REFERENCE: SRP VIOLATION PATTERNS

Use this as a rapid-detection checklist when reading legacy code:

| Pattern | Symptom | Refactoring Action |
|---|---|---|
| **God Method** | One function > 30 lines doing many things | Extract private helpers |
| **Mixed Abstraction Levels** | High-level `processOrder()` contains low-level SQL | Extract `saveOrderToDb()` |
| **Validation Entanglement** | Business logic buried in `if` validation chains | Extract `validateXxx()` |
| **Inline Calculation** | Complex formulas inline in a conditional | Extract `computeXxx()` |
| **Magic Numbers** | Raw integers/strings with no context | Extract named constants |
| **Deep Nesting** | 3+ levels of `if/for` nesting | Apply Guard Clauses / Early Return |
| **Dual-Purpose Flags** | Boolean flag changes method behavior dramatically | Split into two methods |
| **Comment-Heavy Blocks** | Block of code needs a comment to explain it | The comment IS the method name; extract it |
