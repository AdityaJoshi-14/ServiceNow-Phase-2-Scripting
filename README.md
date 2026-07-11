# 📅 Phase 2 – Day 29
# 🚀 Client Scripts (Part 7) – onCellEdit Client Scripts & List View Validation

> **Date:** 14 May 2026  
> **Phase:** ServiceNow Scripting  
> **Topic:** onCellEdit Client Script, List View Validation, callback(), saveAndClose & Inline Editing

---

# 📖 Introduction

Today's session concluded the **Client Script** module by introducing the **onCellEdit Client Script**.

Until now, every Client Script we wrote executed on the **Form View**.

Today we learned scripting on the **List View**.

Users often update records directly from the list without opening the form.

ServiceNow provides **onCellEdit Client Scripts** to validate these inline edits before saving.

The uploaded notes demonstrate a practical example where users are prevented from changing the **Activeness** field from **False → True** directly from the list view. :contentReference[oaicite:0]{index=0}

---

# Learning Objectives

After completing this session you should be able to:

- Understand onCellEdit Client Scripts
- Know where they execute
- Understand callback()
- Prevent invalid inline editing
- Validate values before saving
- Understand saveAndClose
- Restrict editing directly from List View

---

# Client Side APIs Revision

Before beginning today's topic, the trainer revised all Client Side APIs.

## Client APIs

```
g_form

g_user
```

These are the two primary Client-side APIs used throughout ServiceNow scripting. :contentReference[oaicite:1]{index=1}

---

# Client Script Revision

Topics covered so far

```
g_form

↓

Decorations

↓

Messages

↓

Options

↓

Attachments

↓

Get Methods

↓

Set Methods

↓

Reference

↓

Mandatory

↓

Read Only

↓

Visible

↓

Label

↓

Clear Value

↓

oldValue

↓

newValue

↓

isLoading

↓

isTemplate

↓

isNewRecord
```

Trainer also revised

```
g_user

↓

Properties

↓

Methods

↓

Role Checks
```

---

# What is onCellEdit?

Unlike

```
onLoad

↓

Form
```

or

```
onChange

↓

Form
```

onCellEdit works on

```
List View
```

Whenever a user edits a cell directly from the list,

this Client Script executes.

---

# List View Example

```
Asset Requests

--------------------------------

Number

State

Priority

Active

Assignment Group
```

User

double-clicks

```
Active

False

↓

True
```

This action triggers

```
onCellEdit
```

---

# Why onCellEdit?

Sometimes

users should NOT edit

certain values directly from List View.

Instead,

they should open the record.

Example

```
Closed Ticket

↓

Should NOT become

Active

from List View
```

The uploaded notes use the **Activeness** field as the example for inline edit validation. :contentReference[oaicite:2]{index=2}

---

# Business Requirement

Suppose

```
Active

False
```

User tries

```
False

↓

True
```

Requirement

```
Do NOT Allow
```

---

# Real Business Scenario

Asset Request

```
Closed

↓

Active = False
```

User attempts

```
False

↓

True
```

Result

```
Rejected
```

This exact scenario is demonstrated throughout the uploaded notes with screenshots showing the edit attempt and validation. :contentReference[oaicite:3]{index=3}

---

# Creating an onCellEdit Client Script

Client Script Type

```
onCellEdit
```

Function

```javascript
function onCellEdit(
sysIDs,
table,
oldValues,
newValue,
callback
)
{

}
```

Unlike onChange,

the parameters are completely different.

---

# Parameters

## sysIDs

Contains

```
Sys IDs

of selected records.
```

Useful when editing multiple rows.

---

## table

Returns

```
Current Table Name
```

Example

```
u_asset_request_management
```

---

## oldValues

Contains

```
Previous Value
```

Example

```
false
```

---

## newValue

Contains

```
Current Value

entered by user.
```

Example

```
true
```

---

## callback

Special function used

to tell ServiceNow

whether

the change should be saved.

This is the most important parameter in an onCellEdit Client Script.

---

# saveAndClose

The trainer introduced

```
saveAndClose
```

```
true

↓

Save Changes

false

↓

Reject Changes
```

The uploaded notes show that simply displaying an alert does not stop the update—`saveAndClose` must be set to `false` before calling `callback()`. :contentReference[oaicite:4]{index=4}

---

# Initial Script

```javascript
function onCellEdit(
sysIDs,
table,
oldValues,
newValue,
callback
)
{

var saveAndClose = true;

}
```

Initially

changes are allowed.

---

# Validation

```javascript
if(newValue == 'true')
{

alert(

'You should not change the value to True from False'

);

}
```

Result

Alert appears.

BUT

the value still changes.

