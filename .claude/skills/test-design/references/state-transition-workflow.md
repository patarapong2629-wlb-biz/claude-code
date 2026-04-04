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

State Transition can be used in two roles:

**As a leaf technique (single condition):**
Use when a single business condition is itself a state lifecycle — the condition describes distinct states and events that drive transitions (e.g., order status, account lifecycle, booking flow). Design it directly using the workflow below.

**As a combiner technique (group of conditions):**
Use when multiple BVA/EP conditions together drive a state machine — the values from each sub-condition (boundary points or partitions) act as events or guards that determine which transitions are valid or invalid. Apply this after all sub-conditions in the group have been individually designed. The states and transitions are derived from the combined sub-condition outputs.

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
   > "Would you like to optimize test coverage using:
   > 1. **All transitions** — cover every valid + invalid transition (maximum coverage)
   > 2. **Each transition once** — test each transition individually with no chaining (minimum coverage)
   > 3. **Pairwise** — cover all 2-way combinations of consecutive transition pairs
   > 4. **Risk-based** — analyze risk signals and recommend which transitions to focus on
   >
   > Which do you prefer?"

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

### All Transitions (Maximum)
Cover every cell in the state transition table — all valid transitions and all invalid events in each state.
Use when reliability is critical (e.g., financial, medical systems).

### Each Transition Once (Minimum)
Test each transition individually with no chaining. One test case per valid transition + one per invalid event.
Use when time is limited or the flow is simple.

### Pairwise
Cover all 2-way combinations of consecutive transition pairs (state1 → event → state2 → event → state3).
Better at catching bugs that only appear when transitions happen in sequence. Reduces cases vs. all transitions while maintaining good coverage.

### Risk-Based
Gather risk signals by asking the user targeted questions, then **analyze and recommend** which transitions to focus on.

**Step 1 — Gather risk signals** ⏸

Ask the user the following questions (all at once):

> 1. **Historical defects** — Which states or transitions have caused bugs or incidents in the past?
> 2. **Business impact** — Which states are most critical if entered incorrectly or unexpectedly?
> 3. **Usage frequency** — Which transitions happen most often in production?
> 4. **Recent changes** — Which states or events were recently added or modified?
> 5. **Regulatory / compliance** — Are any transitions subject to compliance requirements?

**Step 2 — Score each transition**

| Signal | Weight |
|---|---|
| Historical defect on this transition | High |
| High business impact if this transition fails | High |
| High usage frequency in production | Medium |
| Recently changed state or event involved | Medium |
| Compliance requirement | High |
| No signal / low impact / rarely used | Low |

**Step 3 — Recommend transitions to keep** ⏸

Present a recommended shortlist with reasoning:

| Transition | Risk Level | Reason | Keep? |
|---|---|---|---|
| Pending Payment + pay → Confirmed | High | Core payment flow — most frequent, financial impact | ✓ |
| Cancelled + pay → Cancelled | High | Historical defect: payment retried on cancelled orders | ✓ |
| New + submit → Pending Payment | Medium | Common entry point | ✓ |
| Delivered + cancel → — (invalid) | Low | Terminal state, rare edge case | Optional |

Ask the user: "รีวิว shortlist ด้านบนแล้วยืนยัน หรือปรับเพิ่ม/ลด transition ได้เลยครับ"

---

## Test Case Generation

### Combined UT + AT Table

For State Transition, the transition values (Initial State, Event, Expected State) are identical between UT and AT — only the test name, description, and business context differ. Use a **single combined table** with a Business Context column instead of two separate tables.

**Workflow:**
1. Generate the combined table with one row per selected transition, proposing realistic business context yourself.
2. ⏸ Ask the user: "Please review the table above. Adjust any values or business context if needed."
3. Update based on user feedback.

Label the table: **"Test Cases — [BC name] (UT + AT)"**

| TC-ID | Test Case Name | Description | Initial State | Event / Action | Expected State | Expected Output | Business Context (AT) | Type |
|---|---|---|---|---|---|---|---|---|
| TC-001 | Submit new order | Valid transition: New → Pending Payment | New | submit | Pending Payment | Order moves to payment pending | Alice taps "Place Order" on mobile app | Positive |
| TC-002 | Pay for order | Valid transition: Pending Payment → Confirmed | Pending Payment | pay | Confirmed | Order confirmed | Alice completes payment with Visa card | Positive |
| TC-003 | Cancel pending order | Valid transition: Pending Payment → Cancelled | Pending Payment | cancel | Cancelled | Order cancelled | Bob changes his mind before paying | Positive |
| TC-004 | Pay for cancelled order | Invalid event in Cancelled state | Cancelled | pay | Cancelled | Error / no change | System retries payment on cancelled order | Negative |
