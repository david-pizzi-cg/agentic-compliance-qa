# Agentic Quality Validation and Reporting Framework

## Rationale

### Current Challenge

Organisations use automated systems to detect prohibited items in submitted files for compliance and security. To ensure the detection system is working correctly, a quality assurance process monitors its outputs daily. Currently, this QA process involves multiple manual steps: extracting daily reports from Power BI showing items flagged by the system, manually retrieving the corresponding files from Blob Storage, cross-referencing everything against the prohibited items list to verify accuracy, generating detailed validation reports, and finally publishing findings to a Teams channel for review.

This is a temporary quality check process to validate that the automated detection system is performing as expected.

### Why Automate This Workflow?

This manual process presents several critical challenges:

**Time-Intensive Operations**
- Daily manual extraction from Power BI reports consumes significant staff time
- Manual file retrieval from Blob Storage is repetitive and error-prone
- Cross-referencing files with prohibited items lists requires careful attention and is labour-intensive

**Consistency and Accuracy Concerns**
- Manual processes are susceptible to human error
- Inconsistent report formatting and potential for missed items
- Variable quality depending on operator workload and fatigue

**Scalability Limitations**
- Process doesn't scale well with increasing data volume
- Manual steps create bottlenecks that delay reporting
- Difficult to handle peak periods or urgent requests

**Operational Efficiency**
- Staff time could be better utilised for analysis rather than data collection
- Delays in report publication affect decision-making timelines
- No ability to run on-demand or outside business hours

### Expected Benefits of Automation

**Improved Efficiency**
- Reduce processing time from hours to minutes
- Enable 24/7 automated operation without human intervention
- Free up staff for higher-value analytical work

**Enhanced Accuracy**
- Eliminate human error in data extraction and cross-referencing
- Ensure consistent application of prohibited items rules
- Standardised report formatting and completeness

**Better Timeliness**
- Automatic daily execution ensures reports are always current
- Potential for real-time or near-real-time reporting
- Immediate notification to stakeholders via Teams

**Scalability and Flexibility**
- Easily handle increased data volumes without additional resources
- Simple to modify rules or add new prohibited items
- Foundation for future enhancements and integrations

---

## Overview

### Proposed Solution

To address these challenges, this document outlines the transformation of the manual workflow into an automated system using a multi-agent architecture. The solution implements specialized agents that handle distinct phases of the process: data collection, report generation, and publication.

### Proof of Concept Approach

The proof of concept (POC) uses a simplified three-agent system working in sequence:

1. **Data Collection Agent** - Retrieves report data, submitted files, and prohibited items list from Blob Storage
2. **Report Generation Agent** - Analyses and cross-references data to generate detailed findings
3. **Teams Publishing Agent** - Automatically distributes reports to designated Teams channels

To validate the agent workflow without integration complexity, the POC uses a fixed dataset stored entirely in Blob Storage. This approach allows us to prove the concept and refine the agent coordination before connecting to live data sources like PostgreSQL and Power BI.

---

## Original Manual Workflow

The following diagram illustrates the current manual process for detecting and reporting prohibited items:

```mermaid
flowchart TD
    A[Application] -->|Push Data| B[(PostgreSQL Server)]
    B -->|Pull Data| C[Power BI Report]
    C -->|Daily Manual Extraction| D[Report: Prohibited Items<br/>Detected Yesterday]
    E[Blob Storage] -->|Manual Extraction| F[Submitted Files]
    D -->|Manual Cross-Reference| G{Cross Reference with<br/>Prohibited Items List}
    F -->|Manual Cross-Reference| G
    H[Prohibited Items List] -->|Reference| G
    G -->|Generate| I[Detailed Report]
    I -->|Manual Publishing| J[Teams Channel]
    
    style A fill:#e1f5ff
    style B fill:#fff4e1
    style C fill:#e1ffe1
    style D fill:#ffe1e1
    style E fill:#fff4e1
    style F fill:#ffe1e1
    style G fill:#f5e1ff
    style H fill:#ffe1f5
    style I fill:#e1ffe1
    style J fill:#e1f5ff
    
    classDef manual fill:#ffe1e1,stroke:#ff6b6b,stroke-width:3px
    class D,F,G,I,J manual
```

