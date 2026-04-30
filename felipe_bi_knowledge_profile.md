# 🧠 Felipe Maldonado - BI & Data Engineering Knowledge Profile

## 👤 Professional Profile

**Senior Business Intelligence Analyst** with strong Data Engineering capabilities, based in Quebec, Canada.

I specialize in designing, developing, and delivering end-to-end BI solutions, combining deep business understanding with technical expertise to drive data-informed decision making.

---

## 🎯 Professional Summary

- **10+ years** in Business Intelligence and Data Analytics
- **Strong expertise** in Snowflake, SQL, and Power BI
- **Advanced data modeling** (SCD Type 2, dimensional modeling, star schema)
- **API integration** experience connecting external platforms to data warehouses
- **Full lifecycle ownership**: analysis → design → development → delivery → maintenance
- **Bilingual**: English and French documentation and communication

---

## 🧩 Core Competencies

### 🔹 Data Engineering
- **ETL/ELT Design**: End-to-end pipeline architecture
- **API Integration**: REST API data extraction and transformation
- **Data Pipelines**: Automated data flow orchestration
- **Data Transformation**: Business logic implementation in SQL
- **Incremental Loading**: Optimized data refresh strategies

### 🔹 Data Modeling
- **SCD Type 2**: Historical change tracking implementation
- **Dimensional Modeling**: Facts, dimensions, and relationships
- **Star Schema**: Optimized models for BI performance
- **Data Quality**: Validation, cleansing, and integrity checks
- **Data Lineage**: Tracking data flow from source to consumption

### 🔹 BI & Visualization
- **Power BI Development**: Report building from scratch
- **Performance Optimization**: DAX and data model tuning
- **Business Metrics**: KPI definition and calculation
- **Report Design**: User-centric visualization
- **Print Layout**: Professional report formatting

### 🔹 SQL & Databases
- **Advanced SQL**: Complex queries and optimization
- **Window Functions**: ROW_NUMBER, RANK, LAG, LEAD
- **Query Performance**: Indexing and execution plan analysis
- **CTEs**: Common Table Expressions for readability
- **Stored Procedures**: Modular SQL development

### 🔹 Snowflake
- **Stored Procedures**: JavaScript-based procedures
- **MERGE Logic**: SCD2 and upsert operations
- **Incremental Loads**: Streams and Tasks for automation
- **Data Warehousing**: Micro-partitions and clustering
- **Performance Tuning**: Warehouse sizing and optimization

---

## 🏗️ Technical Stack

| Category | Technologies |
|----------|--------------|
| **Data Warehouse** | Snowflake |
| **Query Language** | SQL (Advanced) |
| **BI Tool** | Power BI |
| **Integration** | REST APIs |
| **Project Management** | Jira, Agile/Scrum |
| **Documentation** | Markdown, Confluence |
| **Version Control** | Git |
| **AI Tools** | ChatGPT, Claude, Windsurf |

---

## 🚀 Key Projects

### 📊 Avatega Migration Project
**Challenge**: Migrate BI reports from legacy database access to API-based integration.

**Deliverables**:
- Rebuilt Airing List and Inventory Avails reports
- Reverse-engineered complex business logic
- Integrated API data sources with Snowflake
- Resolved cost aggregation inconsistencies
- Reduced data refresh time from 4 hours to 30 minutes

**Impact**: Improved data accuracy, enhanced security, reduced maintenance overhead

### 📺 Airing List Optimization
**Challenge**: Performance issues and aggregation errors in critical media reporting.

**Solutions**:
- Optimized Power BI data model
- Fixed double-counting in cost calculations
- Enhanced print layout for professional presentation
- Implemented proper grain alignment

**Impact**: Faster report loading, accurate cost allocation

### 🔗 API Integration Platform
**Challenge**: Integrate external marketing platforms with internal BI systems.

**Implementation**:
- Built data pipelines for multiple API endpoints
- Implemented rate limiting and error handling
- Ensured data consistency across systems
- Created reusable integration patterns

**Impact**: Unified data view, automated reporting, reduced manual work

---

## 🧠 BI Engineering Methodology

### 6-Step Delivery Process

1. **Requirements Analysis**
   - Understand business needs and KPIs
   - Identify data sources and constraints
   - Document acceptance criteria

2. **Data Source Identification**
   - Map available data sources
   - Assess data quality and completeness
   - Identify gaps and missing data

3. **Data Model Design**
   - Design star schema with facts and dimensions
   - Define relationships and cardinality
   - Plan for SCD requirements

4. **Transformation Development**
   - Implement business logic in SQL
   - Build data quality validations
   - Optimize query performance

5. **Report Delivery**
   - Build Power BI visualizations
   - Implement DAX measures
   - Optimize for performance

6. **Stakeholder Validation**
   - User acceptance testing
   - Performance validation
   - Documentation and handoff

---

## ⚙️ Signature Patterns

### 🔹 SCD Type 2 Implementation
```sql
-- Core pattern: Detect → Close → Insert
MERGE INTO DIM_TABLE tgt
USING STAGING_TABLE src
ON tgt.ID = src.ID AND tgt.IS_CURRENT = TRUE
WHEN MATCHED THEN UPDATE SET END_DATE = CURRENT_DATE(), IS_CURRENT = FALSE
WHEN NOT MATCHED THEN INSERT (ID, START_DATE, END_DATE, IS_CURRENT)
VALUES (src.ID, CURRENT_DATE(), NULL, TRUE)
```

