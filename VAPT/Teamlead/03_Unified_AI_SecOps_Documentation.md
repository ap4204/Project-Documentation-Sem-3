# UNIFIED AI SECOPS — AI SOC + AI VAPT INTEGRATION DOCUMENTATION

**Document Version:** 1.0  
**Purpose:** Define how independently developed AI SOC and AI VAPT systems will later operate as one security operations platform.

---

## 1. Executive Summary

The final product combines:

- AI SOC Analyst
- AI VAPT
- Common identity and organization management
- Asset inventory
- Ticket/case management
- AI orchestration
- Reporting
- Audit and compliance
- Unified dashboard

The two teams remain independently responsible for their core products during development. Integration occurs after each product reaches a stable MVP.

```text
                 UNIFIED AI SECOPS
                       │
          ┌────────────┴────────────┐
          ↓                         ↓
       AI SOC                    AI VAPT
          │                         │
     Security Events            Findings
     Alerts                     Vulnerabilities
     Incidents                  Scans
          │                         │
          └────────────┬────────────┘
                       ↓
                Common Case Layer
                       ↓
                    Tickets
                       ↓
                  Task Workflow
                       ↓
                 Remediation
                       ↓
                  Verification
                       ↓
                    Closure
```

---

## 2. Integration Philosophy

Do not merge the codebases too early.

### Phase A

```text
AI SOC Team
    ↓
Independent SOC platform

AI VAPT Team
    ↓
Independent VAPT platform
```

### Phase B

Agree on:

- API contracts
- Shared entity definitions
- Authentication
- Asset identifiers
- Ticket schema
- Severity model
- Status model
- Event format

### Phase C

Integrate:

```text
SOC API ───────┐
               ├── Integration Layer
VAPT API ──────┘
```

### Phase D

Build unified dashboard.

---

## 3. Unified Company Model

The organization is the top-level tenant.

```text
Organization
│
├── Users
├── Teams
├── Assets
├── SOC
│   ├── Events
│   ├── Alerts
│   └── Incidents
│
├── VAPT
│   ├── Scopes
│   ├── Scans
│   ├── Findings
│   └── Vulnerabilities
│
├── Tickets
├── Tasks
├── Reports
└── Audit Logs
```

---

## 4. Shared Asset Identity

The most important integration object is the asset.

Example:

```text
Asset:
AST-001

Name:
Application Server 01

IP/Identifier:
internal identifier

Owner:
IT Team

Criticality:
CRITICAL
```

SOC and VAPT must reference the same `asset_id`.

This enables cross-correlation.

---

## 5. Shared Security Case Model

A common case should support:

```text
source
source_id
organization_id
asset_id
title
description
severity
priority
confidence
status
owner
assignee
evidence
created_at
updated_at
```

Source examples:

```text
SOC
VAPT
MANUAL
SYSTEM
```

---

## 6. Common Ticket Lifecycle

```text
OPEN
 ↓
TRIAGED
 ↓
ASSIGNED
 ↓
ACKNOWLEDGED
 ↓
INVESTIGATING
 ↓
REMEDIATION
 ↓
VERIFICATION
 ↓
RESOLVED
 ↓
CLOSED
```

Special states:

```text
ESCALATED
FALSE_POSITIVE
DUPLICATE
REOPENED
ACCEPTED_RISK
```

---

## 7. Cross-Correlation

This is the most important advanced feature of the unified system.

### Example

VAPT discovers:

```text
AST-001
Critical web vulnerability
```

Later SOC detects:

```text
AST-001
Suspicious web request
```

The integration engine checks:

```text
Same asset?
Related time?
Related service?
Related vulnerability?
Evidence consistent?
```

If appropriate:

```text
VAPT Finding
      +
SOC Alert
      ↓
Correlation
      ↓
Potential exploitation incident
      ↓
High-priority case
```

The AI can summarize the relationship, but the correlation logic should remain auditable.

---

## 8. Unified Orchestrator

The orchestrator coordinates agents but does not bypass permissions.

```text
                    ORCHESTRATOR
                         │
        ┌────────────────┼────────────────┐
        ↓                ↓                ↓
      SOC              VAPT            Ticket
      Agents           Agents          Agents
        │                │                │
        └────────────────┼────────────────┘
                         ↓
                  Policy Engine
                         ↓
                  Approved Action
```

---

## 9. Common AI Agent Framework

Recommended shared concepts:

### Agent

```text
agent_id
name
type
version
status
permissions
```

### Agent Run

