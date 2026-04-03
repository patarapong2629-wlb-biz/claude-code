# State Transition Workflow

## Table of Contents
1. [When to Use](#when-to-use)
2. [Clarifying Questions](#clarifying-questions)
3. [State Diagram (Text)](#state-diagram-text)
4. [State Transition Table](#state-transition-table)
5. [Optimization Options](#optimization-options)
6. [Test Case Generation](#test-case-generation)

---

## When to Use

Use State Transition Testing when a system has distinct **states** and **events/triggers** that cause transitions between them — e.g., order status, account lifecycle, booking flow, approval workflow.

Inputs from BVA/EP (valid/invalid values) can serve as events that cause transitions or block them.

---

## Clarifying Questions

Before designing, confirm:
1. **Complete list of states** — are there any states not mentioned? (draft, pending, active, suspended, closed, etc.)
2. **Initial state** — what state does the object start in?
3. **Terminal state(s)** — which states have no outbound transitions?
4. **All valid events/triggers** — for each state, what events are possible?
5. **Invalid transitions** — what should happen when an invalid event is triggered in a given state? (ignore, error, exception?)
6. **Re-entry** — can a state be re-entered? (e.g., can an order go from Shipped back to Processing?)
7. **Optimization preference** — ask:
   > "Would you like to optimize test coverage using **0-switch coverage** (each transition once), **1-switch coverage** (each pair of consecutive transitions), or test all valid + invalid transitions?"

---

## State Diagram (Text)

Represent the state machine as a text-based diagram before building the table.

Example — Order lifecycle:

```
[New] --submit--> [Pending Payment]
[Pending Payment] --pay--> [Confirmed]
[Pending Payment] --cancel--> [Cancelled]
[Confirmed] --ship--> [Shipped]
[Confirmed] --cancel--> [Cancelled]
[Shipped] --deliver--> [Delivered]
[Delivered] (terminal)
[Cancelled] (terminal)
```

---

## State Transition Table

List every **state × event** combination. Mark valid transitions with the resulting state; mark invalid ones as `—` (no transition) or describe the expected error.

| Current State | Event: submit | Event: pay | Event: ship | Event: deliver | Event: cancel |
|---|---|---|---|---|---|
| New | Pending Payment | — | — | — | — |
| Pending Payment | — | Confirmed | — | — | Cancelled |
| Confirmed | — | — | Shipped | — | Cancelled |
| Shipped | — | — | — | Delivered | — |
| Delivered | — | — | — | — | — |
| Cancelled | — | — | — | — | — |

---

## Optimization Options

### 0-Switch Coverage (Minimum)
Cover each valid transition exactly once. N valid transitions = N test cases.
Use when time is limited or the flow is simple.

### 1-Switch Coverage (Recommended)
Cover each pair of consecutive valid transitions (state1 → event → state2 → event → state3).
Better at catching transition-dependent bugs. Requires more test cases.

### All Transitions (including invalid)
Cover every cell in the state transition table, including invalid events in each state.
Use when reliability is critical (e.g., financial, medical systems).

---

## Test Case Generation

### Unit Test Cases (UT)

One test case per selected transition (valid) or per invalid event (for negative cases).

| TC-ID | Test Case Name | Description | Initial State | Event / Action | Expected State | Expected Output | Type |
|---|---|---|---|---|---|---|---|
| UT-001 | Submit new order | Valid submission | New | submit | Pending Payment | Order moves to payment pending | Positive |
| UT-002 | Pay for order | Valid payment | Pending Payment | pay | Confirmed | Order confirmed | Positive |
| UT-003 | Cancel pending order | Cancel before payment | Pending Payment | cancel | Cancelled | Order cancelled | Positive |
| UT-004 | Pay for cancelled order | Invalid event in Cancelled state | Cancelled | pay | Cancelled | Error / no change | Negative |

### Acceptance Test Cases (AT)

AT cases must be **exactly the same count as UT cases** — one AT per UT, in the same order.

Each AT case covers the same transition as its corresponding UT but uses a realistic business scenario with production-like context (real actor, real object, real trigger).

**Workflow:**
1. After generating the UT table, analyze the requirement context and **propose** realistic AT scenarios yourself.
2. Present the full proposed AT table.
3. ⏸ Ask the user: "Please review the AT data above. Adjust any values or scenario descriptions if needed."
4. Update based on user feedback.

Example:

| TC-ID | Test Case Name | Description | Initial State | Event / Action | Expected State | Expected Output | Type |
|---|---|---|---|---|---|---|---|
| AT-001 | Customer submits first order | Alice places a new order via mobile app | New | Customer taps "Place Order" | Pending Payment | Order confirmation screen shown, awaiting payment | Positive |
| AT-002 | Customer pays via credit card | Alice completes payment with Visa card | Pending Payment | Customer completes payment | Confirmed | Email confirmation sent to alice@example.com | Positive |
| AT-003 | Customer cancels before paying | Bob changes his mind before paying | Pending Payment | Customer taps "Cancel Order" | Cancelled | Order cancelled, no charge applied | Positive |
| AT-004 | Attempt to pay cancelled order | System retries payment on cancelled order | Cancelled | Payment retry triggered | Cancelled | Error returned, no state change | Negative |