The uploaded notes demonstrate this exact behavior: an alert is shown, but the field is still updated because `saveAndClose` remains `true`. :contentReference[oaicite:5]{index=5}

---

# Why?

Because

```
Alert

≠

Validation
```

Displaying an alert

does not stop

saving.

---

# Correct Solution

```javascript
function onCellEdit(
sysIDs,
table,
oldValues,
newValue,
callback
)
{

var saveAndClose = true;

if(newValue == 'true')
{

alert(

'You should not change the value to True from False'

);

saveAndClose = false;

}

callback(saveAndClose);

}
```

Now

```
Alert

↓

No Save

↓

Value Remains

False
```

This corrected version is shown in the uploaded notes and successfully prevents the inline update. :contentReference[oaicite:6]{index=6}

---

# callback()

One of the most important concepts today.

```
callback(true)

↓

Save

---------------

callback(false)

↓

Cancel
```

Without

```
callback()

↓

ServiceNow

doesn't know

what action to perform.
```

---

# Complete Flow

```
User

↓

Edits Cell

↓

onCellEdit

↓

Validation

↓

Allowed?

──────────────

YES

↓

callback(true)

↓

Save

──────────────

NO

↓

Alert

↓

callback(false)

↓

Cancel Save
```

---

# Practical Example

Suppose

```
Activeness

False
```

User edits

```
True
```

System

```
↓

Alert

↓

"Active value should not be changed."

↓

Value

Remains

False
```

The final screenshots in the uploaded PDF confirm that after using `saveAndClose = false`, the field remains **False** even after the edit attempt. :contentReference[oaicite:7]{index=7}

---

# Real Project Scenarios

### Scenario 1

Prevent

```
Priority

1

↓

4
```

from List View.

---

### Scenario 2

Prevent

```
Closed Ticket

↓

Reopen

from List
```

---

### Scenario 3

Prevent

```
Assignment Group

Change

from List View.
```

---

### Scenario 4

Prevent

```
Active

False

↓

True

for Closed Records.
```

---

# Form View vs List View

| Form View | List View |
|------------|-----------|
| onLoad | onCellEdit |
| onSubmit | Inline Edit |
| onChange | callback() |
| g_form | saveAndClose |

---

# Common Mistakes

❌ Showing only an alert.

The value still changes.

---

❌ Forgetting callback()

The script does not complete properly.

---

❌ Leaving

```javascript
saveAndClose = true;
```

Validation fails because saving is still permitted.

---

# Best Practices

✔ Keep onCellEdit logic lightweight.

✔ Validate only necessary fields.

✔ Always call `callback()`.

✔ Set `saveAndClose = false` when rejecting changes.

✔ Display meaningful error messages.

✔ Avoid complex server calls inside onCellEdit.

---

# Common Interview Questions

### Q1. What is onCellEdit?

A Client Script that executes when a field is edited directly from the **List View**.

---

### Q2. Where does onCellEdit execute?

Only in the **List View**.

---

### Q3. What is callback()?

A function that tells ServiceNow whether the inline edit should be saved or cancelled.

---

### Q4. Difference between callback(true) and callback(false)?

```
true

↓

Save

false

↓

Cancel
```

---

### Q5. Can an alert stop a List View update?

No.

Only

```javascript
callback(false)
```

or

```javascript
saveAndClose = false;
```

prevents the update.

---

### Q6. Which parameter contains the new value?

```javascript
newValue
```

---

### Q7. Which parameter contains previous values?

```javascript
oldValues
```

---

### Q8. Which parameter contains the table name?

```javascript
table
```

---

# Assignment

### Practice

Create an **onCellEdit Client Script** that:

- Prevents users from changing the **Activeness** field from **False → True** directly from the List View.
- Display an alert explaining why the update is blocked.
- Use `saveAndClose = false`.
- Call `callback(saveAndClose)` to cancel the inline edit.

---

# Key Takeaways

- `onCellEdit` Client Scripts execute only in the **List View** during inline editing.
- Unlike form-based Client Scripts, they use parameters such as `sysIDs`, `table`, `oldValues`, `newValue`, and `callback`.
- Simply showing an alert does **not** stop the update.
- Use `saveAndClose = false` followed by `callback(saveAndClose)` to reject invalid edits.
- This technique is commonly used to protect important fields from unauthorized inline modifications.

---

# Official Reference

**Client Script (onCellEdit) Documentation**

https://www.servicenow.com/docs/bundle/xanadu-api-reference/page/script/client-scripts/concept/client-scripts.html

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
✅ Day 29 - onCellEdit Client Scripts & List View Validation

Next →
Server-Side Scripting: GlideRecord, Business Rules & Database Operations
```

---

**End of Day 29**

