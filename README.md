# 📘 Day 38 – Script Include & GlideAjax (Client ↔ Server Communication)

**Date:** 27/05/2026

---

# 📖 Introduction

Today's session introduced one of the most important concepts in ServiceNow scripting — **Script Includes** and **GlideAjax**.

Previously, we learned methods like **getReference()** and **Display Business Rules** to fetch data from the server to the client. However, these methods have limitations.

Script Includes, when combined with **GlideAjax**, provide a powerful mechanism for **two-way communication** between Client Scripts and Server Scripts. This allows Client Scripts to send data to the server, execute server-side logic, retrieve results, and use those results dynamically on the form.

The practical implementation involved automatically populating a user's First Name, Last Name, Manager, and Full Name when an email address is entered on the Asset Request form. The uploaded notes walk through the complete setup of a GlideAjax-enabled Script Include, Client Script, and the end-to-end data flow with screenshots. :contentReference[oaicite:0]{index=0}

---

# 🎯 Learning Objectives

After completing this session, you should be able to:

- Understand Script Includes
- Understand GlideAjax
- Understand Client ↔ Server communication
- Pass values from Client Script to Server
- Return server-side data back to the client
- Auto-populate fields using Script Includes
- Understand AbstractAjaxProcessor
- Understand getParameter()
- Understand asynchronous execution using GlideAjax

---

# Ways to Get Server Values on Client

ServiceNow provides multiple ways to retrieve server-side values:

| Method | Direction |
|----------|-----------|
| getReference() | Server → Client |
| Display Business Rule | Server → Client |
| Script Include + GlideAjax | Client ↔ Server |

---

# Scenario

### Requirement

When the **Email** field changes on the Asset Request form:

- Retrieve the corresponding User record.
- Fetch:
  - First Name
  - Last Name
  - Manager
  - Full Name
- Automatically populate these values on the form.

---

# Why Can't GlideRecord Be Used in Client Script?

Example:

```javascript
var gr = new GlideRecord('sys_user');
```

This **cannot** be written inside a Client Script because:

- GlideRecord is a **Server-side API**
- Client Scripts execute inside the browser
- Browsers cannot directly access the ServiceNow database

Therefore, we use:

```
Client Script
        ↓
GlideAjax
        ↓
Script Include
        ↓
Database
        ↓
Response
        ↓
Client Script
```

---

# Communication Flow

```
User changes Email

↓

Client Script

↓

GlideAjax

↓

Script Include

↓

GlideRecord Query

↓

Database

↓

Return Result

↓

Client Script

↓

Populate Fields
```

---

# What is GlideAjax?

GlideAjax is a **Client-side API** used to call a **Script Include**.

It provides asynchronous communication between Client Scripts and Server Scripts.

---

# AJAX

AJAX stands for:

```
Asynchronous
JavaScript
And
XML
```

The browser continues working while the server processes the request.

---

# Script Include

A Script Include is reusable server-side JavaScript that can be called from:

- Business Rules
- Client Scripts (using GlideAjax)
- UI Actions
- Other Script Includes

To make it callable from the client:

- Enable **Client Callable (GlideAjax enabled)**
- Extend `AbstractAjaxProcessor`

The uploaded notes show the Script Include configuration with the **Client Callable** option enabled and the resulting code template. :contentReference[oaicite:1]{index=1}

---

# Script Include

```javascript
var UserAttributes = Class.create();

UserAttributes.prototype = Object.extendsObject(AbstractAjaxProcessor, {

    uservalues: function() {

        var xyz = this.getParameter('bangalore');

        var gr = new GlideRecord('sys_user');

        gr.addQuery('email', xyz);

        gr.query();

        if (gr.next()) {

            return gr.first_name + "&" +
                   gr.manager + "&" +
                   gr.last_name + "&" +
                   gr.name;

        }

    },

    type: 'UserAttributes'

});
```

---

# Understanding the Script

## Create Class

```javascript
Class.create()
```

Creates a reusable ServiceNow class.

---

## AbstractAjaxProcessor

```javascript
Object.extendsObject(AbstractAjaxProcessor)
```

Allows the Script Include to receive GlideAjax requests.

---

## Function

```javascript
uservalues()
```

This function is called from the Client Script.

---

## Reading Client Parameters

```javascript
this.getParameter('bangalore');
```

Retrieves the email address sent from the Client Script.

---

## Query User

```javascript
gr.addQuery('email', xyz);
```

Retrieve the user record matching the email.

---

## Return Data

```javascript
return first_name
       + "&"
       + manager
       + "&"
       + last_name
       + "&"
       + name;
```

Example

```
Abraham&
62826bf03710200044e0bfc8bcbe5df&
Lincoln&
Abraham Lincoln
```

---

# Client Script

