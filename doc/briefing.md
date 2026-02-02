# Project Briefing: SchemaSentinel

### 1. Executive Summary

**SchemaSentinel** is an intelligent, enterprise-grade **Data Migration Architect** platform designed to accelerate the transition from legacy database systems (Oracle, SQL Server, PostgreSQL) to the **Snowflake Data Cloud**.

Unlike simple regex-based find-and-replace tools or code translators, SchemaSentinel approaches migration from a **data logistics perspective**—understanding that converting DDL is only 10% of real-world migration work. The platform leverages **Abstract Syntax Tree (AST) analysis** for deterministic code translation, augmented by a **Generative AI Agent** for complex edge cases, while providing comprehensive **data integrity validation**, **migration complexity assessment**, and **hybrid cutover strategies**.

The system is architected as a scalable, asynchronous microservice capable of supporting multi-terabyte Fortune 500 migrations with zero data loss.

### 2. The Problem Space

Enterprise migrations are often blocked by:

1. **Syntax Incompatibility:** Legacy DDL often contains proprietary keywords (e.g., Oracle's `VARCHAR2`, `CLOB`, or `NUMBER(p,s)`) that do not map 1:1 to Snowflake.
2. **Complex Logic:** Stored procedures and complex views cannot be translated via simple string manipulation; they require deep code parsing.
3. **Scale:** Manual translation of thousands of database objects is error-prone and slow.
4. **Data Integrity:** Moving petabytes of data while maintaining referential integrity and proving no data loss.
5. **Operational Continuity:** "Big Bang" migrations (shutting down apps to move data) are rarely possible; hybrid pipelines are essential.
6. **Performance Optimization:** Legacy systems often have inefficient schemas that should be optimized for Snowflake's unique micro-partitioning architecture.

### 3. Technical Architecture

This project showcases a "Senior Data Migration Architect" stack, utilizing modern standards and robust design patterns.

* **Core Backend:** Java 21 (LTS) with Spring Boot 3.2.
* **Parsing Engine:** **JSqlParser** (Java SQL Parser) for analyzing and manipulating SQL structure (AST).
* **AI Orchestration:** **Spring AI** integrating with an LLM (e.g., OpenAI or Vertex AI) for "semantic translation" of failed parse segments.
* **Asynchronous Processing:** Java `CompletableFuture` or Spring `@Async` combined with an internal event queue to handle bulk file processing without blocking the UI.
* **Frontend Dashboard:** React (TypeScript) for uploading schema files, viewing side-by-side "Diffs," and visualizing **complexity heatmaps**.
* **Infrastructure/DevOps:** Dockerized application deployed via GitHub Actions (CI/CD).

### 4. Key Features & Implementation Strategy

#### Feature A: The AST Transformation Engine (Deterministic Layer)

The primary engine uses static analysis to guarantee accurate translation.

* **Mechanism:** Ingests raw SQL strings → Parses into a Java Object Graph (AST) → Traverses nodes using the **Visitor Pattern** → Modifies nodes to match Snowflake syntax → Reconstructs SQL.
* **Showcase Skill:** Deep understanding of data structures, recursion, and strongly typed Java development.

#### Feature B: The "Fallback" AI Agent (Probabilistic Layer)

A safety net for when deterministic parsing fails.

* **Mechanism:** If the AST parser encounters a syntax error (e.g., a proprietary Oracle hint or obscure function), the specific code block is captured and sent to the AI Agent.
* **Prompt Engineering:** *"Translate this specific Oracle PL/SQL block to Snowflake Scripting. Mark any assumptions with comments."*
* **Showcase Skill:** AI integration, error handling, and hybrid system design.

#### Feature C: The "Migration Report"

* **Mechanism:** Generates a JSON summary detailing:
  * Success Rate (e.g., 95% automated).
  * List of modified data types (e.g., `BLOB` transformed to `BINARY`).
  * "Attention Needed" flags for AI-generated code.

---

### 5. Advanced Data Migration Capabilities

These features demonstrate "Data Migration Architect" seniority—understanding that schema translation is only 10% of real-world migration work.

#### Feature D: Data Gravity Assessment Module

A Data Architect must understand the cost and time of data movement.

* **Metric Extraction:** Uses the AST parser to calculate "Data Gravity" scores—identifying massive tables (via metadata) with high numbers of foreign key dependencies that must be migrated together to maintain referential integrity.
* **Complexity Heatmaps:** A new report view in the React dashboard showing which schemas are "tightly coupled," proving understanding that data cannot always be moved in isolation.
* **Showcase Skill:** Migration planning, dependency analysis, and enterprise-scale thinking.

#### Feature E: Automated Checksum & Parity Generator

Architects are obsessed with data integrity.

* **Validation Scripts:** For every table transformed from Oracle/SQL Server to Snowflake, SchemaSentinel outputs a pair of Java-based validation scripts:
  * **Source Script:** Calculates row counts and aggregate checksums (e.g., `SUM(hash_numeric_columns)`) on the legacy DB.
  * **Target Script:** Runs the equivalent Snowflake-optimized query.
* **The "Architect" Value:** Demonstrates that we don't just "move" data—we "certify" it.

#### Feature F: CDC Integration Strategy (Hybrid Migration Pipelines)

A senior architect knows a "Big Bang" migration (shutting down the app to move data) is rarely possible.

* **Stream-Based Architecture:** Automatically generates **Snowpipe** DDL and **Streams/Tasks** code for the target Snowflake environment.
* **Hybrid Cutover:** Keeps the new Snowflake environment in sync with the legacy Java application during the "transition period."
* **Showcase Skill:** Real-world migration experience, operational continuity, and zero-downtime thinking.

#### Feature G: Intelligent Data Type Optimization

Legacy systems often have inefficient types (e.g., using `CLOB` for everything). A Data Architect optimizes for the destination.

* **Smart Mapping:** Instead of simple `CLOB` → `VARCHAR` mapping, adds logic that checks actual data distribution (if metadata is provided).
* **Snowflake Optimization:** Suggests **Cluster Keys** for large tables based on `WHERE` clauses found in legacy Stored Procedures parsed by JSqlParser.
* **Showcase Skill:** Designing for Snowflake's unique **micro-partitioning** architecture rather than just copying legacy designs.

---

### 6. Architecture Comparison

| Current Code Translator Approach | SchemaSentinel "Architect" Approach |
| --- | --- |
| **Input**: SQL Files | **Input**: SQL Files + Statistics Metadata |
| **Process**: AST Translation | **Process**: AST + Data Volume Analysis |
| **Output**: Snowflake SQL | **Output**: SQL + Validation Logic + CDC Config |
| **Goal**: Code Parity | **Goal**: Operational Cutover & Data Integrity |

---

### 7. Why This Project Wins the Interview

This briefing maps directly to the **Snowflake Software Application Engineer (Migrations)** requirements:

| Requirement / Need | How SchemaSentinel Demonstrates It |
| --- | --- |
| **Java Expertise** | Built on modern Java 21/Spring Boot; demonstrates complex object manipulation (ASTs) rather than just CRUD. |
| **Migration Domain** | Directly tackles the core business problem: converting legacy code to Snowflake with data integrity guarantees. |
| **Problem Solving** | Solves the "90/10 rule" (90% via fast code, 10% via smart AI) efficiently. |
| **Seniority** | Includes Architecture decisions (Async processing, CDC pipelines), DevOps (Docker), and Developer Experience (React UI with complexity heatmaps). |
| **Data Architecture** | Data Gravity assessment, validation script generation, and cluster key optimization demonstrate Fortune 500-level migration leadership. |
| **Operational Excellence** | Hybrid migration pipelines and checksum validation prove understanding of zero-downtime, certified cutover requirements. |
