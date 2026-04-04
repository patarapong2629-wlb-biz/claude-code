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

### Combined UT + AT Table

For EP, input values are identical between UT and AT — only the test name and business context differ. Use a **single combined table** with a Business Context column instead of two separate tables.

**Workflow:**
1. Generate the combined table, proposing realistic business context yourself for each partition.
2. ⏸ Ask the user: "Please review the table above. Adjust any values or business context if needed."
3. Update based on user feedback.

Label the table: **"Test Cases — [BC name] (UT + AT)"**

| TC-ID | Test Case Name | Description | [Input col] | Expected Result | Business Context (AT) | Type |
|---|---|---|---|---|---|---|
| TC-001 | Admin role access | Valid admin partition | admin | Full access granted | Alice (admin) logs in to manage articles | Positive |
| TC-002 | Editor role access | Valid editor partition | editor | Edit access granted | Bob (editor) logs in to edit a post | Positive |
| TC-003 | Viewer role access | Valid viewer partition | viewer | Read-only access | Carol (viewer) logs in to browse | Positive |
| TC-004 | Unknown role | Invalid — role not in allowed list | superuser | Access denied | Dave uses a custom role not in the system | Negative |
| TC-005 | Empty role | Invalid — no role provided | (empty) | Access denied / error | API call sent without a role field | Negative |

---

## Notes

- One representative value per partition is sufficient — testing more than one value in the same partition adds no additional coverage.
- Always include at least one invalid partition to verify rejection behavior.
- If partitions overlap or are unclear, ask for clarification before proceeding.
