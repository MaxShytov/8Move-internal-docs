# Template: End User Documentation

Documentation for end users of the Flutter applications (suppliers, clients, drivers, dispatchers).

**Location:** `docs/features/[app]/[feature-name]/user-guide.md`

---

## Template

```markdown
# [Feature Name] — User Guide

**App:** [Back Office / Supply Now / Fleet Planner / Driver Pro / DocuFlow]
**For:** [Suppliers / Clients / Dispatchers / Drivers / Accountants]

## What is [Feature Name]?

[1-2 sentences in plain language. No technical terms.]

**Example:** [Real-world scenario showing why this feature is useful]

## Getting Started

### Step 1: [First action]

[Description of what to do]

![Step 1 screenshot](images/step-1.png)

### Step 2: [Next action]

[Description of what to do]

![Step 2 screenshot](images/step-2.png)

### Step 3: [Next action]

[Description]

**Result:** [What the user should see after completing these steps]

## How To

### [Common task 1]

1. [Step]
2. [Step]
3. [Step]

### [Common task 2]

1. [Step]
2. [Step]

### [Common task 3]

1. [Step]
2. [Step]

## Tips

- **Tip 1:** [Useful shortcut or best practice]
- **Tip 2:** [Something that saves time]
- **Tip 3:** [Common mistake to avoid]

## FAQ

**Q: [Common question]?**

A: [Clear answer]

**Q: [Another question]?**

A: [Clear answer]

**Q: [Another question]?**

A: [Clear answer]

## Troubleshooting

### I can't see [something]

**Possible reasons:**
- [Reason 1] → [What to do]
- [Reason 2] → [What to do]

### [Something] is not working

**Try these steps:**
1. [Step]
2. [Step]
3. If it still doesn't work, contact support: support@8move.com

## Related Features

- [Related Feature 1](../[feature]/user-guide.md) — [brief description]
- [Related Feature 2](../[feature]/user-guide.md) — [brief description]
```

---

## Guidelines

- **No technical terms.** Write for someone who uses a smartphone daily but is not a programmer
- **Every action = screenshot.** Users should be able to follow along visually
- **Use the same words as the UI.** If the button says "Add Order", write "Click **Add Order**"
- **Name the app.** Always specify which app (Back Office, Supply Now, etc.)
- **Name the role.** Always specify who uses this feature (supplier, client, dispatcher)
- **Keep it short.** Each section should fit on one screen
- **FAQ answers < 50 words.** If longer, it's a "How To" section

### Screenshot requirements

- Save to `docs/features/[app]/[feature-name]/images/`
- Name format: `step-1-description.png` (e.g., `step-1-open-orders.png`)
- Annotate with arrows or highlights where the user should click
- Use the app in the **language matching the doc** (English screenshots for English docs)
- Crop to show only the relevant part of the screen

### Multi-language

User guides should eventually be translated to:

- `docs/user-guides/en/[app]/[feature].md` — English (source)
- `docs/user-guides/fr/[app]/[feature].md` — French
- `docs/user-guides/de/[app]/[feature].md` — German

Translate after the English guide is validated and stable.
