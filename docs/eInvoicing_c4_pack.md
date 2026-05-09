
# UAE E-Invoicing Compliance Gateway
## C4 Architecture Pack + Execution Plan

---

## C4 Level 1 — System Context

```mermaid
C4Context
title UAE E-Invoicing Compliance Gateway — System Context

Person(sme, "SME / Business User", "Uploads invoices or integrates ERP, monitors compliance")
Person(accountant, "Accounting Firm", "Manages multiple clients (v2)")

System_Ext(erp, "ERP / POS / Billing System", "Invoice source")
System_Ext(buyer, "Buyer Systems", "Invoice recipients")
System_Ext(ap, "PEPPOL Access Point", "Certified e-invoice exchange")
System_Ext(fta, "UAE Federal Tax Authority", "Tax authority")

System(gateway, "Compliance Gateway", "Normalizes, validates, routes e-invoices and provides compliance dashboard")

Rel(sme, gateway, "Uses", "Web UI")
Rel(accountant, gateway, "Manages clients", "Web UI")
Rel(erp, gateway, "Submits invoices", "API / File export")
Rel(gateway, ap, "Sends compliant invoices", "PEPPOL BIS 3.0")
Rel(ap, buyer, "Delivers invoices", "PEPPOL Network")
Rel(gateway, fta, "Provides reporting-ready data", "Regulated channels")
```

---

## C4 Level 2 — Container Diagram

```mermaid
C4Container
title Compliance Gateway — Containers

Person(sme, "SME User")
System_Ext(erp, "ERP / POS")
System_Ext(ap, "PEPPOL Access Point")

System_Boundary(gw, "Compliance Gateway") {
  Container(web, "Web Dashboard", "React / Next.js", "Compliance monitoring & invoice tracking")
  Container(api, "API Gateway", "Node.js (NestJS)", "Auth, rate limiting, routing")
  Container(ingest, "Invoice Ingestion", "Node.js", "Accepts invoices, idempotency")
  Container(normalize, "Normalization Service", "Node.js", "ERP → canonical model")
  Container(validate, "Validation Service", "Node.js", "Schema + UAE VAT rules")
  Container(ai, "AI Assist Service", "Node.js + LLM", "Error explanation, suggestions, risk scoring")
  Container(route, "Routing Service", "Node.js", "UBL XML build & dispatch")
  Container(queue, "Message Broker", "RabbitMQ / SQS", "Async processing")
  Container(audit, "Audit Store", "Postgres + Object Storage", "Immutable compliance evidence")
}

Rel(sme, web, "Uses")
Rel(web, api, "Calls")
Rel(erp, api, "Submits invoices")
Rel(api, ingest, "Routes")
Rel(ingest, queue, "Enqueues")
Rel(queue, normalize, "Consumes")
Rel(normalize, validate, "Validates")
Rel(validate, ai, "Explains errors")
Rel(validate, route, "On success")
Rel(route, ap, "Dispatches invoices")
Rel(route, audit, "Stores results")
Rel(validate, audit, "Stores validation evidence")
```

---

## C4 Level 3 — Component Diagram (Validation & Dispatch)

```mermaid
C4Component
title Validation & Dispatch — Components

Container_Boundary(validateSvc, "Validation Service") {
  Component(schema, "Schema Validator", "PEPPOL / UBL rules")
  Component(rules, "Business Rules Engine", "UAE VAT logic")
  Component(errors, "Error Catalog", "Human-readable mappings")
  Component(writer, "Result Writer", "Persistence adapter")
}

Container_Boundary(routeSvc, "Routing Service") {
  Component(ubl, "UBL Builder", "XML generator")
  Component(client, "Access Point Client", "PEPPOL adapter")
  Component(retry, "Retry & DLQ Handler", "Resilience policies")
}

Rel(schema, rules, "Passes validated invoice")
Rel(rules, errors, "Maps errors")
Rel(rules, writer, "Writes results")
Rel(ubl, client, "Sends XML")
Rel(client, retry, "Retries on failure")
```

---

## Execution Plan

### Phase 0 — Foundation (Start Here)
- Confirm UAE B2B invoice scenarios
- Define canonical invoice schema (v1)
- Select Access Point strategy (mock vs partner)
- Lock tech stack choices
- Create MVP backlog (issue-ready)

### Phase 1 — MVP Build
- Invoice ingestion API + idempotency
- Normalization & validation engines
- UBL XML generation
- Dispatch + retry + DLQ
- Compliance dashboard
- Immutable audit trail

### Phase 2 — AI Enhancements
- Error explanation (human language)
- Auto-fix suggestions
- Risk scoring
- Optional document → invoice extraction

### Phase 3 — Commercial Readiness
- Pricing & plans
- Landing page + demo
- Pilot accounting firm onboarding

### Later Versions
- v2: 🥈 Accountant Dashboard (multi-tenant, white-label)
- v3: 🥉 Plug & Play ERP Connectors

---
