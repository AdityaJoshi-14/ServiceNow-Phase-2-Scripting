# 📅 Phase 2 – Day 25
# 🚀 Client Scripts (Part 3) – Advanced GlideForm (g_form), Choice Manipulation & Dynamic Form Behavior

> **Date:** 09 May 2026  
> **Phase:** ServiceNow Scripting  
> **Topic:** Advanced Client Scripts, GlideForm API, Dynamic Choices, Attachments & Field Information

---

# 📖 Introduction

Today's session expanded our understanding of **Client Scripts** by introducing more powerful methods available in the **GlideForm (g_form)** API.

Previously, we learned how to:

- Read field values
- Display messages
- Add decorations
- Remove decorations

Today we learned how to make forms even more dynamic by:

- Enabling and disabling attachments
- Dynamically adding and removing choice values
- Reading field information
- Reading table information
- Reading record sys_ids
- Understanding JavaScript comparison operators
- Practicing JavaScript execution directly from the browser
- Creating state-based Client Scripts

This session moves from simple validation to building **interactive ServiceNow forms**. :contentReference[oaicite:0]{index=0}

---

# Learning Objectives

After completing this session you should be able to:

- Write multiple `if-else if` conditions
- Understand JavaScript comparison operators
- Enable and disable attachments dynamically
- Add and remove Choice field options
- Read any field value using GlideForm
- Get current table name
- Get current record Sys ID
- Execute JavaScript directly inside ServiceNow
- Build state-based Client Scripts

---

# Revision – JavaScript if else

The trainer emphasized that **good scripting starts with good programming logic**.

Simple Example

```javascript
if(condition)
{
    // execute
}
else
{
    // execute
}
```

---

# Multiple Conditions

Many business requirements require checking several values.

Example

```javascript
if(state=="Canceled")
{
    Cancel Activities;
}

else if(state=="In Progress")
{
    Continue Work;
}

else if(state=="On Hold")
{
    Pause Work;
}

else if(state=="Resolved")
{
    Close Activities;
}

else
{
    Default Logic;
}
```

---

# Real Project Example

Suppose an Incident moves through different states.

```
New

↓

In Progress

↓

On Hold

↓

Resolved

↓

Closed

↓

Cancelled
```

Every state requires different business logic.

Instead of creating many Client Scripts,

one Client Script with multiple **else-if** conditions can handle everything.

---

# Development Philosophy

The trainer repeatedly emphasized

```
Learn

↓

Do

↓

Practice

↓

Repeat
```

Instead of only watching videos,

practice every script immediately.

---

# JavaScript Console Shortcut

A very useful shortcut for testing JavaScript.

```
Ctrl + Alt + Shift + J
```

This opens the **JavaScript Executor** inside ServiceNow.

Advantages

- Test code instantly
- No need to save Client Script
- Faster debugging
- Better learning experience

The uploaded notes demonstrate using the JavaScript Executor to run snippets directly against the current form. :contentReference[oaicite:1]{index=1}

---

# GlideForm getValue()

Used to read the value of any field.

Syntax

```javascript
g_form.getValue('field_name');
```

Examples

```javascript
alert(g_form.getValue('u_number'));

alert(g_form.getValue('u_requestor'));

alert(g_form.getValue('u_type_of_asset'));

alert(g_form.getValue('u_is_it_replacement_request'));

alert(g_form.getValue('u_description'));

alert(g_form.getValue('u_asset_owners'));

alert(g_form.getValue('u_ticket_state'));
```

---

# Practical Use

Suppose we need to know whether the request is a replacement request.

```javascript
var replacement =
g_form.getValue('u_is_it_replacement_request');
```

If value equals

```
true
```

Perform one activity.

Otherwise perform another activity.

---

# Complete Requirement

Requirement

```
If Replacement Request

TRUE

↓

Enable Attachments

Add Choice

Show Green Icon

Display Information

Else

↓

Disable Attachments

Remove Choice

Show Red Icon

Display Error
```

---

# Enabling Attachments

Method

```javascript
g_form.enableAttachments();
```

Purpose

Allows users to upload files.

Example

```
Employee

↓

Raises Laptop Request

↓

Can Attach Invoice

↓

Can Attach Warranty
```

---

# Disabling Attachments

Method

```javascript
g_form.disableAttachments();
```

Purpose

Prevents users from uploading attachments.

