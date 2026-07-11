# 📅 Day 23
# 🚀 Introduction to ServiceNow Scripting, Client-Server Architecture & Client Scripts

> **Date:** 06/05/2026

---

# 🎯 Learning Objectives

In this session, we begin one of the most important topics in ServiceNow development—**Scripting**.

By the end of this session, you will understand:

- Why scripting is required in ServiceNow
- Client-Server Architecture
- Difference between Client-side and Server-side scripting
- Common Client-side scripting scenarios
- Common Server-side scripting scenarios
- Introduction to Client Scripts
- GlideForm (`g_form`)
- GlideUser (`g_user`)

---

# 📖 Why Do We Need Scripting?

ServiceNow provides many no-code and low-code features such as UI Policies, Data Policies, and Flow Designer. However, there are many business requirements that cannot be achieved using configuration alone.

This is where **JavaScript scripting** comes into the picture.

Scripting helps developers implement custom business logic, automate repetitive tasks, validate user input, and interact with records dynamically.

---

# 🚀 Why is Scripting Important?

Scripting is used because:

- Complex business requirements cannot always be solved through configuration.
- Repetitive tasks can be automated.
- Dynamic logic can be implemented.
- Manual effort is reduced.
- Applications become more intelligent and interactive.

### Real-world Example

Suppose an employee raises an Asset Request.

Requirement:

- If the selected asset type is **Laptop**, automatically assign it to the **Hardware Team**.
- If it is **Software**, assign it to the **Software Team**.

This dynamic behavior requires scripting.

---

# 📊 Why Beginners Find Scripting Difficult

Many beginners believe scripting is difficult because:

- They have little JavaScript practice.
- They are unfamiliar with ServiceNow APIs.
- They try to memorize instead of understanding.

The reality is:

> **Scripting becomes easier with consistent practice and by understanding real business scenarios.**

---

# 🏗 Client-Server Architecture

ServiceNow follows a **Client-Server Architecture**.

## Diagram

```
               User Browser
                     │
                     │
                     ▼
           Client-side JavaScript
                     │
                     │
        HTTP Request / Response
                     │
                     ▼
           ServiceNow Server
                     │
                     ▼
               Database
```

---

## Client

The Client is the user's browser.

It is responsible for:

- Showing forms
- Accepting user input
- Validating fields
- Displaying messages
- Calling the server when required

---

## Server

The Server is responsible for:

- Reading database records
- Updating records
- Running Business Rules
- Executing GlideRecord queries
- Returning data to the client

---

# 📌 Client-side Scripting

Client-side scripts execute inside the user's browser.

These scripts run instantly without communicating with the database (unless explicitly required).

---

## Common Client-side Activities

✔ Field Validation

✔ Mandatory Field Logic

✔ Auto Population

✔ Data Comparison

✔ Show/Hide Fields

✔ Read Only Fields

✔ Form Submission Validation

---

## Real-world Scenario

While creating an Incident,

If **Priority = Critical**

then

- Make "Reason" mandatory
- Display additional fields
- Prevent submission if Reason is empty

This logic executes immediately in the browser.

---

# 📌 UI Policy vs Client Script

Although both execute on the Client side, they serve different purposes.

| UI Policy | Client Script |
|-----------|---------------|
| No-code / Low-code | JavaScript |
| Simple Conditions | Complex Logic |
| Mandatory, Read-only, Visible | Full control using JavaScript |
| Easy to configure | Flexible and powerful |

### Example

**UI Policy**

If Priority = Critical

→ Make Description Mandatory

---

**Client Script**

If Priority = Critical

AND

Caller belongs to VIP Group

AND

Current Time > 6 PM

→ Show Escalation Message

This complex logic cannot be easily handled by UI Policies.

---

# 📌 Types of Client-side Scripting

ServiceNow provides several Client-side scripting options.

The most common is:

## Client Script

Client Scripts execute on the form.

Examples:

- OnLoad
- OnChange
- OnSubmit
- OnCellEdit

These will be covered in detail in upcoming sessions.

