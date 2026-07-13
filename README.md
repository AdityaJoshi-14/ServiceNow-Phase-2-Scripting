# 📘 Day 39 – Dynamic Reference Qualifiers & Script Includes (Server-Side)

**Date:** 29/05/2026

---

# 📖 Introduction

Today's session focused on **Dynamic Reference Qualifiers**, reusable **Script Includes**, and how ServiceNow dynamically filters reference field values using server-side scripting.

We began by revisiting **GlideRecord** queries using the **IN operator** to retrieve multiple records in a single query. We then explored **Dynamic Reference Qualifiers**, which allow developers to dynamically control which records appear in a reference field based on custom business logic.

A major highlight of today's session was understanding that **Script Includes are not only used with GlideAjax**. They can also be called directly from **server-side scripts** such as Dynamic Reference Qualifiers, Business Rules, Scheduled Jobs, UI Actions, etc.

Finally, we examined a real ServiceNow Script Include (`PAUtils`) that dynamically retrieves all users having the **pa_contributor** role and returns them as an encoded query.

---

# 🎯 Learning Objectives

After completing this session, you should be able to:

- Query multiple records using the **IN** operator.
- Understand Dynamic Reference Qualifiers.
- Call Script Includes from server-side code.
- Build dynamic encoded queries.
- Understand Performance Analytics (PA) contributor filtering.
- Return encoded query strings from Script Includes.
- Dynamically filter reference fields.

---

# Revisiting GlideRecord

## Scenario

Retrieve the Short Description for the following incidents:

```
INC0000015
INC0011706
INC1200053
INC0011292
INC1200064
```

---

## GlideRecord Script

```javascript
var gr = new GlideRecord('incident');

gr.addEncodedQuery('numberININC0000015,INC0011706,INC1200053,INC0011292,INC1200064');

gr.query();

while(gr.next())
{
    gs.print(gr.short_description);
}
```

---

# Understanding the Encoded Query

Instead of writing multiple conditions:

```text
Number = INC0000015

OR

Number = INC0011706

OR

Number = INC1200053

...
```

ServiceNow allows us to write:

```text
numberIN
INC0000015,
INC0011706,
INC1200053,
INC0011292,
INC1200064
```

which becomes

```text
numberININC0000015,INC0011706,INC1200053,INC0011292,INC1200064
```

This is much shorter and more efficient.

---

# Dynamic Reference Qualifier

## What is a Reference Qualifier?

A Reference Qualifier filters records displayed inside a reference field.

Example:

```
Caller

↓

Shows only Active Users
```

---

## What is a Dynamic Reference Qualifier?

Instead of using a fixed filter,

the filter is generated dynamically using a Script Include.

Example

```
Assignment Group

↓

Only Groups related to current user

↓

Generated at runtime
```

---

# Why Use Dynamic Reference Qualifiers?

Without Dynamic Reference Qualifiers

```
Reference Field

↓

All Records
```

With Dynamic Reference Qualifiers

```
Reference Field

↓

Only Valid Records
```

This improves:

- User Experience
- Data Accuracy
- Performance
- Business Logic Enforcement

---

# Script Include from Server Side

Unlike GlideAjax,

Server-side scripts call Script Includes directly.

Syntax

```javascript
new ClassName().FunctionName();
```

Example

```javascript
new PAUtils().getContributors();
```

---

# Understanding the Syntax

```javascript
new PAUtils().getContributors();
```

Where

```
PAUtils

↓

Class (Script Include)

↓

getContributors()

↓

Function
```

Equivalent representation

```
new Class().Function();
```

---

# Real Example

```javascript
new PAUtils().getContributors();
```

This executes

```
Class

↓

PAUtils

↓

Function

↓

getContributors()
```

---

# Script Include

```javascript
getContributors: function(){

    var contributors = [];

    var userRoles = new GlideRecord('sys_user_has_role');

    userRoles.addQuery('role.name','pa_contributor');

    userRoles.query();

    while(userRoles.next())
    {
        contributors.push(userRoles.getValue('user'));
    }

    return 'sys_idIN' + contributors.toString();

},
```

---

# Step-by-Step Explanation

## Step 1

Create an empty array

```javascript
var contributors = [];
```

Stores User Sys IDs.

---

## Step 2

Open Role Mapping Table

```javascript
sys_user_has_role
```

This table stores

```
User

↓

Role
```

relationships.

---

## Step 3

Filter

```javascript
role.name

=

pa_contributor
```

Retrieve only users having the