```text
run_id
agent_id
input_reference
output
confidence
tools_used
start_time
end_time
status
```

### Tool

```text
tool_id
name
type
permission_level
allowed_environment
```

This allows both teams to develop agents independently while using the same orchestration model.

---

## 10. Human-in-the-Loop

The unified platform should distinguish between:

### Autonomous

- Event normalization
- Deduplication
- Ticket drafting
- Summaries
- Report generation
- Low-risk tagging

### Recommended

- Severity adjustment
- Assignment
- Investigation direction
- Remediation suggestion

### Approval required

- High-impact remediation
- Isolation
- Account disablement
- Firewall changes
- Intrusive VAPT actions
- Final closure of critical incidents

---

## 11. Unified Dashboard

Main dashboard:

```text
AI SECOPS
──────────────────────────────────
Critical Incidents       4
High Vulnerabilities    12
Open Tickets             27
SLA Breaches              2
Assets                  146
Active Scans              3
──────────────────────────────────

SOC                         VAPT
Alerts: 123                 Findings: 42
Incidents: 8                Critical: 4
Critical: 2                 High: 11

RECENT SECURITY ACTIVITY
...
```

---

## 12. Dashboard Modules

### Overview

- Security posture
- Incidents
- Vulnerabilities
- Tickets
- SLA

### SOC

- Live events
- Alerts
- Incidents
- Investigations
- AI analysis

### VAPT

- Assets
- Scopes
- Scans
- Findings
- Vulnerabilities
- Retests

### Tickets

- Open
- Assigned
- In progress
- Waiting verification
- Closed

### Reports

- SOC reports
- VAPT reports
- Combined risk report
- Management summary

### Administration

- Users
- Roles
- Policies
- Integrations
- Audit logs

---

## 13. Unified Database

Use PostgreSQL across the entire system.

### Shared tables

```text
organizations
users
roles
permissions
assets
teams
tickets
tasks
audit_logs
notifications
reports
```

### SOC tables

```text
event_sources
security_events
detection_rules
alerts
incidents
incident_events
```

### VAPT tables

```text
vapt_scopes
scan_profiles
scan_jobs
scan_targets
tool_runs
raw_scan_results
findings
vulnerabilities
retests
```

### AI tables

```text
agents
agent_tools
agent_runs
ai_analyses
```

Use foreign keys and consistent UUID/ID strategy.

---

## 14. API Integration

Each module can initially expose its own APIs:

```text
SOC:
 /api/v1/soc/events
 /api/v1/soc/alerts
 /api/v1/soc/incidents

VAPT:
 /api/v1/vapt/scans
 /api/v1/vapt/findings
 /api/v1/vapt/vulnerabilities
```

Common:

```text
/api/v1/assets
/api/v1/tickets
/api/v1/tasks
/api/v1/reports
/api/v1/users
```

Eventually a unified API gateway can expose the whole platform.

---

## 15. Event-Driven Integration

For asynchronous integration:

```text
SOC
 ↓
Security Event / Incident Event
 ↓
Message Bus / Queue
 ↓
Integration Service
 ↓
VAPT / Ticket / AI
```

Likewise:

```text
VAPT
 ↓
New Vulnerability
 ↓
Integration Event
 ↓
SOC Correlation
```

Redis can support the initial implementation. A larger deployment may later introduce a dedicated streaming platform.

---

## 16. Example End-to-End Workflow

### Step 1 — VAPT

```text
Authorized asset
     ↓
Scan
     ↓
Critical vulnerability
```

### Step 2 — Ticket

```text
Vulnerability
     ↓
Ticket VAPT-1001
     ↓
Assigned to developer
```

### Step 3 — SOC

Later:

```text
Suspicious request
     ↓
SOC detection
     ↓
Alert SOC-204
```

### Step 4 — Correlation

```text
SOC-204
   +
VAPT-1001
   +
Same asset
   ↓
AI correlation
```

### Step 5 — Incident

```text
Potential exploitation
       ↓
Critical incident
```

### Step 6 — Response

```text
Human approval
       ↓
Containment/remediation
       ↓
Vulnerability fixed
       ↓
SOC activity stops
```

### Step 7 — Verification

```text
VAPT retest
       ↓
Vulnerability no longer detected
       ↓
SOC confirms no ongoing suspicious activity
```

### Step 8 — Closure

```text
Incident resolved
       ↓
Tickets resolved
       ↓
Audit trail completed
       ↓
Report generated
```

---

## 17. Unified Technology Stack

