# 📘 ServiceNow Phase 2 -- Day 33

# GlideRecord Advanced Methods

**Date:** 21/05/2026

------------------------------------------------------------------------

# 📖 Introduction

In previous sessions we learned how to retrieve, filter, update and
delete records using GlideRecord.

In this class we extend those concepts by learning advanced GlideRecord
methods commonly used in real-world ServiceNow development:

-   getValue()
-   setValue()
-   orderBy()
-   orderByDesc()
-   setLimit()
-   next()
-   \_next()
-   hasNext()
-   setWorkflow()
-   autoSysFields()
-   initialize()
-   insert()

These methods are frequently asked in ServiceNow interviews and are
widely used in Business Rules, Background Scripts, Scheduled Jobs,
Script Includes and Fix Scripts.

------------------------------------------------------------------------

# 🎯 Learning Objectives

-   Read field values using getValue()
-   Update values using setValue()
-   Sort query results
-   Limit returned records
-   Understand next(), \_next() and hasNext()
-   Disable Business Rules using setWorkflow()
-   Prevent system fields from updating using autoSysFields()
-   Insert records programmatically using initialize() and insert()

------------------------------------------------------------------------

# 1. getValue() and setValue()

## getValue()

Returns the backend value of a field.

``` javascript
var sd = gr.getValue('short_description');
```

## setValue()

Updates a field value safely.

``` javascript
gr.setValue('description', sd);
```

### Example

``` javascript
var gr = new GlideRecord('sc_task');
gr.addEncodedQuery('descriptionISEMPTY');
gr.query();

while(gr.next()){
    var sd = gr.getValue('short_description');
    gr.setValue('description',sd);
    gr.update();
}
```

------------------------------------------------------------------------

# 2. orderBy()

Sorts records in Ascending order.

``` javascript
gr.orderBy('number');
```

Example:

``` javascript
var gr=new GlideRecord('incident');
gr.addQuery('category','software');
gr.orderBy('number');
gr.query();
```

------------------------------------------------------------------------

# 3. orderByDesc()

Sorts records in Descending order.

``` javascript
gr.orderByDesc('number');
```

Example:

``` javascript
var gr=new GlideRecord('incident');
gr.addQuery('category','software');
gr.orderByDesc('number');
gr.setLimit(5);
gr.query();

while(gr.next()){
    gs.print(gr.number+" : "+gr.short_description);
}
```

------------------------------------------------------------------------

# 4. setLimit()

Restricts the number of returned records.

``` javascript
gr.setLimit(10);
```

Useful for:

-   Dashboards
-   Reports
-   Scheduled Jobs
-   Performance Optimization

------------------------------------------------------------------------

# Real World Scenario

Retrieve the latest 10 software incidents.

``` javascript
var gr=new GlideRecord('incident');

gr.addQuery('category','software');
gr.orderByDesc('sys_created_on');
gr.setLimit(10);
gr.query();

while(gr._next()){
    gs.print(gr.number+" : "+gr.short_description);
}
```

------------------------------------------------------------------------

# 5. next() vs \_next() vs hasNext()

## next()

Moves GlideRecord to the next record.

``` javascript
while(gr.next()){
}
```

------------------------------------------------------------------------

## \_next()

Internal variation used similarly.

``` javascript
while(gr._next()){
}
```

------------------------------------------------------------------------

## hasNext()

Checks whether records exist without moving the pointer.

``` javascript
if(gr.hasNext()){
    while(gr.next()){
        gs.print(gr.number);
    }
}
else{
    gs.print("NO RESULTS");
}
```

------------------------------------------------------------------------

# Comparison

  Method      Purpose
  ----------- ---------------------------
  next()      Move to next record
  \_next()    Internal iteration
  hasNext()   Check record availability

------------------------------------------------------------------------

# 6. setWorkflow()

Prevents Business Rules, Flows and other workflow logic from executing.

