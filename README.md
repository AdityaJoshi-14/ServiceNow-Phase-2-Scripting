# 📘 Day 36 – Business Rules, Parent-Child Relationships & Scheduled Jobs

**Date:** 25/06/2026

---

# 📖 Introduction

Today's session focused on **Business Rules**, **Parent–Child record relationships**, and **Scheduled Jobs** in ServiceNow.

We learned how to synchronize data between related records automatically using **After Business Rules**. The primary scenarios involved propagating description changes from:

- Problem → Child Incidents
- Parent Incident → Child Incidents
- Change → Change Tasks

We also explored automating record maintenance by:

- Making records inactive when closed/cancelled
- Running Scheduled Jobs for periodic cleanup
- Removing inactive users from group memberships
- Automatically deactivating users who haven't logged in for a long period

These concepts demonstrate how ServiceNow automates related record updates and maintenance tasks with minimal manual effort. The uploaded notes also include screenshots of Business Rule configuration, parent-child relationships, and Scheduled Job creation. :contentReference[oaicite:0]{index=0}

---

# 🎯 Learning Objectives

After completing this session, you should be able to:

- Create After Business Rules
- Update child records automatically
- Understand Parent–Child relationships
- Use GlideRecord to retrieve related records
- Synchronize descriptions between related tables
- Create Scheduled Jobs
- Perform periodic cleanup activities
- Manage inactive records automatically

---

# Business Scenario 1

## Problem → Child Incidents

### Requirement

Whenever the **Description** of a Problem record changes,

Automatically update the same Description in all child Incidents attached to that Problem.

---

## Business Rule Configuration

| Property | Value |
|----------|-------|
| Table | Problem |
| When | After |
| Trigger | Update |

---

## Script

```javascript
(function executeRule(current, previous) {

    var gr = new GlideRecord('incident');

    gr.addQuery('problem_id', current.sys_id);

    gr.query();

    while(gr.next())
    {
        gr.description = current.description;

        gr.work_notes = "The parent problem's description has been changed to: " + current.description;

        gr.update();
    }

})(current, previous);
```

---

# Execution Flow

```text
Problem Updated

↓

Business Rule

↓

Find Child Incidents

↓

Update Description

↓

Update Work Notes

↓

Save Incidents
```

This ensures every Incident linked to the Problem always contains the latest description from its parent. The uploaded notes include screenshots demonstrating the update before and after execution. :contentReference[oaicite:1]{index=1}

---

# Business Scenario 2

## Parent Incident → Child Incidents

### Requirement

Whenever a Parent Incident description changes,

Automatically update the Description of every Child Incident.

---

## Business Rule Configuration

| Property | Value |
|----------|-------|
| Table | Incident |
| When | After |
| Trigger | Update |

---

## Script

```javascript
(function executeRule(current, previous) {

    var gr = new GlideRecord('incident');

    gr.addQuery('parent_incident', current.sys_id);

    gr.query();

    while(gr.next())
    {
        gr.description = current.description;

        gr.work_notes = "The parent incident description has been changed to: " + current.description;

        gr.update();
    }

})(current, previous);
```

---

# Execution Flow

```text
Parent Incident Updated

↓

Business Rule

↓

Find Child Incidents

↓

Copy Description

↓

Save Child Incidents
```

The uploaded notes illustrate the parent incident, its related child incidents, and the successful synchronization after updating the parent description. :contentReference[oaicite:2]{index=2}

---

# Business Scenario 3

## Retrieve Incidents Attached to a Problem

Requirement

Retrieve every Incident linked to:

```
PRB0040117
```

---

## Script

```javascript
var gr = new GlideRecord('incident');

gr.addQuery('problem_id', 'e55d74188323321095e89cb6feaad3bc');

gr.query();

gs.print(gr.getRowCount());

while(gr.next())
{
    gs.print(gr.number + " : " + gr.short_description);
}
```

---

# Output

```text
INC0010001 : Email Issue

INC0010002 : VPN Failure

INC0010003 : SAP Access Failure
```

---

# Parent–Child Relationships

| Parent | Child |
|---------|--------|
| Problem | Incident |
| Incident | Child Incident |
| Change | Change Task |

Understanding these relationships is important because automation typically flows from the parent record to its children.

---

# Business Scenario 4

## Change → Change Task

Requirement

Whenever the Description of a Change Request changes,

Automatically update all associated Change Tasks.

---

## Business Rule Configuration

| Property | Value |
|----------|-------|
| Table | Change Request |
| When | After |
| Trigger | Update |

---

## Script

```javascript
(function executeRule(current, previous){

    var gr = new GlideRecord('change_task');

    gr.addQuery('change_request', current.sys_id);

    gr.query();

    while(gr.next())
    {
        gr.description = current.description;

        gr.update();
    }

    gs.addInfoMessage("Change Task descriptions updated successfully.");

})(current, previous);
```

