# Project Briefing: SchemaSentinel

### 1. Executive Summary

**SchemaSentinel** is an intelligent, automated migration engine designed to accelerate the transition from legacy database systems (Oracle, SQL Server, PostgreSQL) to the **Snowflake Data Cloud**.

Unlike simple regex-based find-and-replace tools, SchemaSentinel leverages **Abstract Syntax Tree (AST) analysis** for deterministic code translation, augmented by a **Generative AI Agent** to handle complex, ambiguous edge cases. The system is architected as a scalable, asynchronous microservice, reflecting the high-throughput nature of enterprise data migrations.

### 2. The Problem Space

Enterprise migrations are often blocked by:

1. **Syntax Incompatibility:** Legacy DDL (Data Definition Language) often contains proprietary keywords (e.g., Oracle's `VARCHAR2`, `CLOB`, or `NUMBER(p,s)`) that do not map 1:1 to Snowflake.
2. **Complex Logic:** Stored procedures and complex views cannot be translated via simple string manipulation; they require deep code parsing.
3. **Scale:** Manual translation of thousands of database objects is error-prone and slow.

### 3. Technical Architecture

This project showcases a "Senior Java Engineer" stack, utilizing modern standards and robust design patterns.

* **Core Backend:** Java 21 (LTS) with Spring Boot 3.2.
* **Parsing Engine:** **JSqlParser** (Java SQL Parser) for analyzing and manipulating SQL structure (AST).
* **AI Orchestration:** **Spring AI** integrating with an LLM (e.g., OpenAI or Vertex AI) for "semantic translation" of failed parse segments.
* **Asynchronous Processing:** Java `CompletableFuture` or Spring `@Async` combined with an internal event queue to handle bulk file processing without blocking the UI.
* **Frontend Dashboard:** React (TypeScript) for uploading schema files and viewing side-by-side "Diffs" (Source vs. Snowflake Target).
* **Infrastructure/DevOps:** Dockerized application deployed via GitHub Actions (CI/CD).

### 4. Key Features & Implementation Strategy

#### Feature A: The AST Transformation Engine (Deterministic Layer)

The primary engine uses static analysis to guarantee accurate translation.

* **Mechanism:** Ingests raw SQL strings  Parses into a Java Object Graph (AST)  Traverses nodes using the **Visitor Pattern**  Modifies nodes to match Snowflake syntax  Reconstructs SQL.
* **Showcase Skill:** Deep understanding of data structures, recursion, and strongly typed Java development.

#### Feature B: The "fallback" AI Agent (Probabilistic Layer)

A safety net for when deterministic parsing fails.

* **Mechanism:** If the AST parser encounters a syntax error (e.g., a proprietary Oracle hint or obscure function), the specific code block is captured and sent to the AI Agent.
* **Prompt Engineering:** *"Translate this specific Oracle PL/SQL block to Snowflake Scripting. Mark any assumptions with comments."*
* **Showcase Skill:** AI integration, error handling, and hybrid system design.

#### Feature C: The "Migration Report"

* **Mechanism:** Generates a JSON summary detailing:
* Success Rate (e.g., 95% automated).
* List of modified data types (e.g., `BLOB` transformed to `BINARY`).
* "Attention Needed" flags for AI-generated code.



### 5. Why This Project Wins the Interview

This briefing maps directly to the **Snowflake Software Application Engineer (Migrations)** requirements:

| Requirement / Need | How SchemaSentinel Demonstrates It |
| --- | --- |
| **Java Expertise** | Built on modern Java 21/Spring Boot; demonstrates complex object manipulation (ASTs) rather than just CRUD. |
| **Migration Domain** | Directly tackles the core business problem: converting legacy code to Snowflake. |
| **Problem Solving** | Solves the "90/10 rule" (90% via fast code, 10% via smart AI) efficiently. |
| **Seniority** | Includes Architecture decisions (Async processing), DevOps (Docker), and Developer Experience (React UI). |