| Layer | Technology |
|---|---|
| Frontend | React + TypeScript + Vite |
| UI | Tailwind CSS |
| Backend | Python + FastAPI |
| Database | PostgreSQL |
| Queue/Cache | Redis |
| Workers | Python background workers |
| AI | LLM API + custom agent architecture |
| Scripting | Python + Bash |
| Containers | Docker |
| Reverse Proxy | Nginx |
| Version Control | Git + GitHub |
| CI/CD | GitHub Actions |
| Deployment | Dockerized cloud VM |
| Monitoring | Prometheus/Grafana in later phase |

---

## 18. Repository Strategy

Recommended organization:

```text
ai-secops/
│
├── frontend/
│
├── services/
│   ├── soc/
│   ├── vapt/
│   ├── ticketing/
│   ├── orchestration/
│   └── identity/
│
├── agents/
│   ├── soc/
│   ├── vapt/
│   └── shared/
│
├── shared/
│   ├── models/
│   ├── schemas/
│   └── utilities/
│
├── infrastructure/
│   ├── docker/
│   ├── nginx/
│   └── database/
│
├── tests/
│
├── docs/
│
└── docker-compose.yml
```

During the independent development phase, VAPT and SOC can work in separate directories/repositories and migrate into this structure during integration.

---

## 19. Environment Strategy

Use the same PostgreSQL technology in every environment.

```text
Development → PostgreSQL
Testing     → PostgreSQL
Staging     → PostgreSQL
Production  → PostgreSQL
```

But use separate databases/instances and credentials.

Example:

```text
secops_dev
secops_test
secops_staging
secops_prod
```

Never use production data for development/testing.

---

## 20. Deployment Architecture

### Academic / MVP

```text
Internet
   ↓
Frontend Hosting
   ↓
HTTPS API
   ↓
Cloud VM
   ↓
Docker Compose
 ┌───────────────┐
 │ API services  │
 │ Workers       │
 │ PostgreSQL    │
 │ Redis         │
 │ Nginx         │
 └───────────────┘
```

### More mature deployment

```text
                 Internet
                    ↓
              Load Balancer
                    ↓
              API Gateway
                    ↓
       ┌────────────┼────────────┐
       ↓            ↓            ↓
      SOC          VAPT       Ticketing
       │            │            │
       └────────────┼────────────┘
                    ↓
             Worker / Queue
                    ↓
          PostgreSQL / Storage
```

---

## 21. Security Architecture

The platform itself must be treated as security-critical.

Required:

- HTTPS
- RBAC
- Tenant isolation
- MFA in mature deployments
- Secure secrets
- Audit logging
- Least privilege
- Database encryption where appropriate
- Network segmentation
- API rate limiting
- Input validation
- Agent permission controls
- Tool allowlists
- Human approval for high-risk actions

---

## 22. Project Development Plan

### Phase 0 — Documentation

- Company workflow
- Requirements
- Architecture
- Roles
- Database model
- API contracts
- AI-agent model
- Security model

### Phase 1 — Foundation

- GitHub
- Docker
- PostgreSQL
- Redis
- FastAPI
- React
- Authentication

### Phase 2 — AI VAPT

- Asset management
- Scope
- Scan engine
- Tool adapters
- Findings
- AI analysis
- Tickets
- Retest

### Phase 3 — AI SOC

- Event ingestion
- Normalization
- Detection
- Correlation
- AI investigation
- Incidents
- Tickets

### Phase 4 — Independent Testing

Both products must independently meet MVP criteria.

### Phase 5 — Integration

- Shared assets
- Shared users
- Shared tickets
- Shared APIs
- Shared AI orchestration

### Phase 6 — Cross-Correlation

```text
VAPT vulnerability
        ↕
SOC incident
        ↕
Asset
        ↓
AI correlation
```

### Phase 7 — Unified Dashboard

### Phase 8 — End-to-End Testing

### Phase 9 — Deployment

---

## 23. Final Product Definition

The final project should be described as:

> **An AI-powered Security Operations and Vulnerability Management platform that combines continuous security monitoring, AI-assisted incident analysis, authorized vulnerability assessment, automated ticket management, remediation tracking, verification, and cross-correlation between vulnerabilities and security incidents.**

The core differentiator is not simply "AI."

The differentiator is:

```text
DETECT
  +
ASSESS
  +
UNDERSTAND
  +
TICKET
  +
ASSIGN
  +
REMEDIATE
  +
VERIFY
  +
CLOSE
```

with AI assisting the security lifecycle while human authorization remains in control of high-impact actions.
