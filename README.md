# 📅 Phase 2 – Day 27
# 🚀 Client Scripts (Part 5) – getReference(), Dot Walking & onChange Parameters

> **Date:** 12 May 2026  
> **Phase:** ServiceNow Scripting  
> **Topic:** getReference(), Dot Walking, Auto Population, onChange Parameters & Client Script Execution

---

# 📖 Introduction

Today's session introduced one of the most important Client Side APIs in ServiceNow:

# **g_form.getReference()**

Until now we have learned:

- Get Family (Read Values)
- Set Family (Update Values)

Today's topic combines both concepts.

We learned how to:

- Retrieve an entire reference record
- Access multiple fields using Dot Walking
- Automatically populate fields
- Understand every parameter of an onChange Client Script
- Learn how onChange actually executes internally

This topic is heavily used in real ServiceNow development because reference fields are present almost everywhere (Requested For, Caller, Assignment Group, Manager, Department, etc.). The uploaded notes build the example around auto-populating user details from the **Requestor** reference field. :contentReference[oaicite:0]{index=0}

---

# Learning Objectives

After today's session you should be able to:

- Understand getReference()
- Perform Dot Walking
- Auto populate fields from a reference field
- Differentiate getValue() and getReference()
- Understand every parameter of an onChange Client Script
- Know when an onChange Client Script executes
- Build reusable Client Scripts

---

# Revision

Previously we studied

## Get Family

```
getValue()

getUniqueValue()

getTableName()
```

These methods only return values.

Today we studied

```
getReference()
```

which returns an **entire record**.

---

# Why do we need getReference()?

Imagine the Requestor field.

```
Requestor

↓

Abraham Lincoln
```

Using

```javascript
g_form.getValue('u_requestor');
```

returns

```
46d44....

(Sys ID)
```

Only the Sys ID is returned.

We cannot directly get

- First Name
- Last Name
- Email
- Manager
- Department

This is where **getReference()** becomes useful. The notes explicitly demonstrate that `getValue()` only returns the Sys ID, while `getReference()` retrieves the referenced record. :contentReference[oaicite:1]{index=1}

---

# What is getReference()?

`getReference()` retrieves the complete referenced record from the server.

Syntax

```javascript
g_form.getReference('reference_field');
```

Example

```javascript
var rq =
g_form.getReference('u_requestor');
```

Now

```
rq

↓

Entire User Record
```

---

# Client → Server Flow

```
Client Script

↓

getReference()

↓

Server

↓

User Record

↓

Client Script

↓

Dot Walking
```

Unlike `getValue()`, `getReference()` makes a server call to fetch additional information.

---

# Dot Walking

Once we receive the record,

we can access every field inside it.

```
rq.first_name

rq.last_name

rq.email

rq.manager

rq.department
```

This is called

# Dot Walking

The uploaded notes introduce `rq` as the object returned by `getReference()` and then access multiple fields using dot notation. :contentReference[oaicite:2]{index=2}

---

# Example

```javascript
var rq =
g_form.getReference('u_requestor');

alert(rq.first_name);

alert(rq.last_name);

alert(rq.manager);

alert(rq.email);

alert(rq.department);
```

Output

```
Abraham

Lincoln

Chris Harris

abraham@example.com

IT
```

---

# Auto Population Requirement

Business Requirement

```
User selects

↓

Requestor

↓

Automatically Populate

↓

First Name

Last Name

Email

Manager

Department
```

Instead of asking users to fill these details manually,

the Client Script fills them automatically.

This auto-population scenario is the primary practical exercise in today's class. :contentReference[oaicite:3]{index=3}

---

# Auto Populate Client Script

```javascript
var rq =
g_form.getReference('u_requestor');

g_form.setValue(
'u_first_name',
rq.first_name);

g_form.setValue(
'u_last_name',
rq.last_name);

g_form.setValue(
'u_email',
rq.email);

g_form.setValue(
'u_manager',
rq.manager);

g_form.setValue(
'u_department',
rq.department);
```

---

# Result

Before

```
Requestor

↓

Abraham Lincoln

First Name

Empty

Last Name

Empty

Email

Empty
```

After selecting Requestor

```
Requestor

↓

Abraham Lincoln

↓

Automatically

↓

First Name

Abraham

↓

Last Name

Lincoln

↓

Email

abraham@example.com

↓

Manager

Chris Harris

↓

Department

IT
```

The screenshots in the uploaded PDF show the form being populated immediately after the Requestor is selected. :contentReference[oaicite:4]{index=4}

---

# getValue() vs getReference()

| getValue() | getReference() |
|------------|----------------|
| Returns field value | Returns complete referenced record |
| Faster | Slightly slower |
| No server lookup | Server call required |
| Cannot Dot Walk | Supports Dot Walking |

Example

### getValue()

```javascript
var user =
g_form.getValue('u_requestor');
```

Output

```
46d4d5...
```

---

### getReference()

```javascript
var user =
g_form.getReference('u_requestor');
```

Output

```
Entire User Record
```

---

# Getting Server Data to Client

Trainer discussed three common approaches.

```
Server

↓

Client
```

Methods

- getReference()
- Display Business Rule
- Script Include

Among these,

today's focus was

```
getReference()
```

---

# onChange Client Script Parameters

Every onChange Client Script receives

five parameters.

```javascript
function onChange(
control,
oldValue,
newValue,
isLoading,
isTemplate)
{

}
```

