# Changelog

All notable documentation and platform changes are documented here.

---

## 2026-02-16

### Platform (Supply Now + Backend)

#### :material-new-box: Supply Now — Change Password

Users can now change their password directly from the Profile screen:

- **Change Password dialog**: current password, new password, confirm password
- **Client-side validation**: min 8 chars, must contain letters and numbers, passwords must match
- **Server-side validation**: checks current password correctness
- **Localized**: all labels translated to EN, DE, FR, IT

#### :material-wrench: Supply Now — Auto-Save Profile

Profile editing now uses auto-save instead of a manual Save button:

- **Name fields**: debounced auto-save (1.5s after typing stops)
- **Avatar**: instant save on upload or clear
- **Improved snackbars**: floating style with check/error icons
- **Supplier tab**: shows supplier count in tab label

#### :material-shield-lock: RLA Filters for Multi-Supplier

Fixed Row-Level Access (RLA) filters to ensure proper data isolation:

- **ClientSupplierLink**: added `rla_roles_filter()` — supplier users see their links, client users see their links
- **Order**: fixed `rla_roles_filter()` to include client user filtering (clients can now see their orders)
- **13 automated tests**: covering RLA isolation for suppliers, clients, cross-isolation, and edge cases

#### :material-database: New Seed Data

- **Swiss Timepieces Group SA**: 2 seed users (Philippe Aubert — admin, Caroline Roux — manager)
- **Standalone Boutique**: client with 0 suppliers + 1 admin user (Thomas Weber) — edge case testing

#### :material-cellphone: Supply Now — Client Company Profile

New "Company" tab on Profile screen showing real company data from the API:

- Company name, official name, VAT number, fiscal number
- Website, email, description
- Admin vs Manager role display

#### :material-api: New API Endpoints

| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/api/identity/users/me/change-password/` | Change current user's password (requires current password, validates strength) |

---

## 2026-02-15

### Documentation

#### :material-new-box: Users & Roles Section
Added comprehensive descriptions for all 6 user roles on the platform:

| Document | Description |
|----------|-------------|
| [Platform Admin](users/platform-admin.md) | Django Admin — full system management, onboarding, configuration |
| [Supplier — Back Office](users/supplier-back-office.md) | Product catalog, orders, invoicing, delivery, fleet |
| [Client — Supply Now](users/client-supply-now.md) | Catalog browsing, ordering, delivery tracking, profile |
| [Driver — Driver Pro](users/driver-driver-pro.md) | Route execution, GPS tracking, signatures, statistics |
| [Delivery Manager — Fleet Manager](users/delivery-manager-fleet-manager.md) | Route planning, fleet management, real-time tracking |
| [Employee — DocuFlow](users/employee-docuflow.md) | Receipt scanning, OCR, expense management |

Each document includes: screen/feature tables, Mermaid diagrams, role differences, and data flows.

#### :material-folder-move: Tasks Reorganized by App
Tasks are now grouped by application instead of flat list:

- **Back Office**: BO-01 Supplier Client Management
- **Supply Now**: SN-01 Multi-Supplier Verification, SN-02 Client Company Profile

#### :material-file-document-plus: BO-01 Task Added
New task [BO-01](tasks/current/back-office/BO-01.md) — Supplier Client Management (Block Clients & Edit Settings) for Back Office app.

### Platform (Supply Now + Backend)

#### :material-api: New API Endpoints

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/api/actors/suppliers/my-settings/` | Returns suppliers linked to current user's client with both supplier-controlled and client-controlled settings |
| POST | `/api/actors/suppliers/{id}/toggle-client-block/` | Toggles `is_blocked` on `SupplierSettingsClientControlled` (admin only) |

#### :material-cellphone: Supply Now — Real Supplier Data on Profile

Replaced hardcoded mock data ("Fresh Foods AG", "Metro Cash & Carry", "Old Supplier Ltd") on Profile > Suppliers tab with real data from API:

- **Supplier card**: name, logo, fulfillment scheme, invoicing period, blocking status
- **Admin toggle**: block/unblock supplier with confirmation dialog
- **Refresh**: pull-to-refresh + refresh button
- **Blocking priority**: "Blocked by Supplier" (red) overrides client toggle

#### :material-cellphone: Supply Now — Blocking in Supplier Selector

Both the Home tab selector and Categories screen selector now show blocking indicators:

- **Selector dropdown**: blocked suppliers shown with red/orange badges, reduced opacity
- **Compact selector button**: red border + block icon when selected supplier is blocked
- **Two-way blocking**: "Supplier blocked you" vs "You blocked supplier" with different messages

#### :material-shield-lock: Django Admin Improvements

- Added supplier/client filters to `ClientSettingsSupplierControlled` admin
- Added supplier/client filters to `SupplierSettingsClientControlled` admin

---

## 2026-02-14

### Documentation

- Initial task docs: SN-01 (Multi-Supplier Verification), SN-02 (Client Company Profile)
- Full detail pages for both tasks
- Task templates for AI Chatbot, Developers, Admins, End Users

---

## 2026-02-13

### Documentation

- Initial MkDocs Material documentation site
- Architecture overview, tech stack
- Backend & Flutter getting started guides
- API overview
- Development workflow guide
