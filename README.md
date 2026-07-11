# 📘 Day 34 – UI Actions in ServiceNow (GlideRecord Scripting)

**Date:** 22/05/2026

---

# 📖 Introduction

In today's session, we learned one of the most commonly used server-side scripting components in ServiceNow known as **UI Actions**.

A UI Action allows developers to execute custom server-side or client-side logic when a user clicks a button, link, or context menu option on a form or list.

To understand UI Actions, we implemented a real-world requirement where a **Cancelled Asset Request** can be **escalated into an Incident** with a single click.

This session also introduced concepts like:

- Creating records using GlideRecord inside UI Actions
- Using the `current` object
- Redirecting users after execution
- Relating two records
- Displaying UI Actions conditionally
- Preventing duplicate actions

---

# 🎯 Learning Objectives

After completing this session you should be able to:

- Understand UI Actions
- Create Form UI Actions
- Write server-side UI Action scripts
- Create Incident records from another table
- Understand the difference between `current` and GlideRecord
- Use `action.setRedirectURL()`
- Show or hide UI Actions using conditions
- Prevent duplicate escalations

---

# What is a UI Action?

A **UI Action** is a configurable action in ServiceNow that allows users to execute scripts directly from the User Interface.

It can appear as:

- Form Button
- Form Link
- Form Context Menu
- List Button
- List Context Menu
- Related Link

Unlike UI Policies or Client Scripts, UI Actions can execute **server-side GlideRecord code**.

---

# Real Time Business Scenario

## Requirement

Whenever an **Asset Request Ticket** is cancelled, users should be able to escalate it as an **Incident**.

Example

```
Asset Request

↓

Cancelled

↓

Click

Escalate as Incident

↓

Incident Created Automatically

↓

Asset Request linked with Incident
```

Instead of manually creating an Incident and copying all information, the system performs the complete process automatically.

---

# Problems to Solve

While implementing the requirement, several business problems were identified.

### Problem 1

Create an Incident automatically.

### Problem 2

Relate the Incident back to the Asset Request.

### Problem 3

Show the UI Action only for Cancelled tickets.

### Problem 4

Redirect user to the appropriate page after execution.

### Problem 5

Allow escalation only once.

---

# Creating the UI Action

The UI Action was created on the **Asset Request Management** table.

Important configuration:

```
Table

↓

Asset Request Management

↓

Form Context Menu

↓

Server Side
```

The UI Action appears under the Form Context Menu.

---

# Simple Test Script

Initially we tested whether the UI Action executes.

```javascript
gs.addInfoMessage("Hello I am UI Action and I am running");
```

Result

```
User Clicks

↓

UI Action Executes

↓

Information Message Displayed
```

This confirmed that the UI Action was correctly configured. :contentReference[oaicite:0]{index=0}

---

# Creating an Incident

Next, we created an Incident record using GlideRecord.

```javascript
var gr = new GlideRecord('incident');

gr.initialize();

gr.caller_id = current.u_requestor;

gr.short_description = current.u_short_description;

gr.description = "This incident is an Escalation from : " + current.u_number;

gr.impact = 1;

gr.urgency = 1;

gr.assignment_group = current.u_assignment_group;

gr.assigned_to = current.u_assigned_to;

gr.state = 2;

gr.insert();
```

---

# Understanding the Script

### Step 1

Create GlideRecord object

```javascript
var gr = new GlideRecord('incident');
```

This creates a GlideRecord object for the Incident table.

---

### Step 2

Initialize

```javascript
gr.initialize();
```

Creates a new empty Incident record.

---

### Step 3

Populate Fields

The Incident inherits information from the Asset Request.

Example

```
Requestor

↓

Caller

--------------------

Short Description

↓

Short Description

--------------------

Assignment Group

↓

Assignment Group

--------------------

Assigned To

↓

Assigned To
```

Instead of asking the user to fill everything again, values are copied automatically.

---

### Step 4

Insert

```javascript
gr.insert();
```

This creates the Incident in the database.

---

# current vs GlideRecord

Two objects are involved.

## current

Represents the current Asset Request record.

Example

```javascript
current.u_requestor

current.u_short_description

current.u_number
```

Think of **current** as

```
Current Form

↓

Current Record

↓

Current Values
```

---

## gr

Represents the new Incident being created.

Example

```javascript
gr.short_description

gr.caller_id

gr.state
```

Think of **gr** as

```
New Incident

↓

Populate Values

↓

Insert
```

---

# Linking Both Records

Creating the Incident alone is not sufficient.

