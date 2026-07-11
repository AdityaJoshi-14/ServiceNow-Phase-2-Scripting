# 📅 Phase 2 - Day 24
# 🚀 Client Scripts (Part 2) - GlideForm (g_form), Conditional Logic & Form Customization

> **Date:** 07 May 2026  
> **Phase:** ServiceNow Scripting  
> **Topic:** Client Scripts, JavaScript Conditions, GlideForm API

---

# 📖 Introduction

Today we officially started writing practical Client Scripts using the **GlideForm (g_form) API**.

Until now, we have mostly configured the platform using:

- Tables
- Forms
- Dictionary
- UI Policies
- Update Sets

From this day onwards we begin writing **JavaScript** inside ServiceNow.

Client Scripts help us make forms **dynamic, interactive and user-friendly** without sending requests to the server.

---

# Why do we need Client Scripts?

Suppose a user is filling an Asset Request form.

Instead of waiting until they click **Submit**, we can immediately:

- Validate entered values
- Show warning messages
- Auto-populate fields
- Add visual indicators
- Make fields mandatory
- Hide unnecessary fields

All of this happens instantly inside the browser.

---

# Client Side Architecture

```
User
   │
   ▼
Browser
(Client Side)

Client Script Executes
       │
       ▼
   GlideForm (g_form)
       │
       ▼
Form Updates Instantly
```

No server communication is required for these operations.

---

# What are Client Scripts?

A Client Script is a JavaScript program that executes on the browser (client side).

It is mainly used for:

- Field validation
- Data comparison
- Auto population
- Submission restriction
- Dynamic form behaviour
- User interaction

---

# Types of Client Scripts

There are **4 Client Script types**.

---

## 1. OnLoad

Runs when the form opens.

### Uses

- Set default values
- Display welcome messages
- Hide sections
- Initialize variables

Example

```javascript
function onLoad()
{
    alert("Form Loaded");
}
```

---

## 2. OnChange

Runs whenever the value of a field changes.

This is the **most commonly used** Client Script.

### Uses

- Field validation
- Dynamic visibility
- Mandatory logic
- Auto-population

Example

```javascript
function onChange(control,
                  oldValue,
                  newValue,
                  isLoading,
                  isTemplate)
{

}
```

---

## 3. OnSubmit

Runs when user clicks

- Save
- Submit
- Update

### Uses

- Prevent invalid records
- Final validation
- Mandatory checks

Example

```javascript
function onSubmit()
{

}
```

---

## 4. OnCellEdit

Runs only when editing records directly from **List View**.

Example

```
Incident List

Priority

3

↓

User edits Priority

↓

OnCellEdit Executes
```

---

# Client Script Execution Flow

```
Record Opens
      │
      ▼
OnLoad

↓

User Changes Field

↓

OnChange

↓

User Clicks Save

↓

OnSubmit

↓

User edits List View

↓

OnCellEdit
```

---

# Today's Requirement

Requirement:

```
If

Is it Replacement Request ?

is TRUE

↓

Do Something

Else

↓

Do Reverse Thing
```

---

# Requirement → Design → Code

Never directly write code.

Always follow this flow.

```
Requirement

↓

Design

↓

Code

↓

Testing

↓

Deployment
```

---

# JavaScript If-Else

Every Client Script starts with JavaScript logic.

Example

```javascript
if(person=="Male")
{
    Give Biryani;
}
else
{
    Give Flower;
}
```

Another Example

```javascript
if(person=="Aditya")
{
    Give Sweet;
}
else
{
    Give Biryani;
}
```

---

# Reading Field Values

GlideForm provides

```javascript
g_form.getValue()
```

Example

```javascript
var replacement =
g_form.getValue('u_is_it_replacement_request');
```

Output

```
true

or

false
```

---

# Basic If-Else Script

```javascript
var replacement =
g_form.getValue('u_is_it_replacement_request');

if(replacement=="true")
{
    alert("Replacement Selected");
}
else
{
    alert("Replacement Not Selected");
}
```

---

# Introduction to GlideForm (g_form)

The most important Client Side API.

```
g_form

↓

Read Values

Write Values

Show Messages

Hide Fields

Make Mandatory

Read Only

Decorations

Validation
```

---

# Frequently Used g_form Methods

| Method | Purpose |
|---------|----------|
| getValue() | Read field value |
| setValue() | Set field value |
| addInfoMessage() | Show information |
| addErrorMessage() | Show error |
| clearMessages() | Remove previous messages |
| addDecoration() | Add icon beside field |
| removeDecoration() | Remove icon |

---

# addDecoration()

Adds an icon beside a field.

Syntax

```javascript
g_form.addDecoration(
'u_assignment_group',
'icon-home',
'Replacement TRUE',
'color-green'
);
```

Result

```
Assignment Group

🏠
```

---

# removeDecoration()

If we never remove the previous decoration,