``` javascript
gr.setWorkflow(false);
```

Example:

``` javascript
var gr=new GlideRecord('incident');
gr.addQuery('category','software');
gr.query();

while(gr.next()){
    gr.work_notes="Today is Thursday";
    gr.setWorkflow(false);
    gr.update();
}
```

Use when performing bulk updates.

------------------------------------------------------------------------

# 7. autoSysFields()

Prevents automatic updates to:

-   sys_updated_by
-   sys_updated_on
-   sys_created_by
-   sys_created_on
-   sys_mod_count

``` javascript
gr.autoSysFields(false);
```

Example:

``` javascript
var gr=new GlideRecord('incident');
gr.addQuery('number','INC0010516');
gr.query();

if(gr.next()){
    gr.work_notes="Hello SK";
    gr.autoSysFields(false);
    gr.update();
}
```

Often combined with:

``` javascript
gr.setWorkflow(false);
```

------------------------------------------------------------------------

# 8. initialize() and insert()

Create records without opening the form.

Execution Flow

    Create GlideRecord

    ↓

    initialize()

    ↓

    Populate Fields

    ↓

    insert()

    ↓

    Record Created

Example

``` javascript
var gr=new GlideRecord('incident');

gr.initialize();

gr.short_description="Have a nice day";
gr.caller_id="a8f98bb0eb32010045e1a5115206fe3a";
gr.state=2;

gr.insert();

gs.print(gr.number);
```

------------------------------------------------------------------------

# CRUD Summary

  Operation   Method
  ----------- ----------------------------------
  Create      initialize(), insert()
  Read        query(), next(), hasNext()
  Update      setValue(), update()
  Delete      deleteRecord(), deleteMultiple()

------------------------------------------------------------------------

# Interview Questions

### Difference between getValue() and setValue()?

-   getValue() reads a field.
-   setValue() updates a field.

### Difference between orderBy() and orderByDesc()?

-   orderBy() → Ascending
-   orderByDesc() → Descending

### Why use setLimit()?

Improves performance by limiting returned records.

### Difference between next() and hasNext()?

-   next() moves to the next record.
-   hasNext() only checks if records exist.

### Why use setWorkflow(false)?

To avoid Business Rules and workflow execution during bulk updates.

### Why use autoSysFields(false)?

To preserve system audit fields.

### Purpose of initialize()?

Creates an empty record before insert().

------------------------------------------------------------------------

# Best Practices

-   Use getValue()/setValue() for cleaner scripts.
-   Always use setLimit() where possible for performance.
-   Use orderByDesc() to fetch recent records.
-   Use hasNext() before processing expected single results.
-   Use setWorkflow(false) carefully.
-   Avoid disabling autoSysFields() unless required.
-   Always test insert scripts in a PDI.

------------------------------------------------------------------------

# Assignment

-   Practice initialize() and insert().
-   Practice setLimit() with orderBy().
-   Compare next(), \_next() and hasNext().
-   Practice autoSysFields() and setWorkflow().
-   Create incidents without using forms.

------------------------------------------------------------------------

# Key Takeaways

-   getValue() reads backend values.
-   setValue() safely updates fields.
-   orderBy() and orderByDesc() control sorting.
-   setLimit() improves performance.
-   hasNext() checks record existence.
-   setWorkflow(false) disables workflow execution.
-   autoSysFields(false) preserves audit fields.
-   initialize() and insert() create records programmatically.

------------------------------------------------------------------------

# 🚀 Phase 2 Progress

    Day 21 ✅
    Day 22 ✅
    Day 23 ✅
    Day 24 ✅
    Day 25 ✅
    Day 26 ✅
    Day 27 ✅
    Day 28 ✅
    Day 29 ✅
    Day 30 ✅
    Day 31 ✅
    Day 32 ✅
    Day 33 ✅

**Next Topic:** More GlideRecord methods and advanced server-side
scripting.
