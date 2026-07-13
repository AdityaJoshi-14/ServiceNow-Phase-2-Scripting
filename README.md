# 📘 Day 42 – Asynchronous Business Rules & ServiceNow Best Practices

**Date:** 02/06/2026

---

# 📖 Introduction

Today's session focused on understanding **Asynchronous Business Rules (Async BR)**, their advantages over traditional **After Business Rules**, and important ServiceNow development best practices.

The primary business scenario implemented was automatically cancelling all **Child Incidents** whenever a **Parent Incident** is cancelled. During this implementation, we learned why proper Business Rule conditions are critical and why **Async Business Rules** are preferred for long-running operations.

The session also covered the differences between synchronous and asynchronous processing, limitations of Async Business Rules, Background Scripts, Scheduled Jobs, Fix Scripts, Update Sets, and real-world deployment considerations.

---

# 🎯 Learning Objectives

After completing this session, you should be able to:

- Understand Async Business Rules.
- Differentiate Between After BR and Async BR.
- Configure proper Business Rule conditions.
- Understand current vs previous objects.
- Know Async Business Rule limitations.
- Differentiate Background Scripts, Scheduled Jobs, and Fix Scripts.
- Understand Update Set limitations.
- Apply ServiceNow development best practices.

---

# Importance of Proper Conditions

One of the biggest performance improvements in ServiceNow is writing **proper Business Rule conditions**.

Instead of executing a Business Rule on every update,

execute it **only when required**.

---

## Wrong Approach

```
Incident Updated

↓

Business Rule Runs

↓

Every Update
```

Every modification triggers the Business Rule, even if the State has not changed.

---

## Correct Approach

```
Incident

↓

State Changes

↓

Cancelled

↓

Business Rule Executes
```

The Business Rule executes only when the required condition is satisfied.

---

# Business Scenario

## Requirement

Whenever a **Parent Incident** is cancelled,

Automatically cancel every **Child Incident**.

---

## Incorrect Condition

```
State = Cancelled
```

Why?

If the record is already Cancelled and someone updates another field,

the Business Rule executes again unnecessarily.

---

## Correct Condition

```
State Changes To

Cancelled
```

This ensures the Business Rule executes **only once**, exactly when the state changes.

---

# Execution Flow

```text
Parent Incident

↓

State Changes

↓

Cancelled

↓

Async Business Rule

↓

Find Child Incidents

↓

Cancel Child Incidents

↓

Update Records
```

---

# Why Async Business Rule?

Imagine

```
Parent Incident

↓

500 Child Incidents
```

Cancelling every child may take time.

If an **After Business Rule** is used,

```
User Clicks Save

↓

Wait

↓

Update Parent

↓

Update 500 Child Incidents

↓

Response Returned
```

The user waits until every child record is processed.

---

# Async Business Rule

With Async Business Rules

```
User Clicks Save

↓

Parent Saved

↓

User Gets Response

↓

Background Processing Starts

↓

Cancel Child Incidents
```

The user does **not** wait.

This significantly improves user experience.

---

# After Business Rule vs Async Business Rule

| After Business Rule | Async Business Rule |
|----------------------|---------------------|
| Runs immediately after save | Runs in background |
| User waits | User does not wait |
| Suitable for immediate processing | Suitable for long-running operations |
| Slower for bulk updates | Better performance |

---

# Synchronous vs Asynchronous

## Synchronous

```
Request

↓

Wait

↓

Process

↓

Return Response
```

Everything happens in sequence.

---

## Asynchronous

```
Request

↓

Immediate Response

↓

Background Processing
```

The user continues working while processing completes.

---

# GlideAjax Comparison

The same concept applies to GlideAjax.

## Asynchronous

```javascript
ga.getXML();
```

The browser continues working while waiting for the server response.

---

## Synchronous

```javascript
ga.getXMLWait();
```

The browser waits until the server returns a response.

Because synchronous execution blocks the browser,

**always prefer**

```javascript
ga.getXML();
```

---

# Async Business Rule Limitation

One important limitation discussed today:

```
current

↓

Available

✔
```

```
previous

↓

Not Available

✖
```

Business Rule template

```javascript
(function executeRule(current, previous /* null when async */){
```

Notice

```
previous

=

null
```

inside Async Business Rules.

---

# Why?

