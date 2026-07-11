# Day 31 – GlideRecord Fundamentals (Server Side Scripting)

**Date:** 18/05/2026

---

# Introduction

Welcome to one of the most important topics in ServiceNow Development.

Until now, we have learned:

- Client Scripts
- UI Policies
- GlideForm (g_form)
- GlideUser (g_user)
- Business Rules

Today we begin learning **GlideRecord**, which is the backbone of almost every server-side script in ServiceNow.

Almost every Business Rule, Scheduled Job, Script Include, Fix Script, UI Action, and Background Script uses GlideRecord.

Think of GlideRecord as SQL for ServiceNow.

Instead of writing SQL queries, ServiceNow developers use GlideRecord APIs.

---

# Learning Objectives

By the end of this chapter you should understand:

- What GlideRecord is
- CRUD Operations
- Querying database records
- Updating records
- Looping through records
- Common GlideRecord methods
- addActiveQuery()
- addInactiveQuery()
- addNullQuery()
- addNotNullQuery()
- addEncodedQuery()

---

# What is GlideRecord?

**Definition**

> GlideRecord is a Server-side API used to perform CRUD (Create, Read, Update and Delete) operations on ServiceNow database tables.

It allows developers to communicate directly with the database.

---

# Why do we need GlideRecord?

Suppose your company has

```
5000 Incidents
```

Some incidents have

```
Caller = Empty
```

Requirement:

Fill Abraham Lincoln as Caller for all empty records.

Can you manually open 5000 records?

No.

Instead,

one GlideRecord script can update every record automatically.

---

# Real Requirement 1

Requirement

```
Find all Incidents

↓

Caller is Empty

↓

Set Caller

=

Abraham Lincoln
```

This is one of the practical examples demonstrated during the session. The uploaded notes first show the Incident list with empty Caller fields, then the Background Script used to update them, and finally the updated records after execution. :contentReference[oaicite:1]{index=1}

---

# Real Requirement 2

Requirement

```
Catalog Tasks

↓

Description Empty

↓

Copy

Short Description

↓

Description
```

Instead of manually editing hundreds of tasks,

GlideRecord performs it automatically.

---

# Manual Method vs GlideRecord

Manual

```
Search Record

↓

Open Record

↓

Update

↓

Save

↓

Repeat

1000 Times
```

Problems

- Time consuming
- Human errors
- Not scalable

---

GlideRecord

```
Write Script

↓

Run Once

↓

1000 Records Updated
```

Much faster.

---

# Where do we practice GlideRecord?

Three windows are generally required.

```
Table List

+

Background Script

+

GlideRecord Documentation
```

Background Script

```
System Definition

↓

Scripts

↓

Background
```

The PDF illustrates this navigation with screenshots before running the example scripts. :contentReference[oaicite:2]{index=2}

---

# GlideRecord Basic Structure

```javascript
var gr = new GlideRecord('incident');
```

Meaning

```
Create

GlideRecord Object

↓

Connect

Incident Table
```

---

# Executing Query

```javascript
gr.query();
```

This executes the query against the database.

Without query(),

nothing will be fetched.

---

# Moving Record by Record

```javascript
while(gr.next())
{

}
```

Think of it like

```
Record 1

↓

Record 2

↓

Record 3

↓

Record 4

↓

...
```

Each loop processes one record.

---

# Updating Records

```javascript
gr.update();
```

This permanently saves changes into the database.

Without

```
update()
```

no data is modified.

---

# Example 1

Update Empty Caller

```javascript
var gr = new GlideRecord('incident');

gr.addEncodedQuery('caller_idISEMPTY');

gr.query();

while(gr.next())
{
gr.caller_id='a8f98bb0eb32010045e1a5115206fe3a';

gr.update();
}
```

---

# Execution Flow

```
Incident Table

↓

Find

Caller Empty

↓

Loop

↓

Update Caller

↓

Save
```

---

# Example 2

Description Empty

↓

Copy Short Description

```javascript
var gr = new GlideRecord('sc_task');

gr.addEncodedQuery('descriptionISEMPTY');

gr.query();

while(gr.next())
{

gr.description=gr.short_description;

gr.update();

}
```

---

# Understanding the Script

```javascript
var gr
```

Creates GlideRecord Object.

---

```javascript
new GlideRecord('incident')
```

Connects to Incident table.

---

```javascript
gr.query()
```

Executes database query.

---

```javascript
while(gr.next())
```

Moves through every matching record.

---

```javascript
gr.update()
```

Saves record.

---

# CRUD Operations

GlideRecord supports

```
Create

Read

Update

Delete
```

CRUD

---

# Method 1

addActiveQuery()

Definition

Returns only

```
Active = True
```

records.

Equivalent Filter

```
Active = true
```

---

Example

```
Get

All Active Incidents

↓

Update

Work Notes
```

```javascript
var gr=new GlideRecord('incident');

gr.addActiveQuery();

gr.query();

gs.print(gr.getRowCount());

while(gr.next())
{

gr.work_notes="Today is Monday - Have a Blessed Week";

gr.update();

}
```

---

# getRowCount()

Returns

```
Number

of

Records
```

Example

```
78 Incidents Found

↓

Print

78
```

```javascript
gs.print(gr.getRowCount());
```

---

# Method 2

