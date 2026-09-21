# Database Management Systems (DBMS) — Complete GATE Syllabus & Overview

**Typical GATE Weightage:** ~7 – 9 Marks

---

## 📌 Core Modules & Key High-Yield Topics

### Module 1: ER-Model & Relational Algebra
* **Entity-Relationship Model:**
  * Entity sets, Weak entities, Identifying relationships
  * Cardinality ratios, Participation constraints (Total vs Partial)
  * Converting ER diagrams to minimal Relational Tables
* **Relational Algebra:**
  * Fundamental operators: Selection ($\sigma$), Projection ($\pi$), Cartesian Product ($\times$), Union ($\cup$), Set Difference ($-$)
  * Derived operators: Joins (Natural, Theta, Equi, Outer Joins), Division operator ($\div$)
* **Tuple Relational Calculus (TRC) & Domain Relational Calculus (DRC):**
  * Safe expressions and expressive power equivalence

### Module 2: SQL (Structured Query Language)
* **DDL, DML, DCL Commands:** Table creation, constraints (Primary Key, Foreign Key, Unique, Check).
* **Complex SQL Queries:**
  * Nested subqueries (Correlated vs Uncorrelated)
  * `GROUP BY` and `HAVING` clause execution semantics
  * Aggregate functions and `NULL` value handling
  * Joins and set operations in SQL

### Module 3: Functional Dependencies & Normalization
* **Closure of Attribute Sets & Minimal Cover:**
  * Armstrong's Axioms (Reflexivity, Augmentation, Transitivity)
  * Finding Candidate Keys and Super Keys
* **Normal Forms:**
  * 1NF: Atomic values
  * 2NF: No partial dependency (non-prime on part of candidate key)
  * 3NF: No transitive dependency ($X \to Y$ where $X$ is superkey or $Y$ is prime attribute)
  * BCNF: For every functional dependency $X \to Y$, $X$ must be a superkey
* **Decomposition Properties:**
  * Lossless Join Decomposition (Testing via attribute intersection condition)
  * Dependency Preservation

### Module 4: Transaction & Concurrency Control
* **ACID Properties:** Atomicity, Consistency, Isolation, Durability.
* **Serializability:**
  * Conflict Serializability: Precedence / Serialization Graph testing
  * View Serializability: Polygraph testing, blind writes
* **Recoverability:**
  * Recoverable Schedules vs Irrecoverable Schedules
  * Cascading Aborts and Cascadeless Schedules
  * Strict Schedules
* **Concurrency Control Protocols:**
  * Two-Phase Locking (2PL): Basic, Strict, Rigorous, Conservative
  * Timestamp Ordering Protocol and Thomas Write Rule

### Module 5: File Organization & Indexing
* **File Structures:** Heap files, Sorted files, Hash files.
* **B-Trees & B+ Trees:**
  * Node structure, Order $p$, Minimum and Maximum keys/pointers
  * Insertions, Deletions, Splitting and Merging
  * Height, Maximum/Minimum records accommodated, and Disk block I/O calculations
