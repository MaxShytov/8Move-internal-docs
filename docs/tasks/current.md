# Current Task

!!! info "Last updated: 2026-02-14"
    This page reflects the current active task. The full task file lives in the main project repository at `docs/_ai_context/CURRENT_TASK_*.md`.

---

## Multi-Supplier Client Access Verification

**Status:** :material-progress-clock: In Progress
**Priority:** Medium
**Estimated effort:** 4-6 hours

### Description

Verify that the multi-supplier architecture (one client linked to multiple suppliers via `ClientSupplierLink`) works correctly across all three applications:

- **Supply Now** — client sees all their linked suppliers
- **Back Office** — supplier sees only their own clients
- **Admin Panel** — platform admin sees all links between clients and suppliers

### Scope

**In scope:**

- Manual UI testing across all 3 apps (checklist scenarios)
- Automated tests for RLA (Row-Level Access) filtering
- Verify seed data is sufficient for test scenarios
- Test `is_blocked` mechanism
- Test edge cases (0 suppliers, 1 supplier, 5 suppliers)

**Out of scope:**

- Creating new functionality
- Changing RLA logic (verification only)
- Performance testing
- UI/UX improvements

### Success Criteria

- [ ] All manual test cases passed (Supply Now, Back Office, Admin)
- [ ] Automated tests written and passing
- [ ] Data isolation between suppliers confirmed
- [ ] Multiple links working (client with 5 suppliers)
- [ ] Blocking mechanism (`is_blocked`) confirmed
- [ ] Seed data sufficient (or supplemented) for all scenarios
- [ ] Issues documented

### Key Risks

| Risk | Severity | Description |
|------|----------|-------------|
| Order RLA filtering | :material-alert: High | `Order.rla_roles_filter` filters only by supplier — clients may not see their orders |
| ClientSupplierLink RLA | :material-alert-outline: Medium | No custom `rla_roles_filter()` — API may return empty data for non-admins |
| Blocking gaps | :material-alert-outline: Medium | `is_blocked` may not be checked in all places (orders, products, UI) |

---

!!! tip "Full task details"
    The complete task file with research, plan, and implementation tracking is at:
    `docs/_ai_context/CURRENT_TASK_Multi_Supplier_Verification.md`