---

# 📌 GlideForm (g_form)

`g_form` is one of the most frequently used Client-side APIs.

It allows developers to interact with the current form.

---

## Common Operations

- Get Field Value
- Set Field Value
- Show Messages
- Hide Fields
- Make Fields Mandatory
- Make Fields Read Only
- Clear Values

---

## Example

```javascript
g_form.setMandatory('description', true);
```

Makes the Description field mandatory.

---

```javascript
g_form.setReadOnly('number', true);
```

Makes the Number field read-only.

---

```javascript
g_form.setValue('state', '2');
```

Sets the State field value.

---

## Real-world Scenario

If Asset Type = Laptop

Automatically populate:

Assignment Group = Hardware Team

using

```javascript
g_form.setValue();
```

---

# 📌 GlideUser (g_user)

`g_user` provides information about the currently logged-in user.

Examples:

- User Name
- User ID
- Roles
- User Preferences

---

## Example

Check if logged-in user has ITIL role.

```javascript
g_user.hasRole('itil');
```

---

## Real-world Scenario

Only users having the ITIL role should see a special message.

Client Script checks:

```
if(g_user.hasRole('itil'))
{
    // show message
}
```

---

# 📌 Server-side Scripting

Server-side scripts execute on the ServiceNow server.

Unlike Client-side scripts, these scripts can directly interact with the database.

---

## Common Server-side Components

- Business Rules
- GlideRecord
- Script Include
- Scheduled Jobs
- Background Scripts
- UI Actions
- Data Policies
- Dynamic Reference Qualifiers
- Fix Scripts

---

## Real-world Scenario

Requirement:

Find all Incident records where

Description is empty

Copy

Short Description

into

Description

This cannot be achieved using Client-side scripting because it requires updating multiple database records.

Instead,

Use:

- Business Rule
- Background Script
- GlideRecord

---

# 📊 Client vs Server

| Client-side | Server-side |
|-------------|-------------|
| Runs in Browser | Runs on Server |
| Faster Response | Database Access |
| Uses g_form | Uses GlideRecord |
| Form Interaction | Record Manipulation |
| Validation | Business Logic |

---

# 📌 Assignment

## Assignment 1

Create a UI Policy.

Requirement:

When

```
State = New
```

Show

```
New Reason
```

and make it Mandatory.

Repeat the same for every State value.

---

## Assignment 2

Understand the purpose of

- g_form
- g_user

Prepare examples where each API should be used.

---

# 💼 Interview Questions

### Q1. Why do we need scripting in ServiceNow?

Scripting is used to implement complex business logic, automate repetitive tasks, validate data, and perform operations that cannot be achieved using configuration alone.

---

### Q2. What is Client-side scripting?

Client-side scripting runs inside the user's browser and is mainly used for field validation, form interaction, auto-population, and UI behavior.

---

### Q3. What is Server-side scripting?

Server-side scripting runs on the ServiceNow server and interacts directly with the database using APIs such as GlideRecord.

---

### Q4. Difference between UI Policy and Client Script?

UI Policies are configuration-based and suitable for simple conditions, whereas Client Scripts use JavaScript to implement complex business logic.

---

### Q5. What is g_form?

`g_form` is a Client-side API used to interact with the current form, including getting and setting field values, making fields mandatory, hiding/showing fields, and displaying messages.

---

### Q6. What is g_user?

`g_user` is a Client-side API that provides information about the currently logged-in user, including roles, user ID, and username.

---

# 📌 Key Takeaways

- Scripting enables complex and dynamic business logic.
- ServiceNow follows a Client-Server Architecture.
- Client-side scripts improve user interaction.
- Server-side scripts handle database operations.
- `g_form` is the primary API for form manipulation.
- `g_user` provides information about the logged-in user.
- UI Policies are suitable for simple UI behavior, while Client Scripts offer greater flexibility.

---

# 🚀 Final Insight

> **"Configuration makes applications functional, but scripting makes them intelligent. A successful ServiceNow developer knows when to configure and when to write code."**
