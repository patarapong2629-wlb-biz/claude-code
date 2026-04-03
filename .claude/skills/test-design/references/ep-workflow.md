# EP Workflow (Equivalence Partitioning)

## Table of Contents
1. [When to Use](#when-to-use)
2. [Clarifying Questions](#clarifying-questions)
3. [Partition Definition](#partition-definition)
4. [Test Case Generation](#test-case-generation)

---

## When to Use

Use Equivalence Partitioning when a field has **discrete categories or classes** where any value in the same class is expected to behave identically. EP reduces the number of tests by picking one representative per partition.

Do not combine with BVA — they are mutually exclusive. For numeric ranges, use BVA instead.

---

## Clarifying Questions

Before designing, confirm:
1. **Complete list of valid categories** — are there categories not mentioned in the requirement?
2. **Null/empty input** — is blank/null a valid input? What is the expected behavior?
3. **Case sensitivity** — for string inputs, does casing matter?
4. **Default value** — is there a default when the field is omitted?
5. **Any exception cases** — any value treated differently from its apparent class?

---

## Partition Definition

Identify all partitions (valid and invalid) and assign one representative value to each.

Example — "User role must be: admin, editor, or viewer":

| Partition | Class | Representative Value |
|---|---|---|
| admin | Valid | `admin` |
| editor | Valid | `editor` |
| viewer | Valid | `viewer` |
| Unknown role | Invalid | `superuser` |
| Empty/null | Invalid | `` (empty string) |

---

## Test Case Generation

### Unit Test Cases (UT)

Generate one test case per partition using the representative value.

Example — "User role determines access":

| TC-ID | Test Case Name | Description | Role Input | Expected Result | Type |
|---|---|---|---|---|---|
| UT-001 | Admin role access | Valid admin role | admin | Full access granted | Positive |
| UT-002 | Editor role access | Valid editor role | editor | Edit access granted | Positive |
| UT-003 | Viewer role access | Valid viewer role | viewer | Read-only access | Positive |
| UT-004 | Unknown role | Role not in allowed list | superuser | Access denied | Negative |
| UT-005 | Empty role | No role provided | (empty) | Access denied / error | Negative |

### Acceptance Test Cases (AT)

AT cases must be **exactly the same count as UT cases** — one AT per UT, in the same order.

For EP, the AT representative value for each partition may differ from the UT value to use more realistic business data (e.g., a real username instead of a raw role string).

**Workflow:**
1. After generating the UT table, analyze the requirement context and **propose** realistic AT values yourself — one per partition.
2. Present the full proposed AT table.
3. ⏸ Ask the user: "Please review the AT data above. Adjust any values or scenario descriptions if needed."
4. Update based on user feedback.

Example — "User role determines access":

| TC-ID | Test Case Name | Description | Role Input | Expected Result | Type |
|---|---|---|---|---|---|
| AT-001 | Admin manages content | Alice (admin) logs in to manage articles | admin | Full access granted | Positive |
| AT-002 | Editor updates article | Bob (editor) logs in to edit a post | editor | Edit access granted | Positive |
| AT-003 | Viewer reads content | Carol (viewer) logs in to browse | viewer | Read-only access | Positive |
| AT-004 | Unauthorized role attempt | Dave uses a custom role not in the system | superuser | Access denied | Negative |
| AT-005 | No role submitted | API call sent without a role field | (empty) | Access denied / error | Negative |

---

## Notes

- One representative value per partition is sufficient — testing more than one value in the same partition adds no additional coverage.
- Always include at least one invalid partition to verify rejection behavior.
- If partitions overlap or are unclear, ask for clarification before proceeding.
