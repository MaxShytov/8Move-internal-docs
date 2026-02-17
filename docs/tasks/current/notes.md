# Working Notes

## 2026-02-17

- [x] Client form UX: SegmentedButton for type, merged Industry+Description into Basic Info, group dividers, red asterisks for required, "Optional" helper text, ID in header subtitle, secondary headers for settings sections
- [x] Client form: moved logo to inline 128px avatar with camera picker (reuses Profile avatar pattern) next to Company Name / Official Name fields

## 2026-02-16

- [x] Created shared `userProfileProvider` in shared_core (state, notifier, provider with auth listener)
- [x] Created `ProfilePersonalTab` in shared_ui (avatar, name auto-save, change password, language, logout)
- [x] Created `SharedProfileScreen` shell in shared_ui (title, tabs, desktop responsive, leading widget)
- [x] Created `ProfilePersonalLabels` model for localization
- [x] Integrated profile screen in Back Office (new screen + /profile route)
- [x] Integrated profile screen in Fleet Planner (new screen + /profile route)
- [x] Integrated profile screen in Driver Pro (new screen + /profile route + bottom nav)
- [x] Replaced DocuFlow profile with shared version, removed custom DioService and UserProvider
- [x] Refactored Supply Now profile to use SharedProfileScreen with 4 tabs
- [x] Back Office: removed language switcher, email, popup menu from dashboard header; avatar now navigates directly to /profile
- [x] Fleet Planner: removed language switcher from dashboard header; fixed avatar to show user image
- [x] Added back arrow (leading widget) to Back Office and Fleet Planner profile screens
- [ ] Full localization verification across all apps (arb files)
- [ ] Manual end-to-end testing (avatar upload, change password, language switch, logout)
