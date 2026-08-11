# AI SOC Analyst — Complete Project Documentation

**Document Version:** 1.0  
**Project Type:** AI-powered Security Operations Center (SOC) platform  
**Development Model:** Independent project; later integrated with AI VAPT  
**Primary Backend:** Python + FastAPI  
**Frontend:** React + TypeScript  
**Database:** PostgreSQL  
**Cache/Queue:** Redis  
**Deployment:** Docker / Docker Compose  
**Primary Goal:** Automate the security-event-to-ticket lifecycle while keeping humans in control of high-risk decisions.

---

## 1. Executive Summary

The AI SOC Analyst is an organizational cybersecurity platform that continuously receives security events from authorized company systems, normalizes and analyzes those events, identifies suspicious activity, correlates related events, creates security alerts/incidents, uses AI agents to assist investigation and prioritization, and automates the ticket lifecycle from creation through remediation, verification, escalation, and closure.

The system is not intended to replace a human SOC analyst. It acts as an AI-assisted security operations layer that reduces repetitive analysis, improves triage speed, maintains evidence, and provides consistent investigation and reporting.

### Core outcome

```text
Security Event
    ↓
Collection
    ↓
Normalization
    ↓
Detection
    ↓
Correlation
    ↓
Alert
    ↓
AI Investigation
    ↓
Incident
    ↓
Ticket
    ↓
Prioritization / Assignment
    ↓
Investigation / Remediation
    ↓
Verification
    ↓
Close / Escalate
    ↓
Report
```

---

## 2. Problem Statement

Traditional SOC operations can generate large volumes of alerts. Analysts must determine:

- Which events are benign?
- Which events are suspicious?
- Which alerts belong to the same attack or incident?
- What asset and user are affected?
- How severe is the situation?
- What evidence supports the conclusion?
- Who should handle it?
- What action should be taken?
- Has remediation actually resolved the issue?

The AI SOC Analyst addresses this by combining event processing, deterministic detection rules, correlation, AI-assisted investigation, ticket management, workflow automation, and human review.

---

## 3. Scope

### In scope

1. Organization and user management.
2. Asset registration.
3. Security event ingestion.
4. Event normalization.
5. Detection rules.
6. Alert generation.
7. Event correlation.
8. AI-assisted investigation.
9. Incident creation.
10. Severity and priority calculation.
11. Ticket creation and assignment.
12. SLA tracking.
13. Evidence management.
14. Remediation tracking.
15. Verification and retesting.
16. Escalation.
17. Audit logging.
18. Reports and dashboards.
19. Notifications.
20. Controlled security-lab demonstrations.

### Out of scope for the initial academic version

- Full enterprise SIEM replacement.
- Autonomous destructive response.
- Uncontrolled Internet-wide scanning.
- Automatic execution of high-risk commands without approval.
- Production malware deployment.
- Autonomous account deletion or infrastructure destruction.

---

## 4. Target Users

| Role | Responsibility |
|---|---|
| Organization Admin | Organization configuration, users, assets |
| SOC Analyst | Investigate alerts/incidents |
| Security Manager | Review critical incidents and approvals |
| IT/Developer | Remediate assigned issues |
| Auditor | Read-only review and audit |
| AI SOC Agents | Detection assistance, correlation, investigation, summarization |
| System Administrator | Platform infrastructure |

---

## 5. Company Onboarding

A company is represented as an organization/tenant.

### Onboarding flow

```text
Create Organization
      ↓
Create Admin
      ↓
Configure Security Policies
      ↓
Register Assets
      ↓
Configure Event Sources
      ↓
Test Event Ingestion
      ↓
Enable Detection Rules
      ↓
Enable AI Analysis
      ↓
Begin Monitoring
```

### Organization data

- Organization ID
- Name
- Industry
- Environment
- Contact information
- Time zone
- Security policy
- SLA policy
- Created date
- Status

---

## 6. Asset Management

The SOC needs context about the systems producing events.

### Asset types

- Server
- Workstation
- Laptop
- Network device
- Firewall
- Application
- API
- Database
- Container
- Cloud resource

### Asset attributes

- Asset ID
- Hostname
- IP/address
- Type
- Owner
- Environment
- Criticality
- Operating system
- Tags
- Location
- Status
- Last seen

### Asset criticality

Suggested levels:

```text
CRITICAL
HIGH
MEDIUM
LOW
```

Asset criticality contributes to incident priority.

---

## 7. Event Sources

Initial academic implementation can support:

- Linux authentication/system logs
- Application logs
- Docker logs
- Test security events
- Authentication events
- Network/security-tool output
- Custom JSON event ingestion

Future sources may include:

- Windows Event Logs
- Cloud logs
- Firewall logs
- EDR
- IDS/IPS
- Identity providers

---

## 8. Event Pipeline

```text
Event Source
    ↓
Collector
    ↓
API / Message Queue
    ↓
Validation
    ↓
Normalization
    ↓
Deduplication
    ↓
Enrichment
    ↓
Detection
    ↓
Correlation
    ↓
Alert
```

### Normalized event model

```json
{
  "event_id": "EVT-10001",
  "organization_id": "ORG-001",
  "timestamp": "2026-08-11T12:00:00Z",
  "source": "linux_auth",
  "asset_id": "AST-001",
  "user": "user123",
  "source_ip": "internal-source",
  "event_type": "authentication_failure",
  "severity": "medium",
  "raw_data": {},
  "normalized_data": {}
}
```

---

## 9. Detection Engine

Detection should combine deterministic rules and AI assistance.

### Rule examples

- Repeated authentication failures.
- Suspicious privilege escalation.
- Unusual process execution.
- Unexpected service creation.
- Impossible/unusual login pattern.
- Known malicious indicator in controlled test data.
- Repeated access to sensitive resources.

### Important design principle

AI should not be the only detection mechanism.

Use:

```text
Rules + Correlation + Statistical Signals + AI
```

This makes the system more explainable and testable.

---

## 10. Correlation Engine

One event may be harmless, but several related events can indicate an incident.

Example:

```text
20 failed logins
       +
successful login
       +
privileged command
       +
sensitive file access
       ↓
Possible account compromise
```

The correlation engine should consider:

- Time window
- Asset
- User
- IP/source
- Event type
- Severity
- Repeated behavior
- Existing incidents

---

## 11. AI SOC Agent Architecture

Do not create one giant AI agent.

Recommended specialized agents:

```text
                    SOC ORCHESTRATOR
                           │
       ┌───────────┬───────┼────────┬───────────┐
       ↓           ↓       ↓        ↓           ↓
  Triage Agent  Context  Threat   Incident   Report
                Agent    Agent     Agent      Agent
       │           │       │        │           │
       └───────────┴───────┴────────┴───────────┘
                           ↓
                    Ticket / Workflow
```

### Triage Agent

Responsibilities:

- Read normalized alert.
- Estimate relevance.
- Identify likely severity.
- Request additional context.
- Recommend escalation.

### Context Agent

Responsibilities:

- Gather asset information.
- Gather related events.
- Find previous incidents.
- Build a timeline.

### Threat Analysis Agent

Responsibilities:

- Interpret suspicious behavior.
- Identify possible attack patterns.
- Map evidence to known security concepts.
- Produce confidence and reasoning.

### Incident Agent

Responsibilities:

- Build incident summary.
- Recommend next steps.
- Determine whether escalation is required.
- Prepare ticket content.

### Report Agent

Responsibilities:

- Produce analyst-friendly summaries.
- Generate management reports.
- Summarize incident timeline.

---

## 12. AI Safety Model

AI should not have unrestricted shell/system access.

Recommended architecture:

```text
AI Agent
   ↓
Tool Request
   ↓
Policy / Permission Layer
   ↓
Approved Tool
   ↓
Sandbox / Controlled Environment
   ↓
Tool Result
   ↓
AI Analysis
```

Actions should be classified:

### Read-only

Can be automated:

- Fetch logs
- Query asset metadata
- Search previous incidents
- Build timelines

### Low-risk

Can be automated under policy:

- Add ticket comment
- Add tags
- Change priority under defined rules
- Notify an analyst

### High-risk

Require approval:

- Disable account
- Isolate machine
- Delete files
- Change firewall rules
- Execute remediation commands

---

## 13. Incident Model

An incident is a confirmed or strongly suspected security event requiring investigation or action.

### Incident states

```text
NEW
↓
TRIAGED
↓
INVESTIGATING
↓
CONTAINMENT
↓
REMEDIATION
↓
VERIFICATION
↓
RESOLVED
↓
CLOSED
```

Alternative states:

- FALSE_POSITIVE
- DUPLICATE
- ESCALATED
- REOPENED

---

## 14. Ticket Lifecycle

The ticket is the operational object used to assign and track work.

```text
Ticket Created
      ↓
Classified
      ↓
Prioritized
      ↓
Assigned
      ↓
Acknowledged
      ↓
Investigating
      ↓
Remediation
      ↓
Verification
      ↓
Closed
```

### Automatic ticket creation conditions

- Critical/high confidence alert.
- Confirmed incident.
- Repeated alert crossing a threshold.
- Security policy violation.
- Manual analyst escalation.

### Automatic closure conditions

Only when policy permits and verification evidence exists.