```javascript
function onChange(control, oldValue, newValue, isLoading, isTemplate) {

    if (isLoading || newValue === '') {

        return;

    }

    var em = g_form.getValue('u_email');

    var ga = new GlideAjax('global.UserAttributes');

    ga.addParam('sysparm_name', 'uservalues');

    ga.addParam('bangalore', em);

    ga.getXML(pop);

    function pop(response) {

        var sk = response.responseXML.documentElement.getAttribute("answer");

        var arrresult = sk.split("&");

        g_form.setValue('u_first_name', arrresult[0]);

        g_form.setValue('u_last_name', arrresult[2]);

        g_form.setValue('u_manager', arrresult[1]);

        g_form.setValue('u_detailed_of_prev_experience', arrresult[3]);

    }

}
```

---

# Client Script Explanation

## Read Email

```javascript
g_form.getValue('u_email')
```

Gets the email entered by the user.

---

## Create GlideAjax Object

```javascript
new GlideAjax('global.UserAttributes')
```

Calls the Script Include.

---

## Specify Function

```javascript
ga.addParam('sysparm_name','uservalues');
```

Specifies which Script Include function should execute.

---

## Send Email

```javascript
ga.addParam('bangalore', em);
```

Passes the email address to the server.

---

## Execute

```javascript
ga.getXML(pop);
```

Calls the server asynchronously.

---

# Callback Function

```javascript
function pop(response)
```

Runs automatically after the server sends the response.

---

# Reading Server Response

```javascript
response.responseXML.documentElement.getAttribute("answer")
```

Returns the string sent by the Script Include.

Example

```
Abraham&
62826bf03710200044e0bfc8bcbe5df&
Lincoln&
Abraham Lincoln
```

---

# Splitting Data

```javascript
var arrresult = sk.split("&");
```

Result

```
[
Abraham,
62826bf03710200044e0bfc8bcbe5df,
Lincoln,
Abraham Lincoln
]
```

---

# Populate Form

```javascript
g_form.setValue('u_first_name', arrresult[0]);

g_form.setValue('u_last_name', arrresult[2]);

g_form.setValue('u_manager', arrresult[1]);

g_form.setValue('u_detailed_of_prev_experience', arrresult[3]);
```

The uploaded notes demonstrate the form before and after the email lookup, showing the automatic population of these fields. :contentReference[oaicite:2]{index=2}

---

# Execution Flow

```
User enters Email

↓

Client Script

↓

Read Email

↓

GlideAjax

↓

Script Include

↓

Read Parameter

↓

GlideRecord

↓

Query User

↓

Return Data

↓

Callback Function

↓

Split String

↓

Populate Fields
```

---

# Important APIs

### Client Side

- g_form
- GlideAjax

---

### Server Side

- GlideRecord
- Script Include
- AbstractAjaxProcessor

---

# Interview Questions

## What is GlideAjax?

A Client-side API used to call a Script Include asynchronously.

---

## Why use Script Include?

To execute reusable server-side logic that can be invoked from Client Scripts.

---

## Why can't GlideRecord be used in Client Script?

GlideRecord is a server-side API and cannot directly access the database from the browser.

---

## What is AbstractAjaxProcessor?

A base class that enables Script Includes to process GlideAjax requests.

---

## What does getParameter() do?

Retrieves parameters sent from the Client Script.

Example

```javascript
this.getParameter('bangalore');
```

---

## What is sysparm_name?

A mandatory GlideAjax parameter that specifies which Script Include function should execute.

---

## Why use split("&")?

Because the Script Include returns a single string containing multiple values separated by `&`. Splitting converts it into an array for easy access.

---

# Best Practices

- Use Script Includes for reusable server-side logic.
- Always extend `AbstractAjaxProcessor` for client-callable Script Includes.
- Keep responses lightweight.
- Prefer asynchronous GlideAjax (`getXML`) over synchronous calls.
- Use delimiters consistently when returning multiple values.
- Validate input parameters before querying the database.

---

# Assignment

- Create a Script Include to fetch user details based on email.
- Use GlideAjax from an onChange Client Script.
- Auto-populate First Name, Last Name, Manager, and Full Name.
- Practice sending different parameters using `getParameter()`.
- Experiment with returning additional user attributes such as Department or Phone Number.

---

# Key Takeaways

- GlideRecord cannot be used directly inside Client Scripts.
- GlideAjax enables asynchronous communication between the client and server.
- Script Includes encapsulate reusable server-side logic.
- `AbstractAjaxProcessor` is required for client-callable Script Includes.
- `sysparm_name` specifies the server-side function to invoke.
- `getParameter()` retrieves values sent from the client.
- Combining GlideAjax with Script Includes enables dynamic form population and efficient client-server interactions.

---

## ✅ End of Day 38 Notes

