# 📅 Phase 2 – Day 28
# 🚀 Client Scripts (Part 6) – Templates, isTemplate & g_user API

> **Date:** 13 May 2026  
> **Phase:** ServiceNow Scripting  
> **Topic:** Templates, isTemplate Parameter, g_user API, Role-based Client Scripting

---

# 📖 Introduction

Today's class focused on two major topics that are frequently used in real-world ServiceNow development:

1. **Templates**
2. **g_user API**

Until now, we have learned how Client Scripts execute using `onLoad`, `onChange`, and `onSubmit`. Today we explored an important behavior:

> **Every onChange Client Script runs not only when a field value changes, but also when a Template is applied.**

We also learned how to retrieve information about the currently logged-in user using the **g_user API** and how to implement role-based UI behavior.

The uploaded notes demonstrate the complete lifecycle of creating templates, applying them, testing `isTemplate`, and using the `g_user` object for client-side security logic. :contentReference[oaicite:0]{index=0}

---

# Learning Objectives

By the end of this session, you should be able to:

- Understand Templates in ServiceNow
- Know why Templates are used
- Understand the `isTemplate` parameter
- Prevent Client Scripts from executing during Template application
- Understand the `g_user` API
- Retrieve logged-in user information
- Perform role-based Client Scripting
- Restrict field access using user roles

---

# Revision

Previously we learned:

```
Client Script Types

✔ onLoad
✔ onSubmit
✔ onChange
✔ onCellEdit
```

We also studied

```
g_form

getValue()

setValue()

getReference()

Dot Walking
```

Today we introduced

```
Templates

isTemplate

g_user
```

---

# What are Templates?

Templates allow users to populate multiple fields automatically with predefined values.

Instead of manually entering the same information repeatedly, users can apply a template that fills the required fields instantly.

The uploaded notes use an Incident example where closing information is repeatedly entered, making Templates useful for repetitive work. :contentReference[oaicite:1]{index=1}

---

# Why Templates?

Imagine an organization closes

```
50 Incidents

Every Day
```

Every Incident requires

```
Assignment Group

Priority

Category

State

Description

Closing Notes
```

Typing these values every time is repetitive.

Templates eliminate this repetitive work.

---

# Without Template

```
Incident

↓

Fill every field manually

↓

Submit
```

Time Consuming

---

# With Template

```
Incident

↓

Apply Template

↓

All predefined values filled automatically

↓

Submit
```

Much Faster

---

# Example Scenario

Suppose every Hardware Incident requires

```
Category

↓

Hardware

Priority

↓

2 - High

Assignment Group

↓

Hardware Team

Description

↓

Cannot access hardware device.
```

Instead of entering these values repeatedly,

create a Template once and reuse it.

The screenshots in the uploaded notes show the template being created and later applied successfully to populate multiple fields. :contentReference[oaicite:2]{index=2}

---

# Creating a Template

Typical Flow

```
Open Record

↓

Toggle Template Bar

↓

Create New Template

↓

Choose Fields

↓

Save
```

Once saved,

the template becomes reusable.

---

# Applying a Template

```
Record

↓

Template

↓

Apply

↓

Fields Auto Populated
```

Example

Before

```
Description

↓

Empty
```

After Applying Template

```
Description

↓

Cannot access Exchange server.

Please investigate.
```

The uploaded notes include before-and-after screenshots showing the template automatically filling the selected fields. :contentReference[oaicite:3]{index=3}

---

# Client Script & Templates

Important Interview Point

Every

```
onChange
```

Client Script executes during

```
1.

Field Value Change

AND

2.

Template Application
```

This behavior is explicitly demonstrated in the uploaded notes by observing the `isTemplate` parameter when a template is applied. :contentReference[oaicite:4]{index=4}

---

# Why does this happen?

Applying a Template changes field values.

Changing field values triggers

```
onChange
```

Therefore,

the Client Script executes.

---

# Understanding isTemplate

Every onChange Client Script contains

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

Today's focus

```
isTemplate
```

---

# Meaning of isTemplate

When

```
Template Applied

↓

isTemplate = true
```

When

```
User Manually Changes Field

↓

isTemplate = false
```

