# Element Mapping Guide

This document maps the generic event modeling terminology used in the process guides to the specific syntax of this library.

## Quick Reference

| Generic Term | This Library Syntax | Purpose |
|--------------|---------------------|---------|
| **Actor/User** | `$actor(Name)` | Define who interacts with the system |
| **Aggregate** | `$aggregate(Name, $context = "Context")` | Define domain aggregates that own events |
| **Screen/UI** | `$screen(Name, Actor)` | User interface elements |
| **Command** | `$command(Name)` | User intents/actions |
| **Event** | `$event(Name, Aggregate)` | Business facts (past tense) |
| **Read Model/View** | `$readmodel(Name)` | Query models/projections |
| **Policy** | `$policy(Name)` | Business invariants/rules (between command and event) |
| **Automation** | `$automation(Name)` | Generic automated process |
| **Saga** | `$saga(Name)` | Long-running stateful process |
| **Processor** | `$processor(Name)` | Side-effect handler |
| **Translation** | `$translation(Name)` | Context mapper between bounded contexts |
| **External System** | `$externalsystem(Name)` or `$integration(Name)` | Third-party systems |
| **Fields/Data** | `$schema = "field:type:required"` | Data structures |

## Key Differences from Generic Guides

### Terminology

**Generic guides use:**
- "Wireframe" → **Use** `$screen()` in this library
- "View" → **Use** `$readmodel()` in this library  
- "Swim Lane" → **Use** `$actor()` and `$aggregate()` in this library
- "Fields" → **Use** `$schema` parameter in this library

### Automation Elements

This library provides **multiple automation types** instead of a generic "policy":

- **`$policy()`** - Business invariants and validation rules (Step 7)
- **`$automation()`** - Generic event-driven automation (Step 7)
- **`$saga()`** - Long-running workflows with state (Step 7)
- **`$processor()`** - Side-effect handlers like emails, notifications (Step 7)
- **`$translation()`** - Context mapping between bounded contexts (Step 7)

**All automation elements are added during Step 7 (Slicing), not in the base model!**

## Configuration

**Generic guides say:**
```
$configureWireframeLane(ActorName)
$configureEventLane(AggregateName, $context = "Context")
```

**This library uses:**
```plantuml
$actor(ActorName)
$aggregate(AggregateName, $context = "Context")
```

## Layout

**Generic guides use:**
```
$enableAutoSpacing()
```

**This library uses:**
```plantuml
$enableAutoLayout()
```

## Example Translation

### Generic Guide Example:
```plantuml
$configureWireframeLane(Customer)
$configureEventLane(Order, $context = "Sales")

$wireframe(Shopping Cart, Customer)
$command(Place Order, $fields = "customerId, productId")
$event(Order Placed, Order, $fields = "orderId, placedAt")
$view(Order History, $fields = "orderId, status")
```

### This Library Syntax:
```plantuml
$actor(Customer)
$aggregate(Order, $context = "Sales")

$screen(Shopping Cart, Customer)
$command(Place Order, $schema = "customerId:UUID:required, productId:UUID:required")
$event(Order Placed, Order, $schema = "orderId:UUID:required, placedAt:timestamp:required")
$readmodel(Order History, $schema = "orderId, status, placedAt")
```

## Schema Format

This library uses a structured schema format:

```
field:type:constraint, field:type:constraint, ...
```

**Examples:**
- `customerId:UUID:required`
- `email:string:required`
- `quantity:int:required`
- `status:string:optional`
- `amount:Money:required`

## When Reading the Process Guides

When you see terminology from the generic process guides:

1. **"Add policies"** → Consider which automation type fits:
   - Validation/invariant? Use `$policy()`
   - Event-driven automation? Use `$automation()` or `$saga()`
   - Side effects? Use `$processor()`
   - Context mapping? Use `$translation()`

2. **"Add fields"** → Use `$schema` parameter with structured format

3. **"Wireframes"** → Use `$screen()`

4. **"Views"** → Use `$readmodel()`

5. **"Configure lanes"** → Use `$actor()` and `$aggregate()`

## The 7 Steps Still Apply!

The process is the same:
1. Brainstorm events
2. Order on timeline
3. Add screens (`$screen()`)
4. Add commands (`$command()`)
5. Add read models (`$readmodel()`)
6. Organize with actors (`$actor()`) and aggregates (`$aggregate()`)
7. **Slice:** Add automation (`$policy()`, `$automation()`, etc.) and schema (`$schema`)

**Don't add automation or schema until Step 7!**

## For More Information

- See [examples/](examples/) for working code using this library's syntax
- See [README.md](README.md) for complete API reference
- The process guides explain **when** to use elements (universal)
- This mapping shows **how** to use them in this specific library
