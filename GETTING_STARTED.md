# Getting Started with event-model-plantuml

Welcome! This guide will help you create effective event models using this library.

## First Time Here?

### 1. Understand the Process

Event modeling is a **7-step workshop technique**. You don't create the complete diagram all at once!

📘 **READ THIS FIRST:** [Event Modeling Process Guide](examples/event-modeling-phases-guide.md)

**The most important lesson:** Don't add `$policy()`, `$automation()`, or `$schema` in your first diagram! These come later during slicing (Step 7).

### 2. See Progressive Examples

Learn by seeing the same system evolve through phases:

| Phase | File | What You'll See |
|-------|------|-----------------|
| **Base Model** (Steps 1-6) | [subscription-billing-base.puml](examples/subscription-billing-base.puml) | Clean business process, NO policies, NO fields |
| **Add Policies** (Step 7a) | [subscription-billing-with-policies.puml](examples/subscription-billing-with-policies.puml) | Automation identified |
| **Add Rules** (Step 7b) | [subscription-billing-with-rules.puml](examples/subscription-billing-with-rules.puml) | Business logic defined |
| **Complete Spec** (Step 7c) | [subscription-billing-sliced.puml](examples/subscription-billing-sliced.puml) | Data structures specified |

### 3. Quick Reference

**[Process Quick Reference](EVENT_MODELING_PROCESS_QUICK_REF.md)** - Print this for workshops!

## Your First Diagram

### Step 1: Basic Setup

```plantuml
@startuml
!include_once https://raw.githubusercontent.com/chilit-nl/plantuml-event-modeling/main/event-modeling-lib.iuml

$enableAutoAlias()
$enableAutoSpacing()

' Your diagram elements go here

$renderEventModelingDiagram()
@enduml
```

### Step 2: Configure Lanes (Step 6)

```plantuml
' Wireframe lanes = actors/users
$configureWireframeLane(Customer)
$configureWireframeLane(Admin)

' Event lanes = aggregates/concepts
$configureEventLane(Order, $context = "Sales")
$configureEventLane(Payment, $context = "Billing")
```

### Step 3: Add Events (Steps 1-2)

Start by brainstorming business facts (past tense):

```plantuml
$event(Order Placed, Order)
$event(Payment Received, Payment)
$event(Order Shipped, Order)
```

### Step 4: Add User Interactions (Step 3)

Add the UI elements:

```plantuml
$wireframe(Shopping Cart, Customer)
$wireframe(Order Management, Admin)
```

### Step 5: Add Commands (Step 4)

Connect UI to events with commands (imperative):

```plantuml
$wireframe(Shopping Cart, Customer)
$command(Place Order)
$event(Order Placed, Order)
```

### Step 6: Add Views (Step 5)

Show what data users see:

```plantuml
$event(Order Placed, Order)
$view(Order History)
$wireframe(Customer Portal, Customer)
$arrow(Order History, Customer Portal)
```

### Step 7: Complete Base Model

Put it all together:

```plantuml
@startuml
!include_once https://raw.githubusercontent.com/chilit-nl/plantuml-event-modeling/main/event-modeling-lib.iuml

$enableAutoAlias()
$enableAutoSpacing()

$configureWireframeLane(Customer)
$configureEventLane(Order)

' Complete workflow
$wireframe(Shopping Cart, Customer)
$command(Place Order)
$event(Order Placed, Order)
$view(Order History)
$wireframe(Customer Portal, Customer)
$arrow(Order History, Customer Portal)

$renderEventModelingDiagram()
@enduml
```

**STOP HERE** if this is your first event modeling session! Get feedback, discuss with stakeholders, and ensure everyone understands the business process.

## Later: Slicing (Step 7)

Only after you have a complete base model should you add:

### Add Automation Policies

```plantuml
' Policy reacts to event and triggers command
$policy(Order Fulfillment Policy)
$eventarrow(Order Placed, Order Fulfillment Policy)
$command(Ship Order)
$commandarrow(Order Fulfillment Policy, Ship Order)
```

### Add Business Rules

```plantuml
$policy(Order Fulfillment Policy, $fields = "Check inventory available\nValidate shipping address\nCalculate shipping cost\nCreate shipping label")
```

### Add Data Structures

```plantuml
$command(Place Order, $fields = "customerId: UUID\nitems: OrderItem[]\nshippingAddress: Address")
$event(Order Placed, Order, $fields = "orderId: UUID\ncustomerId: UUID\nitems: OrderItem[]\ntotalAmount: decimal\nplacedAt: timestamp")
$view(Order History, $fields = "orderId\ndate\nitems\ntotal\nstatus")
```

## Common Pitfalls

### ❌ Don't Do This

```plantuml
' DON'T add policies and fields in your first diagram!
$command(Place Order, $fields = "customerId: UUID\n...")  ❌
$policy(Some Automation)  ❌
```

### ✅ Do This Instead

```plantuml
' Start simple - just the flow
$command(Place Order)  ✅
$event(Order Placed, Order)  ✅
$view(Order History)  ✅
```

**Then later** (Step 7), add policies and fields.

## Important Syntax Notes

### Element Names Cannot Contain Hyphens

```plantuml
' WRONG - will cause rendering errors
$event(Order-Placed)  ❌
$command(Place-Order)  ❌

' CORRECT - use spaces
$event(Order Placed)  ✅
$command(Place Order)  ✅
```

### Past Tense for Events

```plantuml
' Events are facts that happened
$event(Order Placed)      ✅
$event(Payment Received)  ✅
$event(User Registered)   ✅

' Not imperatives
$event(Place Order)       ❌
```

### Imperative for Commands

```plantuml
' Commands are intents/actions
$command(Place Order)     ✅
$command(Submit Payment)  ✅
$command(Register User)   ✅

' Not past tense
$command(Order Placed)    ❌
```

## Next Steps

1. **Read:** [Complete Process Guide](examples/event-modeling-phases-guide.md)
2. **Print:** [Quick Reference Card](EVENT_MODELING_PROCESS_QUICK_REF.md)
3. **Study:** [Progressive examples](examples/)
4. **Practice:** Create your first base model (Steps 1-6 only)
5. **Facilitate:** Run a workshop with stakeholders
6. **Slice:** Add policies and fields (Step 7) when ready to implement

## Resources

- 📘 [Event Modeling Process Guide](examples/event-modeling-phases-guide.md) - Complete walkthrough
- 📘 [Quick Reference](EVENT_MODELING_PROCESS_QUICK_REF.md) - One-page summary
- 📘 [Fields Reference](FIELDS_QUICK_REFERENCE.md) - Syntax for fields
- 🎓 [EventModeling.org](https://eventmodeling.org/) - Official methodology
- 💬 Get help: Review the [examples](examples/) directory

---

**Remember:** Event modeling is iterative. Start simple, build understanding, then add complexity during slicing!