The Asset Request should also know which Incident was created.

```javascript
current.u_related_incident = gr.sys_id;

current.update();
```

Here,

```
Asset Request

↓

Stores Incident Sys ID

↓

Relationship Created
```

This creates traceability between both records. :contentReference[oaicite:1]{index=1}

---

# Updating Work Notes

```javascript
current.u_work_notes = gr.number + " is generated as ESCALATION";

current.update();
```

Result

```
Work Notes

↓

INC0012345

is generated as ESCALATION
```

Users immediately know which Incident was generated.

---

# Display Success Message

```javascript
gs.addInfoMessage(gr.number + " is generated");
```

Displays

```
INC0012345 is generated
```

---

# Redirecting the User

ServiceNow allows redirecting users after the UI Action finishes.

## Redirect to Incident

```javascript
action.setRedirectURL(gr);
```

Result

```
User

↓

Clicks Escalate

↓

Incident Opens
```

---

## Redirect to Current Asset Request

```javascript
action.setRedirectURL(current);
```

Result

```
User

↓

Clicks Escalate

↓

Returns to Asset Request
```

Both approaches are valid depending on business requirements.

---

# Showing UI Action Only for Cancelled Tickets

Initially the UI Action appeared for every Asset Request.

Business requirement:

```
New

×

In Progress

×

On Hold

×

Resolved

×

Closed

×

Cancelled

✓
```

Condition used

```javascript
current.u_ticket_state == 8
```

Now the UI Action appears only when the ticket state is **Cancelled**. :contentReference[oaicite:2]{index=2}

---

# Restricting One-Time Escalation

Another issue:

Users could repeatedly click

```
Escalate as Incident
```

resulting in multiple Incident records.

To prevent this,

Condition:

```javascript
current.u_ticket_state == 8 &&
current.u_related_incident == ''
```

Meaning

```
Cancelled

AND

Related Incident is Empty

↓

Show UI Action
```

If a Related Incident already exists,

```
Hide UI Action
```

This ensures only one Incident is created for one Asset Request.

---

# Complete Execution Flow

```
Cancelled Asset Request

↓

User Clicks

Escalate as Incident

↓

UI Action Executes

↓

Create Incident

↓

Populate Incident Fields

↓

Insert Incident

↓

Save Incident Sys ID

↓

Update Work Notes

↓

Display Success Message

↓

Redirect User

↓

Hide UI Action
```

---

# Real World Benefits

- Saves manual effort.
- Prevents duplicate data entry.
- Maintains relationship between records.
- Ensures controlled escalation.
- Improves user experience.

---

# Interview Questions

## What is a UI Action?

A configurable action that executes client-side or server-side code when users click buttons, links or context menus.

---

## Difference between Client Script and UI Action?

| Client Script | UI Action |
|--------------|-----------|
| Executes automatically | Executes when clicked |
| Mostly client-side | Client or Server |
| Used for validations | Used for actions |

---

## What is `current`?

Represents the current record on which the UI Action is executing.

---

## Why use `initialize()`?

Creates a new empty record before inserting.

---

## Why store `gr.sys_id`?

To relate the newly created Incident back to the Asset Request.

---

## Why use `action.setRedirectURL()`?

To control which page opens after the UI Action completes.

---

## How do you prevent duplicate Incident creation?

By checking whether the Related Incident field is already populated.

---

# Best Practices

- Always use conditions to control UI Action visibility.
- Avoid duplicate record creation.
- Display success messages using `gs.addInfoMessage()`.
- Link parent and child records whenever required.
- Redirect users appropriately after execution.
- Test UI Actions thoroughly in a Personal Developer Instance before deployment.

---

# Assignment

- Create a UI Action to escalate a Cancelled Asset Request as an Incident.
- Populate Incident fields from the Asset Request.
- Store the Incident Sys ID in the Asset Request.
- Display the Incident number in Work Notes.
- Show the UI Action only for Cancelled tickets.
- Restrict escalation to one time only.
- Test both redirect options using `action.setRedirectURL()`.

---

# Key Takeaways

- UI Actions execute custom logic through buttons, links, or context menus.
- GlideRecord can be used inside UI Actions to create related records.
- The `current` object represents the record being acted upon.
- `action.setRedirectURL()` controls post-action navigation.
- Conditions determine when a UI Action is visible.
- Prevent duplicate operations by validating related fields before execution.

---

# Official Documentation

ServiceNow UI Actions Documentation

https://www.servicenow.com/docs/

---

## ✅ End of Day 34 Notes
