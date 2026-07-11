# 📅 Phase 2 - Day 1
# 🚀 Update Set Versioning, Deployment Strategy & Industry Best Practices

> **Date:** 01/05/2026 - 02/05/2026

---

# 🎯 Learning Objectives

In this session, we explored the advanced concepts of **Update Sets**, including versioning, deployment strategies, preview issues, backout operations, and ServiceNow deployment best practices followed in real-world projects.

By the end of this session, you should be able to:

- Understand Update Set versioning.
- Follow the correct deployment lifecycle.
- Resolve preview issues.
- Perform Backout operations.
- Understand the limitations of Update Sets.
- Learn deployment best practices used in ServiceNow projects.

---

# 📖 What is an Update Set?

An **Update Set** is a container in ServiceNow that captures configuration changes made by developers. It is the standard mechanism used to migrate customizations from one instance to another, typically following the flow:

```
Development → Testing → Production
```

Instead of making direct changes in higher environments, developers create Update Sets in the Development instance and move them through the deployment pipeline.

---

# 📌 UI Policy Related List Actions

UI Policies are commonly used to dynamically control fields on a form, but they can also manage **Related Lists**.

### Example

Suppose an Incident is marked as **Closed**.

Instead of showing every related list, the UI Policy can display only:

- Resolution Notes
- Attachments
- Work Notes

This helps simplify the interface for end users.

---

# 📌 Update Set Versioning

Every enhancement or story should be maintained using different versions.

### Example

```
story_incident_changerole_v1.0
story_incident_changerole_v2.0
story_incident_changerole_v3.0
story_incident_changerole_v4.0
```

### Version Flow

```
v1.0
 │
 ▼
v2.0
 │
 ▼
v3.0
 │
 ▼
v4.0
```

Each version contains all previous work plus the latest modifications.

This approach makes deployments organized and traceable.

---

# 📌 Update Set Lifecycle

Every Update Set moves through multiple stages before reaching Production.

```
In Progress
      │
      ▼
Complete
      │
      ▼
Retrieved
      │
      ▼
Previewed
      │
      ▼
Committed
```

### Explanation

### 🟢 In Progress

Development is ongoing.

---

### 🟡 Complete

Development has finished and the Update Set is ready to be moved.

---

### 🔵 Retrieved

The Update Set has been imported into another instance.

---

### 🟣 Previewed

ServiceNow checks for conflicts before deployment.

---

### ✅ Committed

Changes are successfully applied.

---

# 📌 Naming Standards

Never use generic names such as:

```
Update1
My Changes
Testing
```

Instead, follow meaningful naming conventions.

Example:

```
story_incident_changerole_v1.0
story_asset_priorityreason_v2.0
story_problem_assignment_v3.0
```

Benefits:

- Easier tracking
- Better maintenance
- Simplified debugging
- Professional development practice

---

# 📌 Development Lifecycle

Every organization follows a controlled deployment pipeline.

```
Requirement
      │
      ▼
Development Instance
      │
      ▼
Testing Instance
      │
      ▼
User Acceptance Testing
      │
      ▼
Production
```

This process ensures that changes are validated before reaching end users.

---

# 📌 Never Develop in Test or Production

One of the biggest mistakes beginners make is modifying records directly in higher environments.

❌ Incorrect

```
Developer
      │
      ▼
Production
```

✅ Correct

```
Developer
      │
      ▼
Development
      │
      ▼
Testing
      │
      ▼
Production
```

Always perform development in the Development instance.

---

# 📌 Preview Issues

During deployment, ServiceNow performs a **Preview** to identify conflicts.

Common Preview Issues include:

- Dictionary conflicts
- Form Layout conflicts
- UI Policy conflicts
- Missing dependencies
- Version conflicts

Preview allows developers to resolve issues before committing changes.

---

# 📌 Real Scenario

Developer A changes:

```
Short Description
Maximum Length = 100
```

Developer B changes the same field in Test:

