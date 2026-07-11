# 📅 Phase 2 – Day 26
# 🚀 Client Scripts (Part 4) – GlideForm Set Family (setValue, setDisplay, setMandatory, setReadOnly & Label Manipulation)

> **Date:** 11 May 2026  
> **Phase:** ServiceNow Scripting  
> **Topic:** GlideForm Set Family Methods, Dynamic Field Manipulation & Client Script Best Practices

---

# 📖 Introduction

After learning the **Get Family** (`getValue()`, `getTableName()`, `getUniqueValue()`), today's class focused on the **Set Family** of the GlideForm API.

The Get Family **reads information** from the form, whereas the Set Family **modifies the form dynamically**.

Using these methods, we can:

- Make fields mandatory
- Hide or display fields
- Make fields read-only
- Change labels
- Populate fields automatically
- Clear field values

These methods form the foundation of almost every real-world Client Script in ServiceNow. The uploaded class notes introduce the Set Family with several practical business requirements and examples. :contentReference[oaicite:0]{index=0}

---

# Learning Objectives

After completing this session you should be able to:

- Understand the GlideForm Set Family
- Make fields mandatory dynamically
- Show or hide fields
- Make fields read-only
- Rename field labels
- Populate fields automatically
- Clear field values
- Understand execution order in Client Scripts
- Handle string literals correctly in JavaScript

---

# Get Family vs Set Family

| Get Family | Set Family |
|------------|------------|
| Reads data | Changes data |
| Returns values | Updates UI |
| No modification | Dynamic modification |

Examples

### Get Family

```javascript
g_form.getValue()

g_form.getTableName()

g_form.getUniqueValue()
```

### Set Family

```javascript
g_form.setValue()

g_form.setDisplay()

g_form.setMandatory()

g_form.setReadOnly()

g_form.setLabelOf()
```

---

# Business Requirement

Requirement

```
If

Replacement Request

↓

TRUE

↓

Due Date

Mandatory

Visible

↓

Assignment Group

Mandatory

↓

Asset Owners

Read Only
```

This requirement is implemented step-by-step throughout the practical demonstration. :contentReference[oaicite:1]{index=1}

---

# GlideForm Set Family

```
g_form

↓

setMandatory()

↓

setDisplay()

↓

setReadOnly()

↓

setLabelOf()

↓

setValue()

↓

clearValue()
```

These methods allow developers to modify the form instantly without refreshing the page.

---

# setMandatory()

Makes a field mandatory.

```javascript
g_form.setMandatory(
'u_due_date',
true
);
```

Result

```
Due Date *

Mandatory
```

Remove mandatory

```javascript
g_form.setMandatory(
'u_due_date',
false
);
```

---

# Important Rule

The trainer highlighted one important ServiceNow rule:

> **A mandatory field without a value cannot be hidden.**

Wrong Order

```
Mandatory

↓

Hide

❌ Error
```

Correct Order

```
Mandatory = false

↓

Hide Field

✅ Works
```

Always remove the mandatory property before hiding a field. The notes specifically emphasize that the order of operations matters. :contentReference[oaicite:2]{index=2}

---

# setDisplay()

Shows or hides a field.

Show

```javascript
g_form.setDisplay(
'u_due_date',
true
);
```

Hide

```javascript
g_form.setDisplay(
'u_due_date',
false
);
```

Difference

```
Display = false

↓

Field Removed

↓

Form Layout Adjusts Automatically
```

Unlike simply hiding a field visually, `setDisplay(false)` removes the field from the layout and closes the empty space. :contentReference[oaicite:3]{index=3}

---

# setVisible()

Another method available in GlideForm.

```javascript
g_form.setVisible();
```

However,

today's implementation primarily used

```javascript
setDisplay()
```

because it also adjusts the form layout properly.

---

# setReadOnly()

Prevents editing.

Example

```javascript
g_form.setReadOnly(
'u_asset_owners',
true
);
```

Result

```
Asset Owners

Visible

Cannot Edit
```

Enable editing

```javascript
g_form.setReadOnly(
'u_asset_owners',
false
);
```

---

# Dynamic Requirement

```
Replacement Request

TRUE

↓

Asset Owners

Read Only

FALSE

↓

Editable
```

