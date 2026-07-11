# 📅 Phase 2 – Day 30
# 🚀 Business Rules (Part 1) – Introduction, Types & Execution Flow

> **Date:** 15 May 2026  
> **Phase:** ServiceNow Server-Side Scripting  
> **Topic:** Introduction to Business Rules, Before vs After vs Async vs Display

---

# 📖 Introduction

Today marks the beginning of **Server-Side Scripting** in ServiceNow.

Until now, we have been working completely on the **Client Side** using:

- Client Scripts
- UI Policies
- GlideForm (g_form)
- GlideUser (g_user)

These scripts execute only inside the user's browser.

However, many real-world requirements involve:

- Updating multiple records
- Validating data before saving
- Performing background operations
- Querying the database

These operations **cannot** be handled using Client Scripts.

For these scenarios, ServiceNow provides **Business Rules (BRs)**.

Business Rules execute on the **Server**, giving developers direct access to the database and enabling powerful automation.

---

# Learning Objectives

After completing today's session, you should be able to:

- Understand what Business Rules are
- Know why Business Rules are required
- Understand Client Side vs Server Side execution
- Learn the four types of Business Rules
- Understand Before vs After vs Async
- Know when each type should be used
- Understand database transaction flow

---

# Why Business Rules?

Imagine a company with thousands of employees.

One employee reports an issue.

```
INC001001
↓

Laptop Not Booting
```

Service Desk investigates.

If many users report the same issue,

ServiceNow creates a **Problem Record**.

```
INC001001

INC001002

INC001003

INC001004

↓

Same Root Cause

↓

PRB0001025
```

Now,

suppose the Problem is resolved.

Should engineers manually close

every Incident?

No.

A Business Rule can do this automatically.

---

# Real World Example

CloudStrike Outage

```
CloudStrike Issue

↓

Airline Systems Down

↓

Thousands of Incidents

↓

One Problem Record

↓

Problem Fixed

↓

Automatically Close Every Child Incident
```

Without automation,

support engineers would manually close hundreds or thousands of Incidents.

Business Rules eliminate this repetitive work.

---

# Another Example

Password Reset

```
Employee

↓

Forgot Password

↓

Incident Raised

↓

Service Desk

↓

Password Reset
```

Many organizations automate such repetitive processes using Server-side scripting.

---

# Parent-Child Relationship

```
Problem

↓

Parent

↓

Incident

↓

Child
```

One Problem

can have

many Child Incidents.

Example

```
Problem

PRB0040128

↓

INC001001

INC001002

INC001003

INC001004

INC001005
```

---

# Business Requirement

Requirement

```
Whenever

Problem

↓

Closed

↓

Automatically

Close

All Child Incidents
```

This is one of the most common interview scenarios.

---

# Why Client Scripts Cannot Do This

Client Scripts execute

```
Browser

↓

Current Record Only
```

They

cannot

update

500 different Incident records.

Business Rules execute

```
Server

↓

Database

↓

Any Number of Records
```

Therefore,

Business Rules are used.

---

# What is a Business Rule?

**Definition**

> A **Business Rule** is a **Server-side Script** that executes whenever a record is displayed, inserted, updated, deleted or queried.

Business Rules work directly with database records.

Unlike Client Scripts,

they execute on the ServiceNow server.

---

# When do Business Rules Execute?

Business Rules execute during

```
Display

Insert

Update

Delete

Query
```

depending on their configuration.

---

# Types of Business Rules

ServiceNow provides

four major Business Rule types.

```
Business Rule

↓

Before

↓

After

↓

Async

↓

Display
```

Each executes at a different stage of the record lifecycle.

---

# Example Requirement

```
Problem

↓

Closed

↓

Automatically

Close

500 Child Incidents
```

Let's understand

how every Business Rule handles this.

---

# Database Transaction

Whenever a record is saved,

the following happens.

```
User

↓

Clicks

Update

↓

Server

↓

Database

↓

Record Saved
```

Business Rules execute around this transaction.

---

# Before Business Rule

Execution Flow

```
User

↓

Clicks Update

↓

Before Business Rule

↓

Database Update

↓

Complete
```

Meaning

The Business Rule executes

**before**

the record is saved into the database.

---

# Example

Requirement

```
Problem Closed

↓

Close 500 Incidents
```

Execution

```
Before BR

↓

Close

500 Incidents

+

Update Problem

↓

Single Transaction
```

Everything waits

until every action completes.

---

# Transaction Example

```
Problem Update

↓

1 Second

+

500 Incident Updates

↓

500 Seconds

------------------------

Total

501 Seconds
```

The user waits

until every update finishes.

---

# After Business Rule

Execution Flow

```
User

↓

Clicks Update

↓

Problem Saved

↓

After Business Rule

↓

Close Incidents
```

Difference

The Problem record is already updated.

Then

the remaining Business Rule executes.

---

# Transaction

```
Problem Update

↓

1 Second

↓

Business Rule

↓

500 Incident Updates
```

Still

a **single transaction**.

The user still waits

until everything finishes.

---

# Before vs After

```
Before

↓

Business Rule

↓

Database

---------------------

After

↓

Database

↓

Business Rule
```

Both

are synchronous.

The user waits.

---

# What is Synchronous?

```
User

↓

Wait

↓

Wait

↓

Wait

↓

Complete
```

Everything executes

