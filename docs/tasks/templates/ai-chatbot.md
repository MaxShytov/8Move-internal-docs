# Template: AI Chatbot Documentation

This template is used to create documentation for **two AI systems**:

1. **Customer support chatbot** — answers client questions about the platform
2. **Claude Code (_ai_context)** — helps developers understand the codebase

---

## Part 1: Customer Support Chatbot

**Location:** `docs/support/chatbot/[feature-name].md`

**Style:** FAQ format, simple language, no technical jargon.

### Template

```markdown
# [Feature Name] — Support Knowledge Base

## What is [Feature Name]?

[1-2 sentence explanation in simple terms]

## Frequently Asked Questions

### Q: How do I [common action]?

**A:** [Step-by-step answer]

1. Go to [screen/page]
2. Click [button/menu]
3. [Next step]

### Q: Why can't I see [something]?

**A:** This usually happens because:
- [Reason 1]
- [Reason 2]

**Solution:** [What to do]

### Q: What happens when [scenario]?

**A:** [Clear explanation of the behavior]

## Common Issues

### Issue: [Problem description]

**Symptoms:** [What the user sees]
**Cause:** [Why it happens]
**Solution:** [How to fix it]

### Issue: [Another problem]

**Symptoms:** [What the user sees]
**Cause:** [Why it happens]
**Solution:** [How to fix it]

## Escalation

If the above solutions don't help, collect:
- User email
- Screenshot of the error
- Steps to reproduce
- App name and version

Forward to: support@8move.com
```

### Guidelines

- Write as if explaining to someone who has never used the platform
- Use the same terms the user sees in the UI
- Include both English and French answers where possible (Swiss market)
- Each answer should be self-contained (no "see previous answer")
- Keep answers under 100 words

---

## Part 2: Claude Code Context

**Location:** `docs/_ai_context/` (update existing files or add new context)

**Purpose:** Help Claude Code understand new features when working on the codebase.

### What to update

After completing a feature, add relevant context to `_ai_context/` files:

```markdown
## [Feature Name] (added YYYY-MM-DD)

### Key Files
- `app/[app_name]/models.py` — [Model Name] model (lines XX-YY)
- `app/[app_name]/serializers.py` — [Serializer Name]
- `app/[app_name]/views.py` — [ViewSet Name]

### Architecture Decisions
- [Decision 1]: [Why it was made]
- [Decision 2]: [Trade-offs]

### Common Patterns
- [Pattern]: [How it's used in this feature]

### Gotchas
- [Thing that's not obvious but important]
- [Edge case that affects implementation]
```

### Guidelines

- Focus on "why" not "what" — Claude can read the code
- Document non-obvious decisions and trade-offs
- List related files with line numbers for quick reference
- Include gotchas and edge cases that could trip up future work