This practical scenario is demonstrated using the Asset Request Management application. :contentReference[oaicite:4]{index=4}

---

# Complete Example

```javascript
if(replacement=="true")
{

    g_form.setMandatory(
    'u_due_date',
    true);

    g_form.setDisplay(
    'u_due_date',
    true);

    g_form.setMandatory(
    'u_assignment_group',
    true);

    g_form.setReadOnly(
    'u_asset_owners',
    true);

}
else
{

    g_form.setMandatory(
    'u_due_date',
    false);

    g_form.setDisplay(
    'u_due_date',
    false);

    g_form.setMandatory(
    'u_assignment_group',
    false);

    g_form.setReadOnly(
    'u_asset_owners',
    false);

}
```

---

# setLabelOf()

Changes the label dynamically.

Syntax

```javascript
g_form.setLabelOf(
'u_assigned_to',
'SKFACTS'
);
```

Result

Before

```
Assigned To
```

After

```
SKFACTS
```

This method is useful when different business conditions require different labels for the same field. :contentReference[oaicite:5]{index=5}

---

# Real Project Scenario

Requirement

```
Replacement Request

TRUE

↓

Assigned To

↓

Replacement Engineer
```

Else

```
Assigned To

↓

Support Engineer
```

Instead of creating two fields,

change only the label dynamically.

---

# setValue()

One of the most important GlideForm methods.

Used to populate fields automatically.

Syntax

```javascript
g_form.setValue(
'field_name',
'value'
);
```

---

# Example 1

Replacement Request

```javascript
g_form.setValue(
'u_is_it_replacement_request',
'true'
);
```

---

# Example 2

Manager

```javascript
g_form.setValue(
'u_manager',
'a8f98bb0eb32010045e1a5115206fe3a'
);
```

The reference field requires the **Sys ID** of the user record, as demonstrated in the practical exercise. :contentReference[oaicite:6]{index=6}

---

# Example 3

Type of Asset

```javascript
g_form.setValue(
'u_type_of_asset',
'lp'
);
```

---

# Example 4

Ticket State

```javascript
g_form.setValue(
'u_ticket_state',
'6'
);
```

Output

```
Resolved
```

---

# Example 5

Multiple Reference Values

```javascript
g_form.setValue(
'u_asset_owners',
'06826bf03710200044e0bfc8bcbe5d8a,7682abf03710200044e0bfc8bcbe5d13'
);
```

Multiple reference records are passed as comma-separated Sys IDs. This example is covered in the uploaded notes using two Asset Owners. :contentReference[oaicite:7]{index=7}

---

# Example 6

Short Description

```javascript
g_form.setValue(
'u_short_description',
'Hello SK'
);
```

---

# Real Business Scenario

Suppose

Category

```
Database
```

Automatically

```
Assignment Group

↓

Database Team
```

Instead of asking users,

populate values automatically using `setValue()`. This type of automation is introduced as a common business requirement. :contentReference[oaicite:8]{index=8}

---

# clearValue()

Removes field value.

```javascript
g_form.clearValue(
'u_description'
);
```

Result

```
Description

↓

Empty
```

---

# Alternative

Same result

```javascript
g_form.setValue(
'u_description',
''
);
```

or

```javascript
g_form.setValue(
'u_description',
""
);
```

Both approaches clear the field, although `clearValue()` is clearer and more expressive. :contentReference[oaicite:9]{index=9}

---

# JavaScript Strings

Very important interview topic.

---

## Single Quotes

```javascript
'Hello'
```

---

## Double Quotes

```javascript
"Hello"
```

Both are valid.

---

# Correct Example

```javascript
g_form.setValue(
'u_description',
'Sreedhar is a good boy'
);
```

or

```javascript
g_form.setValue(
'u_description',
"Sreedhar is a good boy"
);
```

---

# Wrong Example

```javascript
'Sreedhar's house'
```

❌

JavaScript thinks

```
'Sreedhar'
```

ends the string.

---

# Correct

```javascript
"Sreedhar's house is beautiful"
```

or escape the apostrophe.

```javascript
'Sreedhar\'s house is beautiful'
```

---

# Execution Order

Suppose

```
Mandatory

↓

Display

↓

Read Only
```

The order matters.

Especially when hiding mandatory fields.