For critical incidents, require human approval before final closure.

---

## 15. Severity and Priority

Severity describes technical impact.

```text
CRITICAL
HIGH
MEDIUM
LOW
INFO
```

Priority can combine:

```text
Severity
+
Asset Criticality
+
Confidence
+
Business Impact
+
Exposure
```

Example:

```text
Severity = HIGH
Asset Criticality = CRITICAL
Confidence = HIGH

→ Priority = CRITICAL
```

---

## 16. Assignment

Assignment can be rule-based.

Example:

```text
SOC alert
    ↓
Type = Authentication
    ↓
Assign → SOC Analyst Team
```

Or:

```text
Asset owner = Payments Team
    ↓
Assign remediation task → Payments Team
```

AI can recommend assignment, but organization policy remains authoritative.

---

## 17. SLA Management

Each priority can have an SLA.

Example:

| Priority | Acknowledge | Target Resolution |
|---|---:|---:|
| Critical | 15 min | 4 hours |
| High | 30 min | 8 hours |
| Medium | 4 hours | 2 business days |
| Low | 1 business day | 5 business days |

These values are configurable; they are not universal security standards.

---

## 18. Database — PostgreSQL

Use PostgreSQL for development, testing, staging, and production.

Core tables:

```text
organizations
users
roles
permissions
user_roles
assets
asset_tags
event_sources
security_events
detection_rules
alerts
incidents
incident_events
tickets
ticket_comments
ticket_assignments
tasks
evidence
ai_analyses
agent_runs
notifications
sla_policies
audit_logs
reports
```

Use JSONB for variable AI/tool output while keeping core relationships relational.

---

## 19. Redis and Background Workers

Use Redis for:

- Job queue support
- Caching
- Temporary processing state
- Rate limiting
- Worker coordination

Workers handle:

- Event processing
- AI analysis
- Correlation
- Report generation
- Notifications
- Scheduled tasks

---

## 20. API Design

Suggested endpoints:

```text
/api/v1/auth
/api/v1/organizations
/api/v1/users
/api/v1/assets
/api/v1/events
/api/v1/alerts
/api/v1/incidents
/api/v1/tickets
/api/v1/tasks
/api/v1/evidence
/api/v1/agents
/api/v1/reports
/api/v1/audit
```

Use versioned APIs from the beginning.

---

## 21. Frontend

Recommended:

- React
- TypeScript
- Vite
- Tailwind CSS
- Charting library

Dashboard should show:

- Active incidents
- Critical alerts
- Open tickets
- SLA breaches
- Asset health
- Event volume
- AI analysis status
- Recent activity

---

## 22. Security

Required:

- Strong authentication
- RBAC
- Tenant isolation
- Input validation
- Secure secrets
- HTTPS
- Audit logging
- API rate limiting
- Least privilege
- Database access controls
- Approval workflows for dangerous actions

---

## 23. Deployment

Development:

```text
Docker Compose
├── frontend
├── soc-api
├── soc-worker
├── postgres
├── redis
└── reverse-proxy
```

Production:

```text
HTTPS
  ↓
Reverse Proxy / Load Balancer
  ↓
Frontend
  ↓
API
  ↓
Workers
  ↓
PostgreSQL + Redis
```

SOC ingestion components should be isolated from public-facing components.

---

## 24. Testing Strategy

### Unit tests

- Parsers
- Detection rules
- Severity calculation
- Ticket transitions
- Permission checks

### Integration tests

- API + PostgreSQL
- API + Redis
- Event ingestion pipeline
- AI tool interfaces

### Security tests

- Authentication
- Authorization
- Tenant isolation
- Injection resistance
- API abuse
- Secret leakage

### AI tests

- Prompt injection resistance
- Hallucination checks
- Evidence grounding
- Tool permission checks
- Reproducibility
- False positive/negative evaluation

---

## 25. Metrics

Measure:

- Mean Time to Detect (MTTD)
- Mean Time to Acknowledge (MTTA)
- Mean Time to Respond (MTTR)
- Alert-to-ticket conversion
- False positive rate
- AI recommendation acceptance
- SLA compliance
- Ticket closure time
- Detection accuracy

---

## 26. Success Criteria

The academic MVP is successful when it can:

1. Register an organization.
2. Register assets.
3. Receive controlled security events.
4. Normalize events.
5. Detect suspicious behavior.
6. Correlate multiple events.
7. Ask AI agents to analyze evidence.
8. Create an incident.
9. Automatically create a ticket.
10. Assign the ticket.
11. Track remediation.
12. Verify resolution.
13. Close the ticket.
14. Record a complete audit trail.
15. Display the workflow on a dashboard.

---
