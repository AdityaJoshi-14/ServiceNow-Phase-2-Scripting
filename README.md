# 📘 Day 37 – Display Business Rules & g_scratchpad

**Date:** 26/05/2026

---

# 📖 Introduction

Today's session focused on **Display Business Rules**, one of the four Business Rule types in ServiceNow.

Unlike Before, After, and Async Business Rules, a **Display Business Rule** executes whenever a record is opened or displayed. Its primary purpose is to prepare server-side data and make it available to client-side scripts using the **g_scratchpad** object.

The practical scenario implemented today was automatically populating the **Requestor Groups** field on a new Asset Request record with all groups that the logged-in user belongs to. This was achieved by combining a Display Business Rule, GlideRecord, Arrays, g_scratchpad, and an onLoad Client Script. The uploaded notes include step-by-step screenshots of the Display Business Rule configuration, scratchpad usage, and the completed implementation. :contentReference[oaicite:0]{index=0}

---

# 🎯 Learning Objectives

After completing this session, you should be able to:

- Understand Display Business Rules
- Differentiate between all Business Rule types
- Use g_scratchpad to transfer server-side data to the client
- Create and use Arrays in JavaScript
- Retrieve logged-in user information using gs.getUserID()
- Query user groups using GlideRecord
- Auto-populate fields on new records
- Understand reusable functions and classes conceptually

---

# User Dependencies

A ServiceNow user can have multiple dependencies such as:

- Groups
- Roles
- Active Assigned Tickets
- Approvals
- Ownership

These relationships are often required while building automations.

---

# Business Rule Types

| Type | Executes |
|------|----------|
| Before | Before Database Update |
| After | After Database Update |
| Async | Runs asynchronously after transaction |
| Display | Whenever the form is opened/displayed |

---

# Display Business Rule

## Characteristics

Display Business Rules:

- Execute whenever a record is opened.
- Execute before the form is displayed to the user.
- Run on the server.
- Can pass data to Client Scripts.
- Use **g_scratchpad** to transfer server-side values.

---

# Scenario

When a **New Asset Request** form is opened:

- Requestor should be the logged-in user.
- Requestor Groups should automatically contain all groups of the logged-in user.

---

# JavaScript Arrays

Arrays store multiple values of the same type.

Example:

```javascript
var students = [];

students.push("Sruthi");
students.push("Aditya");
students.push("Akshay");
students.push("Surya");
```

Result

```text
[
Sruthi,
Aditya,
Akshay,
Surya
]
```

---

# Display Business Rule

## Server Script

```javascript
(function executeRule(current, previous) {

    gs.addInfoMessage("I am Display BR - I am running here");

    var empgroups = [];

    var gr = new GlideRecord('sys_user_grmember');

    gr.addQuery('user', gs.getUserID());

    gr.query();

    while (gr.next()) {

        empgroups.push(gr.getValue('group'));

    }

    g_scratchpad.b47 = empgroups;

})(current, previous);
```

---

# Explanation

### Create an Empty Array

```javascript
var empgroups = [];
```

Stores every group Sys ID of the logged-in user.

---

### Access Group Membership Table

```javascript
var gr = new GlideRecord('sys_user_grmember');
```

This table stores the relationship:

```
User ↔ Group
```

---

### Retrieve Logged-in User

```javascript
gs.getUserID();
```

Returns the Sys ID of the current logged-in user.

---

### Query User Groups

```javascript
gr.addQuery('user', gs.getUserID());
```

Retrieve every group that the logged-in user belongs to.

---

### Store Groups

```javascript
empgroups.push(gr.getValue('group'));
```

Example

```text
[
019ad92ec7230010393d265c95c260dd,
477a05d153013010b846ddeeff7b1225
]
```

---

### Pass Values to Client

```javascript
g_scratchpad.b47 = empgroups;
```

Now the client-side scripts can access these values.

---

# Why g_scratchpad?

Display Business Rules run on the server.

Client Scripts run in the browser.

Since Client Scripts cannot directly access server-side variables,

**g_scratchpad** acts as the bridge between Server and Client.

---

# Data Flow

