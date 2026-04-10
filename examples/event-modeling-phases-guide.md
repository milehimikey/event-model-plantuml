# Complete Event Modeling Process Guide

This guide demonstrates the **complete event modeling process** from initial brainstorming through slicing and implementation planning. Event modeling is an iterative workshop-based technique that progressively builds understanding and detail.

## The Event Modeling Process

Event modeling follows **7 steps** as defined at [eventmodeling.org](https://eventmodeling.org/posts/what-is-event-modeling/):

1. **Brainstorming** - Identify events
2. **The Plot** - Order events on a timeline
3. **The Storyboard** - Add wireframes (user interactions)
4. **Identify Inputs** - Add commands
5. **Identify Outputs** - Add views (read models)
6. **Apply Scenarios** - Organize into swim lanes and contexts
7. **Elaborate** - Add slices, policies, and field details

**Key Principle:** Start simple and add complexity progressively. Don't overwhelm stakeholders with technical details upfront.

## When to Use What

| Element | Phase | Purpose |
|---------|-------|---------|
| Events | Steps 1-2 | Business facts (past tense) |
| Wireframes | Step 3 | User interfaces |
| Commands | Step 4 | User intents (imperative) |
| Views | Step 5 | Read models / queries |
| Swim Lanes | Step 6 | Organize by actor/aggregate |
| **Policies** | **Step 7 (Slicing)** | **Automation & business rules** |
| **Fields** | **Step 7 (Slicing)** | **Data structures & specifications** |

## Important: Base Model vs. Sliced Model

### Base Model (Steps 1-6)
- **Purpose:** Understand the business process
- **Audience:** All stakeholders (business + technical)
- **Content:** Events, commands, views, wireframes, basic flow
- **NO policies, NO fields** - Keep it simple!

### Sliced Model (Step 7)
- **Purpose:** Implementation planning
- **Audience:** Development team + domain experts
- **Content:** Policies (automation), fields (data structures), business rules
- **This is where technical details emerge**

---

## Phase 1: Base Event Model (Steps 1-6)

**Goal:** Understand the complete business process without implementation details.

### Example: Subscription Billing (Base Model)

See [subscription-billing-base.puml](subscription-billing-base.puml) for the complete diagram.

**Characteristics:**
- ✅ Shows the complete business flow
- ✅ Human-driven processes (manual actions)
- ✅ Clear workflows: UI → Command → Event → View
- ❌ No automation policies yet
- ❌ No field details yet
- ❌ No business rules specified

**Key Insight:** At this phase, "Generate Invoice" is shown as a manual action by the Finance team. We understand WHAT happens, but not HOW it's triggered.

---

## Phase 2: Slicing - Add Automation (Step 7a)

**Goal:** Identify which workflows should be automated.

### Example: Subscription Billing with Policies

See [subscription-billing-with-policies.puml](subscription-billing-with-policies.puml) for the complete diagram.

**What Changed:**
- ✅ Added **Billing Cycle Policy** - Automatically generates invoices on billing dates
- ✅ Added **Payment Verification Policy** - Integrates with payment gateway
- ✅ Added **Payment Application Policy** - Automatically applies payments to invoices

**Syntax:**
```plantuml
' Add policy that reacts to events
$policy(Billing Cycle Policy)
$eventarrow(Subscription Activated, Billing Cycle Policy)
$command(Generate Invoice)
$commandarrow(Billing Cycle Policy, Generate Invoice)
```

**Key Insight:** Policies show automation. They react to events and trigger commands. This is when you identify what should be automated vs. manual.

---

## Phase 3: Slicing - Add Business Rules (Step 7b)

**Goal:** Define the business logic and invariants for each policy.

### Example: Policies with Business Rules

See [subscription-billing-with-rules.puml](subscription-billing-with-rules.puml) for the complete diagram.

**What Changed:**
- ✅ Added fields to policies showing business rules

**Syntax:**
```plantuml
$policy(Billing Cycle Policy, $fields = "Check billing cycle date\nGather all pending charges\nCalculate proration if needed\nAggregate subscription fees\nValidate minimum charge amount")

$policy(Payment Verification Policy, $fields = "Call payment gateway API\nValidate funds availability\nProcess transaction\nHandle 3D Secure if required\nRetry on timeout (max 3 attempts)")
```

**Key Insight:** This is where domain experts specify the actual business logic. These become the acceptance criteria for implementation.

---

## Phase 4: Slicing - Add Data Structures (Step 7c)

**Goal:** Specify exact fields for commands, events, and views.

### Example: Complete Sliced Model

See [subscription-billing-sliced.puml](subscription-billing-sliced.puml) for the complete diagram.

**What Changed:**
- ✅ Added fields to commands (input parameters)
- ✅ Added fields to events (domain data)
- ✅ Added fields to views (display fields)

**Syntax:**
```plantuml
$command(Subscribe to Plan, $fields = "customerId: UUID\nplanId: UUID\nbillingCycle: monthly|annual\nstartDate: date\npaymentMethodId: UUID")

$event(Subscription Activated, Subscription, $fields = "subscriptionId: UUID\ncustomerId: UUID\nplanId: UUID\nrecurringAmount: decimal\nbillingCycle: monthly|annual\nactivatedAt: timestamp\nstatus: active")

$view(Active Subscriptions, $fields = "subscriptionId\nplanName\ncustomerName\namount\nnextBillingDate\nstatus\ncreatedAt")
```

**Key Insight:** Now the development team knows exactly what data to capture, store, and display. This becomes the API contract.

---

## Phase 5: Breaking into Implementation Slices

**Goal:** Prioritize vertical slices for iterative development.

Each vertical slice is a complete workflow that can be implemented independently:

**Slice Priority Example:**
1. **P0 - Subscribe to Plan** (Core value)
2. **P0 - Generate Invoice** (Core value)
3. **P0 - Submit Payment** (Core value)
4. **P1 - Change Subscription** (Important)
5. **P1 - Payment Verification** (Important)
6. **P2 - Reconciliation** (Nice to have)

Each slice includes:
- Wireframe (UI)
- Command handler
- Event sourcing
- View projection
- Tests for the complete flow

---

## Summary: The Progressive Journey

```
Phase 1: Base Model
├─ Events only → Understand what happens
├─ + Wireframes → Who interacts
├─ + Commands → What actions
├─ + Views → What data is shown
└─ + Swim lanes → Organize by actor/aggregate
    └─ RESULT: Business understanding ✅

Phase 2: Slicing
├─ + Policies → What should be automated
├─ + Policy rules → How does automation work
└─ + Fields → Exact data structures
    └─ RESULT: Implementation specification ✅
```

## Best Practices

1. **Don't skip the base model** - Resist the urge to add policies and fields too early
2. **Workshop format** - Do this collaboratively with business and technical stakeholders
3. **Iterate** - Each phase adds detail; go back and refine as you learn
4. **Focus on value** - Not every workflow needs automation policies
5. **Vertical slices** - Each slice should be independently deliverable

## See Also

- [Base billing example](fintech-billing-system.puml) - Phase 1 complete
- [Iterative example](iterative-example.md) - Steps 1-6 in detail
- [Extensive example](extensive-example.md) - Complex scenario
- [Fields reference](../FIELDS_QUICK_REFERENCE.md) - Syntax guide

---

**Next:** See the example files referenced above for working code samples of each phase!
