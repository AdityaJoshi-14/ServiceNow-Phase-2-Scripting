# 📘 Day 41 – ServiceNow Best Practices & Phase 2 Revision

**Date:** 01/06/2026

---

# 📖 Introduction

Today's session was dedicated to **Phase 2 Revision** and **ServiceNow Development Best Practices**.

Unlike previous classes where we focused on implementing new features, this session emphasized **writing clean, maintainable, scalable, and performant ServiceNow code**.

The instructor reviewed all major scripting topics covered in Phase 2 and discussed common mistakes developers make during projects. Most of these best practices are based on real-world enterprise development standards and are frequently discussed in ServiceNow interviews.

The topics revised include:

- Client Scripts
- g_form API
- g_user API
- Business Rules
- GlideRecord
- UI Policies
- Script Includes
- GlideAjax
- ServiceNow Client-Server Architecture
- Performance Optimization

---

# 🎯 Learning Objectives

After completing this session, you should be able to:

- Follow ServiceNow scripting best practices.
- Write clean and maintainable code.
- Choose the correct scripting component.
- Improve application performance.
- Avoid unnecessary scripting.
- Reduce server load.
- Build reusable server-side logic.
- Prevent recursive Business Rules.

---

# Phase 2 Revision

The following topics were revised:

```
Phase 2

↓

Client Scripts

↓

g_form

↓

onChange()

↓

g_user

↓

Business Rules

↓

GlideRecord
```

These topics form the foundation of ServiceNow scripting and are commonly tested during technical interviews.

---

# ServiceNow Architecture Revision

Throughout Phase 2, we have worked with both Client-side and Server-side scripting.

```
Browser (Client)

↓

HTML

↓

CSS

↓

JavaScript

↓

HTTP Request

↓

ServiceNow Server

↓

Business Rules

↓

GlideRecord

↓

Database
```

Understanding where code executes is essential for choosing the correct scripting component.

---

# Why Reduce Scripts?

One important discussion today was:

> Every script adds processing overhead.

Every additional script means:

- More memory usage
- More processing time
- More maintenance
- More debugging effort

Therefore,

**Always use scripting only when necessary.**

---

# ServiceNow Best Practices

## 1. Use Comments

Always document important logic.

Instead of

```javascript
gr.update();
```

Prefer

```javascript
// Update all child incidents with parent's description
gr.update();
```

Benefits:

- Easier maintenance
- Better readability
- Faster debugging
- Helpful for future developers

---

# 2. UI Policy vs Client Script

Whenever possible,

Prefer

```
UI Policy

↓

instead of

↓

Client Script
```

Why?

UI Policies are:

- Easier to configure
- No coding required
- Faster to maintain
- Better for simple field behaviors

Examples

✔ Mandatory

✔ Read Only

✔ Visible

---

## When Should Client Scripts Be Used?

Use Client Scripts only when business logic cannot be achieved using UI Policies.

Examples

- Complex validations
- Dynamic calculations
- Server communication
- Multiple field dependencies

---

# Which Runs First?

One interview question discussed today:

```
UI Policy

vs

Client Script
```

Generally,

```
Client Script

↓

UI Policy
```

Both execute during form interactions, but UI Policies eventually enforce field properties such as Mandatory, Visible, and Read Only.

**Interview Tip:** Know the execution behavior and avoid implementing the same logic in both UI Policies and Client Scripts.

---

# 3. Avoid Scripts Inside UI Policies

UI Policies support:

```
UI Policy Actions
```

Whenever possible,

avoid writing

```
UI Policy Scripts
```

Reason:

- Harder to maintain
- More scripting
- Increased complexity

Always prefer:

```
Condition

↓

UI Policy Action
```

instead of

```
Condition

↓

UI Policy Script
```

---

# 4. Avoid Global Client Scripts

Never create

```
Table = Global
```

Client Scripts unless absolutely necessary.

Why?

A Global Client Script executes on **every form**.

Example

```
Incident

↓

Runs

Problem

↓

Runs

Change

↓

Runs

User

↓

Runs
```

This unnecessarily increases client-side processing.

Instead,

attach Client Scripts only to the required table.

---

# 5. Avoid Global Business Rules

The same principle applies to Business Rules.

Avoid

```
Table = Global
```

Business Rules.

Global Business Rules execute for every table transaction, increasing server load and reducing performance.

Always scope Business Rules to the required table.

---

# 6. Avoid DOM Manipulation

Do not manipulate HTML elements directly inside Client Scripts.

Avoid

```javascript
document.getElementById()

document.forms

innerHTML

style.display
```

Reason:

- Unsupported
- Breaks after upgrades
- Difficult to maintain
- UI changes may stop the script

Always use

```
g_form
```

instead.

Examples

```javascript
g_form.setValue()

g_form.setMandatory()

g_form.setDisplay()

g_form.setReadOnly()
```