```text
Display Business Rule

↓

GlideRecord

↓

Get User Groups

↓

Store in Array

↓

g_scratchpad

↓

Client Script

↓

Populate Form
```

---

# Client Script

Type:

```
onLoad
```

---

## Script

```javascript
function onLoad() {

    var nw = g_form.isNewRecord();

    if (nw) {

        g_form.setValue('u_requestor_groups', g_scratchpad.b47);

    }

}
```

---

# Explanation

Check if the record is new

```javascript
g_form.isNewRecord()
```

Returns

```text
true
```

for new records.

Only then populate

```javascript
u_requestor_groups
```

using

```javascript
g_scratchpad.b47
```

---

# Execution Flow

```text
User Opens Form

↓

Display Business Rule Runs

↓

Retrieve Logged-in User

↓

Retrieve User Groups

↓

Store in Array

↓

g_scratchpad

↓

Client Script onLoad

↓

Populate Requestor Groups
```

---

# Checking Scratchpad

Temporary test:

```javascript
g_scratchpad.b47test = "This is the Value for Best Students!!!";
```

Then use the JavaScript Executor to verify the value from the client side. The uploaded notes demonstrate this validation step with screenshots. :contentReference[oaicite:1]{index=1}

---

# Why use gs.getUserID()?

Instead of

```javascript
current.sys_id
```

we use

```javascript
gs.getUserID()
```

because during a new record creation there is no reliable current user record to reference.

---

# Array Example

```javascript
var empgroups = [];

empgroups.push("Group A");

empgroups.push("Group B");

empgroups.push("Group C");
```

Result

```text
[
Group A,
Group B,
Group C
]
```

---

# Real Scenario

The Display Business Rule returns

```text
[
Group Sys ID 1,
Group Sys ID 2,
Group Sys ID 3
]
```

The Client Script automatically fills

```
Requestor Groups
```

without user interaction.

The uploaded notes show this behavior on a newly created Asset Request form where the Requestor Groups field is auto-populated. :contentReference[oaicite:2]{index=2}

---

# JavaScript Reusability Concept

Programming becomes easier by grouping related functionality inside classes and functions.

Example:

```text
CarDesign()

↓

3CylinderEngine()

↓

AutoGear()

↓

MusicSystem()

↓

Navigation()
```

Then simply call

```text
CarDesign().AutoGear();

CarDesign().MusicSystem();

CarDesign().Navigation();
```

instead of rewriting code.

---

# Interview Questions

## What is a Display Business Rule?

A server-side Business Rule that executes whenever a record is displayed and prepares data for the client.

---

## What is g_scratchpad?

A server-to-client object used to transfer values from a Display Business Rule to Client Scripts.

---

## Can Client Scripts access GlideRecord directly?

No.

Client Scripts cannot execute server-side GlideRecord queries.

Instead,

Display Business Rules pass required data through **g_scratchpad**.

---

## Why use gs.getUserID()?

It retrieves the Sys ID of the currently logged-in user.

---

## What does g_form.isNewRecord() do?

It determines whether the current form is a new record.

---

# Best Practices

- Use Display Business Rules only for data required during form load.
- Pass only necessary information through `g_scratchpad`.
- Use Arrays when multiple values need to be transferred.
- Populate fields only on new records using `g_form.isNewRecord()`.
- Keep Display Business Rules lightweight for better form performance.

---

# Assignment

- Replicate the same Display Business Rule scenario.
- Retrieve another user-related value (such as Roles or Departments) using `g_scratchpad`.
- Practice creating arrays and passing them from server to client.
- Experiment with `g_form.isNewRecord()` to control field initialization.

---

# Key Takeaways

- Display Business Rules execute whenever a form is displayed.
- `g_scratchpad` bridges server-side and client-side scripts.
- `gs.getUserID()` retrieves the logged-in user's Sys ID.
- Arrays can store multiple related values such as Group Sys IDs.
- Client Scripts use `g_form.isNewRecord()` to initialize fields only for new records.
- Combining Display Business Rules with Client Scripts enables dynamic form initialization.

---

## ✅ End of Day 37 Notes

