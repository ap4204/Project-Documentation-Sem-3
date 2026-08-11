# AI VAPT — Complete Project Documentation
**Document Version:** 1.0  
**Project Type:** AI-assisted Vulnerability Assessment and Penetration Testing platform  
**Development Model:** Independent project; later integrated with AI SOC  
**Primary Goal:** Automate authorized vulnerability discovery, validation, prioritization, ticketing, remediation tracking, and retesting.

---

## 1. Executive Summary

AI VAPT is an AI-assisted security assessment platform for authorized assets. It manages the VAPT lifecycle from scope definition and asset discovery through scanning, result normalization, deduplication, validation, risk prioritization, reporting, ticket creation, remediation tracking, and retesting.

The system must operate only against assets for which the organization has explicit authorization.

```text
Authorized Scope
      ↓
Asset Discovery
      ↓
Service / Application Discovery
      ↓
Security Assessment
      ↓
Finding Collection
      ↓
Normalization
      ↓
Deduplication
      ↓
AI-Assisted Validation
      ↓
Risk Prioritization
      ↓
Vulnerability Record
      ↓
Ticket
      ↓
Remediation
      ↓
Retest
      ↓
Verified / Reopen
      ↓
Report
```

---

## 2. Problem Statement

Traditional VAPT generates large numbers of scan results that analysts must manually:

- Review
- Deduplicate
- Validate
- Prioritize
- Document
- Assign
- Track
- Retest

AI VAPT reduces repetitive work while maintaining authorization boundaries and human approval for intrusive or high-risk actions.

---

## 3. Scope

### In scope

- Organization management
- Authorized asset inventory
- Scope definition
- Scan profiles
- Controlled reconnaissance
- Vulnerability scanning
- Result ingestion
- Deduplication
- AI-assisted finding analysis
- Risk scoring
- Evidence management
- Vulnerability database
- Ticket creation
- Remediation workflow
- Retesting
- Reporting
- Audit logging
- Controlled Docker security lab

### Out of scope

- Unauthorized scanning
- Internet-wide scanning
- Credential theft
- Persistence
- Destructive exploitation
- Production malware deployment
- Autonomous high-impact exploitation

---

## 4. VAPT Roles

| Role | Responsibility |
|---|---|
| VAPT Admin | Scope, users, policies |
| VAPT Analyst | Assessment and validation |
| Security Manager | Approval for sensitive actions |
| Asset Owner | Remediation |
| Developer | Application fixes |
| AI VAPT Agents | Analysis, prioritization, reporting |

---

## 5. Authorization and Scope

Before every assessment:

```text
Organization
   ↓
Authorized Asset
   ↓
Approved Scope
   ↓
Scan Policy
   ↓
Assessment
```

Each target should have:

- Asset ID
- Scope ID
- Owner
- Authorization status
- Environment
- Approved test window
- Allowed tools
- Allowed assessment intensity
- Exclusions

If authorization is missing, the assessment must not start.

---

## 6. Asset Inventory

Asset types:

- Domain
- Web application
- API
- Server
- Container
- Database
- Network device
- Cloud resource

Asset attributes:

```text
asset_id
name
type
owner
environment
criticality
scope
authorization_status
tags
```

---

## 7. Scan Profiles

Example profiles:

### Passive / Safe

- Information collection
- Technology identification
- Configuration review
- Non-intrusive checks

### Standard

- Approved vulnerability checks
- Service enumeration
- Web security checks

### Controlled Intensive

Only for dedicated lab/test environments.

The system should require explicit approval for higher-risk profiles.

---

## 8. VAPT Pipeline

```text
Scope Validation
      ↓
Target Preparation
      ↓
Discovery
      ↓
Assessment
      ↓
Result Collection
      ↓
Parser
      ↓
Normalization
      ↓
Deduplication
      ↓
Validation
      ↓
Risk Scoring
      ↓
Human Review where required
      ↓
Vulnerability
      ↓
Ticket
```

---

## 9. Security Tool Integration

Tools should be integrated through controlled adapters.

Concept:

```text
VAPT Agent
   ↓
Tool Adapter
   ↓
Policy Check
   ↓
Approved Security Tool
   ↓
Sandbox / Lab
   ↓
Raw Output
   ↓
Parser
   ↓
Normalized Finding
```

Possible authorized lab tools include:

- Nmap
- OWASP ZAP
- Nuclei
- Nikto
- WhatWeb

Tool selection should depend on scope and authorization.

---

## 10. Result Normalization

Different tools produce different formats.

The platform should convert them to one schema:

```json
{
  "finding_id": "FND-1001",
  "asset_id": "AST-001",
  "title": "Example vulnerability",
  "severity": "HIGH",
  "confidence": 0.92,
  "evidence": {},
  "source_tool": "approved-scanner",
  "raw_reference": "..."
}
```

---

## 11. Deduplication

The same vulnerability may be reported by several tools.

Example:

```text
Tool A → Finding X
Tool B → Finding X
Tool C → Finding X
```

The system should correlate them into:

```text
One Vulnerability
+
Multiple Evidence Sources
```

Deduplication keys can use:

- Asset
- Endpoint
- Vulnerability identifier
- Technology
- Location
- Evidence similarity

---

## 12. AI VAPT Agents

Recommended architecture:

