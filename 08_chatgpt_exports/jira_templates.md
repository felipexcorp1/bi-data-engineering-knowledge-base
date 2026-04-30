# Jira Templates for BI & Data Engineering

## Standard Issue Templates

### 1. Analysis Task
**Type**: Task
**Summary**: [ANALYSIS] Analyze data requirements for [Report/Feature Name]

**Description**:
```markdown
h2. Business Context
* Stakeholder: [Name/Department]
* Business Problem: [Description]
* Expected Outcome: [Description]

h2. Data Requirements
* Data Sources: [List sources]
* Key Metrics: [List metrics needed]
* Dimensions: [List dimensions needed]
* Time Period: [Historical/Real-time/Specific range]

h2. Technical Analysis
* Current Data Availability: [Yes/No/Partial]
* Data Quality Assessment: [Status]
* Required Transformations: [List]
* Estimated Complexity: [Low/Medium/High]

h2. Deliverables
* [ ] Data mapping document
* [ ] SQL queries for data extraction
* [ ] Data model recommendation
* [ ] Gap analysis if data unavailable

h2. Acceptance Criteria
* Data requirements documented
* Technical feasibility confirmed
* Stakeholder sign-off received
```

### 2. Development Task
**Type**: Task
**Summary**: [DEV] Implement [Feature/Report Name]

**Description**:
```markdown
h2. Overview
Implement [feature/report] based on analysis in [JIRA-XXX]

h2. Technical Approach
* Database: [Snowflake/SQL Server/etc]
* Tool: [Power BI/Tableau/etc]
* Schema Changes: [Yes/No]
* New Tables: [List if any]

h2. Implementation Steps
# [ ] Create/update database objects
# [ ] Implement data transformations
# [ ] Build report visualizations
# [ ] Add measures/calculations
# [ ] Test with sample data

h2. Testing Checklist
* [ ] Unit tests pass
* [ ] Data validation completed
* [ ] Performance meets requirements
* [ ] Edge cases handled

h2. Acceptance Criteria
* Feature works as specified
* Data accuracy validated
* Performance within SLA
* Code reviewed and approved
```

### 3. Testing Task
**Type**: Bug/Test
**Summary**: [TEST] Test [Feature/Report Name]

**Description**:
```markdown
h2. Test Scope
Testing for [Feature/Report Name] implemented in [JIRA-XXX]

h2. Test Cases
| Test Case | Expected Result | Actual Result | Status |
|-----------|----------------|---------------|--------|
| [Test 1] | [Expected] | [Actual] | [Pass/Fail] |
| [Test 2] | [Expected] | [Actual] | [Pass/Fail] |

h2. Data Validation
* [ ] Row counts match source
* [ ] Key metrics validated
* [ ] Historical data accurate
* [ ] Null values handled correctly

h2. Performance Testing
* Load Time: [Measured]
* Query Performance: [Measured]
* Memory Usage: [Measured]

h2. Defects Found
* [Defect 1]: [Description]
* [Defect 2]: [Description]

h2. Sign-off
* Tested by: [Name]
* Date: [Date]
* Status: [Ready for UAT/Needs Fixes]
```

### 4. Documentation Task
**Type**: Task
**Summary**: [DOC] Document [Feature/Report Name]

**Description**:
```markdown
h2. Documentation Requirements
Create documentation for [Feature/Report Name]

h2. Documentation Checklist
* [ ] Technical documentation
* [ ] User guide/README
* [ ] Data dictionary
* [ ] Troubleshooting guide
* [ ] Change log

h2. Technical Documentation
* Architecture overview
* Data flow diagram
* Key formulas/measures
* Dependencies

h2. User Documentation
* How to access
* How to use
* Common use cases
* Known limitations

h2. Review
* Reviewed by: [Name]
* Approved by: [Name]
* Publication date: [Date]
```

## Bug Report Template

**Type**: Bug
**Summary**: [BUG] [Component Name] - [Brief Description]

**Description**:
```markdown
h2. Environment
* Environment: [Dev/Stage/Prod]
* Report/Feature: [Name]
* Last Changed: [Date/Version]

h2. Bug Description
* Observed Behavior: [What actually happens]
* Expected Behavior: [What should happen]
* Reproduction Steps:
# Step 1
# Step 2
# Step 3

h2. Impact
* Users Affected: [Number/Group]
* Business Impact: [High/Medium/Low]
* Workaround Available: [Yes/No]

h2. Screenshots/Logs
[Attach relevant screenshots or error logs]

h2. Root Cause Analysis
* Initial Assessment: [Description]
* Investigation Notes: [Notes]

h2. Resolution
* Fix Implemented: [Description]
* Testing Performed: [Description]
* Deployment Plan: [Date/Environment]
```

## Enhancement Request Template

**Type**: Story
**Summary**: [ENH] [Feature Name] - [Brief Description]

