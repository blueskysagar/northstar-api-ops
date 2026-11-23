
# ADR-001: Multi-Tenant Architecture and Tenant Isolation

- **Status:** Accepted
- **Date:** 2026-11-22
- **Decision owners:** NorthStar Architecture

## 1. Context

NorthStar AI Ops is an enterprise incident intelligence platform designed to serve multiple independent organisations.

Each organisation uses NorthStar to investigate production incidents, analyse operational telemetry, retrieve technical documentation and manage its own users.

For example:

- Acme Bank uses NorthStar to investigate failures in its existing Payment Service.
- Delta Insurance uses NorthStar to investigate failures in its existing Customer Service.

These Payment and Customer Services belong to the organisations using NorthStar. They are not microservices that NorthStar itself is building.

Although multiple organisations use the same NorthStar platform, each organisation must only access its own authorised data.

For example, Acme Bank must never access Delta Insurance's incidents, logs, traces, documents or investigation results.

Multi-tenancy is a foundational requirement from the beginning of the project. It must not be introduced as a later architectural change.

Tenant isolation must be maintained across:

- User authentication and authorization.
- Microservices and APIs.
- PostgreSQL databases.
- Redis caches.
- Kafka messages and consumers.
- Document and object storage.
- Vector search and AI retrieval.
- Logs, traces, audit records and background jobs.

## 2. Decision

NorthStar will use a multi-tenant architecture from day one.

The initial data isolation strategy will use:

- Shared databases per microservice.
- Shared tables where appropriate.
- Tenant-scoped records.
- PostgreSQL Row-Level Security (RLS) as an additional database-level protection.

Each independently deployable microservice will own and manage its data.

A service must not directly access another service's database. Services will communicate through defined APIs and asynchronous events where appropriate.

The architecture must support a future dedicated-database deployment model for individual tenants when justified by compliance, data residency or isolation requirements.

## 3. Identity and Membership

A user will have one global identity and may belong to multiple tenant organisations.

Each organisation membership will have its own roles and permissions.

For example:

- A user may be an ORG_ADMIN in Acme Bank.
- The same user may be a VIEWER in Delta Insurance.

The user's permissions will be evaluated within the currently selected tenant.

Being an administrator in one organisation will not automatically grant administrative access to another organisation.

Platform-level support operators will be separate from customer organisation administrators. Any privileged support access must be explicitly authorized, restricted and audited.

## 4. Tenant Authorization

Every protected request must establish:

1. The authenticated user's identity.
2. The requested tenant context.
3. The user's verified membership in that tenant.
4. Permission to perform the requested operation.
5. Authorization to access the specific resource.

A tenant identifier supplied by a client must never be trusted without server-side validation.

Each microservice must enforce authorization at its own security boundary. Authorization must not depend only on the API Gateway.

Example:

A user belonging to Acme Bank requests an incident belonging to Delta Insurance.

NorthStar must reject the request, even if the user knows Delta's tenant identifier or incident identifier.

## 5. Database Isolation

Tenant-owned records must include tenant ownership information.

PostgreSQL Row-Level Security (RLS) will provide an additional database-level isolation boundary.

Application-level authorization and database-level isolation must work together.

The implementation must ensure:

- Tenant-scoped data cannot be accessed across organisations.
- RLS policies are correctly configured and enforced.
- Database connections establish tenant context safely.
- Background jobs establish tenant context explicitly.
- Database migrations and privileged maintenance access are controlled and audited.

Example:

When Acme requests its incidents, the application and database must ensure that only authorised Acme records are returned.

If an application query accidentally omits a tenant filter, correctly configured RLS should provide another layer of protection, provided the database connection uses a role that cannot bypass those policies.

## 6. Distributed System Isolation

Tenant context must be propagated securely across synchronous service calls and asynchronous workflows.

Every service processing tenant-owned information must validate the tenant context and the authorization appropriate to its operation.

Key requirements include:

- Kafka messages must carry the relevant tenant context.
- Consumers must not treat a tenant identifier in an event as authorization by itself.
- Tenant-specific Redis cache keys must include tenant scope.
- Object storage access must be protected by authorization, not merely by folder or object naming.
- Vector search must enforce tenant and document-level access restrictions.
- AI retrieval must filter authorized information before providing content to an AI model.
- Logs, traces, notifications and background jobs must not expose information between tenants.

Example:

An Acme investigation must not retrieve Delta's technical documents from the vector database, even if those documents contain information relevant to the investigation.

## 7. Alternatives Considered

### Option A: Separate database per tenant

Advantages:
- Stronger physical database separation.
- Can support specific enterprise compliance and data residency requirements.

Disadvantages:
- Higher infrastructure and operational costs.
- More complex database migrations and monitoring.
- Additional connection, backup and maintenance management.

### Option B: Separate schema per tenant

Advantages:
- Logical separation between tenant data.
- Some operational flexibility.

Disadvantages:
- Schema creation and migration become more complex.
- Managing large numbers of schemas is difficult.
- Authorization is still required.

### Option C: Shared database and tenant-scoped tables

Advantages:
- Efficient infrastructure utilisation.
- Simpler centralised operations.
- Suitable for many multi-tenant SaaS platforms.
- PostgreSQL RLS provides an additional database enforcement layer.

Disadvantages:
- Incorrect tenant context or database policies can expose customer data.
- Requires rigorous security testing and operational controls.

Selected option: Option C, with the ability to support dedicated databases for tenants whose requirements justify them.


## 8. Consequences

The selected shared-database approach reduces infrastructure duplication and simplifies centralised database operations.

However, it requires strict tenant isolation controls across the platform.

The main consequences are:

- Every service and data store must enforce the tenant isolation rules defined in this ADR.
- Automated security tests must verify that cross-tenant access is rejected across APIs, databases, caches, messaging and AI workflows.
- Operational monitoring and audit records must cover privileged access to tenant data.
- The architecture must allow dedicated database deployment for tenants with specific compliance, residency or isolation requirements.

These responsibilities are necessary to maintain the security and reliability of a shared multi-tenant platform.Consequences

The selected approach provides efficient infrastructure utilisation and centralised operations.

However, it introduces engineering responsibilities for maintaining tenant isolation throughout the platform.

The architecture must:

- Enforce tenant isolation across every service and data store.
- Support future dedicated-database deployment where required.
- Include cross-tenant access tests.
- Test tenant isolation across APIs, databases, caches, asynchronous messages, document retrieval and AI workflows.
- Include appropriate monitoring and auditing for privileged access.

## 9. Security Principle

Tenant isolation is a security invariant.

No user, service, background job or AI workflow may access tenant data without verified authorization.

This principle applies throughout the entire NorthStar platform, not only at the database level.

Any privileged platform support or maintenance access must follow explicitly defined and audited authorization controls.