Both icons remain.

Wrong

```
🏠 📖
```

Correct

```javascript
g_form.removeDecoration(
'u_assignment_group',
'icon-book',
'Replacement FALSE',
'color-red'
);
```

Always remove previous decoration before adding a new one.

---

# Information Message

```javascript
g_form.addInfoMessage(
"Replacement Request is TRUE"
);
```

Output

```
Blue Information Banner

Replacement Request is TRUE
```

---

# Error Message

```javascript
g_form.addErrorMessage(
"Replacement Request is FALSE"
);
```

Output

```
Red Error Banner

Replacement Request is FALSE
```

---

# clearMessages()

Suppose user changes values multiple times.

Without clearing

```
TRUE

FALSE

TRUE

FALSE

TRUE
```

Messages keep increasing.

Correct Approach

```javascript
g_form.clearMessages();
```

Now only one message remains.

---

# Complete Client Script

```javascript
function onChange(control,
                  oldValue,
                  newValue,
                  isLoading,
                  isTemplate)
{

    if(isLoading || newValue=="")
    {
        return;
    }

    var replacement =
    g_form.getValue('u_is_it_replacement_request');

    if(replacement=="true")
    {

        g_form.addDecoration(
        'u_assignment_group',
        'icon-home',
        'Replacement TRUE',
        'color-green');

        g_form.removeDecoration(
        'u_assignment_group',
        'icon-book',
        'Replacement FALSE',
        'color-red');

        g_form.clearMessages();

        g_form.addInfoMessage(
        "Replacement Request is TRUE");

    }

    else
    {

        g_form.removeDecoration(
        'u_assignment_group',
        'icon-home',
        'Replacement TRUE',
        'color-green');

        g_form.addDecoration(
        'u_assignment_group',
        'icon-book',
        'Replacement FALSE',
        'color-red');

        g_form.clearMessages();

        g_form.addErrorMessage(
        "Replacement Request is FALSE");

    }

}
```

---

# Practical Scenario

Suppose an employee raises an Asset Request.

If

```
Replacement = TRUE
```

Output

```
Green House Icon

Information Banner

Assignment Group Highlighted
```

If

```
Replacement = FALSE
```

Output

```
Red Book Icon

Error Banner

Different Decoration
```

This provides immediate visual feedback to the user.

---

# Development Tips

Always keep **3 browser tabs open**

```
1. ServiceNow Form

2. Client Script

3. GlideForm API Documentation
```

This improves development speed significantly.

---

# Best Practices

✔ Keep Client Scripts small

✔ Always use clear variable names

✔ Remove previous decorations

✔ Clear previous messages

✔ Test both TRUE and FALSE conditions

✔ Use g_form instead of DOM manipulation

✔ Follow Requirement → Design → Code approach

✔ Refer to official GlideForm documentation whenever required

---

# Common Interview Questions

### Q1. What are the four types of Client Scripts?

- OnLoad
- OnChange
- OnSubmit
- OnCellEdit

---

### Q2. Which Client Script runs most frequently?

OnChange

---

### Q3. What is GlideForm?

Client-side API used to manipulate ServiceNow forms.

---

### Q4. Which method reads a field value?

```javascript
g_form.getValue()
```

---

### Q5. Difference between addInfoMessage() and addErrorMessage()?

Info Message

- Blue banner
- General information

Error Message

- Red banner
- Indicates validation errors

---

### Q6. Why use removeDecoration()?

To prevent duplicate icons from appearing on the same field.

---

### Q7. Why use clearMessages()?

To remove old messages before displaying new ones.

---

# Assignment

Practice the following:

### JavaScript

- if-else
- alerts
- variable creation

### GlideForm Methods

- getValue()
- addDecoration()
- removeDecoration()
- addInfoMessage()
- addErrorMessage()
- clearMessages()
- alert()

---

# Key Takeaways

- Client Scripts execute only in the browser.
- There are four types of Client Scripts.
- OnChange is the most frequently used Client Script.
- g_form is the primary API for manipulating forms.
- Use decorations to improve the user interface.
- Always clear previous messages and remove previous decorations.
- Follow Requirement → Design → Code while developing.
- Practice JavaScript fundamentals before moving to advanced ServiceNow scripting.

---

# Official Documentation

GlideForm API

https://www.servicenow.com/docs/bundle/xanadu-api-reference/page/app-store/dev_portal/API_reference/GlideForm/concept/c_GlideFormAPI.html

---

# Phase 2 Progress

```
✅ Day 21 - Update Set Versioning
✅ Day 22 - Backout & Update Set Best Practices
✅ Day 23 - Introduction to Client & Server Scripting
✅ Day 24 - Client Scripts, GlideForm API, Decorations & Messages

Next →
Advanced g_form Methods, Field Manipulation, GlideAjax & Server Communication
```

---

**End of Day 24**
