## 2026-02-16

### SN-02: Client Company Profile

- [x] Company tab: real data from API (name, official name, VAT, address, logo)
- [x] Company logo editing (camera/gallery → S3 upload)
- [x] Swiss-style language selectors (EN/DE/FR/IT abbreviation badges)
- [x] Company blocking management (admin self-block/unblock)
- [x] Personal tab: change password dialog
- [x] Personal tab: auto-save name fields (debounced 1.5s)
- [x] Addresses tab: full CRUD with Google Places autocomplete
- [x] Addresses tab: multi-company address display with company names
- [x] Addresses tab: purpose assignment (Primary/Billing/Registered) with color-coded badges
- [x] Company tab: purpose slots with address picker for each role
- [x] Backend: order_count annotation + edit/delete protection for addresses with orders
- [x] Backend: fix set-purpose (clear old purpose) + fix get_purpose (dict key collision)
- [x] Localization: all address strings in EN, DE, FR, IT
- [x] Company card redesign: section dividers (Addresses, Contact Info, Settings), VAT repositioned
- [x] Contact info section: website + email with admin editing via bottom sheet
- [x] Default delivery address: first in list, admin picker, checkout hint
- [x] Address comments displayed in Addresses tab cards
- [x] UX: image source pickers → bottom sheets, purpose picker title, InkWell, flexible labels, address count in tab