```
Maximum Length = 150
```

When the Update Set is moved,

ServiceNow detects a conflict.

The developer must compare:

- Local Version
- Remote Version

and choose the appropriate resolution.

---

# 📌 Moving Between Update Sets

After an Update Set has been moved to Testing,

❌ Never continue development inside the same Update Set.

Instead,

```
Complete Current Update Set
            │
            ▼
Create New Update Set
            │
            ▼
Continue Development
```

This ensures proper version management.

---

# 📌 Backout Update Sets

Sometimes a deployment introduces unexpected issues.

Instead of manually undoing every change, ServiceNow allows administrators to **Backout** an Update Set.

Deployment Order

```
v1
│
▼
v2
│
▼
v3
│
▼
v4
```

Rollback Order

```
v4
▲
│
v3
▲
│
v2
▲
│
v1
```

Backout always occurs in reverse order.

---

# 📌 XML Migration Limitations

Update Sets cannot migrate everything.

Examples include:

- Reports
- Dashboards
- Scheduled Reports
- Homepages
- Some Data Records

These items require separate migration strategies.

---

# 📌 Scoped Application Update Sets

Each Scoped Application maintains its own Update Sets.

```
Global Scope
        │
        ▼
Global Update Set

Scoped Application
        │
        ▼
Scoped Update Set
```

Global Update Sets cannot capture changes from another application scope.

---

# 📌 Batch vs Merged Update Sets

## Batch Update Sets (Recommended)

```
Update Set 1
Update Set 2
Update Set 3
        │
        ▼
Batch Deployment
```

Advantages:

- Easy rollback
- Better tracking
- Independent deployments

---

## Merged Update Sets (Not Recommended)

```
Update Set 1
      +
Update Set 2
      +
Update Set 3
      │
      ▼
Merged Update Set
```

Disadvantages:

- Difficult debugging
- Difficult rollback
- Complex deployments

---

# 📌 Industry Best Practices

✅ Follow proper naming conventions.

✅ Always develop in Development Instance.

✅ Never make direct changes in Test or Production.

✅ Move changes only through Update Sets.

✅ Preview every Update Set before committing.

✅ Resolve all Preview Issues before deployment.

✅ Never reuse an Update Set after it has been deployed.

✅ Use Update Sources whenever possible.

✅ Follow:

```
Development
      │
      ▼
Testing
      │
      ▼
Production
```

✅ Prefer Batch Update Sets over Merged Update Sets.

---

# 💼 Interview Questions

## What is an Update Set?

An Update Set is a container that captures configuration changes and is used to migrate those changes between ServiceNow instances.

---

## Why should Update Sets be versioned?

Versioning improves traceability, rollback capability, and maintenance.

---

## What are Preview Issues?

Preview Issues are conflicts detected during Update Set deployment before the Commit operation.

---

## Why should development never happen in Production?

Direct changes in Production can impact live users and bypass testing, increasing business risk.

---

## What is the difference between Batch and Merged Update Sets?

Batch Update Sets keep deployments independent, while Merged Update Sets combine multiple changes into one, making rollback and debugging difficult.

---

# 📝 Assignments

### Assignment 1

Development Instance

- Create Update Set 1
- Implement a UI Policy
- Mark it Complete

Create another Update Set

- Add UI Policy Actions
- Mark it Complete

---

### Assignment 2

Testing Instance

- Retrieve Update Sets
- Preview Changes
- Resolve Preview Issues
- Commit Successfully

---

# 🎯 Key Takeaways

- Update Sets are the backbone of ServiceNow deployments.
- Always maintain proper versioning.
- Never reuse deployed Update Sets.
- Resolve Preview Issues before Commit.
- Understand Backout before deploying to Production.
- Follow the Development → Testing → Production lifecycle.

---

# 🚀 Final Insight

> **"Writing code is only one part of ServiceNow development. The real skill lies in deploying changes safely, managing versions effectively, and maintaining system stability through proper Update Set practices."**