### 🔹 API Integration Pattern
```
Extract → Transform → Load → Rebuild Logic
```
- Extract from REST API with pagination
- Transform using business rules
- Load to Snowflake staging
- Rebuild logic in data warehouse

### 🔹 Reporting Best Practices
- Aggregate at correct business grain
- Avoid double counting through proper joins
- Validate data assumptions with stakeholders
- Use star schema for optimal performance
- Filter early in queries

---

## 🤖 AI-Augmented Workflows

### AI Tools Stack
- **ChatGPT**: Requirements analysis, documentation, learning
- **Claude/Claude Code**: Code generation, debugging, refactoring
- **Windsurf**: IDE integration, real-time assistance
- **Codex**: SQL generation and optimization

### AI Use Cases

#### SQL Generation
- Prompt: "Write a query to find top 10 customers by spend"
- Context: Schema, business requirements, platform
- Output: Optimized SQL with window functions

#### Documentation
- Prompt: "Create data dictionary for DIM_CUSTOMER table"
- Context: Column definitions, business rules
- Output: Markdown documentation with examples

#### Debugging
- Prompt: "Debug this SQL error: [error message]"
- Context: Query, schema, error details
- Output: Root cause analysis and fix

#### Jira Templates
- Prompt: "Create Jira task for analysis phase"
- Context: Project details, requirements
- Output: Structured task with checklist

---

## 💬 Communication & Collaboration

### Strengths
- **Business-Technical Bridge**: Translate requirements to technical specs
- **Bilingual Documentation**: Clear docs in English and French
- **Complex Logic Simplification**: Explain technical concepts to non-technical stakeholders
- **Stakeholder Alignment**: Ensure expectations match deliverables

### Documentation Standards
- **Technical Specs**: Detailed implementation plans
- **User Guides**: Step-by-step instructions
- **Data Dictionaries**: Column definitions and business meaning
- **Change Logs**: Track modifications and impact

---

## 🎯 Career Roadmap (Long-Term)

### Short Term (0-6 months)
- [ ] Deepen Snowflake expertise (Streams, Tasks, Materialized Views)
- [ ] Automate workflows using AI tools
- [ ] Expand Python skills for data engineering
- [ ] Build portfolio of end-to-end projects

*See `roadmap.md` for detailed 6-month learning plan*

### Mid Term (6-18 months)
- [ ] Transition to Data Engineer role
- [ ] Increase compensation through skill leverage
- [ ] Master cloud data platforms (beyond Snowflake)
- [ ] Contribute to open-source BI projects

### Long Term (18+ months)
- [ ] Achieve financial freedom
- [ ] Build scalable, automated data systems
- [ ] Establish thought leadership in BI/DE space
- [ ] Mentor junior analysts and engineers

---

## 🧾 Reusable Templates

### 🔹 Jira Analysis Task (French)
```
**Objectif**: Analyse de l'incohérence signalée par le client

**Tâches**:
- [ ] Comprendre le problème business
- [ ] Identifier la source de données
- [ ] Analyser les données actuelles
- [ ] Identifier la cause racine
- [ ] Proposer une solution
```

### 🔹 Jira Development Task (French)
```
**Objectif**: Développement des règles de calcul

**Tâches**:
- [ ] Implémenter la logique SQL
- [ ] Créer/mettre à jour les tables
- [ ] Tester avec données d'échantillon
- [ ] Valider les résultats
- [ ] Documenter les changements
```

### 🔹 Jira Testing Task (French)
```
**Objectif**: Tests intégrés pour validation

**Tâches**:
- [ ] Préparer les cas de test
- [ ] Exécuter les tests
- [ ] Valider les résultats attendus
- [ ] Documenter les anomalies
- [ ] Signer pour livraison
```

---

## 🧠 Knowledge Philosophy

This repository represents my:

> **External professional brain** - version-controlled, structured, and optimized for AI collaboration.

**Principles**:
- **Version Control**: Track evolution of knowledge
- **Structure**: Organized for both human and AI consumption
- **Reusability**: Templates and patterns for rapid application
- **Continuous Learning**: Evolving with experience and new technologies

---

## 📌 AI Usage Instructions

### Primary Context File
Use this profile as the **primary context** when:

- Generating SQL queries and Snowflake procedures
- Creating Power BI reports and data models
- Writing Jira documentation and technical specs
- Simulating interview responses
- Providing architectural recommendations
- Debugging data issues

### Response Alignment Guidelines

**Always align responses with**:
- **Senior BI Analyst mindset**: Business-first, data-driven
- **Data Engineering best practices**: Scalable, maintainable, performant
- **Business-driven decision making**: Solve real problems, not just technical ones
- **Bilingual capability**: English and French when appropriate
- **AI-augmented approach**: Leverage AI tools for efficiency

### Quality Standards

**Code Quality**:
- Follow SQL best practices
- Include comments for complex logic
- Optimize for performance
- Handle edge cases and NULLs

**Documentation Quality**:
- Clear and concise
- Include examples
- Explain business context
- Provide step-by-step instructions

**Communication Quality**:
- Explain technical concepts simply
- Use business language for stakeholders
- Provide context and rationale
- Be precise and accurate
