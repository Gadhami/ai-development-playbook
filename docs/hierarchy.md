# Company → Product Hierarchy

This template supports a three-tier instruction hierarchy. Each tier adds specificity without duplicating what the tier above already provides.

## Tiers

```
Tier 1: AI Dev Playbook (this repo)
│   Universal coding standards, architecture patterns, agents, prompts.
│   Works for any .NET/Angular project out of the box.
│
└── Tier 2: Company-Level Overrides
    │   Org-specific policies: branching strategy, PR rules, security requirements,
    │   naming conventions, deployment processes.
    │
    └── Tier 3: Product-Level Overrides
            Project-specific context: domain terminology, data model notes,
            key business rules, environment details, team conventions.
```

## How It Works

All three tiers merge into a single `.github/copilot-instructions.md` per project. The instruction files (`.github/instructions/`), agents, and prompts come from Tier 1 and are shared across all projects.

### Tier 1: Universal (this repo)

The `.github/instructions/`, `.github/agents/`, and `.github/prompts/` folders. These are copied unchanged to every project.

### Tier 2: Company-Level

Add a section to your `copilot-instructions.md`:

```markdown
## Company Standards

- **Branching:** GitFlow. Feature branches from `develop`, release branches for staging.
- **PR Policy:** Minimum 2 reviewers. All CI checks must pass. Squash merge only.
- **Security:** OWASP Top 10 compliance required. Security review for auth changes.
- **Logging:** Structured JSON logging via Serilog. Correlation IDs on all requests.
- **Observability:** OpenTelemetry for traces and metrics. Grafana dashboards.
```

### Tier 3: Product-Level

Add another section to your `copilot-instructions.md`:

```markdown
## Product Context

- **Domain:** [e.g., Resource Management — scheduling, capacity planning, workforce optimization]
- **Key entities:** [e.g., Resource, Assignment, Booking, Skill, Office]
- **Multi-tenancy:** [e.g., Tenant-per-database. Tenant resolved via subdomain.]
- **Locales:** [e.g., en-US, fr-FR, pt-BR]
- **External integrations:** [e.g., Azure AD for auth, SendGrid for email, RabbitMQ for messaging]
```

## Example: Full copilot-instructions.md

```markdown
# MyCompany — OrderHub

## Project Overview
- **Project:** OrderHub
- **Stack:** .NET 10, Angular 19, PostgreSQL, EF Core
- **Architecture:** Clean Architecture

## Core Principles
[...from profile template...]

## Company Standards
- Branching: trunk-based development. Short-lived feature branches.
- PR Policy: 1 reviewer minimum. CI must pass. Squash merge.
- All APIs must be versioned. No breaking changes without deprecation.

## Product Context
- Domain: Order management and fulfillment.
- Key entities: Order, OrderItem, Customer, Warehouse, Shipment.
- Events: OrderPlaced, OrderFulfilled, ShipmentDispatched.
- External: Stripe for payments, Twilio for SMS notifications.

## Architecture
[...from profile template...]

## Working Style
[...from profile template...]
```

## Guidelines

- **Don't repeat Tier 1 in Tier 2 or 3.** The instruction files handle language-specific standards.
- **Company tier = policies.** How the team works, not how to write C#.
- **Product tier = context.** Domain knowledge that helps the AI make better decisions.
- **Keep it concise.** The AI reads this on every conversation. Every line must earn its place.
