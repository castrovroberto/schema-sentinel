# SchemaSentinel

> An intelligent, enterprise-grade **Data Migration Architect** platform for transitioning from legacy database systems to Snowflake Data Cloud

[![Java](https://img.shields.io/badge/Java-21-orange.svg)](https://openjdk.java.net/projects/jdk/21/)
[![Spring Boot](https://img.shields.io/badge/Spring%20Boot-3.2-brightgreen.svg)](https://spring.io/projects/spring-boot)
[![React](https://img.shields.io/badge/React-TypeScript-blue.svg)](https://reactjs.org/)
[![License](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)

## 🎯 Overview

**SchemaSentinel** is an enterprise-grade **Data Migration Architect** platform designed to accelerate the transition from legacy database systems (Oracle, SQL Server, PostgreSQL) to **Snowflake Data Cloud**. Unlike simple regex-based code translators, SchemaSentinel approaches migration from a **data logistics perspective**—understanding that converting DDL is only 10% of real-world migration work.

The platform leverages **Abstract Syntax Tree (AST) analysis** for deterministic code translation, augmented by a **Generative AI Agent** for complex edge cases, while providing comprehensive **data integrity validation**, **migration complexity assessment**, and **hybrid cutover strategies**.

### Key Differentiators

- **AST-Based Translation**: Deterministic, accurate transformations using static analysis
- **AI-Powered Fallback**: Intelligent handling of edge cases and proprietary syntax
- **Data Gravity Assessment**: Migration complexity analysis with dependency heatmaps
- **Data Integrity Validation**: Automated checksum and parity script generation
- **CDC Integration**: Snowpipe/Streams/Tasks generation for hybrid migrations
- **Intelligent Optimization**: Smart type mapping and cluster key suggestions for Snowflake
- **Scalable Architecture**: Asynchronous processing for high-throughput enterprise migrations
- **Visual Dashboard**: Side-by-side diff visualization with complexity heatmaps

## 🏗️ Architecture

This project showcases a modern enterprise Java stack with robust design patterns:

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                           React Dashboard                                    │
│  ┌────────────────┐ ┌────────────────┐ ┌────────────────┐                   │
│  │  File Upload   │ │  Diff Viewer   │ │ Complexity     │                   │
│  │                │ │                │ │ Heatmaps       │                   │
│  └────────────────┘ └────────────────┘ └────────────────┘                   │
└───────────────────────────────┬─────────────────────────────────────────────┘
                                │
┌───────────────────────────────▼─────────────────────────────────────────────┐
│                      Spring Boot 3.2 API                                    │
│  ┌─────────────────────────────────────────────────────────────────────┐    │
│  │  Core Migration Engine                                               │    │
│  │  ┌────────────────────┐  ┌────────────────────┐                     │    │
│  │  │  AST Parser Engine │  │  AI Agent (Fallback│                     │    │
│  │  │  └─Visitor Pattern │  │  └─LLM Integration │                     │    │
│  │  └────────────────────┘  └────────────────────┘                     │    │
│  └─────────────────────────────────────────────────────────────────────┘    │
│  ┌─────────────────────────────────────────────────────────────────────┐    │
│  │  Data Migration Architect Modules                                    │    │
│  │  ┌──────────────┐ ┌──────────────┐ ┌──────────────┐ ┌─────────────┐ │    │
│  │  │ Data Gravity │ │  Checksum &  │ │     CDC      │ │ Intelligent │ │    │
│  │  │ Assessment   │ │   Parity     │ │ Integration  │ │ Optimizer   │ │    │
│  │  └──────────────┘ └──────────────┘ └──────────────┘ └─────────────┘ │    │
│  └─────────────────────────────────────────────────────────────────────┘    │
│  ┌─────────────────────────────────────────────────────────────────────┐    │
│  │  Async Processing (CompletableFuture/@Async + Event Queue)          │    │
│  └─────────────────────────────────────────────────────────────────────┘    │
└─────────────────────────────────────────────────────────────────────────────┘
```

### Technology Stack

| Layer | Technology |
|-------|-----------|
| **Core Backend** | Java 21 (LTS), Spring Boot 3.2 |
| **SQL Parsing** | JSqlParser (AST manipulation) |
| **AI Integration** | Spring AI (OpenAI/Vertex AI) |
| **Asynchronous Processing** | CompletableFuture, Spring @Async |
| **Frontend** | React with TypeScript |
| **Infrastructure** | Docker, GitHub Actions (CI/CD) |

## 🚀 Core Features

### Feature A: AST Transformation Engine (Deterministic Layer)

The primary engine uses static analysis to guarantee accurate translation:

1. **Input**: Raw SQL strings from legacy databases
2. **Parse**: Transform into Java Object Graph (AST) using JSqlParser
3. **Traverse**: Visit nodes using the Visitor Pattern
4. **Transform**: Modify nodes to match Snowflake syntax
5. **Reconstruct**: Generate Snowflake-compatible SQL

**Showcases**: Deep understanding of data structures, recursion, and strongly-typed Java development.

### Feature B: AI Agent Fallback (Probabilistic Layer)

When deterministic parsing encounters proprietary syntax or ambiguous constructs:

1. **Capture**: Isolate the problematic code block
2. **Query**: Send to AI agent with context-aware prompt
3. **Generate**: Produce Snowflake-compatible alternative
4. **Flag**: Mark AI-generated code for manual review

**Showcases**: AI integration, error handling, and hybrid system design.

### Feature C: Migration Report Generator

Automated reporting includes:

- ✅ **Success Rate**: Percentage of objects automatically migrated (e.g., 95%)
- 📊 **Transformation Log**: Data type mappings (e.g., `BLOB` → `BINARY`)
- ⚠️ **Attention Flags**: Items requiring manual review
- 📝 **Audit Trail**: Complete history of transformations

---

## 🏛️ Data Migration Architect Features

These capabilities demonstrate that schema translation is only 10% of real-world migration work—the other 90% is moving petabytes of data while maintaining integrity and minimizing downtime.

### Feature D: Data Gravity Assessment Module

A Data Architect must understand the cost and time of data movement.

- **Metric Extraction**: Uses the AST parser to calculate "Data Gravity" scores—identifying massive tables (via metadata) with high numbers of foreign key dependencies that must be migrated together to maintain referential integrity.
- **Complexity Heatmaps**: A report view in the React dashboard showing which schemas are "tightly coupled," proving understanding that data cannot always be moved in isolation.

**Showcases**: Migration planning, dependency analysis, and enterprise-scale thinking.

### Feature E: Automated Checksum & Parity Generator

Architects are obsessed with data integrity.

- **Validation Scripts**: For every table transformed from Oracle/SQL Server to Snowflake, SchemaSentinel outputs a pair of Java-based validation scripts:
  - **Source Script**: Calculates row counts and aggregate checksums (e.g., `SUM(hash_numeric_columns)`) on the legacy DB.
  - **Target Script**: Runs the equivalent Snowflake-optimized query.

**The "Architect" Value**: Demonstrates that we don't just "move" data—we "certify" it.

### Feature F: CDC Integration Strategy (Hybrid Migration Pipelines)

A senior architect knows a "Big Bang" migration (shutting down the app to move data) is rarely possible.

- **Stream-Based Architecture**: Automatically generates **Snowpipe** DDL and **Streams/Tasks** code for the target Snowflake environment.
- **Hybrid Cutover**: Keeps the new Snowflake environment in sync with the legacy application during the "transition period."

**Showcases**: Real-world migration experience, operational continuity, and zero-downtime thinking.

### Feature G: Intelligent Data Type Optimization

Legacy systems often have inefficient types (e.g., using `CLOB` for everything). A Data Architect optimizes for the destination.

- **Smart Mapping**: Instead of simple `CLOB` → `VARCHAR` mapping, adds logic that checks actual data distribution (if metadata is provided).
- **Snowflake Optimization**: Suggests **Cluster Keys** for large tables based on `WHERE` clauses found in legacy Stored Procedures parsed by JSqlParser.

**Showcases**: Designing for Snowflake's unique **micro-partitioning** architecture rather than just copying legacy designs.

---

## 📊 Architecture Comparison

| Code Translator Approach | SchemaSentinel "Architect" Approach |
|--------------------------|-------------------------------------|
| **Input**: SQL Files | **Input**: SQL Files + Statistics Metadata |
| **Process**: AST Translation | **Process**: AST + Data Volume Analysis |
| **Output**: Snowflake SQL | **Output**: SQL + Validation Logic + CDC Config |
| **Goal**: Code Parity | **Goal**: Operational Cutover & Data Integrity |

---

## 📋 Supported Sources

- **Oracle**: PL/SQL, Oracle-specific DDL
- **SQL Server**: T-SQL, SQL Server-specific syntax
- **PostgreSQL**: PL/pgSQL, PostgreSQL extensions

## 🎓 Why SchemaSentinel?

This project demonstrates expertise in:

| Requirement | Demonstration |
|------------|---------------|
| **Java Expertise** | Modern Java 21/Spring Boot; complex object manipulation (ASTs) beyond CRUD |
| **Migration Domain** | Directly tackles core business problem: legacy → Snowflake conversion with data integrity guarantees |
| **Problem Solving** | Elegant "90/10 rule" implementation (90% deterministic, 10% AI-assisted) |
| **Data Architecture** | Data Gravity assessment, validation scripts, and cluster key optimization demonstrate Fortune 500-level migration leadership |
| **Operational Excellence** | Hybrid CDC pipelines and checksum validation prove zero-downtime, certified cutover expertise |
| **Seniority** | Architecture decisions (async processing, CDC), DevOps (Docker), DX (React UI with complexity heatmaps) |

## 🛠️ Getting Started

### Prerequisites

- Java 21 or higher
- Node.js 18+ (for frontend)
- Docker (optional, for containerized deployment)
- OpenAI API key or Vertex AI credentials (for AI fallback)

### Installation

```bash
# Clone the repository
git clone https://github.com/yourusername/schema-sentinel.git
cd schema-sentinel

# Backend setup
cd backend
./mvnw clean install

# Frontend setup
cd ../frontend
npm install
```

### Running the Application

```bash
# Start backend (from backend directory)
./mvnw spring-boot:run

# Start frontend (from frontend directory)
npm start
```

## 📚 Project Structure

```
schema-sentinel/
├── backend/
│   ├── src/
│   │   ├── main/
│   │   │   ├── java/
│   │   │   │   └── com/schemasentinel/
│   │   │   │       ├── parser/         # AST parsing engine
│   │   │   │       ├── ai/             # AI agent integration
│   │   │   │       ├── gravity/        # Data Gravity assessment
│   │   │   │       ├── validation/     # Checksum & parity scripts
│   │   │   │       ├── cdc/            # CDC/Snowpipe generation
│   │   │   │       ├── optimizer/      # Intelligent type optimization
│   │   │   │       ├── service/        # Business logic
│   │   │   │       └── api/            # REST controllers
│   │   │   └── resources/
│   │   └── test/
│   └── pom.xml
├── frontend/
│   ├── src/
│   │   ├── components/
│   │   ├── services/
│   │   └── App.tsx
│   └── package.json
├── doc/
│   ├── briefing.md
│   └── schema-sentinel-srd.md
└── README.md
```

## 🤝 Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

## 📄 License

This project is licensed under the MIT License - see the LICENSE file for details.

## 🔗 Related Links

- [Snowflake Documentation](https://docs.snowflake.com/)
- [JSqlParser](https://github.com/JSQLParser/JSqlParser)
- [Spring AI](https://spring.io/projects/spring-ai)

---

**Built with ❤️ for seamless, certified enterprise database migrations**