Async Business Rules execute **after** the original transaction has already completed.

The previous version of the record is no longer available.

---

# current vs previous

| Object | Before | After | Async |
|----------|--------|--------|-------|
| current | ✔ | ✔ | ✔ |
| previous | ✔ | ✔ | ✖ |

---

# One-Time Scripts

Today's session also discussed different methods for executing server-side scripts.

---

# 1. Background Script

Purpose

```
Run Once
```

Characteristics

- Temporary execution
- No record created
- Used for testing
- Used for one-time data fixes
- Not reusable

Typical Uses

- Data cleanup
- Bulk updates
- Testing GlideRecord scripts

---

# 2. Scheduled Job

Purpose

```
Repeated Execution

OR

On Demand
```

Characteristics

- Record is created
- Can execute Daily
- Weekly
- Monthly
- Yearly
- On Demand

Example

```
Deactivate Users

↓

Every Month
```

---

# Important Note

Scheduled Jobs

```
DO NOT

capture

inside

Update Sets
```

Therefore,

they must be recreated or migrated separately.

---

# 3. Fix Script

Purpose

```
One-Time

Deployment Script
```

Usually executed during

- Production fixes
- Data correction
- Upgrade support
- Migration activities

Unlike Background Scripts,

Fix Scripts become part of the application and can be executed when required.

---

# Comparison

| Type | Record Created | Reusable |
|------|---------------|----------|
| Background Script | No | No |
| Scheduled Job | Yes | Yes |
| Fix Script | Yes | Yes |

---

# Development Lifecycle

ServiceNow development follows

```
Developer

↓

DEV

↓

TEST

↓

PRODUCTION
```

Never perform development directly in Test or Production.

---

# Clone & Upgrade

Two important platform operations were discussed.

## Clone

```
Production

↓

Copied To

↓

Test

↓

Copied To

↓

Development
```

Clone copies data and configuration from one instance to another.

---

## Upgrade

```
Old Release

↓

Upgrade

↓

New Release
```

Example

```
Washington

↓

Xanadu

↓

Yokohama
```

Always validate customizations after an upgrade.

---

# Best Practices

- Always write proper Business Rule conditions.
- Use **Changes To** instead of checking only field values.
- Prefer Async Business Rules whenever immediate processing is not required.
- Avoid synchronous processing whenever possible.
- Prefer `ga.getXML()` over `ga.getXMLWait()`.
- Never depend on the `previous` object inside Async Business Rules.
- Use Background Scripts only for temporary execution.
- Use Scheduled Jobs for recurring activities.
- Remember that Scheduled Jobs are **not captured in Update Sets**.
- Test asynchronous logic thoroughly before deployment.

---

# Interview Questions

## Why are Async Business Rules preferred?

Because they execute in the background and improve user experience by avoiding delays during record updates.

---

## Difference between After and Async Business Rules?

After Business Rules execute immediately after the transaction and the user waits for completion.

Async Business Rules execute in the background after the transaction has completed.

---

## Why should "State Changes To Cancelled" be used instead of "State = Cancelled"?

Because "Changes To" executes only when the state actually changes, preventing unnecessary Business Rule execution.

---

## Can the previous object be used in Async Business Rules?

No.

`previous` is always **null** in Async Business Rules.

---

## Difference between getXML() and getXMLWait()?

| getXML() | getXMLWait() |
|-----------|--------------|
| Asynchronous | Synchronous |
| Does not block browser | Blocks browser |
| Preferred | Avoid unless necessary |

---

## Difference between Background Script and Scheduled Job?

Background Scripts execute only once and create no record.

Scheduled Jobs create records and execute repeatedly or on demand.

---

## Are Scheduled Jobs captured in Update Sets?

No.

They must be recreated or migrated separately.

---

# Key Takeaways

- Always configure proper Business Rule conditions.
- Use **Changes To** conditions instead of simple value checks.
- Async Business Rules improve performance by processing records in the background.
- `previous` is unavailable inside Async Business Rules.
- Prefer asynchronous APIs such as `ga.getXML()`.
- Background Scripts are temporary, while Scheduled Jobs and Fix Scripts are reusable.
- Scheduled Jobs are not included in Update Sets.
- Understanding synchronous vs asynchronous execution is essential for building scalable ServiceNow applications.

---

## ✅ End of Day 42 Notes

