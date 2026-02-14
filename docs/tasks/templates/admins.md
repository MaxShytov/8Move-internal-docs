# Template: Platform Admin Documentation

Documentation for platform administrators who manage the system through Django Admin and configuration panels.

**Location:** `docs/features/[app]/[feature-name]/admin-guide.md`

---

## Template

```markdown
# [Feature Name] — Admin Guide

## What This Feature Does

[1-2 sentences explaining the feature from the admin's perspective]

## Where to Find It

**Django Admin:** [http://localhost:8000/admin/](http://localhost:8000/admin/)

Navigate to: **[App Label]** → **[Model Name]**

## Managing [Resources]

### Viewing [Resources]

1. Go to **Admin** → **[App]** → **[Model]**
2. You will see a list of all [resources] with columns:
   - [Column 1] — [what it shows]
   - [Column 2] — [what it shows]
   - [Status] — Active/Inactive

**Filters available:**
- By status (Active / Inactive)
- By [field] ([options])
- Search by [fields]

### Creating a New [Resource]

1. Click **"Add [Resource]"** button
2. Fill in the required fields:
   - **[Field 1]** — [what to enter, any rules]
   - **[Field 2]** — [what to enter, any rules]
   - **[Field 3]** (optional) — [what to enter]
3. Click **Save**

!!! warning "Important"
    [Any critical rules or constraints the admin should know]

### Editing a [Resource]

1. Click on the [resource] name in the list
2. Modify the fields as needed
3. Click **Save**

### Deactivating / Deleting

**To deactivate:** Change status to **Inactive**. The [resource] will no longer appear in the apps but data is preserved.

**To delete:** Click **Delete** button. This is permanent.

!!! danger "Before deleting"
    Check that no [related objects] are linked to this [resource]. If there are active links, deactivate instead of deleting.

## Configuration

### [Setting Group Name]

| Setting | Location | Default | Description |
|---------|----------|---------|-------------|
| [Setting 1] | Admin → [path] | [default] | [what it controls] |
| [Setting 2] | Admin → [path] | [default] | [what it controls] |

### [Another Setting Group]

[Description of settings and how to configure them]

## Inline Management

When editing a [parent resource], you can manage related objects inline:

- **[Related Object 1]** — [what it does, how to add/remove]
- **[Related Object 2]** — [what it does, how to add/remove]

## Common Admin Tasks

### Task: [Common operation name]

**When:** [When this needs to be done]
**Steps:**

1. [Step 1]
2. [Step 2]
3. [Step 3]

**Result:** [What happens after completing these steps]

### Task: [Another common operation]

**When:** [When this needs to be done]
**Steps:**

1. [Step 1]
2. [Step 2]

## Troubleshooting

### Problem: [Common issue]

**Symptoms:** [What the admin sees]
**Solution:** [Step-by-step fix]

### Problem: [Another issue]

**Symptoms:** [What the admin sees]
**Solution:** [Step-by-step fix]

## Related

- [User Guide](user-guide.md) — for end users
- [Technical Docs](../../architecture/django-backend/workflows/[feature].md) — for developers
```

---

## Guidelines

- Write for someone who knows Django Admin basics but not the business logic
- Include exact navigation paths ("Admin → App → Model")
- Show what each field means and what values are valid
- Warn about destructive actions (delete, deactivate)
- Document inline editing for related objects
- Include "Common Tasks" section with real scenarios
- No code snippets — this is for non-developers