Real Scenario

```
Replacement Request = FALSE

↓

Attachment Option Hidden
```

The requirement and screenshots showing attachment enable/disable behavior are explained on the first pages of the uploaded notes. :contentReference[oaicite:2]{index=2}

---

# Adding Choice Options

GlideForm provides

```javascript
g_form.addOption()
```

Syntax

```javascript
g_form.addOption(
'field',
'value',
'label',
'index'
);
```

Example

```javascript
g_form.addOption(
'u_ticket_state',
'ch1',
'Replacement Choice',
2
);
```

Meaning

```
Choice Field

↓

New Option Added

↓

Displayed at Position 2
```

The uploaded PDF walks through adding dynamic choices and explains the meaning of each parameter in the GlideForm API. :contentReference[oaicite:3]{index=3}

---

# Removing Choice Options

Method

```javascript
g_form.removeOption()
```

Example

```javascript
g_form.removeOption(
'u_ticket_state',
'ch2'
);
```

Purpose

Remove unnecessary options dynamically.

Example

```
Replacement TRUE

↓

Show

Replacement Choice

Hide

No Replacement Choice
```

Without removing previous choices,

both options remain visible.

The practical screenshots illustrate why `removeOption()` is required after dynamically adding options. :contentReference[oaicite:4]{index=4}

---

# clearOptions()

Method

```javascript
g_form.clearOptions();
```

Purpose

Removes **every option** from a Choice field.

Example

```
Ticket State

↓

EMPTY
```

⚠️ **Warning**

The trainer specifically advised **not** to use this method in production because it removes all available choices, which can confuse users or break functionality. :contentReference[oaicite:5]{index=5}

---

# Decorations & Messages

When TRUE

```javascript
g_form.addDecoration(...);

g_form.addInfoMessage(...);
```

When FALSE

```javascript
g_form.removeDecoration(...);

g_form.addDecoration(...);

g_form.addErrorMessage(...);
```

Always clear previous messages before displaying new ones.

```javascript
g_form.clearMessages();
```

---

# Reading Record Information

ServiceNow provides additional methods.

---

## Get Current Record Sys ID

```javascript
g_form.getUniqueValue();
```

Output

```
07a169f283c4031095e89cb6feaad3d3
```

Every record has a unique Sys ID.

Even a newly opened record receives a generated Sys ID before it is saved, as demonstrated in the practical examples. :contentReference[oaicite:6]{index=6}

---

## Get Current Table Name

```javascript
g_form.getTableName();
```

Output

```
u_asset_request_management
```

Useful when writing reusable Client Scripts.

---

# JavaScript Comparison Operators

Very Important Interview Topic.

---

## Assignment Operator

```javascript
=
```

Assigns a value.

Example

```javascript
a = 10;
```

---

## Equality Operator

```javascript
==
```

Checks only value.

Example

```javascript
10 == "10"

TRUE
```

---

## Strict Equality

```javascript
===
```

Checks

- Value
- Data Type

Example

```javascript
10 === "10"
```

Result

```
FALSE
```

Because

```
Number

≠

String
```

---

# Camel Case Naming

Examples

```
ticketState

assetOwner

requestType

replacementRequest
```

Camel Case improves readability and follows JavaScript naming conventions.

---

# String vs Number

Example

```javascript
var a = "10";
var b = "20";

var total = a + b;
```

Output

```
1020
```

Because both are strings.

---

Numbers

```javascript
var a = 10;

var b = 20;

var total = a + b;
```

Output

```
30
```

Understanding the difference between strings and numbers is essential when building Client Scripts.

---

# Complete Client Script Logic

```javascript
var replacement =
g_form.getValue('u_is_it_replacement_request');

if(replacement=="true")
{

    g_form.enableAttachments();

    g_form.addOption(
    'u_ticket_state',
    'ch1',
    'Replacement Choice',
    2);

    g_form.removeOption(
    'u_ticket_state',
    'ch2');

    g_form.addDecoration(
    'u_assignment_group',
    'icon-home',
    'Replacement TRUE',
    'color-green');

    g_form.clearMessages();

    g_form.addInfoMessage(
    "Replacement Request TRUE");

}

else
{

    g_form.disableAttachments();

    g_form.removeOption(
    'u_ticket_state',
    'ch1');

    g_form.addOption(
    'u_ticket_state',
    'ch2',
    'No Replacement Choice',
    4);

    g_form.removeDecoration(
    'u_assignment_group',
    'icon-home');

    g_form.addDecoration(
    'u_assignment_group',
    'icon-book',
    'Replacement FALSE',
    'color-red');

    g_form.clearMessages();

    g_form.addErrorMessage(
    "Replacement Request FALSE");

}
```