addInactiveQuery()

Definition

Returns only

```
Active = False
```

records.

Example

```javascript
gr.addInactiveQuery();
```

Equivalent

```
Active=false
```

---

Example

```
Inactive Incidents

↓

Update

Work Notes
```

---

# Method 3

addNullQuery()

Definition

Returns records where a field is

```
NULL

or

EMPTY
```

Example

```javascript
gr.addNullQuery('problem_id');
```

Meaning

```
Problem

is Empty
```

---

Requirement

```
Problem Empty

↓

Update

Work Notes

↓

"This Incident is not attached with any Problem."
```

---

# Method 4

addNotNullQuery()

Definition

Returns records where field

contains a value.

```javascript
gr.addNotNullQuery('problem_id');
```

Equivalent

```
Problem IS NOT EMPTY
```

---

Requirement

```
Problem Exists

↓

Update

Work Notes
```

---

# Method 5

addEncodedQuery()

Most commonly used GlideRecord method.

Instead of writing many conditions,

we use a single encoded query.

Example

```
Active=True

AND

Category=Software

OR

Database

AND

Problem Empty

AND

Description Empty
```

Encoded Query

```javascript
active=true^category=software^ORcategory=database
```

---

# Another Way

Instead of directly writing

```javascript
gr.addEncodedQuery(...);
```

Store query inside a variable.

```javascript
var qry="active=true^category=software^ORcategory=database";

gr.addEncodedQuery(qry);
```

Useful

when query becomes very long.

---

# What is Encoded Query?

Encoded Query is simply

the filter copied from List View.

Example

```
State = New

Priority = High

Category = Software

↓

Right Click

↓

Copy Query

↓

Paste

Inside

addEncodedQuery()
```

---

# Where do we get Encoded Query?

```
List View

↓

Apply Filters

↓

Right Click

↓

Copy Query
```

The uploaded notes demonstrate copying the encoded query from the Incident list and then using it inside the script. :contentReference[oaicite:3]{index=3}

---

# Important GlideRecord Methods Learned

| Method | Purpose |
|---------|----------|
| query() | Execute query |
| next() | Move record |
| update() | Save record |
| getRowCount() | Count records |
| addActiveQuery() | Active=true |
| addInactiveQuery() | Active=false |
| addNullQuery() | Empty values |
| addNotNullQuery() | Non-empty values |
| addEncodedQuery() | Complex conditions |

---

# Execution Flow

```
Create GlideRecord

↓

Add Filters

↓

Execute Query

↓

Loop Records

↓

Modify Values

↓

Update Database
```

---

# Interview Questions

### What is GlideRecord?

A Server-side API used to perform CRUD operations on ServiceNow database tables.

---

### Is GlideRecord Client Side?

No.

Server Side only.

---

### Why use GlideRecord?

To retrieve, insert, update and delete records from the database.

---

### What does query() do?

Executes the database query.

---

### What does next() do?

Moves to the next matching record.

---

### Why is update() required?

Without update(),

changes are never saved.

---

### Difference between addNullQuery() and addNotNullQuery()

```
addNullQuery()

↓

Empty Values

----------------

addNotNullQuery()

↓

Non Empty Values
```

---

### What is Encoded Query?

A compact filter string copied from the List View to represent multiple query conditions.

---

# Best Practices

✅ Always filter records before updating.

✅ Avoid updating unnecessary records.

✅ Use Encoded Queries for complex filters.

✅ Use getRowCount() for debugging.

✅ Never run update scripts directly in Production without testing.

---

# Assignment

Practice all five GlideRecord methods:

- addActiveQuery()
- addInactiveQuery()
- addNullQuery()
- addNotNullQuery()
- addEncodedQuery()

Create Background Scripts for each method and verify the updated records.

---

# Key Takeaways

- GlideRecord is the foundation of all Server-side scripting in ServiceNow.
- It is used for CRUD operations on database tables.
- `query()` fetches records, `next()` iterates through them, and `update()` saves changes.
- `addActiveQuery()` filters active records.
- `addInactiveQuery()` filters inactive records.
- `addNullQuery()` returns empty fields.
- `addNotNullQuery()` returns populated fields.
- `addEncodedQuery()` allows complex filtering using encoded query strings copied from List Views.
- Mastering GlideRecord is essential for Business Rules, Script Includes, Scheduled Jobs, Fix Scripts, Background Scripts, and interviews.

---

# Official Documentation

GlideRecord API

https://www.servicenow.com/docs/r/xanadu/api-reference/server-api-reference/c_GlideRecordAPI.html

---

# Phase 2 Progress

```
✅ Day 21 - Update Set Versioning
✅ Day 22 - Backout & Deployment
✅ Day 23 - Client vs Server Scripting
✅ Day 24 - GlideForm Basics
✅ Day 25 - GlideForm Advanced Methods
✅ Day 26 - GlideForm Set Methods
✅ Day 27 - getReference() & Dot Walking
✅ Day 28 - g_user API
✅ Day 29 - onCellEdit Client Script
✅ Day 30 - Business Rules Introduction
✅ Day 31 - GlideRecord Fundamentals

Next →
GlideRecord CRUD Methods (insert(), deleteRecord(), initialize(), get()), Query Optimization, and Business Rule Coding
```

---

**End of Day 31**

