# 📘 Day 35 – Advanced UI Actions & Business Rules (User Group Automation)

**Date:** 23/05/2026

---

# 📖 Introduction

In today's session, we explored more advanced ServiceNow scripting concepts by combining **UI Actions**, **Business Rules**, and **GlideRecord** to automate user administration tasks.

Unlike previous sessions where we worked mainly with Incident records, today we focused on the **User (sys_user)** table and **Group Membership (sys_user_grmember)** table.

The primary objective was to automate the management of **VIP Support Group membership** and understand how to convert an **Email Address into a Reference (sys_id)** before creating or updating records.

This session demonstrates real-world automation that is commonly implemented in enterprise ServiceNow environments. The uploaded notes also include screenshots showing the UI Action execution, Business Rule configuration, VIP group setup, and successful testing of the automation. :contentReference[oaicite:0]{index=0}

---

# 🎯 Learning Objectives

After completing this session, you should be able to:

- Convert Email IDs into Reference Fields
- Query records using GlideRecord
- Understand nested GlideRecord usage
- Automatically add users to groups
- Automatically remove users from groups
- Create UI Actions for group management
- Perform cleanup activities using deleteMultiple()
- Understand Before Business Rules
- Prevent invalid database updates

---

# Business Scenario 1

## Email to Reference Conversion

### Problem

The Asset Request form contains only an **Email ID**.

However,

```
Incident.caller_id
```

expects a **Reference Field (sys_user)** and not an email address.

Example

```
Asset Request

↓

Email

john@example.com

↓

Need

Caller = John Smith
```

Simply assigning

```javascript
gr.caller_id = current.u_email;
```

will **NOT** work because `caller_id` expects the **sys_id** of a User record.

---

# Solution

Search the User table using the email and retrieve the corresponding sys_id.

```javascript
var gr = new GlideRecord('sys_user');

gr.addQuery('email', 'howard.johnson@example.com');

gr.query();

if(gr.next())
{
    gs.print(gr.name);
    gs.print(gr.sys_id);
}
```

---

# Email → Reference Conversion

This logic is typically used inside a UI Action.

```javascript
var usr = new GlideRecord('sys_user');

usr.addQuery('email', current.u_email);

usr.query();

if(usr.next())
{
    gr.caller_id = usr.sys_id;
}
```

---

# Execution Flow

```
Email Entered

↓

Search sys_user

↓

User Found

↓

Retrieve sys_id

↓

Assign caller_id

↓

Incident Created
```

This ensures that reference fields always store valid record identifiers instead of plain text.

---

# Nested GlideRecord

One important concept introduced today is that **a GlideRecord can be used inside another GlideRecord**.

Example:

```
Asset Request

↓

GlideRecord (Incident)

↓

Need Caller

↓

Second GlideRecord (User)

↓

Retrieve sys_id

↓

Assign Caller
```

Nested GlideRecords are very common in ServiceNow scripting when multiple tables are involved. :contentReference[oaicite:1]{index=1}

---

# Business Scenario 2

## VIP Support Group Automation

### Requirement

Whenever the **VIP** checkbox is selected on a User record,

Automatically

```
Add User

↓

VIP Support Group
```

If the VIP checkbox is unchecked,

```
Remove User

↓

VIP Support Group
```

This automation eliminates manual group administration.

---

# Understanding the Group Membership Table

Many beginners assume that group membership is stored on the User table.

Actually,

```
User

+

Group

↓

sys_user_grmember
```

Every record in

```
sys_user_grmember
```

represents one User–Group relationship.

Example

| User | Group |
|------|-------|
| John | VIP Support |
| David | ITIL |
| Andrew | Network |

If the record is deleted,

the user is automatically removed from the group.

---

# Business Rule 1

## Automatically Add User to VIP Group

### Trigger

```
Table

↓

sys_user

↓

Insert / Update

↓

VIP = True
```

---

### Script

```javascript
(function executeRule(current, previous){

var gr = new GlideRecord('sys_user_grmember');

gr.initialize();

gr.user = current.sys_id;

gr.group = '4a0d80148355b21095e89cb6feaad3e5';

gr.insert();

gs.addInfoMessage("User is added to VIP Support Group");

action.setRedirectURL(current);

})(current, previous);
```

---

# Script Explanation

### Create Membership Record

```javascript
var gr = new GlideRecord('sys_user_grmember');
```

Creates a new Group Membership record.

---

### Initialize

```javascript
gr.initialize();
```

Creates an empty membership record.

---

### Assign User

```javascript
gr.user = current.sys_id;
```

Stores the current User.

---

### Assign Group

```javascript
gr.group = 'VIP Group Sys_ID';
```

Assigns the VIP Support Group.

---

### Insert

```javascript
gr.insert();
```

Creates the relationship.

---

### Result

```
User

↓

VIP = True

↓

Business Rule

↓

Membership Record Created

↓

User added to VIP Support
```

The uploaded notes demonstrate this process with screenshots showing the user record and the corresponding entry in the `sys_user_grmember` table. :contentReference[oaicite:2]{index=2}

---

# Business Rule 2

## Remove User from VIP Group

Requirement

When

```
VIP = False
```

Remove the user from the VIP Support Group.

---

### Script

