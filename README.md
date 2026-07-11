# 📅 ServiceNow Phase 2 - Day 32

# 🚀 GlideRecord - addQuery() Method & Query Operators

> **Date:** 20/05/2026\
> **Module:** Server Side Scripting\
> **Topic:** GlideRecord Query Operators (`addQuery`) & Record Deletion

## Introduction

This session focuses on the GlideRecord `addQuery()` method and
different query operators used to retrieve, update, and delete records
efficiently. It extends previous concepts such as `addActiveQuery()`,
`addInactiveQuery()`, `addNullQuery()`, `addNotNullQuery()`,
`addEncodedQuery()`, and `getRowCount()`.

## Learning Objectives

-   Understand `addQuery()`
-   Learn query operators
-   Build multiple conditions
-   Update and delete records
-   Understand CRUD operations

## Syntax

``` javascript
gr.addQuery(field, operator, value);
gr.addQuery(field, value); // Equality
```

## Scenarios

### 1. Software Incidents

``` javascript
gr.addQuery('category','=','software');
```

### 2. Non-Software Incidents

``` javascript
gr.addQuery('category','!=','software');
```

### 3. Date Query

``` javascript
gr.addQuery('sys_created_on','<','2026-05-01 00:00:01');
```

### 4. Updated More Than 1000 Times

``` javascript
gr.addQuery('sys_mod_count','>','1000');
```

### 5. String Operators

-   =
-   !=
-   STARTSWITH
-   ENDSWITH
-   CONTAINS
-   DOES NOT CONTAIN

### 6. IN Operator

``` javascript
gr.addQuery('number','IN','INC001,INC002');
```

### 7. NOT IN Operator

``` javascript
gr.addQuery('number','NOT IN','INC001,INC002');
```

### 8. Multiple addQuery()

``` javascript
gr.addQuery('active',true);
gr.addQuery('category','software');
gr.addQuery('short_description','CONTAINS','skfacts');
```

Each addQuery adds an **AND** condition.

## Delete Methods

### deleteRecord()

Deletes one record at a time.

### deleteMultiple()

Deletes all matching records together.

## CRUD

  Operation   Method
  ----------- ----------------------------------
  Create      insert()
  Read        query()
  Update      update()
  Delete      deleteRecord(), deleteMultiple()

## Other Methods

-   next()
-   hasNext()
-   setLimit()
-   orderBy()
-   setWorkflow()
-   autoSysFields()
-   get()
-   setValue()
-   initialize()

## Interview Questions

1.  Difference between addQuery() and addEncodedQuery()?
2.  Difference between deleteRecord() and deleteMultiple()?
3.  What is sys_mod_count?
4.  What is sys_created_on?

## Best Practices

-   Verify record count before updates.
-   Use logs for deletion tracking.
-   Test in PDI before Production.

## Assignment

-   Practice all addQuery operators.
-   Practice deleteRecord() and deleteMultiple().

## Phase Progress

Day21 ✅ Day22 ✅ Day23 ✅ Day24 ✅ Day25 ✅ Day26 ✅ Day27 ✅ Day28 ✅
Day29 ✅ Day30 ✅ Day31 ✅ Day32 ✅