Always

```
Mandatory OFF

↓

Hide Field
```

This sequence prevents client-side validation issues.

---

# Practical Scenario

Employee raises a replacement request.

Automatically

```
Replacement

↓

TRUE

↓

Manager

↓

Abraham Lincoln

↓

Asset

↓

Laptop

↓

Ticket State

↓

Resolved

↓

Asset Owners

↓

Two Users

↓

Short Description

↓

Hello SK
```

This demonstrates how Client Scripts can populate an entire form without manual user input. :contentReference[oaicite:10]{index=10}

---

# Interview Tip

The trainer emphasized:

> **Interviews focus heavily on the Set Family.**

Expect practical questions like:

- Difference between `setDisplay()` and `setVisible()`
- Difference between `clearValue()` and `setValue("")`
- Why is order important when hiding mandatory fields?

---

# Bonus Tip

Suppose

Backend value

```
6
```

Display

```
Resolved
```

Use

```javascript
var backendState =
g_form.getValue(
'u_ticket_state'
);

var displayState =
g_form.getOption(
'u_ticket_state',
backendState
).text;

alert(
'Backend Value : '
+ backendState +
' Display Value : '
+ displayState
);
```

This technique retrieves both the stored value and the user-friendly display label, making debugging and scripting easier.

---

# Best Practices

✅ Use meaningful variable names.

✅ Use `clearValue()` for readability.

✅ Remove mandatory before hiding.

✅ Use Sys IDs for reference fields.

✅ Test every script using JavaScript Executor.

✅ Keep Client Scripts focused on one business requirement.

---

# Common Interview Questions

### Q1. Difference between Get Family and Set Family?

Get Family reads data.

Set Family modifies data.

---

### Q2. Which method makes a field mandatory?

```javascript
g_form.setMandatory()
```

---

### Q3. Which method hides a field?

```javascript
g_form.setDisplay()
```

---

### Q4. Which method makes a field read-only?

```javascript
g_form.setReadOnly()
```

---

### Q5. Which method changes a field label?

```javascript
g_form.setLabelOf()
```

---

### Q6. Which method clears a field?

```javascript
g_form.clearValue()
```

---

### Q7. Can a mandatory field be hidden?

Not unless the mandatory property is removed first.

---

### Q8. What should be passed to a reference field in `setValue()`?

The Sys ID of the referenced record.

---

# Assignment

Practice the following:

- `setMandatory()`
- `setDisplay()`
- `setReadOnly()`
- `setLabelOf()`
- `setValue()`
- `clearValue()`
- Single vs Double Quotes

### Practical Task

Create a Client Script that:

- Makes Due Date mandatory.
- Makes Assignment Group mandatory.
- Makes Asset Owners read-only.
- Changes the Assigned To label.
- Automatically populates Manager, Type of Asset, Ticket State, Asset Owners, and Short Description.
- Clears the Description field when required.
- Displays both backend and display values of the Ticket State.

---

# Key Takeaways

- The Set Family modifies form behavior dynamically.
- `setMandatory()`, `setDisplay()`, and `setReadOnly()` are among the most frequently used GlideForm methods.
- Always remove mandatory before hiding a field.
- Use `setValue()` to automate form population.
- `clearValue()` removes existing values cleanly.
- Handle JavaScript strings carefully when apostrophes are involved.
- Reference fields require Sys IDs, not display names.
- Understanding the Set Family is essential for real-world ServiceNow development and interviews.

---

# Official Reference

**GlideForm (g_form) API**

https://www.servicenow.com/docs/bundle/xanadu-api-reference/page/app-store/dev_portal/API_reference/GlideForm/concept/c_GlideFormAPI.html

---

# Phase 2 Progress

```
✅ Day 21 - Update Set Versioning & Deployment
✅ Day 22 - Backout & Best Practices
✅ Day 23 - Introduction to Client & Server Scripting
✅ Day 24 - GlideForm Basics & Decorations
✅ Day 25 - Advanced g_form Methods & Dynamic Choices
✅ Day 26 - GlideForm Set Family (setMandatory, setDisplay, setReadOnly, setValue)

Next →
Advanced Client Script APIs, Section Control, UI Manipulation & Server Communication
```

---

**End of Day 26**