one after another.

---

# Async Business Rule

Async means

```
Asynchronous
```

Execution Flow

```
User

↓

Problem Saved

↓

Immediately Complete

↓

Background Job

↓

Close

500 Incidents
```

The Problem saves immediately.

The Incident updates continue

in the background.

---

# Transaction Comparison

### Before / After

```
Transaction

↓

Problem

+

500 Incidents

↓

501 Operations
```

Single Transaction

---

### Async

```
Transaction 1

↓

Problem

↓

Complete

--------------------

Transaction 2

↓

500 Incident Updates

↓

Background
```

Two separate transactions.

---

# Milk & Biryani Example

Trainer Example

Suppose you go shopping.

Need

```
Milk

1 Minute

+

Chicken Biryani

30 Minutes
```

### Before / After

```
Wait

For Both

↓

31 Minutes
```

You receive

everything together.

---

### Async

```
Milk

↓

Immediately

↓

Go Home

↓

Chicken

Arrives Later
```

You don't wait

for the longer task.

This is exactly

how Async Business Rules work.

---

# Why Async?

Suppose

```
500 Incidents

Need Update
```

Using Before

or After

the user waits

a long time.

Using Async

```
Problem Saved

Immediately

↓

Background

Updates

500 Incidents
```

Much better user experience.

---

# Difference Between Before, After and Async

| Before | After | Async |
|---------|--------|--------|
| Runs before DB update | Runs after DB update | Runs after DB update in background |
| Synchronous | Synchronous | Asynchronous |
| User waits | User waits | User does **not** wait |
| Same transaction | Same transaction | Separate transaction |

---

# Display Business Rule

The trainer briefly introduced the fourth type.

Display Business Rules execute

```
Before

Form Opens

↓

Send Data

to Client
```

They are commonly used to pass server-side information to Client Scripts using `g_scratchpad`.

This topic will be covered in detail later.

---

# Real Project Scenarios

### Scenario 1

Problem Closed

↓

Automatically

Close Child Incidents

---

### Scenario 2

Employee Deleted

↓

Deactivate Related Records

---

### Scenario 3

Change Request Approved

↓

Create Implementation Tasks

---

### Scenario 4

Asset Retired

↓

Update Inventory Automatically

---

# Business Rule Execution Timeline

```
User

↓

Update Record

↓

Before BR

↓

Database Update

↓

After BR

↓

Async BR

↓

Background Processing
```

---

# Best Practices

✅ Use **Before** when modifying the current record before saving.

✅ Use **After** when working with related records after the current record has been saved.

✅ Use **Async** for long-running operations such as updating hundreds of records.

✅ Avoid heavy processing in synchronous Business Rules.

✅ Keep Business Rules focused on one business requirement.

---

# Common Interview Questions

### Q1. What is a Business Rule?

A server-side script that executes when a record is displayed, inserted, updated, deleted or queried.

---

### Q2. Is a Business Rule Client Side or Server Side?

Server Side.

---

### Q3. Name the four types of Business Rules.

- Before
- After
- Async
- Display

---

### Q4. Difference between Before and After Business Rules?

Before executes before the database update.

After executes after the database update.

---

### Q5. Difference between After and Async?

Both execute after the record is saved.

The difference is:

- After → User waits.
- Async → Executes in the background; the user does not wait.

---

### Q6. Why use Async Business Rules?

To avoid delays caused by long-running server operations.

---

### Q7. Which Business Rule is best for updating 500 related records?

Async Business Rule.

---

### Q8. Can Client Scripts update hundreds of records in the database?

No.

Only Server-side scripts such as Business Rules can perform such operations.

---

# Assignment

Understand the following:

- Difference between Before and After Business Rules.
- Difference between Synchronous and Asynchronous execution.
- Draw the execution flow of:
  - Before Business Rule
  - After Business Rule
  - Async Business Rule

Think about the following requirement:

> **When a Problem is Closed, automatically close all Child Incidents.**

Which type of Business Rule would you choose and why?

---

# Key Takeaways

- Business Rules are Server-side scripts that execute during database operations.
- They are used for automation, validation and updating related records.
- Before Business Rules execute before the record is saved.
- After Business Rules execute after the record is saved but still keep the user waiting.
- Async Business Rules execute in the background, improving performance for long-running tasks.
- Display Business Rules are used to send server-side data to the client before the form loads.
- Understanding Business Rule execution order is one of the most frequently asked ServiceNow interview topics.

---

# Official Reference

**Business Rules**

https://www.servicenow.com/docs/bundle/xanadu-application-development/page/script/business-rules/concept/c_BusinessRules.html

---

# Phase 2 Progress

```
✅ Day 21 - Update Set Versioning & Deployment
✅ Day 22 - Backout Process & Best Practices
✅ Day 23 - Client vs Server Scripting
✅ Day 24 - GlideForm Basics
✅ Day 25 - Advanced GlideForm Methods
✅ Day 26 - GlideForm Set Family
✅ Day 27 - getReference() & Dot Walking
✅ Day 28 - Templates, isTemplate & g_user
✅ Day 29 - onCellEdit Client Scripts
✅ Day 30 - Introduction to Business Rules (Before, After, Async & Display)

Next →
Business Rules Practical Implementation, Conditions, current & previous Objects, GlideRecord Introduction
```

---

**End of Day 30**
