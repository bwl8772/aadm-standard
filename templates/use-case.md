# Use case

_Describes one goal-oriented interaction: who is involved, what starts it, the happy path, variants, and how you know it succeeded._

---

## Use case title

<!-- Short, unique name (e.g. “Export weekly timesheet”). -->

## Objective

<!-- One sentence: what this use case achieves for the actor(s). -->

## Actors

| Actor | Description |
|-------|-------------|
| Primary | <!-- human or system driving the goal --> |
| Secondary | <!-- optional: helpers, admins, other systems --> |

## Trigger

<!-- What initiates this use case (user action, schedule, webhook, etc.). -->

## Preconditions

<!-- What must already be true (auth state, data exists, feature enabled). -->

- 

## Basic flow

<!-- Numbered steps for the main success path. -->

1. 
2. 
3. 

## Alternate flows

<!-- Variants: errors, optional branches, permission denials. -->

| ID | Condition | Flow |
|----|-----------|------|
| A1 | <!-- e.g. invalid date range --> | <!-- steps --> |
| A2 | | |

## Acceptance criteria

<!-- Mirror or link to [acceptance-criteria.md](acceptance-criteria.md). -->

- [ ] 
- [ ] 

## Postconditions

<!-- Stable state after success (data changed, audit record, notification sent). -->

---

## Example (fictional)

**Use case title:** Export weekly timesheet as CSV  

**Objective:** Let an authenticated member download their hours for a selected week in CSV form.

**Actors:** Primary — workspace member; Secondary — none.

**Trigger:** User chooses **Export** on the timesheet view after selecting a week.

**Preconditions:** User is logged in; belongs to the workspace; timesheet feature is on.

**Basic flow:**

1. System validates the selected week belongs to the allowed export window.  
2. System builds a CSV from recorded entries for that user and week.  
3. System offers the file download and logs an audit event (“timesheet exported”).

**Alternate flows:**

| ID | Condition | Flow |
|----|-----------|------|
| A1 | Week outside allowed range | Show validation message; no file |
| A2 | No entries for week | Offer empty template CSV or informative message (pick one policy) |

**Acceptance criteria:**

- [ ] Authorized user receives CSV with correct rows only for their data  
- [ ] Unauthorized access returns denial without file contents  

**Postconditions:** Export audit row exists; no change to underlying entry data.

---

_Link to [customer-story.md](customer-story.md) for the narrative line; [layer-map.md](layer-map.md) for UDALI placement._