```
Performance Analytics Contributor
```

role.

---

## Step 4

Store User Sys IDs

```javascript
contributors.push(userRoles.getValue('user'));
```

Example

```text
[
62826bf03710200044e0bfc8bcbe5df1,

a8f98bb0eb32010045e1a5115206fe3a,

0a826bf03710200044e0bfc8bcbe5d7a,

71826bf03710200044e0bfc8bcbe5d3b,

2fc94d35eb32010045e1a5115206fe22
]
```

---

## Step 5

Convert Array

```javascript
contributors.toString()
```

Result

```text
62826bf03710200044e0bfc8bcbe5df1,

a8f98bb0eb32010045e1a5115206fe3a,

0a826bf03710200044e0bfc8bcbe5d7a,

71826bf03710200044e0bfc8bcbe5d3b,

2fc94d35eb32010045e1a5115206fe22
```

---

## Step 6

Return Encoded Query

```javascript
return 'sys_idIN' + contributors.toString();
```

Final Output

```text
sys_idIN

62826bf03710200044e0bfc8bcbe5df1,

a8f98bb0eb32010045e1a5115206fe3a,

0a826bf03710200044e0bfc8bcbe5d7a,

71826bf03710200044e0bfc8bcbe5d3b,

2fc94d35eb32010045e1a5115206fe22
```

---

# Why Return "sys_idIN"?

Dynamic Reference Qualifiers expect an **encoded query**.

Example

```
Reference Field

↓

sys_idIN

1,2,3,4,5
```

ServiceNow automatically filters only those records.

---

# Execution Flow

```text
Reference Field Opens

↓

Dynamic Reference Qualifier

↓

Script Include Executes

↓

GlideRecord

↓

Retrieve Users

↓

Store Sys IDs

↓

Create Encoded Query

↓

Return

↓

Reference Field Filtered
```

---

# Performance Analytics (PA)

PA stands for

```
Performance Analytics
```

It is a ServiceNow application used for:

- KPI Tracking
- Reports
- Dashboards
- Trends
- Forecasting
- Historical Metrics

The **pa_contributor** role grants users permissions to contribute to Performance Analytics.

---

# Difference Between GlideAjax & Direct Script Include

| GlideAjax | Direct Script Include |
|------------|-----------------------|
| Client Side | Server Side |
| Uses AbstractAjaxProcessor | Normal Script Include |
| Asynchronous | Direct Function Call |
| Used inside Client Scripts | Used inside Business Rules, Reference Qualifiers, Scheduled Jobs, etc. |

---

# Where Dynamic Reference Qualifiers Are Used

- Assignment Group
- Caller
- Requested For
- Manager
- Department
- Configuration Item (CI)
- Any Reference Field

---

# Interview Questions

## What is a Dynamic Reference Qualifier?

A server-side filter that dynamically controls which records appear in a reference field.

---

## Why use a Script Include?

To create reusable server-side logic that can be called from multiple places.

---

## How do you call a Script Include from server-side code?

```javascript
new ClassName().FunctionName();
```

Example

```javascript
new PAUtils().getContributors();
```

---

## Why return `sys_idIN`?

Because Dynamic Reference Qualifiers require an encoded query string to filter reference records.

---

## Which table stores User–Role mappings?

```
sys_user_has_role
```

---

## Which method converts an Array into a comma-separated string?

```javascript
toString()
```

---

## What does `contributors.push()` do?

It adds each user's Sys ID into the contributors array.

---

# Best Practices

- Use Script Includes for reusable server-side logic.
- Prefer Dynamic Reference Qualifiers over hardcoded filters.
- Return encoded queries (`sys_idIN`) for reference field filtering.
- Query only required records to improve performance.
- Store values in arrays before generating encoded queries.

---

# Assignment

- Practice GlideRecord using the **IN** operator.
- Create a Script Include that returns active users.
- Build a Dynamic Reference Qualifier using that Script Include.
- Experiment with filtering users by role.
- Understand how encoded query strings are generated.

---

# Key Takeaways

- `numberIN` retrieves multiple records using a single encoded query.
- Dynamic Reference Qualifiers filter reference fields dynamically.
- Script Includes can be invoked directly from server-side code.
- `new Class().Function()` is the syntax for server-side Script Include calls.
- `sys_user_has_role` stores user-role relationships.
- Arrays and `toString()` are commonly used to build encoded query strings.
- Returning `sys_idIN...` enables dynamic filtering of reference field records.

---

## ✅ End of Day 39 Notes