---

# 7. Avoid getReference()

Today's recommendation:

Instead of

```
getReference()
```

Prefer

```
Script Include

+

GlideAjax
```

Why?

getReference()

- Retrieves the complete record
- Higher network traffic
- More processing

GlideAjax

- Retrieves only required data
- Faster
- Better performance
- Less bandwidth

---

# 8. If Using getReference(), Use Callback

Wrong

```javascript
var usr = g_form.getReference('caller_id');
```

Preferred

```javascript
g_form.getReference('caller_id', function(user){

    alert(user.email);

});
```

Using callbacks prevents blocking the browser while waiting for server data.

---

# Business Rule Best Practices

---

## Avoid Global Business Rules

Only attach Business Rules to the required table.

---

## Business Rule vs Script Include

Whenever reusable logic is required,

prefer

```
Script Include
```

instead of duplicating Business Rule code.

Example

```
Business Rule

↓

Script Include

↓

Reusable Function

↓

Called Anywhere
```

Advantages

- Code reuse
- Easier maintenance
- Better organization
- Reduced duplication

---

## Display Business Rule vs Script Include

If multiple Business Rules require the same logic,

move the logic into a Script Include.

Example

```
Display BR

↓

Script Include

↓

Function

↓

Reusable
```

---

## Write More Functions

Instead of

```javascript
1000 Lines

Inside Business Rule
```

Prefer

```
Business Rule

↓

Function 1

↓

Function 2

↓

Function 3
```

Smaller reusable functions improve readability and maintenance.

---

## Always Write Proper Conditions

Bad

```
Business Rule

↓

Runs

Every Update
```

Good

```
Business Rule

↓

Only When

↓

State Changes

↓

Priority Changes

↓

Assignment Group Changes
```

Proper conditions improve performance by avoiding unnecessary execution.

---

## Prefer Async Business Rules

Whenever immediate execution is not required,

prefer

```
Async Business Rule
```

instead of

```
Before

or

After
```

Reason

```
User Save

↓

Immediate Response

↓

Background Processing
```

This reduces user wait time.

---

# Avoid current.update()

One of the most important best practices discussed today.

Avoid

```javascript
current.update();
```

inside a Business Rule.

Why?

```
Business Rule

↓

current.update()

↓

Business Rule Again

↓

current.update()

↓

Business Rule Again

↓

Infinite Loop
```

This creates **recursive execution**.

---

# If Required

If absolutely necessary,

disable workflow before updating.

```javascript
current.setWorkflow(false);

current.update();
```

This prevents recursive Business Rule execution.

However,

**Best Practice:** redesign the logic so `current.update()` is not needed inside Business Rules.

---

# Summary of Best Practices

| Recommendation | Reason |
|----------------|--------|
| Use Comments | Better readability |
| Prefer UI Policy | Less scripting |
| Avoid UI Policy Scripts | Easier maintenance |
| Avoid Global Client Scripts | Better performance |
| Avoid Global Business Rules | Reduce server load |
| Avoid DOM Manipulation | Upgrade-safe code |
| Prefer GlideAjax over getReference() | Better performance |
| Use Callback with getReference() | Non-blocking execution |
| Use Script Includes | Code reusability |
| Write Reusable Functions | Cleaner architecture |
| Add Proper Conditions | Prevent unnecessary execution |
| Prefer Async Business Rules | Faster user response |
| Avoid current.update() | Prevent recursion |

---

# Interview Questions

## Why should UI Policies be preferred over Client Scripts?

Because UI Policies require less scripting, are easier to maintain, and are ideal for field-level behavior such as Mandatory, Visible, and Read Only.

---

## Why avoid Global Client Scripts?

Because they execute on every form, increasing unnecessary client-side processing.

---

## Why avoid DOM Manipulation?

DOM changes are unsupported, may break after upgrades, and bypass the ServiceNow API.

---

## Why is GlideAjax preferred over getReference()?

GlideAjax returns only the required data, reducing network traffic and improving performance.

---

## Why avoid current.update() inside a Business Rule?

Because it can trigger the same Business Rule repeatedly, causing recursive execution and potential infinite loops.

---

## When should Async Business Rules be used?

When processing can occur after the user receives a response and immediate execution is not required.

---

# Key Takeaways

- Use scripting only when necessary.
- Prefer configuration over customization whenever possible.
- UI Policies are generally preferred over Client Scripts for field behavior.
- Avoid Global scripts to improve performance.
- Use GlideAjax instead of getReference() for efficient client-server communication.
- Reuse code through Script Includes and functions.
- Always define Business Rule conditions properly.
- Prefer Async Business Rules when appropriate.
- Never use `current.update()` inside Business Rules unless absolutely necessary, and only with proper safeguards.

---

## ✅ End of Day 41 Notes

