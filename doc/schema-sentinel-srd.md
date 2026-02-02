# Software Requirements Document (SRD)
## SchemaSentinel - Database Migration Engine

**Version:** 1.0  
**Date:** 2026  
**Status:** Draft  
**Authors:** SchemaSentinel Development Team

---

## Table of Contents

1. [Introduction](#1-introduction)
2. [System Overview](#2-system-overview)
3. [Functional Requirements](#3-functional-requirements)
4. [Non-Functional Requirements](#4-non-functional-requirements)
5. [System Architecture](#5-system-architecture)
6. [Data Requirements](#6-data-requirements)
7. [Interface Requirements](#7-interface-requirements)
8. [Security Requirements](#8-security-requirements)
9. [Performance Requirements](#9-performance-requirements)
10. [Testing Requirements](#10-testing-requirements)
11. [Deployment Requirements](#11-deployment-requirements)
12. [Constraints and Assumptions](#12-constraints-and-assumptions)
13. [Risks and Mitigation](#13-risks-and-mitigation)
14. [Glossary](#14-glossary)

---

## 1. Introduction

### 1.1 Purpose

This Software Requirements Document (SRD) defines the functional and non-functional requirements for **SchemaSentinel**, an intelligent, enterprise-grade **Data Migration Architect** platform designed to accelerate the transition from legacy database systems to Snowflake Data Cloud.

### 1.2 Scope

SchemaSentinel addresses the critical challenge of migrating database schemas, DDL statements, stored procedures, and views from:
- **Oracle Database** (PL/SQL)
- **Microsoft SQL Server** (T-SQL)
- **PostgreSQL** (PL/pgSQL)

To **Snowflake Data Cloud**, while maintaining referential integrity, business logic, data type compatibility, and **data integrity validation**.

**Extended Capabilities (Data Migration Architect):**
- Migration complexity assessment (Data Gravity analysis)
- Automated validation script generation for data integrity verification
- Change Data Capture (CDC) integration for hybrid migration pipelines
- Intelligent data type optimization for Snowflake architecture

**Out of Scope:**
- Actual data extraction and loading (ETL/ELT execution)
- Network infrastructure configuration
- Snowflake account provisioning
- Physical data transport

### 1.3 Definitions and Acronyms

| Term | Definition |
|------|------------|
| **AST** | Abstract Syntax Tree - A tree representation of source code structure |
| **DDL** | Data Definition Language - SQL commands for defining database structures |
| **DML** | Data Manipulation Language - SQL commands for manipulating data |
| **JSqlParser** | Java-based SQL parser library for AST generation |
| **LLM** | Large Language Model - AI model for code translation |
| **PL/SQL** | Oracle's procedural extension to SQL |
| **T-SQL** | Microsoft SQL Server's procedural extension to SQL |
| **PL/pgSQL** | PostgreSQL's procedural extension to SQL |

### 1.4 References

- [Snowflake SQL Reference](https://docs.snowflake.com/en/sql-reference-commands.html)
- [JSqlParser Documentation](https://github.com/JSQLParser/JSqlParser)
- [Spring AI Framework](https://docs.spring.io/spring-ai/reference/index.html)
- Oracle PL/SQL Language Reference
- Microsoft T-SQL Language Reference
- PostgreSQL PL/pgSQL Documentation

---

## 2. System Overview

### 2.1 System Purpose

SchemaSentinel automates the translation of database objects from legacy systems to Snowflake, reducing manual effort by 90%+ while ensuring accuracy through deterministic AST-based transformations and AI-powered fallback mechanisms.

### 2.2 System Context

```
┌─────────────────────────────────────────────────────────┐
│                   External Systems                      │
├─────────────────────────────────────────────────────────┤
│  • Oracle Database     • SQL Server    • PostgreSQL     │
│  • Snowflake Data Cloud                                 │
│  • OpenAI API / Vertex AI (for AI fallback)             │
└─────────────────────────────────────────────────────────┘
                          ↓ ↑
┌─────────────────────────────────────────────────────────┐
│              SchemaSentinel System                      │
├─────────────────────────────────────────────────────────┤
│  Frontend Dashboard (React/TypeScript)                  │
│         ↓ ↑                                             │
│  REST API (Spring Boot)                                 │
│         ↓                                               │
│  ┌────────────────────────────────────┐                 │
│  │  AST Transformation Engine         │                 │
│  └────────────────────────────────────┘                 │
│  ┌────────────────────────────────────┐                 │
│  │  AI Agent Fallback                 │                 │
│  └────────────────────────────────────┘                 │
│  ┌────────────────────────────────────┐                 │
│  │  Async Processing Queue            │                 │
│  └────────────────────────────────────┘                 │
└─────────────────────────────────────────────────────────┘
```

### 2.3 User Classes

| User Class | Description | Primary Needs |
|------------|-------------|---------------|
| **Database Administrator** | Manages database migrations | Upload SQL files, review translations, download results |
| **Migration Engineer** | Performs technical migrations | Detailed transformation reports, diff views, validation |
| **Project Manager** | Oversees migration projects | High-level progress reports, success metrics |

---

## 3. Functional Requirements

### 3.1 FR-001: SQL File Upload and Parsing

**Priority:** HIGH  
**Description:** The system shall accept SQL files from legacy database systems and parse them into AST structures.

**Requirements:**
- FR-001.1: Accept files with extensions: `.sql`, `.ddl`, `.plsql`, `.tsql`, `.pgsql`
- FR-001.2: Support file sizes up to 10MB per file
- FR-001.3: Support batch uploads (up to 100 files per batch)
- FR-001.4: Detect source database type (Oracle, SQL Server, PostgreSQL) automatically or via user selection
- FR-001.5: Parse SQL statements using JSqlParser into AST representation
- FR-001.6: Handle malformed SQL with graceful error reporting

**Input:** SQL file(s)  
**Output:** Parsed AST objects, parsing status report  
**Error Handling:** Return detailed parse errors with line numbers and error context

---

### 3.2 FR-002: AST-Based Deterministic Transformation

**Priority:** HIGH  
**Description:** The system shall transform legacy SQL ASTs to Snowflake-compatible SQL using deterministic rules.

**Requirements:**
- FR-002.1: Implement Visitor Pattern for AST traversal
- FR-002.2: Transform data types according to mapping rules (see Data Type Mappings table)
- FR-002.3: Convert proprietary functions to Snowflake equivalents
- FR-002.4: Transform DDL statements (CREATE TABLE, CREATE VIEW, CREATE PROCEDURE)
- FR-002.5: Handle DML statements (INSERT, UPDATE, DELETE, SELECT)
- FR-002.6: Preserve comments and documentation
- FR-002.7: Maintain referential integrity in foreign key constraints

**Data Type Mappings:**

| Source System | Source Type | Snowflake Type | Notes |
|---------------|-------------|----------------|-------|
| Oracle | VARCHAR2(n) | VARCHAR(n) | Direct mapping |
| Oracle | NUMBER(p,s) | NUMBER(p,s) | Preserve precision |
| Oracle | CLOB | VARCHAR(16777216) | Convert to VARCHAR |
| Oracle | BLOB | BINARY | Convert to BINARY |
| Oracle | DATE | TIMESTAMP_NTZ | Timezone-neutral |
| Oracle | TIMESTAMP | TIMESTAMP_NTZ | Timezone-neutral |
| SQL Server | NVARCHAR(n) | VARCHAR(n) | Unicode support implicit |
| SQL Server | DATETIME | TIMESTAMP_NTZ | Convert datetime |
| SQL Server | IMAGE | BINARY | Binary data |
| PostgreSQL | TEXT | VARCHAR | Snowflake prefers VARCHAR |
| PostgreSQL | BYTEA | BINARY | Binary data |

**Input:** Parsed AST from legacy SQL  
**Output:** Transformed AST compatible with Snowflake  
**Error Handling:** Log transformation failures for AI fallback

---

### 3.3 FR-003: AI-Powered Fallback Translation

**Priority:** MEDIUM  
**Description:** The system shall use AI agents to translate code segments that fail deterministic transformation.

**Requirements:**
- FR-003.1: Identify code blocks that fail AST parsing or transformation
- FR-003.2: Extract context (surrounding code, function signatures, comments)
- FR-003.3: Generate context-aware prompts for LLM translation
- FR-003.4: Support multiple AI providers (OpenAI, Vertex AI)
- FR-003.5: Mark AI-generated code with review flags
- FR-003.6: Include assumptions and limitations in AI-generated code comments
- FR-003.7: Implement retry logic for transient AI API failures
- FR-003.8: Rate limit AI API calls to prevent quota exhaustion

**Prompt Template:**
```
Translate the following {SOURCE_DB} {OBJECT_TYPE} code to Snowflake Scripting.

Source Code:
{FAILED_CODE_BLOCK}

Context:
- Surrounding code: {CONTEXT}
- Original file: {FILENAME}
- Line numbers: {START_LINE}-{END_LINE}

Requirements:
1. Maintain business logic and functionality
2. Use Snowflake-compatible syntax
3. Mark any assumptions with comments
4. Include potential limitations or warnings
```

**Input:** Failed code segments, context information  
**Output:** AI-translated Snowflake code, confidence score  
**Error Handling:** Log failures, provide manual intervention option

---

### 3.4 FR-004: Asynchronous Processing

**Priority:** HIGH  
**Description:** The system shall process large migration jobs asynchronously without blocking the user interface.

**Requirements:**
- FR-004.1: Implement job queue using Spring @Async or CompletableFuture
- FR-004.2: Support concurrent processing of multiple files (configurable thread pool)
- FR-004.3: Provide job status tracking (PENDING, PROCESSING, COMPLETED, FAILED)
- FR-004.4: Implement progress reporting (percentage complete, files processed)
- FR-004.5: Support job cancellation for long-running migrations
- FR-004.6: Persist job state for recovery after system restart
- FR-004.7: Implement job prioritization (optional: FIFO or priority-based)

**Input:** Migration job request  
**Output:** Job ID, status updates via WebSocket or polling  
**Error Handling:** Retry failed jobs with exponential backoff

---

### 3.5 FR-005: Migration Report Generation

**Priority:** HIGH  
**Description:** The system shall generate comprehensive migration reports in JSON format.

**Requirements:**
- FR-005.1: Calculate success rate (percentage of objects successfully migrated)
- FR-005.2: List all data type transformations with before/after mapping
- FR-005.3: Flag AI-generated code segments requiring review
- FR-005.4: Include transformation warnings (e.g., potential data loss, deprecated features)
- FR-005.5: Generate execution summary (files processed, errors encountered, processing time)
- FR-005.6: Export reports in JSON format
- FR-005.7: Support CSV export for data type mappings (optional)

**Report Structure (JSON):**
```json
{
  "migrationId": "uuid",
  "timestamp": "ISO-8601",
  "summary": {
    "totalFiles": 100,
    "successfulFiles": 95,
    "failedFiles": 5,
    "successRate": 0.95,
    "processingTime": "PT2H30M"
  },
  "transformations": [
    {
      "sourceType": "VARCHAR2(255)",
      "targetType": "VARCHAR(255)",
      "occurrences": 45,
      "files": ["file1.sql", "file2.sql"]
    }
  ],
  "aiGenerated": [
    {
      "file": "complex_procedure.sql",
      "lines": "150-300",
      "confidence": 0.85,
      "reviewRequired": true
    }
  ],
  "warnings": [
    {
      "type": "DATA_TYPE_LOSS",
      "message": "CLOB to VARCHAR may truncate large text",
      "files": ["file3.sql"]
    }
  ],
  "errors": [
    {
      "file": "file5.sql",
      "line": 42,
      "message": "Unsupported Oracle package reference",
      "action": "REQUIRES_MANUAL_REVIEW"
    }
  ]
}
```

**Input:** Completed migration job  
**Output:** JSON/CSV report  
**Error Handling:** Generate partial reports for failed migrations

---

### 3.6 FR-006: Side-by-Side Diff Visualization

**Priority:** MEDIUM  
**Description:** The frontend dashboard shall display original and transformed SQL side-by-side for review.

**Requirements:**
- FR-006.1: Render original SQL with syntax highlighting
- FR-006.2: Render transformed Snowflake SQL with syntax highlighting
- FR-006.3: Highlight differences between original and transformed code
- FR-006.4: Support line-by-line comparison
- FR-006.5: Enable inline commenting on specific transformations
- FR-006.6: Support navigation between multiple files in a batch
- FR-006.7: Export individual file diffs as HTML or PDF (optional)

**Input:** Original SQL, Transformed SQL  
**Output:** Visual diff display in React UI  
**Error Handling:** Display error messages if rendering fails

---

### 3.7 FR-007: File Download and Export

**Priority:** MEDIUM  
**Description:** The system shall allow users to download transformed SQL files and reports.

**Requirements:**
- FR-007.1: Download individual transformed SQL files
- FR-007.2: Download batch ZIP archive of all transformed files
- FR-007.3: Download migration report (JSON)
- FR-007.4: Maintain original file names with `.snowflake` suffix (optional)
- FR-007.5: Preserve directory structure in batch downloads (optional)

**Input:** Migration job ID, file selection  
**Output:** File download (individual or ZIP)  
**Error Handling:** Return 404 if file not found

---

### 3.8 FR-008: Validation and Syntax Checking

**Priority:** MEDIUM  
**Description:** The system shall validate transformed SQL against Snowflake syntax rules.

**Requirements:**
- FR-008.1: Perform basic syntax validation before export
- FR-008.2: Check for reserved keyword conflicts
- FR-008.3: Validate data type compatibility
- FR-008.4: Flag unsupported Snowflake features
- FR-008.5: Provide validation warnings (non-blocking)

**Input:** Transformed SQL  
**Output:** Validation report with warnings/errors  
**Error Handling:** Mark files with validation errors for review

---

### 3.9 FR-009: Data Gravity Assessment

**Priority:** MEDIUM  
**Description:** The system shall analyze schema complexity and dependency relationships to calculate "Data Gravity" scores for migration planning.

**Requirements:**
- FR-009.1: Extract table relationships via foreign key constraint analysis
- FR-009.2: Calculate dependency depth for each table (number of related tables)
- FR-009.3: Accept optional metadata input (row counts, table sizes) for enhanced analysis
- FR-009.4: Generate "Data Gravity" scores based on table size × dependency count
- FR-009.5: Identify tightly-coupled table clusters that must migrate together
- FR-009.6: Generate complexity heatmap data for dashboard visualization
- FR-009.7: Produce migration wave recommendations (suggested migration order)

**Data Gravity Score Formula:**
```
Gravity Score = log10(row_count) × (1 + FK_dependency_count × 0.5) × size_factor
```

**Input:** Parsed AST, optional table statistics metadata (JSON)  
**Output:** Data Gravity report with scores, heatmap data, and migration wave recommendations  
**Error Handling:** Proceed with AST-only analysis if metadata unavailable

---

### 3.10 FR-010: Automated Validation Script Generation

**Priority:** HIGH  
**Description:** The system shall generate paired validation scripts to verify data integrity post-migration.

**Requirements:**
- FR-010.1: Generate source database validation query for each transformed table
- FR-010.2: Generate corresponding Snowflake validation query
- FR-010.3: Include row count validation (`SELECT COUNT(*)`)
- FR-010.4: Include aggregate checksum validation for numeric columns (`SUM(HASH(columns))`)
- FR-010.5: Include NULL count validation for key columns
- FR-010.6: Generate validation as Java-executable scripts or standalone SQL
- FR-010.7: Support batch validation script generation for all tables in migration
- FR-010.8: Include comparison logic to detect mismatches

**Validation Script Structure:**
```java
public class TableValidationScript {
    public ValidationResult validateRowCount();
    public ValidationResult validateChecksums();
    public ValidationResult validateNullCounts();
    public ComparisonReport compareResults(SourceResult src, TargetResult tgt);
}
```

**Input:** Transformed table DDL, original table schema  
**Output:** Paired validation scripts (source + target), comparison report template  
**Error Handling:** Skip validation generation for unsupported data types with warning

---

### 3.11 FR-011: CDC Integration Configuration Generation

**Priority:** MEDIUM  
**Description:** The system shall generate Change Data Capture (CDC) configurations for hybrid migration pipelines.

**Requirements:**
- FR-011.1: Generate Snowpipe DDL for incremental data loading
- FR-011.2: Generate Snowflake Stream definitions for change tracking
- FR-011.3: Generate Snowflake Task definitions for automated processing
- FR-011.4: Include staging table definitions for CDC landing zones
- FR-011.5: Generate merge statements for applying changes to target tables
- FR-011.6: Support configurable CDC frequency (near-real-time, hourly, daily)
- FR-011.7: Include error handling and dead-letter queue configurations

**CDC Configuration Output:**
```sql
-- Snowpipe Definition
CREATE PIPE {schema}.{table}_PIPE AS
COPY INTO {staging_table} FROM @{stage_name}/{path};

-- Stream Definition  
CREATE STREAM {schema}.{table}_STREAM ON TABLE {staging_table};

-- Task Definition
CREATE TASK {schema}.{table}_MERGE_TASK
  WAREHOUSE = {warehouse}
  SCHEDULE = 'USING CRON 0 * * * * UTC'
AS
  MERGE INTO {target_table} t USING {table}_STREAM s ON ...;
```

**Input:** Transformed table DDL, migration configuration  
**Output:** Snowpipe DDL, Stream/Task definitions, merge statements  
**Error Handling:** Generate CDC stubs with manual configuration notes for complex scenarios

---

### 3.12 FR-012: Intelligent Data Type Optimization

**Priority:** MEDIUM  
**Description:** The system shall optimize data type mappings and suggest Snowflake-specific performance enhancements.

**Requirements:**
- FR-012.1: Analyze actual data distribution when metadata provided
- FR-012.2: Suggest optimal VARCHAR lengths based on max observed values
- FR-012.3: Recommend NUMBER precision reduction where appropriate
- FR-012.4: Identify candidates for VARIANT type (semi-structured data patterns)
- FR-012.5: Parse WHERE clauses from stored procedures to identify filter patterns
- FR-012.6: Recommend Cluster Keys based on frequent filter columns
- FR-012.7: Suggest partitioning strategies for large tables (DATE-based clustering)
- FR-012.8: Generate optimization report with storage and query performance impact estimates

**Cluster Key Recommendation Logic:**
```
1. Parse all WHERE clauses in related stored procedures
2. Identify most frequently filtered columns
3. Prioritize columns with high cardinality
4. Recommend cluster key: ALTER TABLE t CLUSTER BY (col1, col2)
```

**Input:** Transformed DDL, related stored procedures, optional statistics metadata  
**Output:** Optimized DDL with recommendations, optimization report  
**Error Handling:** Provide conservative mappings if optimization analysis fails

---

## 4. Non-Functional Requirements

### 4.1 Performance Requirements

| Requirement | Target | Notes |
|-------------|--------|-------|
| **File Processing** | < 5 seconds per file (avg) | For typical DDL files (< 1KB) |
| **Batch Processing** | 100 files in < 10 minutes | With concurrent processing |
| **API Response Time** | < 500ms (p95) | For synchronous operations |
| **AI Fallback Latency** | < 30 seconds per request | Dependent on AI provider |
| **Concurrent Users** | 50 simultaneous users | With horizontal scaling |

### 4.2 Scalability Requirements

- NFR-002.1: Support horizontal scaling via Docker containers
- NFR-002.2: Queue-based architecture for high-throughput workloads
- NFR-002.3: Stateless API design for load balancing
- NFR-002.4: Support processing of 10,000+ database objects per migration

### 4.3 Reliability Requirements

- NFR-003.1: System availability: 99.5% uptime
- NFR-003.2: Automatic retry for transient failures (max 3 retries)
- NFR-003.3: Job state persistence for crash recovery
- NFR-003.4: Graceful degradation when AI service unavailable

### 4.4 Security Requirements

- NFR-004.1: Encrypt sensitive data at rest (if stored)
- NFR-004.2: HTTPS for all API communications
- NFR-004.3: API authentication/authorization (OAuth 2.0 or API keys)
- NFR-004.4: Sanitize file uploads to prevent injection attacks
- NFR-004.5: Log all API access for audit trail
- NFR-004.6: Secure storage of AI API credentials (environment variables or vault)

### 4.5 Usability Requirements

- NFR-005.1: Intuitive web interface requiring minimal training
- NFR-005.2: Clear error messages with actionable guidance
- NFR-005.3: Responsive design for desktop and tablet devices
- NFR-005.4: Accessibility compliance (WCAG 2.1 Level AA)

### 4.6 Maintainability Requirements

- NFR-006.1: Comprehensive code documentation and inline comments
- NFR-006.2: Unit test coverage > 80%
- NFR-006.3: Integration tests for critical workflows
- NFR-006.4: Modular architecture for easy extension

### 4.7 Compatibility Requirements

- NFR-007.1: Java 21+ runtime environment
- NFR-007.2: Modern browsers (Chrome, Firefox, Safari, Edge - latest 2 versions)
- NFR-007.3: Docker 20.10+ for containerized deployment
- NFR-007.4: Support for Oracle 11g+, SQL Server 2012+, PostgreSQL 9.5+

---

## 5. System Architecture

### 5.1 High-Level Architecture

```
┌──────────────────────────────────────────────────────────────┐
│                     Client Layer                             │
│  React + TypeScript SPA (Port 3000)                          │
│  • File Upload UI                                            │
│  • Diff Viewer                                               │
│  • Report Dashboard                                          │
│  • Complexity Heatmaps                                       │
└──────────────────────┬───────────────────────────────────────┘
                       │ HTTP/REST
┌──────────────────────▼───────────────────────────────────────┐
│                    API Gateway                               │
│  Spring Boot 3.2 REST API (Port 8080)                        │
│  • Authentication                                            │
│  • Request Routing                                           │
│  • Rate Limiting                                             │
└──────────────────────┬───────────────────────────────────────┘
                       │
┌──────────────────────▼───────────────────────────────────────┐
│                  Business Logic Layer                        │
├──────────────────────────────────────────────────────────────┤
│  ┌──────────────────┐  ┌──────────────────┐                  │
│  │  Migration       │  │  File            │                  │
│  │  Service         │  │  Service         │                  │
│  └────────┬─────────┘  └────────┬─────────┘                  │
│           │                     │                            │
│  ┌────────▼─────────────────────▼─────────┐                  │
│  │      AST Transformation Engine         │                  │
│  │  • JSqlParser Integration              │                  │
│  │  • Visitor Pattern Implementation      │                  │
│  │  • Type Mapping Rules                  │                  │
│  └────────┬─────────────────────┬─────────┘                  │
│           │                     │                            │
│  ┌────────▼─────────┐  ┌────────▼─────────┐                  │
│  │  AI Agent        │  │  Async Job       │                  │
│  │  Service         │  │  Queue           │                  │
│  │  • Spring AI     │  │  • @Async        │                  │
│  │  • OpenAI/Vertex │  │  • Completable   │                  │
│  └──────────────────┘  │    Future        │                  │
│                        └──────────────────┘                  │
│                                                              │
│  ┌───────────────────────────────────────────────────────┐   │
│  │         Data Migration Architect Modules              │   │
│  ├───────────────────────────────────────────────────────┤   │
│  │ ┌─────────────┐ ┌─────────────┐ ┌─────────────┐       │   │
│  │ │ Data Gravity│ │ Validation  │ │ CDC Config  │       │   │
│  │ │ Analyzer    │ │ Generator   │ │ Generator   │       │   │
│  │ └─────────────┘ └─────────────┘ └─────────────┘       │   │
│  │ ┌─────────────┐                                       │   │
│  │ │ Intelligent │                                       │   │
│  │ │ Optimizer   │                                       │   │
│  │ └─────────────┘                                       │   │
│  └───────────────────────────────────────────────────────┘   │
└──────────────────────────────────────────────────────────────┘
                       │
┌──────────────────────▼───────────────────────────────────────┐
│                   External Services                          │
│  • OpenAI API / Vertex AI (AI Translation)                   │
│  • (Optional) Database for job persistence                   │
└──────────────────────────────────────────────────────────────┘
```

### 5.2 Component Design

#### 5.2.1 AST Transformation Engine

**Component:** `com.schemasentinel.parser.TransformationEngine`

**Responsibilities:**
- Parse SQL into AST using JSqlParser
- Apply transformation rules via Visitor Pattern
- Generate Snowflake-compatible SQL

**Key Classes:**
- `SqlParserService`: Wraps JSqlParser
- `SnowflakeVisitor`: Implements transformation logic
- `DataTypeMapper`: Maps legacy types to Snowflake
- `FunctionTransformer`: Converts proprietary functions

#### 5.2.2 AI Agent Service

**Component:** `com.schemasentinel.ai.AgentService`

**Responsibilities:**
- Identify failed transformation segments
- Generate context-aware prompts
- Call LLM APIs (OpenAI/Vertex AI)
- Post-process AI responses

**Key Classes:**
- `AgentService`: Main AI orchestration
- `PromptBuilder`: Constructs translation prompts
- `ResponseProcessor`: Validates and marks AI code

#### 5.2.3 Async Processing Queue

**Component:** `com.schemasentinel.async.JobProcessor`

**Responsibilities:**
- Queue migration jobs
- Process files asynchronously
- Track job status and progress
- Handle retries and failures

**Key Classes:**
- `JobService`: Job lifecycle management
- `AsyncFileProcessor`: Processes individual files
- `JobRepository`: Persists job state (optional)

#### 5.2.4 Data Gravity Analyzer

**Component:** `com.schemasentinel.gravity.DataGravityAnalyzer`

**Responsibilities:**
- Analyze foreign key relationships across schemas
- Calculate dependency depth and coupling metrics
- Generate Data Gravity scores
- Produce complexity heatmap data
- Recommend migration waves

**Key Classes:**
- `GravityAnalyzer`: Main analysis orchestration
- `DependencyGraphBuilder`: Constructs FK relationship graph
- `GravityScoreCalculator`: Computes gravity scores
- `HeatmapGenerator`: Generates visualization data
- `WaveRecommender`: Suggests migration order

#### 5.2.5 Validation Script Generator

**Component:** `com.schemasentinel.validation.ValidationGenerator`

**Responsibilities:**
- Generate source database validation queries
- Generate target Snowflake validation queries
- Create row count and checksum validators
- Produce comparison report templates

**Key Classes:**
- `ValidationGenerator`: Main generation orchestration
- `SourceQueryBuilder`: Builds legacy DB validation queries
- `SnowflakeQueryBuilder`: Builds Snowflake validation queries
- `ChecksumCalculator`: Implements hash-based validation
- `ComparisonReportBuilder`: Creates comparison templates

#### 5.2.6 CDC Configuration Generator

**Component:** `com.schemasentinel.cdc.CdcConfigGenerator`

**Responsibilities:**
- Generate Snowpipe DDL definitions
- Create Stream and Task configurations
- Produce merge statements for CDC processing
- Generate staging table definitions

**Key Classes:**
- `CdcConfigGenerator`: Main CDC orchestration
- `SnowpipeBuilder`: Generates Snowpipe DDL
- `StreamTaskBuilder`: Creates Stream/Task definitions
- `MergeStatementBuilder`: Builds merge logic
- `StagingTableBuilder`: Designs landing zone tables

#### 5.2.7 Intelligent Optimizer

**Component:** `com.schemasentinel.optimizer.IntelligentOptimizer`

**Responsibilities:**
- Analyze data distribution metadata
- Optimize data type mappings
- Parse stored procedures for filter patterns
- Recommend cluster keys and partitioning

**Key Classes:**
- `IntelligentOptimizer`: Main optimization orchestration
- `DataDistributionAnalyzer`: Analyzes column statistics
- `WhereClauseExtractor`: Parses filter patterns from SPs
- `ClusterKeyRecommender`: Suggests cluster keys
- `OptimizationReportBuilder`: Generates recommendations

### 5.3 Design Patterns

| Pattern | Usage | Benefit |
|---------|-------|---------|
| **Visitor Pattern** | AST transformation | Separates transformation logic from AST structure |
| **Strategy Pattern** | Multiple AI providers | Interchangeable AI backends |
| **Factory Pattern** | Parser creation | Database-specific parser instantiation |
| **Observer Pattern** | Job progress updates | Real-time UI updates |
| **Template Method** | Migration workflow | Consistent processing pipeline |
| **Builder Pattern** | CDC/Validation generation | Flexible configuration construction |
| **Graph Pattern** | Data Gravity analysis | Dependency relationship modeling |

---

## 6. Data Requirements

### 6.1 Data Models

#### 6.1.1 Migration Job

```java
public class MigrationJob {
    private String jobId;              // UUID
    private String userId;             // User identifier
    private JobStatus status;          // PENDING, PROCESSING, COMPLETED, FAILED
    private SourceDatabase sourceType; // ORACLE, SQL_SERVER, POSTGRESQL
    private List<FileMetadata> files;  // Uploaded files
    private LocalDateTime createdAt;
    private LocalDateTime completedAt;
    private MigrationReport report;    // Generated report
}
```

#### 6.1.2 File Metadata

```java
public class FileMetadata {
    private String fileId;             // UUID
    private String fileName;
    private long fileSize;
    private String originalContent;    // Original SQL
    private String transformedContent; // Snowflake SQL
    private TransformationStatus status;
    private List<TransformationWarning> warnings;
    private List<TransformationError> errors;
}
```

#### 6.1.3 Migration Report

```java
public class MigrationReport {
    private String migrationId;
    private LocalDateTime timestamp;
    private Summary summary;
    private List<DataTypeTransformation> transformations;
    private List<AIGeneratedSegment> aiGenerated;
    private List<Warning> warnings;
    private List<Error> errors;
}
```

### 6.2 Data Storage

**Temporary Storage:**
- Uploaded files stored in-memory or temporary filesystem
- Cleanup after job completion or TTL expiry (24 hours)

**Persistent Storage (Optional):**
- Job metadata in relational database (PostgreSQL/H2)
- Reports stored as JSON files or in database
- Audit logs for compliance

---

## 7. Interface Requirements

### 7.1 REST API Endpoints

#### 7.1.1 File Upload

```
POST /api/v1/migrations
Content-Type: multipart/form-data

Request:
  - files: File[] (required)
  - sourceType: ORACLE | SQL_SERVER | POSTGRESQL (required)

Response:
{
  "jobId": "uuid",
  "status": "PENDING",
  "fileCount": 10
}
```

#### 7.1.2 Job Status

```
GET /api/v1/migrations/{jobId}

Response:
{
  "jobId": "uuid",
  "status": "PROCESSING",
  "progress": 0.65,
  "filesProcessed": 65,
  "totalFiles": 100
}
```

#### 7.1.3 Download Transformed Files

```
GET /api/v1/migrations/{jobId}/files/{fileId}

Response:
Content-Type: text/plain
[Transformed SQL content]
```

#### 7.1.4 Get Migration Report

```
GET /api/v1/migrations/{jobId}/report

Response:
Content-Type: application/json
[MigrationReport JSON]
```

#### 7.1.5 Download Batch ZIP

```
GET /api/v1/migrations/{jobId}/download

Response:
Content-Type: application/zip
[ZIP archive of all transformed files]
```

### 7.2 WebSocket Events (Optional)

```
ws://host/api/v1/migrations/{jobId}/stream

Events:
- job.progress: { progress: 0.65, filesProcessed: 65 }
- job.completed: { jobId: "uuid", reportId: "uuid" }
- job.failed: { jobId: "uuid", error: "message" }
```

### 7.3 Frontend Components

- **FileUploadComponent**: Drag-and-drop file upload
- **JobStatusComponent**: Real-time job progress display
- **DiffViewerComponent**: Side-by-side SQL comparison
- **ReportViewerComponent**: Migration report visualization
- **FileDownloadComponent**: Download transformed files
- **HeatmapComponent**: Data Gravity complexity heatmap visualization
- **ValidationDashboard**: Validation script status and results display

### 7.4 Data Migration Architect API Endpoints

#### 7.4.1 Data Gravity Analysis

```
POST /api/v1/migrations/{jobId}/gravity
Content-Type: application/json

Request:
{
  "tableMetadata": [
    { "tableName": "ORDERS", "rowCount": 10000000, "sizeBytes": 5368709120 }
  ]
}

Response:
{
  "jobId": "uuid",
  "gravityScores": [
    { "tableName": "ORDERS", "gravityScore": 8.5, "dependencies": 12 }
  ],
  "heatmapData": { ... },
  "migrationWaves": [
    { "wave": 1, "tables": ["CUSTOMERS", "PRODUCTS"] },
    { "wave": 2, "tables": ["ORDERS", "ORDER_ITEMS"] }
  ]
}
```

#### 7.4.2 Generate Validation Scripts

```
POST /api/v1/migrations/{jobId}/validation
Content-Type: application/json

Request:
{
  "tables": ["ORDERS", "CUSTOMERS"],
  "validationType": "FULL" | "ROW_COUNT_ONLY"
}

Response:
{
  "jobId": "uuid",
  "validationScripts": [
    {
      "tableName": "ORDERS",
      "sourceScript": "SELECT COUNT(*), SUM(HASH_AGG(...)) FROM ORDERS",
      "targetScript": "SELECT COUNT(*), SUM(HASH(...)) FROM ORDERS"
    }
  ]
}
```

#### 7.4.3 Download Validation Scripts

```
GET /api/v1/migrations/{jobId}/validation/download

Response:
Content-Type: application/zip
[ZIP archive of validation scripts (Java + SQL)]
```

#### 7.4.4 Generate CDC Configuration

```
POST /api/v1/migrations/{jobId}/cdc
Content-Type: application/json

Request:
{
  "tables": ["ORDERS"],
  "cdcFrequency": "NEAR_REALTIME" | "HOURLY" | "DAILY",
  "warehouse": "MIGRATION_WH"
}

Response:
{
  "jobId": "uuid",
  "cdcConfigurations": [
    {
      "tableName": "ORDERS",
      "snowpipeDDL": "CREATE PIPE ...",
      "streamDDL": "CREATE STREAM ...",
      "taskDDL": "CREATE TASK ...",
      "mergeDML": "MERGE INTO ..."
    }
  ]
}
```

#### 7.4.5 Download CDC Configuration

```
GET /api/v1/migrations/{jobId}/cdc/download

Response:
Content-Type: application/zip
[ZIP archive of CDC configuration scripts]
```

#### 7.4.6 Get Optimization Recommendations

```
POST /api/v1/migrations/{jobId}/optimize
Content-Type: application/json

Request:
{
  "includeClusterKeys": true,
  "tableStatistics": [
    { "tableName": "ORDERS", "columnStats": { ... } }
  ]
}

Response:
{
  "jobId": "uuid",
  "optimizations": [
    {
      "tableName": "ORDERS",
      "originalDDL": "CREATE TABLE ...",
      "optimizedDDL": "CREATE TABLE ... CLUSTER BY (ORDER_DATE)",
      "recommendations": [
        { "type": "CLUSTER_KEY", "column": "ORDER_DATE", "reason": "High filter frequency in SPs" }
      ],
      "estimatedImpact": { "storageSavings": "15%", "queryImprovement": "40%" }
    }
  ]
}
```

---

## 8. Security Requirements

### 8.1 Authentication and Authorization

- **SEC-001**: Implement OAuth 2.0 or API key authentication
- **SEC-002**: Role-based access control (RBAC) for different user types
- **SEC-003**: Session management with secure cookies/HTTP-only tokens
- **SEC-004**: Rate limiting to prevent abuse (100 requests/minute per user)

### 8.2 Data Protection

- **SEC-005**: Encrypt file uploads in transit (HTTPS/TLS 1.2+)
- **SEC-006**: Sanitize file content to prevent SQL injection in parser
- **SEC-007**: Do not log sensitive SQL content in production logs
- **SEC-008**: Secure storage of AI API credentials (environment variables or secrets manager)

### 8.3 Input Validation

- **SEC-009**: Validate file types and sizes before processing
- **SEC-010**: Reject files exceeding size limits (10MB)
- **SEC-011**: Validate file names to prevent path traversal attacks
- **SEC-012**: Rate limit file uploads per user/IP

---

## 9. Performance Requirements

### 9.1 Response Times

- **PERF-001**: File upload response: < 1 second
- **PERF-002**: Job status API: < 500ms (p95)
- **PERF-003**: File download: < 2 seconds for files < 1MB
- **PERF-004**: Report generation: < 5 seconds for 100 files

### 9.2 Throughput

- **PERF-005**: Process 100 files concurrently with 10 worker threads
- **PERF-006**: Support 50 simultaneous API requests
- **PERF-007**: Handle 1000 files in a single batch migration

### 9.3 Resource Utilization

- **PERF-008**: Memory usage: < 2GB for processing 100 files
- **PERF-009**: CPU utilization: Efficient thread pool sizing
- **PERF-010**: Network bandwidth: Compress large file downloads

---

## 10. Testing Requirements

### 10.1 Unit Testing

- **TEST-001**: Unit tests for all transformation rules (> 80% coverage)
- **TEST-002**: Unit tests for data type mappings
- **TEST-003**: Unit tests for AI prompt generation
- **TEST-004**: Unit tests for report generation logic

### 10.2 Integration Testing

- **TEST-005**: End-to-end migration workflow tests
- **TEST-006**: API endpoint integration tests
- **TEST-007**: Async job processing integration tests
- **TEST-008**: File upload and download integration tests

### 10.3 Acceptance Testing

- **TEST-009**: Test with real-world SQL samples from each source database
- **TEST-010**: Validate transformation accuracy (manual review of sample outputs)
- **TEST-011**: Performance testing with large file batches
- **TEST-012**: Load testing with concurrent users

### 10.4 Test Data

- Sample SQL files from each source database type
- Complex stored procedures and views
- Edge cases (unsupported features, malformed SQL)

---

## 11. Deployment Requirements

### 11.1 Build and Packaging

- **DEPLOY-001**: Maven/Gradle build configuration
- **DEPLOY-002**: Docker containerization for backend
- **DEPLOY-003**: Docker containerization for frontend (or static hosting)
- **DEPLOY-004**: Multi-stage Docker builds for optimized images

### 11.2 CI/CD Pipeline

**GitHub Actions Workflow:**
1. Lint and format code
2. Run unit tests
3. Build Docker images
4. Run integration tests
5. Push images to container registry
6. Deploy to staging environment (optional)
7. Manual approval for production

### 11.3 Environment Configuration

**Environment Variables:**
- `SPRING_PROFILES_ACTIVE`: dev, staging, prod
- `OPENAI_API_KEY` or `VERTEX_AI_CREDENTIALS`: AI service credentials
- `SERVER_PORT`: Application port (default 8080)
- `CORS_ALLOWED_ORIGINS`: Frontend URLs
- `MAX_FILE_SIZE`: File upload limit
- `THREAD_POOL_SIZE`: Concurrent processing threads

### 11.4 Deployment Architecture

```
┌─────────────────────────────────────────┐
│         Load Balancer (nginx)           │
└──────────────┬──────────────────────────┘
               │
    ┌──────────┴──────────┐
    │                     │
┌───▼────┐          ┌────▼────┐
│ App    │          │ App     │
│ Node 1 │          │ Node 2  │
└────────┘          └─────────┘
```

**Scaling:**
- Horizontal scaling via Docker containers
- Stateless application design
- Shared job queue (optional: Redis/RabbitMQ for multi-instance)

---

## 12. Constraints and Assumptions

### 12.1 Constraints

- **CONST-001**: Limited to schema/DDL migration (no data migration)
- **CONST-002**: Dependent on JSqlParser library capabilities
- **CONST-003**: AI translation quality depends on LLM provider
- **CONST-004**: Processing time scales with file complexity
- **CONST-005**: Some Oracle/SQL Server features may not have Snowflake equivalents

### 12.2 Assumptions

- **ASSUME-001**: Users have basic knowledge of source and target databases
- **ASSUME-002**: Source SQL files are syntactically valid (minor parsing errors handled)
- **ASSUME-003**: AI API credentials are available and configured
- **ASSUME-004**: Snowflake account and permissions are pre-configured
- **ASSUME-005**: Network connectivity to AI services is available
- **ASSUME-006**: Files uploaded are legitimate SQL files (basic validation)

---

## 13. Risks and Mitigation

| Risk | Impact | Probability | Mitigation |
|------|--------|-------------|------------|
| **AI API failures/unavailability** | High | Medium | Fallback to manual review queue; retry logic; multiple AI provider support |
| **Inaccurate transformations** | High | Low | Comprehensive testing; AI-generated code flags; manual review workflow |
| **Performance bottlenecks with large batches** | Medium | Medium | Async processing; horizontal scaling; batch chunking |
| **Unsupported database features** | Medium | High | Clear documentation; warning flags; manual intervention points |
| **Security vulnerabilities in file uploads** | High | Low | Input validation; sanitization; security scanning |
| **JSqlParser limitations** | Medium | Medium | AI fallback for unsupported syntax; parser updates tracking |
| **Cost overruns from AI API usage** | Low | Medium | Rate limiting; usage monitoring; caching for similar patterns |

---

## 14. Glossary

| Term | Definition |
|------|------------|
| **AST (Abstract Syntax Tree)** | Tree data structure representing syntactic structure of source code |
| **DDL (Data Definition Language)** | SQL subset for defining database structures (CREATE, ALTER, DROP) |
| **Deterministic Transformation** | Rule-based transformation with predictable, repeatable outcomes |
| **JSqlParser** | Java library for parsing SQL into AST |
| **LLM (Large Language Model)** | AI model capable of understanding and generating natural language/code |
| **Migration** | Process of moving database objects from one system to another |
| **PL/SQL** | Oracle's procedural extension to SQL |
| **Probabilistic Transformation** | AI-based transformation with variable outcomes requiring review |
| **Snowflake Scripting** | Snowflake's procedural language extension |
| **T-SQL** | Microsoft SQL Server's procedural extension to SQL |
| **Visitor Pattern** | Design pattern for traversing and operating on object structures |
| **PL/pgSQL** | PostgreSQL's procedural extension to SQL |

---

## Document History

| Version | Date | Author | Changes |
|---------|------|--------|---------|
| 1.0 | 2024 | Development Team | Initial SRD creation |

---

**End of Document**