Understanding these parameters is important for interviews and debugging. The uploaded notes dedicate several screenshots to displaying each parameter value. :contentReference[oaicite:5]{index=5}

---

# Parameter 1

## control

Represents the HTML element that triggered the event.

Example

```javascript
alert(control);
```

Output

```
[object HTMLInputElement]
```

Useful when working with DOM-related functionality.

---

# Parameter 2

## oldValue

Previous value of the field.

Example

```
Old

↓

Laptop

↓

New

↓

Desktop
```

Output

```
Laptop
```

---

# Parameter 3

## newValue

Current value after user modification.

Output

```
Desktop
```

---

# Parameter 4

## isLoading

Boolean value.

```
true

↓

Form Loading

false

↓

User Changed Field
```

Almost every onChange Client Script starts with

```javascript
if(isLoading)
{
    return;
}
```

to prevent unnecessary execution during form load.

---

# Parameter 5

## isTemplate

Returns

```
true

↓

Template Applied

false

↓

Normal Form
```

Used when forms are populated using templates.

---

# Parameter Demonstration

```javascript
alert("Control : " + control);

alert("Old Value : " + oldValue);

alert("New Value : " + newValue);

alert("Loading : " + isLoading);

alert("Template : " + isTemplate);
```

The trainer demonstrated these alerts individually to explain what each parameter contains. :contentReference[oaicite:6]{index=6}

---

# onChange Execution

Important Interview Question

Every onChange Client Script executes at two different times.

```
1.

Form Loads

↓

Runs

-------------------

2.

Field Value Changes

↓

Runs Again
```

This behavior is highlighted at the end of the uploaded notes. :contentReference[oaicite:7]{index=7}

---

# Why do we use

```javascript
if(isLoading)
{
    return;
}
```

Because

during form loading,

the script executes once automatically.

Usually,

we only want the logic to run after the user changes the field.

---

# Real Project Scenario

Employee selects

```
Requestor

↓

Rajesh Patil
```

Automatically

```
First Name

Rajesh

↓

Last Name

Patil

↓

Email

rajesh@example.com

↓

Manager

Chris Harris

↓

Department

IT
```

No manual typing required.

---

# Complete Flow

```
User

↓

Selects Requestor

↓

onChange Executes

↓

getReference()

↓

Fetch User Record

↓

Dot Walking

↓

setValue()

↓

Auto Populate Fields
```

---

# Best Practices

✅ Use `getReference()` only when multiple fields are required.

✅ Use `getValue()` when only the Sys ID or field value is needed.

✅ Always check `isLoading` inside onChange scripts.

✅ Keep Client Scripts focused on a single business requirement.

✅ Use meaningful variable names such as `requestor`, `caller`, or `manager`.

---

# Common Interview Questions

### Q1. What is getReference()?

Returns the complete referenced record from the server.

---

### Q2. What is Dot Walking?

Accessing fields of a referenced record using dot notation.

Example

```javascript
rq.first_name
```

---

### Q3. Difference between getValue() and getReference()?

`getValue()`

Returns only the field value.

`getReference()`

Returns the complete record.

---

### Q4. Why is getReference() slower?

Because it performs a server call to retrieve the referenced record.

---

### Q5. Name three methods to retrieve server-side information on the client.

- getReference()
- Display Business Rule
- Script Include

---

### Q6. Name all parameters of an onChange Client Script.

- control
- oldValue
- newValue
- isLoading
- isTemplate

---

### Q7. When does an onChange Client Script execute?

- During form loading
- Whenever the field value changes

---

### Q8. Why do we check `isLoading`?

To prevent the script from running unnecessarily while the form is loading.

---

# Assignment

Practice the following:

### 1. Auto Populate Script

Create a Client Script that:

- Reads Requestor
- Auto-populates
  - First Name
  - Last Name
  - Email
  - Manager
  - Department

using `getReference()`.

---

### 2. Practice onChange Parameters

Display alerts for:

- control
- oldValue
- newValue
- isLoading
- isTemplate

Understand how their values change during form load and user interaction.

---

# Key Takeaways

- `getReference()` retrieves an entire referenced record, unlike `getValue()` which only returns a field value.
- Dot Walking allows access to fields like `first_name`, `email`, and `department` from the referenced record.
- Combining `getReference()` with `setValue()` enables automatic field population.
- Every onChange Client Script executes twice: once during form load and again when the field value changes.
- Understanding `control`, `oldValue`, `newValue`, `isLoading`, and `isTemplate` is essential for debugging and interviews.
- Use `isLoading` checks to avoid unnecessary execution during form initialization.

---

# Official Reference

**GlideForm (g_form) API**

https://www.servicenow.com/docs/bundle/xanadu-api-reference/page/app-store/dev_portal/API_reference/GlideForm/concept/c_GlideFormAPI.html

---

# Phase 2 Progress

```
✅ Day 21 - Update Set Versioning & Deployment
✅ Day 22 - Backout Process & Best Practices
✅ Day 23 - Client vs Server Scripting
✅ Day 24 - GlideForm Basics & Decorations
✅ Day 25 - Advanced g_form Methods & Dynamic Choices
✅ Day 26 - GlideForm Set Family
✅ Day 27 - getReference(), Dot Walking & onChange Parameters

Next →
Advanced Client Scripting, GlideAjax, Asynchronous Calls & Client-Server Communication
```

---

**End of Day 27**