---

# Assignment Requirement

Create a Client Script.

Condition

```
Whenever

Ticket State

changes

↓

Display

Current State
```

Example

```
New

↓

"You have selected New State"

In Progress

↓

"The State is In Progress"

Resolved

↓

"The State is Resolved"

Cancelled

↓

"The State is Cancelled"
```

To determine the backend values for each state, the trainer demonstrated checking the field definition/XML information before writing the script. :contentReference[oaicite:7]{index=7}

---

# Real Project Scenario

Imagine an Asset Replacement application.

```
Replacement Request

↓

TRUE

↓

Attachments Enabled

↓

Replacement Choice Added

↓

Green Decoration

↓

Information Banner
```

If

```
FALSE
```

```
Attachments Disabled

↓

Choice Removed

↓

Red Decoration

↓

Error Banner
```

This creates an intuitive user experience without requiring a page refresh.

---

# Best Practices

✅ Practice JavaScript daily.

✅ Prefer `===` when comparing values and data types.

✅ Use meaningful variable names.

✅ Remove old choice values before adding new ones.

✅ Clear previous messages.

✅ Avoid `clearOptions()` unless absolutely necessary.

✅ Test scripts using the JavaScript Executor (`Ctrl + Alt + Shift + J`).

✅ Read the official GlideForm API documentation before using unfamiliar methods.

---

# Common Interview Questions

### Q1. Difference between `=` and `==`?

`=` assigns a value.

`==` compares values.

---

### Q2. Difference between `==` and `===`?

`==` compares values only.

`===` compares both value and data type.

---

### Q3. Which method enables attachments?

```javascript
g_form.enableAttachments();
```

---

### Q4. Which method disables attachments?

```javascript
g_form.disableAttachments();
```

---

### Q5. Which method adds a choice dynamically?

```javascript
g_form.addOption();
```

---

### Q6. Which method removes a choice?

```javascript
g_form.removeOption();
```

---

### Q7. Which method returns the current table name?

```javascript
g_form.getTableName();
```

---

### Q8. Which method returns the Sys ID?

```javascript
g_form.getUniqueValue();
```

---

### Q9. Why should `clearOptions()` be used carefully?

Because it removes every available choice from the field.

---

# Assignment

Practice the following:

- JavaScript `if`, `else if`, and `else`
- `enableAttachments()`
- `disableAttachments()`
- `addOption()`
- `removeOption()`
- `getValue()`
- `getTableName()`
- `getUniqueValue()`

### Practical Task

Create a Client Script that:

- Executes when **Ticket State** changes.
- Displays an alert showing the currently selected state.
- Handle all available states:
  - New
  - In Progress
  - On Hold
  - Resolved
  - Closed
  - Cancelled

---

# Key Takeaways

- JavaScript decision-making is fundamental to ServiceNow scripting.
- `g_form` provides powerful APIs for modifying forms dynamically.
- Attachments and Choice fields can be controlled based on business conditions.
- Use `getValue()`, `getTableName()`, and `getUniqueValue()` to retrieve record information.
- Understand the difference between `=`, `==`, and `===`.
- Test small code snippets quickly using the built-in JavaScript Executor.
- Dynamic Client Scripts significantly improve user experience by responding instantly to user actions.

---

# Official Reference

**GlideForm (g_form) API**

https://www.servicenow.com/docs/bundle/xanadu-api-reference/page/app-store/dev_portal/API_reference/GlideForm/concept/c_GlideFormAPI.html

---

# Phase 2 Progress

```
✅ Day 21 - Update Set Versioning & Deployment
✅ Day 22 - Backout Process & Best Practices
✅ Day 23 - Introduction to Client & Server Scripting
✅ Day 24 - Client Scripts, g_form & Decorations
✅ Day 25 - Advanced g_form Methods, Attachments, Dynamic Choices & Field Information

Next →
Client Script APIs (Part 4), GlideAjax, Form Manipulation & Advanced Validation
```

---

**End of Day 25**