```text
                     VAPT ORCHESTRATOR
                             │
       ┌──────────┬──────────┼──────────┬──────────┐
       ↓          ↓          ↓          ↓          ↓
   Scope Agent  Recon     Finding    Risk      Report
                Agent     Agent      Agent      Agent
                           │
                      Validation
                         Agent
```

### Scope Agent

Ensures target is authorized and assessment policy permits the requested action.

### Recon Agent

Interprets approved discovery results.

### Finding Agent

Normalizes and explains findings.

### Validation Agent

Assesses whether evidence supports the finding.

For higher-risk validation, human approval should be required.

### Risk Agent

Considers:

- Technical severity
- Asset criticality
- Exposure
- Confidence
- Business impact
- Evidence quality

### Report Agent

Produces technical and management summaries.

---

## 13. Vulnerability Lifecycle

```text
DISCOVERED
    ↓
TRIAGED
    ↓
VALIDATING
    ↓
CONFIRMED
    ↓
TICKETED
    ↓
REMEDIATION
    ↓
RETEST
    ↓
FIXED → CLOSED
    │
    └── NOT FIXED → REOPENED
```

Other statuses:

- FALSE_POSITIVE
- ACCEPTED_RISK
- DUPLICATE
- MITIGATED

---

## 14. Risk Model

Use a transparent model.

Example conceptual formula:

```text
Risk =
Technical Severity
× Asset Criticality
× Exposure
× Confidence
```

Do not rely only on an LLM's opinion.

The system can incorporate established vulnerability scoring data when available, while keeping the final organizational priority configurable.

---

## 15. Ticket Creation

Example:

```text
Vulnerability Confirmed
        ↓
Ticket Created
        ↓
Priority Calculated
        ↓
Asset Owner Identified
        ↓
Task Assigned
```

Ticket should include:

- Vulnerability
- Affected asset
- Evidence
- Severity
- Priority
- Business impact
- Recommended remediation
- Due date
- Owner
- Retest requirements

---

## 16. Remediation

Example:

```text
Developer receives ticket
       ↓
Fix implemented
       ↓
Evidence submitted
       ↓
Retest scheduled
```

The ticket must not automatically become "closed" merely because the developer says it is fixed.

---

## 17. Retesting

```text
Original Finding
      ↓
Remediation
      ↓
Retest
      ↓
 ┌────┴────┐
 ↓         ↓
Fixed    Still Present
 ↓         ↓
Close    Reopen
```

This is one of the most important features of the complete workflow.

---

## 18. Database — PostgreSQL

Recommended core tables:

```text
organizations
users
roles
permissions
assets
vapt_scopes
scan_profiles
scan_jobs
scan_targets
tool_adapters
tool_runs
raw_scan_results
findings
vulnerabilities
vulnerability_evidence
vulnerability_assets
ai_analyses
risk_scores
tickets
tasks
remediation_records
retests
reports
audit_logs
```

Use JSONB for tool-specific output and AI analysis.

---

## 19. Frontend

React + TypeScript dashboard.

Main views:

- VAPT overview
- Assets
- Scope
- Scan jobs
- Scan results
- Findings
- Vulnerabilities
- Tickets
- Retests
- Reports
- AI activity
- Audit logs

---

## 20. Backend

Python + FastAPI.

Suggested APIs:

```text
/api/v1/assets
/api/v1/scopes
/api/v1/scan-profiles
/api/v1/scans
/api/v1/findings
/api/v1/vulnerabilities
/api/v1/evidence
/api/v1/retests
/api/v1/tickets
/api/v1/reports
/api/v1/agents
```

---

## 21. Workers

Long-running assessments should run outside the API process.

```text
FastAPI
   ↓
Job Queue
   ↓
VAPT Worker
   ↓
Tool Adapter
   ↓
Result Parser
   ↓
PostgreSQL
```

Redis can support queueing/caching.

---

## 22. Docker Lab

For academic demonstrations:

```text
VAPT Lab
├── vulnerable-web-app
├── test-server
├── API-test-app
├── scanner/worker
└── supporting services
```

The lab should be isolated from unrelated systems.

---

## 23. Security Controls

- Explicit authorization
- Scope enforcement
- Target allowlist
- Tool allowlist
- Rate limits
- Scan profiles
- Approval workflow
- Audit logging
- Secrets management
- Network isolation
- Non-root containers where practical

---

## 24. Testing

### Functional

- Scope validation
- Scan execution
- Result parsing
- Deduplication
- Risk scoring
- Ticket creation
- Retest
- Closure

### Security

- Unauthorized target rejection
- Scope bypass tests
- RBAC
- API authentication
- Injection testing
- Secret leakage

### AI

- Evidence grounding
- False positive handling
- Prompt injection resistance
- Tool permission enforcement
- Consistency tests

---

## 25. Success Criteria

The MVP is successful when it can:

1. Create an organization.
2. Register an authorized asset.
3. Create a VAPT scope.
4. Run a controlled assessment.
5. Collect tool output.
6. Normalize findings.
7. Deduplicate findings.
8. Use AI to explain/prioritize them.
9. Create a vulnerability.
10. Generate a ticket.
11. Assign remediation.
12. Retest the affected asset in the controlled environment.
13. Automatically resolve or reopen based on evidence.
14. Produce a report.
15. Maintain an audit trail.

---
