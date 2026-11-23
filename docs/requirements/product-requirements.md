# NorthStar AI Ops — Product Requirements

- **Document:** Product Requirements
- **Version:** 1.0
- **Status:** Initial Draft
- **Date:** 2026-11-23

## 1. Product Overview

NorthStar AI Ops is a multi-tenant, AI-powered production incident intelligence platform.

It helps engineering and platform teams investigate production incidents by collecting and analysing operational evidence, including:

- Application logs.
- Application metrics.
- Distributed traces.
- Deployment history.
- Technical documentation and runbooks.
- Previous incident reports.

NorthStar uses AI-assisted investigation to identify possible causes, explain supporting evidence and recommend corrective actions.

The platform must provide evidence-based results and must not present unsupported AI-generated assumptions as confirmed facts.

## 2. Business Problem

When production systems fail, engineers often need to investigate information spread across multiple monitoring systems, databases, deployment tools and technical documents.

This makes incident investigation time-consuming and difficult.

NorthStar aims to bring relevant operational evidence together and help engineers investigate incidents more efficiently.

## 3. Target Users

### 3.1 Platform Engineers

Investigate infrastructure, deployment and platform-related failures.

### 3.2 Backend Engineers

Investigate application errors, API failures, database problems and distributed system issues.

### 3.3 Site Reliability Engineers (SREs)

Analyse service reliability, operational incidents and system behaviour.

### 3.4 Engineering Managers

Review incident progress, investigation findings and incident reports.

### 3.5 Organisation Administrators

Manage their organisation's users, memberships, roles and access permissions.

## 4. Functional Requirements

### FR-01: Organisation Management

The platform must support multiple independent customer organisations (tenants).

Each organisation must have its own users, configuration and authorised operational data.

### FR-02: Identity and Access Management

The platform must support user authentication, organisation membership, roles and permissions.

A user may belong to multiple organisations with different permissions in each organisation.

### FR-03: Incident Management

Users must be able to:

- Create incidents.
- View authorised incidents.
- Update incident information.
- Track investigation status.
- View investigation results.
- Access incident history.

### FR-04: Operational Telemetry

The platform must collect or integrate with operational evidence from external customer systems, including logs, metrics and distributed traces.

### FR-05: Knowledge Management

Users must be able to upload and manage authorised technical documents, runbooks and previous incident reports.

The platform must support searching relevant documentation.

### FR-06: AI-Assisted Investigation

Users must be able to request an investigation of a production incident.

The platform must gather authorised evidence and use AI to produce:

- A summary of the incident.
- Possible root causes.
- Supporting evidence.
- Confidence or uncertainty information.
- Recommended investigation steps.
- Relevant document and evidence references.

### FR-07: Investigation Workflow

The platform must support multi-step investigations involving different services and tools.

Investigation progress and results must be tracked.

Failed or interrupted investigations must have a defined recovery strategy.

### FR-08: Human Approval

Actions classified as requiring approval must not execute until an authorised human approves them.

The initial platform will provide investigation and recommendations rather than autonomously modifying customer production systems.

### FR-09: Notifications

The platform must notify authorised users about relevant incident and investigation events.

### FR-10: Audit History

The platform must record important security-sensitive and administrative activities, including privileged access and approval decisions.

## 5. Non-Functional Requirements

### NFR-01: Tenant Isolation

Customer organisations must not access each other's data without explicitly authorised access.

This requirement applies across APIs, databases, caches, messaging, document storage and AI workflows.

### NFR-02: Security

The platform must implement authentication, authorization, secure service communication, secret management and appropriate data protection.

### NFR-03: Reliability

The platform must handle service failures, communication failures and interrupted background processing without silently losing important work.

### NFR-04: Scalability

The architecture must support independently scaling services according to workload.

It must be designed to support increasing numbers of organisations, users, incidents and telemetry records.

### NFR-05: Observability

The platform must provide structured logs, metrics and distributed traces to help engineers monitor and troubleshoot the system.

### NFR-06: Performance

APIs, database queries, event processing and AI workflows must be measured and optimised.

Performance targets will be defined through workload analysis and testing.

### NFR-07: Maintainability

Services must have clear responsibilities, documented APIs and independently manageable implementations.

### NFR-08: Testability

The platform must include unit, integration, security, contract and end-to-end testing where appropriate.

### NFR-09: AI Reliability and Safety

AI-generated findings must be grounded in authorised evidence.

The platform must communicate uncertainty, validate structured outputs and prevent unauthorised tool execution.

### NFR-10: Auditability

Important user actions, privileged operations and approval decisions must be traceable.

## 6. Initial Scope

The initial project will focus on:

- Multi-tenant SaaS architecture.
- Independently deployable backend microservices.
- Incident management.
- Operational telemetry ingestion and integration.
- Technical document ingestion and retrieval.
- AI-assisted incident investigation.
- Controlled tool execution and human approval.
- Event-driven communication between services.
- Monitoring, testing and operational reliability.

## 7. Out of Scope for the Initial Release

The initial release will not:

- Automatically modify customer production infrastructure.
- Execute destructive remediation without human approval.
- Claim that simulated telemetry represents real customer production data.
- Implement every possible monitoring or cloud-provider integration.
- Guarantee that AI identifies the correct root cause for every incident.

## 8. Important Product Constraints

- The platform must be multi-tenant from day one.
- Customer data isolation is a security requirement.
- NorthStar's own microservices must have clear ownership and responsibilities.
- External customer applications are systems NorthStar investigates, not services NorthStar builds.
- AI must operate within application-enforced security and authorization boundaries.
- Architectural decisions must be documented and justified.

## 9. Requirements Status

This document establishes the initial product requirements.

Detailed service boundaries, API contracts, event definitions, database designs, security controls and measurable performance targets will be refined during system design.

Requirements may evolve as technical constraints and implementation details become clearer.