```javascript
(function executeRule(current, previous){

var gr = new GlideRecord('sys_user_grmember');

gr.addQuery('user', current.sys_id);

gr.addQuery('group','4a0d80148355b21095e89cb6feaad3e5');

gr.query();

if(gr.next())
{
    gr.deleteRecord();

    gs.addInfoMessage("User Removed from VIP Support Group");

    action.setRedirectURL(current);
}

})(current, previous);
```

---

# Execution Flow

```
VIP Unchecked

↓

Business Rule

↓

Search Membership

↓

Delete Membership

↓

User Removed
```

The notes include screenshots verifying that the group membership record disappears from `sys_user_grmember` after the Business Rule executes. :contentReference[oaicite:3]{index=3}

---

# Cleanup Activity

## Remove Inactive Users From Every Group

Requirement

Whenever a User becomes inactive,

Remove the user from **all groups**.

---

### Script

```javascript
var gr = new GlideRecord('sys_user_grmember');

gr.addQuery('user', current.sys_id);

gr.query();

gr.deleteMultiple();
```

---

# Why deleteMultiple()?

Instead of deleting records one by one,

```
deleteRecord()

↓

100 Queries
```

Use

```
deleteMultiple()

↓

Single Database Operation
```

This is much faster and recommended for cleanup scripts.

---

# UI Action

## Make VIP

Instead of waiting for a Business Rule,

the administrator can manually click

```
Make VIP
```

---

### Script

```javascript
var gr = new GlideRecord('sys_user_grmember');

gr.initialize();

gr.user = current.sys_id;

gr.group = '4a0d80148355b21095e89cb6feaad3e5';

gr.insert();

current.vip = true;

current.update();

gs.addInfoMessage("The user is added to VIP Support Group");

action.setRedirectURL(current);
```

---

# Execution Flow

```
Click

Make VIP

↓

Create Membership

↓

VIP=True

↓

Save User

↓

Redirect
```

---

# UI Action

## Remove VIP

### Script

```javascript
var gr = new GlideRecord('sys_user_grmember');

gr.addQuery('user', current.sys_id);

gr.addQuery('group','4a0d80148355b21095e89cb6feaad3e5');

gr.query();

if(gr.next())
{
    gr.deleteRecord();

    gs.addInfoMessage("The user has been removed from VIP Support Group");
}

current.vip = false;

current.update();

action.setRedirectURL(current);
```

---

# Execution Flow

```
Click

Remove VIP

↓

Delete Membership

↓

VIP=False

↓

Save User

↓

Redirect
```

The uploaded notes contain screenshots showing both the **Make VIP** and **Remove VIP** UI Actions, along with their successful execution. :contentReference[oaicite:4]{index=4}

---

# Before Business Rule

Today's final topic introduced the **Before Business Rule**.

Scenario:

```
Incident

↓

Assignment Group

↓

Validate

↓

Abort Save
```

Example

```javascript
if(!grp.hasNext())
{
    current.setAbortAction(true);
}
```

---

# What does setAbortAction(true) do?

Normally,

```
User

↓

Update

↓

Database Save
```

With

```javascript
current.setAbortAction(true);
```

the transaction becomes

```
User

↓

Update

↓

Validation Failed

↓

Stop Database Update
```

The record is **NOT** saved.

This is useful when business validations fail.

---

# Difference Between Business Rule and UI Action

| Business Rule | UI Action |
|--------------|-----------|
| Executes automatically | Executes on user click |
| Runs on Insert/Update/Delete | Runs only when selected |
| No user interaction | User initiated |
| Automation | Manual action |

---

# Interview Questions

## Why can't we assign an email directly to caller_id?

Because `caller_id` is a Reference field that stores the `sys_id` of a User record, not an email address.

---

## Which table stores User–Group relationships?

```
sys_user_grmember
```

---

## Why use deleteMultiple()?

It removes multiple records in a single database operation, making cleanup activities more efficient.

---

## What is Nested GlideRecord?

Using one GlideRecord query inside another GlideRecord to retrieve related information from different tables.

---

## When should a Before Business Rule be used?

When validation is required **before** the record is committed to the database.

---

## What does current.setAbortAction(true) do?

Stops the database transaction and prevents the record from being saved.

---

# Best Practices

- Never hardcode values unless necessary; store reusable sys_ids in system properties when possible.
- Always validate records using `gr.next()` before accessing fields.
- Use `deleteMultiple()` for bulk cleanup activities.
- Keep Business Rules focused on a single responsibility.
- Use UI Actions for user-initiated tasks and Business Rules for automatic processes.
- Use `current.setAbortAction(true)` only when you intentionally want to block a save operation.

---

# Assignment

- Implement Email → Reference conversion in a UI Action.
- Create a Business Rule to automatically add/remove users from the VIP Support Group.
- Create UI Actions to manually Make VIP and Remove VIP.
- Build a cleanup Business Rule that removes inactive users from all groups.
- Practice a Before Business Rule using `current.setAbortAction(true)`.

---

# Key Takeaways

- Reference fields require **sys_id**, not display values such as email addresses.
- Nested GlideRecords are useful when working across multiple related tables.
- `sys_user_grmember` stores all User–Group relationships.
- Business Rules can automate group membership based on field values.
- UI Actions provide manual control for administrative tasks.
- `deleteMultiple()` is ideal for cleanup operations.
- `current.setAbortAction(true)` prevents invalid database updates.

---

## ✅ End of Day 35 Notes
```