### Workflow Steps

1. **Data Ingestion**: Application pushes data to PostgreSQL server
2. **Reporting**: PostgreSQL data is pulled into Power BI report
3. **Manual Extraction**: Daily manual extraction of report showing prohibited items detected the previous day
4. **File Retrieval**: Manual extraction of corresponding submitted files from Blob Storage
5. **Cross-Reference**: Manual cross-referencing of files and reports with prohibited items list to generate detailed report
6. **Publication**: Manual publishing of report to Teams channel

**Note**: Steps highlighted in red indicate manual intervention points that could be automated with custom agents.

---

## Proof of Concept: Multi-Agent Automation (Simplified)

The following diagram shows the simplified POC using custom agents with a fixed dataset:

```mermaid
flowchart TD
    A[Report Data:<br/>Prohibited items detected]
    B[Submitted Files]
    C[Prohibited Items List]
    
    A --> D[Blob Storage:<br/>Fixed Dataset]
    B --> D
    C --> D
    
    D -->|Automated Pull| E[Agent 1:<br/>Data Collection Agent]
    
    E -->|Collected Data & Files| F[Agent 2:<br/>Report Generation Agent]
    
    F -->|Generated Report| G[Agent 3:<br/>Teams Publishing Agent]
    
    G -->|Post| H[Teams Channel]
    
    style A fill:#fff4e1
    style B fill:#fff4e1
    style C fill:#ffe1f5
    style D fill:#ffd700,stroke:#ff8c00,stroke-width:3px
    style E fill:#90ee90,stroke:#2d862d,stroke-width:3px
    style F fill:#90ee90,stroke:#2d862d,stroke-width:3px
    style G fill:#90ee90,stroke:#2d862d,stroke-width:3px
    style H fill:#e1f5ff
    
    classDef agent fill:#90ee90,stroke:#2d862d,stroke-width:3px
    class E,F,G agent
```

### POC Architecture: Three-Agent System

#### **Agent 1: Data Collection Agent**
- **Purpose**: Automate data and file retrieval from Blob Storage
- **Responsibilities**:
  - Pull report data (prohibited items detected) from Blob Storage
  - Retrieve corresponding submitted files from Blob Storage
  - Retrieve prohibited items list from Blob Storage
  - Consolidate all data for processing

#### **Agent 2: Report Generation Agent**
- **Purpose**: Analyse and generate detailed reports
- **Responsibilities**:
  - Cross-reference report data with submitted files
  - Match detected items against prohibited items list
  - Generate formatted detailed report with findings and analysis

#### **Agent 3: Teams Publishing Agent**
- **Purpose**: Automate report distribution
- **Responsibilities**:
  - Format report for Teams channel presentation
  - Post report to designated Teams channel
  - Include relevant metadata and timestamps

### POC Simplifications

- **Fixed Dataset**: All data pre-loaded in Blob Storage (no live data feeds)
- **Single Source**: Blob Storage contains report data, files, and prohibited items list
- **No Integration Complexity**: Bypasses PostgreSQL, Power BI, and multiple storage systems
- **Focus on Agents**: Tests agent coordination and automation without infrastructure dependencies

### Key Benefits of Simplified POC

- **Faster Development**: No need to set up database or BI connections
- **Easier Testing**: Fixed dataset ensures consistent, reproducible results
- **Clear Validation**: Focus on agent functionality rather than data integration
- **Lower Complexity**: Simpler architecture for proof of concept phase
- **Iterative Approach**: Can add complexity after validating core agent workflow

**Note**: Agents highlighted in green represent the automated custom agents.
