# Event Modeling Process - Quick Reference

## The 7 Steps

| Step | Name | Elements Added | Purpose |
|------|------|----------------|---------|
| 1 | **Brainstorming** | Events | Identify what happens |
| 2 | **The Plot** | Events (ordered) | Timeline of events |
| 3 | **The Storyboard** | Wireframes | User interactions |
| 4 | **Identify Inputs** | Commands | User intents/actions |
| 5 | **Identify Outputs** | Views | Read models/queries |
| 6 | **Apply Scenarios** | Swim lanes | Organize by actor/aggregate |
| 7 | **Elaborate (Slicing)** | **Policies + Fields** | Implementation details |

## Critical Distinction

### Base Model (Steps 1-6)
**Purpose:** Understand the business process  
**Audience:** All stakeholders (business + technical)  
**Elements:** Events, Commands, Views, Wireframes, Swim Lanes  
**❌ DO NOT ADD:** Policies, Fields  

### Sliced Model (Step 7)
**Purpose:** Implementation planning  
**Audience:** Development team + domain experts  
**Elements:** Everything from base model + Policies + Fields  
**✅ NOW ADD:** Automation policies, Business rules, Data structures  

## When to Use What

### Always Include (Steps 1-6)
- ✅ **Events** - Business facts (past tense: "Order Placed", "Payment Confirmed")
- ✅ **Commands** - User intents (imperative: "Place Order", "Confirm Payment")
- ✅ **Views** - Read models (nouns: "Order List", "Payment History")
- ✅ **Wireframes** - User interfaces
- ✅ **Swim Lanes** - Actor/aggregate organization

### Add During Slicing Only (Step 7)
- ⚠️ **Policies** - Automation (reacts to events, triggers commands)
- ⚠️ **Fields** - Data structures (command params, event data, view columns)

## Common Mistakes

### ❌ Mistake #1: Adding Policies Too Early
**Wrong:** Adding policies in the base model  
**Right:** Show manual workflows first, add automation during slicing  
**Why:** Stakeholders need to understand WHAT happens before HOW it's automated  

### ❌ Mistake #2: Adding Fields Too Early
**Wrong:** Specifying data structures during initial modeling  
**Right:** Focus on the process flow first, add fields during slicing  
**Why:** Too much detail overwhelms stakeholders and obscures the business process  

### ❌ Mistake #3: Skipping the Base Model
**Wrong:** Jumping straight to policies and fields  
**Right:** Always create the base model first  
**Why:** You need shared understanding before technical specification  

## Quick Syntax Guide

### Base Model Elements
```plantuml
$wireframe(Customer Portal, Customer)           ' UI element
$command(Subscribe to Plan)                     ' User intent
$event(Subscription Activated, Subscription)    ' Business fact
$view(Active Subscriptions)                     ' Read model
```

### Slicing Elements (Step 7 Only!)
```plantuml
' Add automation policy
$policy(Billing Cycle Policy)
$eventarrow(Subscription Activated, Billing Cycle Policy)
$commandarrow(Billing Cycle Policy, Generate Invoice)

' Add business rules to policy
$policy(Billing Cycle Policy, $fields = "Check billing date\nGather charges\nCalculate total")

' Add data structures
$command(Subscribe, $fields = "customerId: UUID\nplanId: UUID")
$event(Subscription Activated, Subscription, $fields = "subscriptionId: UUID\nactivatedAt: timestamp")
$view(Subscriptions, $fields = "subscriptionId\nplanName\nstatus")
```

## The Progressive Journey

```
Workshop Session 1: Base Model
├─ Step 1-2: Brainstorm and order events
├─ Step 3: Add wireframes (who interacts)
├─ Step 4: Add commands (what actions)
├─ Step 5: Add views (what data is shown)
└─ Step 6: Organize into swim lanes
    └─ OUTPUT: Shared understanding of business process ✅

Workshop Session 2: Slicing
├─ Step 7a: Identify automation (add policies)
├─ Step 7b: Define business rules (add policy fields)
├─ Step 7c: Specify data (add fields to commands/events/views)
└─ Step 7d: Prioritize slices for implementation
    └─ OUTPUT: Technical specification ready for development ✅
```

## Element Color Guide

- **Orange** - Events (business facts)
- **Blue** - Commands (user intents)
- **Green** - Views (read models)
- **White** - Wireframes (UI)
- **Purple** - Policies (automation - SLICING ONLY)
- **Pink** - External Systems

## Resources

📘 **[Complete Process Guide](examples/event-modeling-phases-guide.md)** - Detailed walkthrough with examples  
📘 **[Progressive Examples](examples/)** - Same system through all phases  
📘 **[Fields Reference](FIELDS_QUICK_REFERENCE.md)** - Syntax for adding fields  
🌐 **[EventModeling.org](https://eventmodeling.org/)** - Official event modeling site  

## Remember

> "Start simple. Understand the business process first.  
> Add automation and technical details during slicing.  
> Don't overwhelm stakeholders with implementation details upfront."

**The golden rule:** If you're not sure whether to add policies or fields yet, **don't**. Create the base model first.