---

# Execution Flow

```text
Change Request Updated

↓

Business Rule

↓

Find Change Tasks

↓

Update Description

↓

Save Tasks
```

The uploaded notes include screenshots showing the relationship between Change Requests and Change Tasks along with the Business Rule implementation. :contentReference[oaicite:3]{index=3}

---

# Business Scenario 5

## Automatically Make Closed/Cancelled Tickets Inactive

Requirement

Whenever a ticket reaches

- Closed
- Cancelled

Automatically set

```
Active = False
```

---

## Possible Implementations

- Business Rule
- UI Action
- Background Script
- Scheduled Job

---

## Business Rule Script

```javascript
(function executeRule(current, previous){

    current.u_activeness = false;

    current.update();

})(current, previous);
```

---

# Alternative

Instead of scripting,

Business Rule Conditions alone can be configured to set the Active field to False whenever the state becomes Closed or Cancelled. The uploaded notes demonstrate both the scripting approach and the no-code condition-based configuration. :contentReference[oaicite:4]{index=4}

---

# Execution Flow

```text
State

↓

Closed / Cancelled

↓

Business Rule

↓

Active = False

↓

Record Updated
```

---

# Scheduled Job

## Monthly Cleanup of User Group Membership

Requirement

Every month,

Remove inactive users from every group.

---

## Script

```javascript
var gr = new GlideRecord('sys_user_grmember');

gr.addEncodedQuery('user.active=false');

gr.query();

gr.deleteMultiple();
```

---

# Why Use addEncodedQuery?

Instead of querying

```
sys_user

↓

sys_user_grmember
```

Encoded Query allows directly filtering related records.

Example

```
user.active=false
```

This is shorter, cleaner, and more efficient.

---

# Longer Version

```javascript
var gr = new GlideRecord('sys_user');

gr.addQuery('active', false);

gr.query();

while(gr.next())
{
    var grr = new GlideRecord('sys_user_grmember');

    grr.addQuery('user', gr.sys_id);

    grr.query();

    grr.deleteMultiple();
}
```

Both approaches achieve the same result, but the encoded query version is preferred because it is simpler and more efficient. The uploaded notes compare both implementations. :contentReference[oaicite:5]{index=5}

---

# Scheduled Job

## Deactivate Users Who Haven't Logged In

Requirement

Run every month.

If a user hasn't logged in for more than 18 months,

Automatically deactivate the user.

---

## Script

```javascript
var gr = new GlideRecord('sys_user');

gr.addEncodedQuery('last_loginRELATIVELT@year@ago@1^active=true');

gr.query();

while(gr.next())
{
    gr.active = false;

    gr.update();
}
```

---

# Execution Flow

```text
Scheduled Job

↓

Find Old Login Records

↓

Deactivate User

↓

Update Record
```

---

# Scheduled Jobs

Navigation

```text
System Definition

↓

Scheduled Jobs

↓

New

↓

Monthly

↓

Script Execution
```

The uploaded notes include screenshots of the Scheduled Job configuration page, scheduling options, and script execution settings. :contentReference[oaicite:6]{index=6}

---

# Business Rule Types Used Today

| Business Rule | Purpose |
|--------------|---------|
| After | Update child records after parent is saved |

---

# Interview Questions

## Why use an After Business Rule?

Because the parent record must be committed to the database before updating related child records.

---

## Why use current.sys_id?

To uniquely identify the current parent record and retrieve all related child records.

---

## Why update child records using GlideRecord?

Because child records exist in a different table and must be queried separately.

---

## What is a Scheduled Job?

A Scheduled Job is a server-side process that executes automatically at a predefined date or time without user interaction.

---

## Why use deleteMultiple()?

It removes multiple records in a single database operation, making cleanup activities faster and more efficient.

---

# Best Practices

- Use **After Business Rules** when updating related child records.
- Query child records using the parent record's `sys_id`.
- Prefer `addEncodedQuery()` for concise and efficient filtering.
- Use Scheduled Jobs for recurring maintenance tasks instead of manual execution.
- Avoid unnecessary updates by only modifying records that require changes.

---

# Assignment

- Replicate the Before Business Rule use case in the Asset table.
- Create a Business Rule to synchronize Problem descriptions with child Incidents.
- Create a Business Rule to synchronize Parent Incident descriptions with Child Incidents.
- Implement Change Request → Change Task description synchronization.
- Practice creating Scheduled Jobs for automated maintenance tasks.

---

# Key Takeaways

- After Business Rules are ideal for synchronizing related records.
- Parent–Child relationships can be automated using GlideRecord queries.
- `current.sys_id` identifies the parent record for querying related children.
- Scheduled Jobs automate recurring cleanup and maintenance activities.
- Encoded Queries simplify complex filters and improve readability.
- `deleteMultiple()` is the preferred method for bulk deletion tasks.

---

## ✅ End of Day 36 Notes