---

# Default Behavior

```
Form Loading

↓

Client Script

↓

Runs

------------------

Template Applied

↓

Client Script

↓

Runs

------------------

User Changes Field

↓

Client Script

↓

Runs
```

---

# Requirement

Suppose

you DO NOT want your Client Script

to execute

while a Template is applied.

---

# Solution

```javascript
function onChange(
control,
oldValue,
newValue,
isLoading,
isTemplate)
{

if(isLoading ||
newValue=='' ||
isTemplate)
{
return;
}

}
```

Now

```
Template Applied

↓

Client Script

↓

Stops
```

The uploaded notes demonstrate adding `isTemplate` to the early return condition, after which the script no longer runs during template application. :contentReference[oaicite:5]{index=5}

---

# Client Script Flow

Without

```
Template

↓

Client Script Executes
```

With

```
if(isTemplate)

↓

return;
```

```
Template

↓

No Execution
```

---

# GlideForm Review

Before learning g_user,

the trainer revised

```
g_form
```

Definition

> **g_form is the Client-side API used to perform operations on forms.**

Examples

```
getValue()

setValue()

setMandatory()

setDisplay()

setReadOnly()

addInfoMessage()

addDecoration()

removeDecoration()
```

---

# Introduction to g_user

Just like

```
g_form

↓

Form API
```

there is

```
g_user

↓

Logged In User API
```

The uploaded notes explain that `g_user` provides information about the current logged-in user and includes both properties and methods. :contentReference[oaicite:6]{index=6}

---

# Why g_user?

Suppose

```
Current User

↓

Abraham Lincoln
```

Need

```
First Name

Last Name

Roles

User ID

Sys ID
```

Instead of querying the server,

we use

```
g_user
```

---

# g_user Properties

## 1. First Name

```javascript
alert(g_user.firstName);
```

Output

```
System
```

Returns

Current User's First Name.

---

## 2. Full Name

```javascript
alert(
g_user.getFullName()
);
```

Output

```
System Administrator
```

Returns

First Name + Last Name.

---

## 3. Last Name

```javascript
alert(g_user.lastName);
```

Output

```
Administrator
```

---

## 4. User Sys ID

```javascript
alert(g_user.userID);
```

Output

```
6816f79cc0a8016401c5...
```

Returns

Logged In User's Sys ID.

---

## 5. Username

```javascript
alert(g_user.userName);
```

Output

```
admin
```

Returns

User ID (Login Name).

The uploaded notes verify the returned Sys ID by opening the corresponding user record. :contentReference[oaicite:7]{index=7}

---

# Properties vs Methods

Properties

```
firstName

lastName

userID

userName
```

Methods

```
getFullName()

hasRole()

hasRoleExactly()

hasRoleFromList()

hasRoles()
```

---

# Role Methods

---

## hasRole()

```javascript
g_user.hasRole('itil_admin');
```

Returns

```
true

if user has

itil_admin

OR

admin
```

Admin automatically satisfies role checks.

---

## hasRoleExactly()

```javascript
g_user.hasRoleExactly(
'itil_admin'
);
```

Returns

```
true

ONLY

if user explicitly has

itil_admin
```

Admin does not automatically satisfy this method.

The uploaded notes compare `hasRole()` and `hasRoleExactly()` using the admin user. :contentReference[oaicite:8]{index=8}

---

## hasRoleFromList()

```javascript
g_user.hasRoleFromList(

'itil,canvas_user'

);
```

Returns

```
true

if user has

ANY

role

from list
```

---

## hasRoles()

```javascript
g_user.hasRoles();
```

Returns

```
true

if user has

at least

one role
```

---

# Real Requirement

Only

```
ITIL

Users
```

should edit

```
Asset Owners
```

Everyone else

Read Only.

---

# Solution

```javascript
var sk =
g_user.hasRoleExactly(
'itil'
);

if(sk)
{
g_form.setReadOnly(
'u_asset_owners',
false
);
}
else
{
g_form.setReadOnly(
'u_asset_owners',
true
);
}
```

---

# Multiple Roles Requirement

Allow

```
ITIL

OR

Canvas User
```

