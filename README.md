# Data Cleaning with Traceability

> **This project is not about cleaning data correctly.**
> **It is about cleaning data safely.**

---

## Why This Project Exists

In real-world systems, data cleaning rarely has a single correct answer.

Company names, user inputs, addresses, and free-text fields are:

* Ambiguous
* Context-dependent
* Defined differently by different stakeholders at different times

When data volume grows, manual verification becomes impossible, and traditional cleaning approaches (string replacement, overwrite-in-place) introduce a high risk:

> **Data does not disappear — it becomes untraceable.**

This project proposes a design-first approach to data cleaning where **traceability, reversibility, and safety** are first-class concerns.

---

## The Real Problem

Most data cleaning failures are not caused by bad rules.
They are caused by **loss of relationships**.

Common failure modes:

* Raw values are overwritten
* Multiple entities are merged without record
* Cleaning decisions cannot be explained or revisited

These failures produce what is commonly referred to as **orphan data**:

> Data that still exists, but no one knows where it came from, why it looks the way it does, or how to fix it.

This project treats orphan prevention as the primary goal.

---

## Design Principles

### 1. Raw Data Is Immutable

Original input data must never be overwritten.

* Raw values are preserved indefinitely
* All cleaning results are stored as derived states
* The system must always be able to reconstruct the original input

> If raw data is lost, no future correction is possible.

---

### 2. Cleaning Is Mapping, Not Replacement

Data cleaning is not string manipulation.

It is a **relationship assignment problem**.

Instead of:

```
Raw Value → Clean Value (overwrite)
```

We model:

```
Raw Value → Mapping → Normalized Entity
```

Mappings are treated as data, not side effects.

This allows:

* One-to-many relationships
* Many-to-one relationships
* Reassignment without data loss

---

### 3. Postpone Irreversible Decisions

When a decision cannot be confidently made, **do not force it**.

* Similar values may remain separate
* Multiple candidates may coexist
* Final merges are delayed

Irreversible actions (such as merging entities) must be:

* Explicit
* Auditable
* Reversible

---

## Required Data Structures

Every cleaned record should be backed by the following minimum components:

### 1. Raw Identifier

The original input value.

* Never modified
* Never deleted

---

### 2. Stable Internal ID

An identifier that does not change even if names or representations change.

* Primary reference key
* Prevents identity drift

---

### 3. Normalized Value

The current cleaned or standardized representation.

* Treated as a **view**, not a source of truth
* May change over time

---

### 4. Mapping ID

Represents the relationship between raw input and normalized entity.

* Raw → Normalized
* Can be reassigned
* Can support multiple mappings

---

### 5. Decision Metadata

Records *why* a mapping exists.

Examples:

* Rule-based normalization
* Manual assignment
* System suggestion

---

### 6. Timestamp / Versioning

Every decision must be time-aware.

* Enables rollback
* Enables audit
* Enables comparison across versions

---

## Data Lifecycle

A typical lifecycle in this design:

```
Raw Input
   ↓
Candidate Generation
   ↓
Mapping Assignment
   ↓
Cleaned View
   ↓
(Optional) Merge / Reassignment
```

Key property:

> **No step is final.**

---

## Failure Modes & Defenses

### Failure: Overwriting Raw Data

**Defense:** Raw data is immutable.

---

### Failure: Premature Merging

**Defense:** Merges are delayed and explicit.

---

### Failure: "Why Does This Look Like This?"

**Defense:** Every mapping has decision metadata.

---

## Scope & Non-Goals

### This Project Does NOT:

* Define a single correct naming standard
* Guarantee semantic correctness
* Replace domain expertise

### This Project DOES:

* Prevent data loss
* Prevent orphan data
* Enable future correction
* Support evolving definitions

---

## Mental Model

Instead of asking:

> "Is this value correct?"

Ask:

> "Can I explain how this value came to be — and can I change it safely?"

If the answer is yes, the system is working.

---

## When To Use This Pattern

This design is suitable when:

* Data volume prevents full manual verification
* Naming has no absolute ground truth
* Definitions change over time
* Long-term maintainability matters

---

## Final Note

Clean data is temporary.

**Traceable data survives change.**


---
### Disclaimer

This repository documents general data engineering design patterns and does not reference any proprietary system, dataset, or internal workflow.