**Description**:
```markdown
h2. Business Value
* Problem Statement: [Current limitation]
* Proposed Solution: [Enhancement description]
* Expected Benefits: [Quantifiable if possible]
* Priority: [High/Medium/Low]

h2. Requirements
* Functional Requirements:
** [Requirement 1]
** [Requirement 2]

* Non-Functional Requirements:
** Performance: [Specific requirements]
** Security: [Any security considerations]
** Compatibility: [Systems to integrate with]

h2. Technical Considerations
* Effort Estimate: [Story points or hours]
* Dependencies: [Other tasks/systems]
* Risks: [Potential risks]

h2. Acceptance Criteria
* [ ] Requirement 1 met
* [ ] Requirement 2 met
* [ ] Performance requirements met
* [ ] User acceptance testing passed
```

## Sprint Planning Template

**Summary**: Sprint Planning - [Sprint Name/Number]

**Description**:
```markdown
h2. Sprint Goals
* Goal 1: [Description]
* Goal 2: [Description]

h2. Capacity
* Team Members: [List]
* Total Capacity: [Hours/Story Points]
* Reserved for Bugs: [Percentage]

h2. Backlog Items
| JIRA | Summary | Type | Estimate | Priority |
|------|----------|------|----------|----------|
| [ID] | [Summary] | [Type] | [Est] | [Prio] |
| [ID] | [Summary] | [Type] | [Est] | [Prio] |

h2. Risks & Dependencies
* Risk 1: [Description]
* Dependency 1: [Description]

h2. Definition of Done
* Code reviewed
* Tests passing
* Documentation updated
* Deployed to [Environment]
```

## Code Review Template

**Type**: Sub-task
**Summary**: [CR] Code Review - [Feature/PR Name]

**Description**:
```markdown
h2. Review Checklist
* [ ] Code follows style guidelines
* [ ] Logic is correct and efficient
* [ ] Error handling implemented
* [ ] No hardcoded values
* [ ] Comments added where needed
* [ ] Tests included
* [ ] Documentation updated

h2. Specific Comments
* [File/Line]: [Comment]
* [File/Line]: [Comment]

h2. Performance Considerations
* Query performance: [Comments]
* Index usage: [Comments]
* Memory usage: [Comments]

h2. Security Review
* SQL injection prevention: [Yes/No]
* Data masking: [Yes/No]
* Access controls: [Yes/No]

h2. Review Decision
* [ ] Approved
* [ ] Approved with comments
* [ ] Needs changes
* [ ] Rejected

h2. Reviewer: [Name]
h2. Date: [Date]
```

## Data Issue Template

**Type**: Bug
**Summary**: [DATA] Data Quality Issue - [Source/Table Name]

**Description**:
```markdown
h2. Issue Details
* Data Source: [System/Table]
* Issue Type: [Missing/Duplicate/Incorrect/Inconsistent]
* Discovery Date: [Date]
* Discovered By: [Name/Process]

h2. Impact Assessment
* Records Affected: [Count]
* Time Period: [Date range]
* Business Impact: [Description]
* Downstream Systems: [List]

h2. Root Cause Analysis
* Root Cause: [Description]
* Contributing Factors: [List]

h2. Resolution Plan
* Immediate Action: [Description]
* Long-term Fix: [Description]
* Prevention Measures: [Description]

h2. Communication
* Stakeholders Notified: [Yes/No]
* User Communication: [Sent/Not needed]

h2. Validation
* [ ] Data corrected
* [ ] Root cause fixed
* [ ] Prevention implemented
* [ ] Monitoring added
```

## Deployment Template

**Type**: Task
**Summary**: [DEPLOY] Deploy [Feature/Report] to [Environment]

**Description**:
```markdown
h2. Deployment Details
* Feature: [Name]
* Environment: [Dev/Stage/Prod]
* Scheduled Date: [Date]
* Deployment Window: [Time range]

h2. Pre-Deployment Checklist
* [ ] Code merged to main branch
* [ ] All tests passing
* [ ] Code review completed
* [ ] Documentation updated
* [ ] Stakeholders notified
* [ ] Rollback plan documented

h2. Deployment Steps
# [ ] Backup current state
# [ ] Deploy changes
# [ ] Run smoke tests
# [ ] Verify data integrity
# [ ] Monitor for errors

h2. Post-Deployment Checklist
* [ ] Smoke tests pass
* [ ] Data validation complete
* [ ] Performance acceptable
* [ ] No errors in logs
* [ ] Users notified

h2. Rollback Plan
* Trigger: [Conditions for rollback]
* Steps: [Rollback procedure]
* Estimated Time: [Duration]

h2. Sign-off
* Deployed by: [Name]
* Verified by: [Name]
* Deployment Status: [Success/Failed/Rolled back]
```