to edit

```
Asset Owners
```

---

# Solution

```javascript
var sk =
g_user.hasRoleFromList(

'itil,canvas_user'

);

if(sk)
{
g_form.setReadOnly(
'u_asset_owners',
false
);
}
else
{
g_form.setReadOnly(
'u_asset_owners',
true
);
}
```

---

# Complete Flow

```
Logged In User

↓

g_user

↓

Check Role

↓

Allowed?

↓

YES

↓

Editable

---------------

NO

↓

Read Only
```

---

# Real Project Scenarios

### Scenario 1

Only HR users

can edit

```
Salary
```

---

### Scenario 2

Only ITIL users

can edit

```
Assignment Group
```

---

### Scenario 3

Managers

can edit

```
Approval Status
```

---

# Best Practices

✔ Use Templates for repetitive field values.

✔ Add `isTemplate` in onChange Client Scripts if logic should not run during template application.

✔ Use `g_user` only for client-side user information.

✔ Use `hasRoleFromList()` when multiple roles are allowed.

✔ Prefer `hasRoleExactly()` when strict role validation is required.

---

# Common Interview Questions

### Q1. What are Templates?

Templates automatically populate predefined field values to reduce repetitive manual work.

---

### Q2. What is `isTemplate`?

A Boolean parameter in onChange Client Scripts that becomes `true` when the script is triggered by applying a Template.

---

### Q3. How do you stop a Client Script from executing during Template application?

```javascript
if(isTemplate)
{
return;
}
```

---

### Q4. What is `g_user`?

Client-side API that provides information about the currently logged-in user.

---

### Q5. Difference between `g_form` and `g_user`?

| g_form | g_user |
|---------|---------|
| Works with Form | Works with Logged-in User |
| Manipulates fields | Retrieves user information |
| Form API | User API |

---

### Q6. Difference between `hasRole()` and `hasRoleExactly()`?

- `hasRole()` returns `true` for the specified role **or** if the user is an admin.
- `hasRoleExactly()` returns `true` only if the user explicitly has that specific role.

---

### Q7. Which method checks multiple roles?

```javascript
g_user.hasRoleFromList()
```

---

### Q8. Which method checks whether the user has at least one role?

```javascript
g_user.hasRoles()
```

---

# Assignment

### 1. Templates

- Create a Template.
- Apply it on a record.
- Observe the value of `isTemplate`.

---

### 2. g_user Practice

Practice the following methods:

- `firstName`
- `lastName`
- `getFullName()`
- `userID`
- `userName`
- `hasRole()`
- `hasRoleExactly()`
- `hasRoleFromList()`
- `hasRoles()`

---

### 3. Role-Based Client Script

Create an **onLoad Client Script** that allows only users with the `itil` or `canvas_user` role to edit the **Asset Owners** field.

---

# Key Takeaways

- Templates reduce repetitive data entry by automatically populating predefined values.
- Every `onChange` Client Script executes during both field value changes and Template application.
- Use the `isTemplate` parameter to skip script execution when a Template is applied.
- `g_user` provides client-side access to information about the logged-in user.
- Methods like `hasRole()`, `hasRoleExactly()`, and `hasRoleFromList()` enable role-based UI customization.
- Combining `g_user` with `g_form` allows secure and dynamic client-side behavior.

---

# Official Reference

**GlideUser (g_user) API**

https://www.servicenow.com/docs/bundle/xanadu-api-reference/page/app-store/dev_portal/API_reference/GlideUser/concept/c_GlideUserAPI.html

---

# Phase 2 Progress

```
✅ Day 21 - Update Set Versioning & Deployment
✅ Day 22 - Backout Process & Best Practices
✅ Day 23 - Client vs Server Scripting
✅ Day 24 - GlideForm Basics
✅ Day 25 - Advanced GlideForm Methods
✅ Day 26 - GlideForm Set Family
✅ Day 27 - getReference(), Dot Walking & onChange Parameters
✅ Day 28 - Templates, isTemplate & g_user API

Next →
Client Script Validation, GlideAjax, Asynchronous Calls & Client-Server Communication
```

---

**End of Day 28**

