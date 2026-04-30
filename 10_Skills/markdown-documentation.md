# Markdown Documentation Skill

## Overview
Expertise in creating clear, structured, and professional technical documentation using Markdown for BI specifications, data dictionaries, runbooks, and knowledge bases.

## Capabilities

### Technical Documentation
- Data dictionaries and schemas
- API documentation
- Architecture diagrams (via Mermaid)
- Runbooks and operational guides
- Technical specifications

### BI-Specific Documents
- Data model documentation
- ETL/ELT pipeline descriptions
- Report specifications
- Business rule documentation
- Jira ticket templates

### Knowledge Management
- README files
- Wiki pages
- FAQ and troubleshooting guides
- Onboarding documentation
- Meeting notes and decisions

### Bilingual Documentation
- English technical documentation
- French business communication
- Mixed language contexts
- Translation considerations

## Document Structure

### Standard Technical Document
```markdown
# Document Title

## Overview
Brief summary of what this document covers.

## Prerequisites
- Required knowledge
- System access needed
- Tools required

## Main Content
### Section 1
Details...

### Section 2
Details...

## Examples
\`\`\`sql
-- Code example
SELECT * FROM table;
\`\`\`

## Troubleshooting
| Issue | Solution |
|-------|----------|
| Problem | Fix |

## References
- Link to related docs
- External resources
```

### Data Dictionary Template
```markdown
# Data Dictionary: [Table Name]

## Overview
Description of the table and its purpose.

## Columns

| Column | Data Type | Nullable | Description | Example |
|--------|-----------|----------|-------------|---------|
| id | INTEGER | NO | Primary key | 12345 |
| name | VARCHAR(100) | NO | Customer name | "John Doe" |
| created_at | TIMESTAMP | NO | Record creation time | 2024-01-15 10:30:00 |

## Relationships
- **Foreign Keys**: Links to other tables
- **Referenced By**: Tables that reference this one

## Business Rules
- Rule 1: Description
- Rule 2: Description

## Notes
Additional context or considerations.
```

## Formatting Guidelines

### Headers
```markdown
# H1 - Document title (only one per doc)
## H2 - Major sections
### H3 - Subsections
#### H4 - Detailed points
```

### Code Blocks
```markdown
Inline code: `SELECT * FROM table`

Block code with language:
\`\`\`sql
SELECT 
    column1,
    column2
FROM table_name
WHERE condition = 'value';
\`\`\`

\`\`\`python
def function_name():
    return "Hello World"
\`\`\`

\`\`\`dax
Total Sales = SUM(FACT_SALES[AMOUNT])
\`\`\`
```

### Tables
```markdown
| Header 1 | Header 2 | Header 3 |
|----------|----------|----------|
| Cell 1   | Cell 2   | Cell 3   |
| Cell 4   | Cell 5   | Cell 6   |

Alignment:
| Left | Center | Right |
|:-----|:------:|------:|
| A    | B      | C     |
```

### Lists
```markdown
Unordered:
- Item 1
- Item 2
  - Nested item
  - Another nested
- Item 3

Ordered:
1. First step
2. Second step
   1. Sub-step
   2. Another sub-step
3. Third step

Task lists:
- [x] Completed task
- [ ] Pending task
- [ ] Another pending
```

### Emphasis
```markdown
*Italic* or _Italic_
**Bold** or __Bold__
***Bold and Italic***
~~Strikethrough~~
`Code`
```

### Links and Images
```markdown
[Link text](https://example.com)
[Relative link](./other-file.md)
[Link with title](https://example.com "Title")

![Alt text](image.png)
![Alt text](image.png "Title")
```

### Blockquotes
```markdown
> This is a blockquote
> spanning multiple lines
>
> > Nested blockquote
```

### Mermaid Diagrams
```markdown
\`\`\`mermaid
flowchart TD
    A[Start] --> B{Is it working?}
    B -->|Yes| C[Great!]
    B -->|No| D[Debug]
    D --> B
\`\`\`

\`\`\`mermaid
erDiagram
    CUSTOMER ||--o{ ORDER : places
    ORDER ||--|{ LINE_ITEM : contains
    CUSTOMER {
        int id
        string name
    }
\`\`\`
```

## Best Practices

### Content Guidelines
1. Start with an overview/executive summary
2. Use clear, concise language
3. Include examples and code snippets
4. Add troubleshooting sections
5. Link to related documentation
6. Keep sections focused and short
7. Use visuals (diagrams, tables) when helpful
8. Update documentation as code changes

### Style Guidelines
1. Use sentence case for headers (not Title Case)
2. Be consistent with formatting
3. Use active voice when possible
4. Define acronyms on first use
5. Include table of contents for long docs
6. Add date and author to living documents
7. Use relative links for internal references

### Jira/Project Documentation
```markdown
# [ANALYSIS] Issue: [Description]

## Problem Statement
Clear description of the business problem.

## Data Sources
- Source 1: Description
- Source 2: Description

## Analysis Performed
1. Step one
2. Step two
3. Step three

## Findings
- Finding 1
- Finding 2

## Recommendation
Suggested solution or next steps.

## Attachments
- [Link to analysis file]
- [Screenshots]
```

## Bilingual Documentation

### French Templates
```markdown
# Analyse - [Sujet]

## Contexte
Description du contexte business.

## Problème
Description du problème identifié.

## Analyse
Étapes d'analyse effectuées.

## Résultats
Résultats et conclusions.

## Recommandations
Suggestions de solutions.
```

### Mixed Language Guidelines
- Use English for technical terms (SQL, API, JSON)
- Use appropriate language for business context
- Provide translations for critical terms
- Be consistent within a document

## Business Context
Good documentation enables:
- Knowledge transfer and onboarding
- Troubleshooting and support
- Decision making and planning
- Compliance and audit requirements
- Collaboration across teams
- Maintenance and evolution of systems

## Tools and Extensions
- **Markdownlint**: Linting for consistency
- **Prettier**: Auto-formatting
- **Mermaid**: Diagrams in markdown
- **Table of Contents generators**: Auto-generate TOCs
- **Markdown previewers**: VS Code, Typora, etc.
