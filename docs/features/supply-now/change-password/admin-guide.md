# Change Password — Admin Guide

## What This Feature Does

Allows Supply Now users to change their password directly from the app's Profile screen. The feature validates the current password and enforces password strength requirements.

## Where to Find It

This is a self-service feature — users change their own password in the **Supply Now** app. There is no admin panel for this.

**Django Admin:** Password can also be changed by a Platform Admin via:

Navigate to: **Identity** → **Users** → select user → **Password** section

## How It Works

1. User opens **Profile** → **Personal** tab → **Change Password**
2. User enters current password, new password, and confirmation
3. App validates locally (min 8 chars, letters + numbers, match)
4. API validates server-side (checks current password is correct)
5. Password is updated in the database

## Password Requirements

| Rule | Requirement |
|------|-------------|
| Minimum length | 8 characters |
| Character types | Must contain at least one letter AND one number |
| Confirmation | New password must be entered twice and match |
| Current password | Must provide correct current password |

## Common Admin Tasks

### Task: Reset a user's password (if they forgot it)

**When:** User cannot log in and forgot their password.

**Steps:**

1. Go to **Django Admin** → **Identity** → **Users**
2. Find the user by email
3. Click on the user
4. Use the **password change form** link at the top
5. Set a new password and inform the user

**Result:** User can log in with the new password.

### Task: Check if a user changed their password recently

**When:** Investigating a security concern.

**Steps:**

1. There is no password change log currently
2. Check the user's `last_login` field in Django Admin — if it's recent, they likely logged in with a new password

## Troubleshooting

### Problem: User says "Current password is incorrect" but is sure it's right

**Symptoms:** User gets 400 error when trying to change password.
**Solution:**

1. Check if the user has multiple accounts (different emails)
2. Reset the password via Django Admin if needed
3. Verify no spaces or special characters are being copied accidentally

### Problem: User cannot see the Change Password option

**Symptoms:** "Change Password" row is missing in Profile.
**Solution:** Ensure the user is on the latest version of Supply Now. This feature was added in the 2026-02-16 release.

## Related

- [User Guide](user-guide.md) — for end users
- [Technical Docs](../../../architecture/django-backend/workflows/change-password.md) — for developers
