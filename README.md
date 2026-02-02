# SchemaSentinel

> An intelligent, automated migration engine for transitioning from legacy database systems to Snowflake Data Cloud

[![Java](https://img.shields.io/badge/Java-21-orange.svg)](https://openjdk.java.net/projects/jdk/21/)
[![Spring Boot](https://img.shields.io/badge/Spring%20Boot-3.2-brightgreen.svg)](https://spring.io/projects/spring-boot)
[![React](https://img.shields.io/badge/React-TypeScript-blue.svg)](https://reactjs.org/)
[![License](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)

## 🎯 Overview

**SchemaSentinel** is an enterprise-grade migration tool designed to accelerate the transition from legacy database systems (Oracle, SQL Server, PostgreSQL) to **Snowflake Data Cloud**. Unlike simple regex-based find-and-replace tools, SchemaSentinel leverages **Abstract Syntax Tree (AST) analysis** for deterministic code translation, augmented by a **Generative AI Agent** to handle complex, ambiguous edge cases.

### Key Differentiators

- **AST-Based Translation**: Deterministic, accurate transformations using static analysis
- **AI-Powered Fallback**: Intelligent handling of edge cases and proprietary syntax
- **Scalable Architecture**: Asynchronous processing for high-throughput enterprise migrations
- **Visual Dashboard**: Side-by-side diff visualization for migration review
- **Comprehensive Reporting**: Detailed migration reports with success rates and attention flags

## 🏗️ Architecture

This project showcases a modern enterprise Java stack with robust design patterns:

```
┌─────────────────┐
│  React Dashboard │  ← Upload files, view diffs
└────────┬────────┘
         │
┌────────▼─────────────────────┐
│   Spring Boot 3.2 API        │
│   ┌────────────────────────┐  │
│   │  AST Parser Engine       │  │  ← JSqlParser (Deterministic)
│   │  └─Visitor Pattern    │  │
│   └────────────────────────┘  │
│   ┌────────────────────────┐  │
│   │  AI Agent (Fallback)   │  │  ← Spring AI (Probabilistic)
│   │  └─LLM Integration     │  │
│   └────────────────────────┘  │
│   ┌────────────────────────┐  │
│   │  Async Processing      │  │  ← CompletableFuture/@Async
│   │  └─Event Queue         │  │
│   └────────────────────────┘  │
└───────────────────────────────┘
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

## 📋 Supported Sources

- **Oracle**: PL/SQL, Oracle-specific DDL
- **SQL Server**: T-SQL, SQL Server-specific syntax
- **PostgreSQL**: PL/pgSQL, PostgreSQL extensions

## 🎓 Why SchemaSentinel?

This project demonstrates expertise in:

| Requirement | Demonstration |
|------------|---------------|
| **Java Expertise** | Modern Java 21/Spring Boot; complex object manipulation (ASTs) beyond CRUD |
| **Migration Domain** | Directly tackles core business problem: legacy → Snowflake conversion |
| **Problem Solving** | Elegant "90/10 rule" implementation (90% deterministic, 10% AI-assisted) |
| **Seniority** | Architecture decisions (async processing), DevOps (Docker), DX (React UI) |

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
│   │   │   │       ├── parser/      # AST parsing engine
│   │   │   │       ├── ai/          # AI agent integration
│   │   │   │       ├── service/     # Business logic
│   │   │   │       └── api/         # REST controllers
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
│   └── briefing.md
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

**Built with ❤️ for seamless database migrations**
