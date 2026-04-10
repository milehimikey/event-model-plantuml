# Fintech Billing System - Base Event Model

This is a **base event model** (Steps 1-6) demonstrating a comprehensive billing system for a fintech platform.

## Purpose

This diagram represents the **understanding phase** of event modeling:
- **Audience:** All stakeholders (business + technical)
- **Goal:** Understand the complete business process
- **Status:** Ready for stakeholder review and discussion

## Philosophy: Base Model First

This diagram follows event modeling best practices:
- **Start Simple** - Shows only the core business flow without implementation details
- **Human First** - All processes shown as manual human actions (Finance team generates invoices, confirms payments, etc.)
- **No Policies** - Automation patterns are identified later during slicing (Step 7)
- **No Field Details** - Data structures are defined when implementing slices (Step 7)

**This is by design!** We want stakeholders to understand WHAT happens before we discuss HOW it's automated.

## Overview

The model covers the complete business processes for a billing system that handles:
- **Subscription Management** - Recurring billing cycles and plan changes
- **Line Item Ingestion** - Receiving chargeable items from external order systems
- **Manual Charges** - One-time charges added by billing administrators
- **Invoice Generation** - Manual invoice creation workflow
- **Invoice Approval & Delivery** - Finance team approval and delivery workflow
- **Payment Processing** - Customer payment submission and manual verification
- **Payment Application** - Manual matching of payments to invoices
- **Reconciliation** - Period-based reconciliation of invoices vs payments

## Business Workflows

The model shows **11 key business workflows** in left-to-right order:

1. **Subscribe to Plan** - Customer activates a subscription
2. **Change Subscription** - Customer modifies their plan
3. **Line Items from External System** - Chargeable items received from Order System
4. **Manual Charge Management** - Billing admin adds charges
5. **Invoice Generation** - Finance team generates invoices
6. **Invoice Approval** - Finance team approves invoices
7. **Invoice Delivery** - Invoice delivered to customer
8. **Payment Processing** - Customer submits payment
9. **Payment Confirmation** - Finance team confirms or rejects payment
10. **Apply Payment** - Finance team applies payment to invoice
11. **Reconciliation** - Finance team reconciles for a period
12. **Subscription Cancellation** - Customer cancels subscription

## Actors (Swim Lanes)

- **Customer** - Subscribes to plans, makes changes, submits payments
- **BillingAdmin** - Adds manual charges, reviews line items
- **FinanceTeam** - Generates/approves invoices, confirms payments, reconciles

## Aggregates (Event Lanes)

All within the "Billing" bounded context:
- **Subscription** - Subscription lifecycle
- **LineItem** - Individual chargeable items
- **Invoice** - Invoice lifecycle
- **Payment** - Payment lifecycle
- **Reconciliation** - Reconciliation sessions

## Key Patterns

### External System Integration
Shows integration with an external Order System receiving `Chargeable Item Received` events.

### Manual-First Approach
All workflows are shown as manual human-driven processes:
- Finance team manually generates invoices
- Finance team manually confirms/rejects payments
- Finance team manually applies payments to invoices
- Finance team manually completes reconciliation

### Why No Automation?
This base model focuses on **what happens**, not **how it happens**. During slicing sessions, teams will:
1. Identify which workflows should be automated (add policies)
2. Define business rules and invariants (add policy fields)
3. Specify data structures (add fields to commands/events/views)

### Read Models (Views)
Views show the query side of CQRS:
- Active Subscriptions, Cancelled Subscriptions
- Pending Charges
- Invoices to Review, Approved Invoices, Delivered Invoices, Paid Invoices
- Payments Pending Verification, Confirmed Payments, Rejected Payments
- Active Reconciliations, Reconciliation Reports

## Rendering

To generate the diagram:

```bash
plantuml -tpng examples/fintech-billing-system.puml
```

Or view the pre-generated PNG:
```bash
open examples/fintech-billing-system.png
```

## Next Steps: Slicing (Step 7)

After stakeholders understand and approve this base flow, the development team would create a **sliced model** by:

### Step 7a: Identify Automation
Add `$policy()` elements to show what should be automated:
- ✅ **Billing Cycle Policy** - Automatically generate invoices on billing dates
- ✅ **Payment Verification Policy** - Integrate with payment gateway
- ✅ **Payment Application Policy** - Automatically apply payments to invoices
- ❌ **Invoice Approval** - Keep as manual (Finance team review)

**See:** [subscription-billing-with-policies.puml](subscription-billing-with-policies.puml) for example

### Step 7b: Define Business Rules
Add `$fields` to policies showing logic:
```plantuml
$policy(Billing Cycle Policy, $fields = "Check billing cycle date\nGather pending charges\nCalculate proration\nValidate minimum amount")
```

**See:** [subscription-billing-with-rules.puml](subscription-billing-with-rules.puml) for example

### Step 7c: Specify Data Structures
Add `$fields` to commands, events, and views:
```plantuml
$command(Subscribe to Plan, $fields = "customerId: UUID\nplanId: UUID\nbillingCycle: monthly|annual")
$event(Subscription Activated, Subscription, $fields = "subscriptionId: UUID\nactivatedAt: timestamp\nstatus: active")
$view(Active Subscriptions, $fields = "subscriptionId\nplanName\namount\nnextBillingDate")
```

**See:** [subscription-billing-sliced.puml](subscription-billing-sliced.puml) for example

### Step 7d: Prioritize Implementation Slices
Break into vertical slices for iterative development:
1. **P0** - Subscribe to Plan (Core value)
2. **P0** - Generate Invoice (Core value)
3. **P0** - Submit Payment (Core value)
4. **P1** - Change Subscription (Important)
5. **P2** - Reconciliation (Nice to have)

## Important Notes

⚠️ **PlantUML Limitation**: Element names cannot contain hyphens (`-`). Use spaces instead:
- ✅ `Manual Charge` or `ManualCharge`
- ❌ `Manual-Charge` (will cause rendering errors)

## Learning Resources

**New to event modeling?** This example is great for understanding a complex business process, but you should first learn the methodology:

1. **[Getting Started Guide](../GETTING_STARTED.md)** - Start here for your first diagram
2. **[Event Modeling Process Guide](event-modeling-phases-guide.md)** - Complete 7-step process
3. **[Progressive Examples](subscription-billing-base.puml)** - Simpler examples through all phases
4. **[Quick Reference](../EVENT_MODELING_PROCESS_QUICK_REF.md)** - One-page workshop guide

## Event Modeling Principles

This diagram demonstrates key event modeling principles:
- **Events are facts** - Past tense (Activated, Generated, Confirmed)
- **Commands are intents** - Imperative (Subscribe, Generate, Confirm)
- **Views are projections** - Read models built from events
- **Wireframes show UX** - Where users interact with the system
- **Left to right = time** - Business process flows chronologically
- **Manual first** - Show human workflows before automation (policies come in Step 7)
