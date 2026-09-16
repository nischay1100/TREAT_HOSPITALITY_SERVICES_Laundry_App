# TREAT HOSPITALITY SERVICES — Laundry Management App
# UI/UX Specification — Production Master V1.0

**Document ID:** THS-LAUNDRY-UIUX-V1.0  
**Product:** TREAT HOSPITALITY SERVICES Laundry Management App  
**Scope:** V1 Android Laundry Operations  
**Status:** Production UI/UX Contract  
**Parent:** SRS V5.0  
**Product Contract:** PRD V1.0  
**Technical Contract:** TRD V1.0  
**Platform:** Android mobile application  
**Stack:** Expo + React Native + TypeScript + Expo Router  
**Roles:** CUSTOMER / STAFF / ADMIN  
**Business:** TREAT HOSPITALITY SERVICES  
**Timezone:** Asia/Kolkata  
**Currency:** INR / ₹  
**Primary UX goals:** FAST / SIMPLE / OFFLINE / SECURE / ACCURATE / EASY TO USE

---

# 0. Purpose

This document converts the approved SRS V5.0, PRD V1.0 and TRD V1.0 into a screen-level and interaction-level UI/UX contract.

It defines:

- screen inventory;
- route hierarchy;
- role-based navigation;
- route protection;
- screen layouts;
- reusable components;
- forms;
- validation presentation;
- loading/empty/error/offline/sync states;
- order creation flows;
- order details;
- status actions;
- received-laundry presentation;
- invoice finalization UI;
- payment UI;
- invoice UI;
- correction/reissue UI;
- People UI;
- master-data UI;
- reports UI;
- settings UI;
- accessibility;
- responsive behavior;
- destructive-action confirmation;
- offline communication;
- privacy boundaries;
- UI-to-use-case contracts;
- AI-agent UI implementation controls.

This document must not create a new business rule.

---

# 1. Authority and Precedence

## 1.1 Document hierarchy

```text
SRS V5.0
   ↓
PRD V1.0
   ↓
TRD V1.0
   ↓
UI/UX V1.0
   ↓
Backend Schema + Security Contract
   ↓
Test Matrix / Implementation Plan
   ↓
Code
```

The SRS remains the authoritative parent.

The PRD defines product behavior.

The TRD defines technical implementation boundaries.

This document defines how approved behavior is presented and interacted with.

## 1.2 UI/UX cannot override product rules

UI/UX must never change:

- roles;
- permissions;
- order sources;
- collection methods;
- return methods;
- order statuses;
- payment methods;
- payment status;
- GST rules;
- cancellation cutoffs;
- invoice finalization;
- invoice correction;
- historical-data rules;
- offline authorization;
- synchronization rules.

## 1.3 UI is not security

A hidden, disabled, or unavailable button is a UX control only.

Backend authorization remains authoritative.

The UI must nevertheless hide or disable actions the current user is not permitted to perform so that the interface does not invite unauthorized actions.

---

# 2. UX Principles

## 2.1 Product personality

The application must feel:

- simple;
- professional;
- calm;
- operational;
- trustworthy;
- fast;
- uncluttered.

It must not feel like a large ERP.

## 2.2 Staff-first operational principle

Staff workflows must minimize unnecessary taps.

Common Staff tasks should be reachable quickly:

```text
Search order
Open order
Perform next permitted action
Record payment
Create walk-in
Check sync state
```

## 2.3 Customer-first clarity

Customer screens must answer:

1. What is my order?
2. What is its current status?
3. What happens next?
4. What did I request?
5. What is only an estimate?
6. Is my invoice finalized?
7. How much is paid/due?

## 2.4 Admin-first control

Admin screens must make controlled management explicit:

```text
People
Master Data
Orders
Financial Finalization
Payments
Corrections
Reports
Business Settings
```

Admin UI must expose control without exposing unrelated ERP functionality.

---

# 3. Global UI Rules

## 3.1 Platform

Primary target is Android.

The UI must work on small Android screens as well as larger phones.

Do not assume a fixed screen width.

## 3.2 Orientation

V1 mobile UX is portrait-first.

Landscape must not be required for normal workflows.

If a device enters landscape, content must remain usable or use the platform's normal responsive behavior; critical information must not be clipped.

## 3.3 Safe areas

All screens must respect Android system bars and safe display regions.

Interactive controls must never be hidden under system navigation areas.

## 3.4 Scrolling

Long screens use vertical scrolling.

Do not place essential forms inside nested scroll containers unless required.

Lists must use virtualized list patterns where appropriate.

## 3.5 Horizontal overflow

No production screen may require horizontal scrolling for normal business content.

Long:

- names;
- addresses;
- business names;
- invoice numbers;
- order IDs;

must wrap or truncate safely with a clear way to inspect the full value.

---

# 4. Design System

## 4.1 Design-system status

The functional UI contract is binding.

Exact brand colors require owner approval if no approved brand token is supplied by the business.

Until approved:

```text
BRAND_COLOR_SPECIFICATION_BLOCKER = visual token approval only
```

Agents must not invent a brand identity while implementing business screens.

A neutral accessibility-safe prototype palette may be used only in isolated design/prototype work and must not be treated as the final brand system.

## 4.2 Typography

Use a system-readable Android font stack.

Recommended hierarchy:

```text
Display / page title
Heading
Section heading
Body
Secondary
Caption
Numeric emphasis
```

Typography must prioritize readability over decorative styling.

Do not use condensed or decorative fonts for operational data.

## 4.3 Type scale

Baseline:

```text
Page title:       24sp
Section title:    18sp
Card title:       16sp
Body:             15–16sp
Secondary:        13–14sp
Caption:          12sp
Large financial:  22–28sp
```

Exact scaling may use accessibility font scaling without clipping.

## 4.4 Spacing

Use a consistent 4dp-based spacing system.

Preferred values:

```text
4dp
8dp
12dp
16dp
20dp
24dp
32dp
```

Default screen horizontal padding:

```text
16dp
```

Dense Staff list rows may use:

```text
12dp–16dp
```

## 4.5 Touch targets

Interactive controls should provide at least approximately 44–48dp touch area.

Do not make critical actions tiny merely to fit more content.

## 4.6 Cards

Cards are used for:

- order summaries;
- dashboard metrics;
- customer summaries;
- staff summaries;
- financial summaries;
- report summaries.

Do not wrap every piece of text in a card.

## 4.7 Icons

Icons support text; they do not replace important labels.

Do not rely on an icon alone for:

- cancel;
- finalize;
- payment;
- correction;
- delivery;
- collection;
- destructive actions.

---

# 5. State Presentation System

The application has several independent state dimensions.

Never combine them into one generic status chip.

```text
Account Status
Order Status
Payment Status
Invoice Status
Sync Status
Return Status
```

## 5.1 Order status

Display the exact business state through an approved human-readable label.

The stored enum is controlled by SRS/backend.

## 5.2 Payment status

Allowed presentation:

```text
Payment Pending
Partially Paid
Paid
```

## 5.3 Invoice status

Conceptually:

```text
Not Finalized
Finalized
```

Revision state may be shown separately when applicable.

## 5.4 Sync status

Display:

```text
Online
Offline
Syncing
Synced
Sync Error
```

Use icon + text where possible.

Do not use color alone.

## 5.5 Return status

Only show return controls/status when relevant.

Conceptually:

```text
Not Required
Return Pending
Returned
```

## 5.6 Color semantics

Color must never be the only state indicator.

Each state should combine:

```text
icon/shape
+
text
+
optional color
```

The final color palette must meet accessible contrast.

---

# 6. Global Application Shell

## 6.1 Shared top area

Authenticated screens may use:

```text
[Screen title]                         [Optional action]
```

The exact top action is screen-specific.

Do not place unrelated actions in a global header.

## 6.2 Offline indicator

When offline, the application must provide a persistent but non-intrusive indication.

Recommended:

```text
Offline
Changes will sync when connection returns.
```

When syncing:

```text
Syncing…
```

When synchronized:

```text
Synced
```

For errors:

```text
Sync needs attention
Retry
```

Do not show raw backend errors.

## 6.3 Global loading

Loading screens should preserve context.

Avoid an indefinite blank screen.

Use:

- skeleton;
- progress indicator;
- loading label;

depending on operation length.

## 6.4 Global error

Use:

```text
What happened
What the user can do
Retry / Go Back / Close
```

Do not display:

- stack traces;
- Firebase paths;
- Security Rule internals;
- SQL errors;
- tokens;
- credentials.

---

# 7. Authentication Route Tree

Conceptual route tree:

```text
/
├── splash
├── auth
│   ├── login
│   ├── register
│   ├── verify-email
│   ├── forgot-password
│   └── complete-profile
│
└── authenticated
    ├── customer
    ├── staff
    └── admin
```

The exact Expo Router filesystem may differ, but route semantics must remain equivalent.

## 7.1 Route protection

Unauthenticated user:

```text
Protected route → Authentication flow
```

Authenticated Customer:

```text
Customer route → allowed
Staff route → blocked
Admin route → blocked
```

Authenticated Staff:

```text
Staff route → allowed
Customer management route → blocked
Admin route → blocked
```

Authenticated Admin:

```text
Admin route → allowed
```

Route parameters never establish authorization.

---

# 8. Splash / Startup Screen

## Purpose

Safely initialize:

```text
SQLite
↓
Authentication
↓
Trusted local profile
↓
Role
↓
Business context
↓
Correct panel
↓
Network state
↓
Sync
```

## UI

Show:

- app identity;
- subtle loading indicator;
- short status text where useful.

Avoid technical diagnostics.

Possible user-facing states:

```text
Starting…
Checking account…
Preparing your workspace…
Syncing…
```

Do not show:

```text
Firestore initialization failed
SQL migration exception
Firebase token...
```

---

# 9. Login Screen

## Fields

```text
Email
Password
```

Actions:

```text
Login
Forgot Password
Register
```

Customer registration is public.

Staff/Admin public registration controls must not be shown.

## Validation

Email:

- required;
- valid email format.

Password:

- required.

## States

```text
Idle
Submitting
Success → role routing
Invalid credentials
Unverified account
Inactive account
Network unavailable
Server failure
```

## Offline login

Do not create a new offline account.

A previously authenticated user may enter the permitted offline experience according to the approved session policy.

---

# 10. Customer Registration

## Step 1 — Account

```text
Email
Password
Confirm Password
```

Action:

```text
Create Account
```

## Step 2 — Verification

Show:

```text
Check your email
```

Actions:

```text
Refresh Verification
Resend Verification
```

The UI must not invent phone OTP.

## Step 3 — Profile

Customer profile fields:

```text
Name
Mobile Number
Customer Type
Address
PIN Code
```

When:

```text
Customer Type = BUSINESS
```

show:

```text
Business Name
Business Type
Business Address where applicable
Business Phone where applicable
```

When:

```text
Customer Type = PERSONAL
```

business-specific fields are not shown.

## Business Type

Human-readable labels map to approved stored enum values.

Approved categories include:

```text
Hotel
Resort
B&B
Guest House
Other
```

## Profile completion

Primary action:

```text
Continue
```

Do not allow incomplete required profile data to silently become a fully active customer profile.

---

# 11. Forgot Password

Flow:

```text
Forgot Password
↓
Email
↓
Send Reset Link
↓
Confirmation
```

Use Firebase password reset.

Do not create custom OTP reset UX.

---

# 12. Customer Navigation

Customer core destinations:

```text
Dashboard
Orders
New Order
Profile
```

Order Details and Invoice are contextual routes.

Recommended shell:

```text
Home
Orders
Profile
```

New Order is a prominent action rather than necessarily a permanent tab.

The exact tab labels may be refined visually, but the required destinations must remain reachable.

---

# 13. Customer Dashboard

## Purpose

Give immediate understanding of active work.

## Layout

```text
Header
  Welcome / customer context

Primary CTA
  + Place New Order

Active Order
  Order ID
  Current Status
  Progress summary
  View Order

Recent Orders
  Order cards

Invoice availability
  Only when finalized
```

## Do not show

- other customers;
- Staff private data;
- Admin data;
- internal notes;
- sync diagnostics;
- internal audit data.

## Empty state

If no orders:

```text
No orders yet
Place your first laundry order.
[Place New Order]
```

---

# 14. Customer Orders Screen

## List item

Each order card should show enough information to identify it:

```text
Order ID
Order date
Current status
Collection method
Return method
Estimated amount OR finalized financial summary according to state
```

Do not label an estimate as an invoice.

## Search

Customer may search only their own orders if search is provided.

No global search.

## Filters

Only customer-relevant filters should be exposed.

The UI must not expose internal operational filters unnecessarily.

---

# 15. Customer Order Details

## Required sections

```text
Order Header
Current Status
Timeline
Collection
Return
Original Order
Estimated Amount where applicable
Final Invoice when finalized
Payment Information
Customer Note where appropriate
Cancellation action when allowed
```

## Order header

Show:

```text
Order ID
Current status
Order source
Order date
```

The customer should not be forced to understand internal enum names.

## Timeline

Timeline must be chronological.

Each event may show:

```text
Status label
Date/time
```

Internal actor identities should not be shown unless explicitly customer-facing.

## Original Order

Show:

```text
Items
Services
Ordered quantities
Collection method
Return method
Pickup information
Customer note
```

## Received Data

Customer visibility of received-laundry internal verification must follow the approved customer-facing contract.

Do not expose internal audit information.

## Final invoice

Before finalization:

```text
Invoice
Locked

Your final invoice will be available after processing and finalization.
```

After finalization:

```text
Invoice
Finalized

View Invoice
```

---

# 16. Customer New Order Flow

The flow is:

```text
New Order
↓
Items
↓
Services
↓
Quantities
↓
Collection
↓
Return
↓
Pickup information if required
↓
Customer note
↓
Review Estimate
↓
Submit
↓
Order Created
↓
Order Details
```

## 16.1 Step indicator

Use a compact progress indicator:

```text
1 Items
2 Services
3 Quantity
4 Collection
5 Return
6 Review
```

The exact step count may collapse if the UI dynamically combines approved screens, but the business information must remain equivalent.

## 16.2 Items

Display available active items.

Each item:

```text
Item name
Optional short description if defined
Quantity control
```

Inactive historical items must not be offered as new selectable master data.

## 16.3 Services

Display active services.

The user must be able to select applicable services according to the approved data model.

Do not invent service combinations or bundles.

## 16.4 Quantity

Use:

```text
−  quantity  +
```

with accessible text input where appropriate.

Rules:

- no negative quantity;
- required quantity must be valid;
- invalid input shows inline validation;
- quantity must not silently change during navigation.

## 16.5 Collection

Exactly two choices:

```text
Pickup by Us
Customer Drop-Off
```

Use large selection cards/radio controls.

## 16.6 Return

Exactly two choices:

```text
Delivery by Us
Customer Pickup
```

Only expose these approved choices.

## 16.7 Pickup information

Show only when the selected collection method requires pickup scheduling.

Do not request pickup scheduling data for Customer Drop-Off.

For Staff WALK_IN, this customer scheduling flow is not reused.

## 16.8 Review Estimate

Clearly label:

```text
ESTIMATED ORDER
```

not:

```text
FINAL INVOICE
```

Display:

```text
Selected items/services
Quantities
Collection
Return
Pickup details where applicable
Estimated subtotal/amount as defined by product rules
```

The estimate must be visually distinct from a finalized invoice.

## 16.9 Submit

Before submission:

```text
Place Order
```

If the local operation succeeds offline:

```text
Order created
Sync pending
```

Do not claim cloud success until cloud acknowledgement exists.

---

# 17. Customer Cancellation UI

Cancellation is conditional.

The UI must ask the domain/application layer whether cancellation is currently allowed.

Do not calculate cutoffs in the UI.

## Confirmation

Use:

```text
Cancel Order?
This action will cancel the order. If laundry is already with the business, a return may be required.
[Keep Order] [Cancel Order]
```

The exact final copy may be refined, but it must not promise a refund or invent financial behavior.

## Rejected cancellation

Show:

```text
Cancellation is no longer available for this order.
```

Do not expose backend state-rule internals.

---

# 18. Staff Navigation

Staff navigation must prioritize operations.

Core destinations:

```text
Dashboard
Orders
Walk-In Order
Payments
Profile / Session
Sync / Diagnostics where appropriate
```

Recommended bottom navigation:

```text
Home
Orders
Walk-In
More
```

Payment access may be contextual from orders and may also have a dedicated destination if needed by implementation.

The exact shell must not introduce Admin management sections.

---

# 19. Staff Dashboard

## Goal

Optimize for immediate operational action.

## Layout

```text
Header
  Staff name / business context
  Sync indicator

Operational Work
  New
  Pickup Pending
  Picked Up
  Received
  Processing
  Ready
  Ready for Pickup
  Out for Delivery

Financial Work
  Payment Pending
  Partially Paid

Quick Action
  + Walk-In Order

Recent / Priority Orders
```

Only states applicable to the current operational workflow should be actionable.

## Do not show

- staff management;
- price master administration;
- GST settings;
- business settings;
- invoice correction controls;
- unrestricted reports;
- internal security information.

---

# 20. Staff Orders Screen

## Header

```text
Orders
[Search]
[Filter]
```

## Search fields

Allowed operational search includes:

```text
Order ID
Customer name
Customer phone
Customer information
Invoice number where available
```

## Filters

Possible approved filters:

```text
New
Pickup Pending
Picked Up
Received
Processing
Ready
Ready for Pickup
Out for Delivery
Delivered
Collected
Cancelled
Payment Pending
Partially Paid
Paid
```

Only filters supported by the current backend/query contract are shown.

## List card

Show:

```text
Order ID
Customer name
Business name if applicable
Current order status
Payment status
Relevant date
Collection/return indicator
```

Staff should see only operational customer information required for work.

---

# 21. Staff Order Detail

## Layout

```text
Order Header
Customer / Walk-In identity
Operational Status
Original Order
Received Data where applicable
Finalization Summary
Payment Summary
Invoice
Available Actions
Activity / status timeline where permitted
```

## Action area

The primary action is the next valid state-machine action.

Example:

```text
[Mark Picked Up]
```

then later:

```text
[Start Processing]
```

Never show an arbitrary list of all statuses.

## Invalid state

If the server rejects an action due to stale state:

```text
This order has changed.
Refresh to see the latest status.
[Refresh]
```

Do not overwrite the newer state silently.

---

# 22. Staff Operational Status Actions

The UI asks the domain layer for valid next actions.

Concept:

```text
Current State
+
Order Flow
+
Role
+
Preconditions
↓
Available Action
```

The UI does not implement its own state machine.

## Action feedback

After successful local commit:

```text
Status updated
Sync pending
```

After cloud acknowledgement:

```text
Status updated
Synced
```

After rollback:

```text
The update could not be saved.
No change was made.
[Retry]
```

---

# 23. Staff Walk-In Order

This is a dedicated flow.

```text
Walk-In
↓
Customer Details
↓
Laundry Details
↓
Fixed Amount
↓
Review
↓
Create Walk-In
↓
Walk-In Order Detail
```

## 23.1 Header

```text
New Walk-In Order
```

Clearly distinguish:

```text
WALK-IN
```

from registered Customer order.

## 23.2 Customer details

Required/approved data:

```text
Customer Name
Mobile Number
Address
```

Do not offer:

```text
Create Account
Register Customer
Convert to Customer
```

## 23.3 Laundry details

Allow approved item/service information.

The exact available fields come from the backend schema.

## 23.4 Fixed amount

Show:

```text
Fixed Amount
₹ [amount]
```

Use numeric keyboard.

This is not labelled:

```text
Estimated Amount
```

because the approved walk-in model fixes the amount at creation.

## 23.5 Review

Show:

```text
WALK-IN ORDER
Customer
Mobile
Address
Items/Services
Fixed Amount
```

## 23.6 Create

Primary action:

```text
Create Walk-In
```

After local success:

```text
Walk-in created
Sync pending
```

No registered Customer account is created.

---

# 24. Walk-In Order Detail

Show:

```text
WALK-IN
Customer Name
Mobile
Address
Items/Services
Fixed Amount
Payment Status
Operational Completion/Collection State
Payment History
```

Do not expose normal registered-customer profile controls.

Do not expose normal received-quantity verification or final-rate editing workflow.

The UI must follow the approved walk-in state contract.

---

# 25. Staff Payment Flow

Flow:

```text
Open Eligible Order
↓
Record Payment
↓
Select Method
↓
Enter Amount
↓
Review
↓
Confirm
↓
Saved
```

## Payment methods

Exactly:

```text
Cash
UPI
Online
```

Online is only recordable where the approved product/backend path permits it.

Do not imply that an online gateway exists.

## Amount

Numeric keyboard.

Display:

```text
Amount received
₹
```

Do not allow invalid zero/negative normal payment entry.

Do not allow an amount that causes an invalid overpayment.

Backend/domain validation remains authoritative.

## Payment summary

Before saving:

```text
Current Paid
Current Due
Payment Amount
New Expected Paid
New Expected Due
```

Where final invoice data is available.

## Success

```text
Payment recorded
Paid ₹X
Due ₹Y
```

Include:

```text
Sync pending
```

if local-only.

---

# 26. Payment History UI

Payment history is append-only.

Each entry may show:

```text
Amount
Method
Date/time
Recorded by
Status/adjustment indication where applicable
```

Staff cannot edit/delete old entries.

Admin correction must be a separate controlled flow.

Do not display an "Edit Payment" button to Staff.

---

# 27. Overpayment UI

If an attempted payment would exceed the valid settlement:

```text
Payment cannot be recorded.

The amount entered is greater than the remaining payable amount.
```

Actions:

```text
[Change Amount]
[Cancel]
```

Do not show:

```text
Apply excess to next order
Create wallet credit
Automatic refund
```

unless those behaviors are explicitly added to a future approved specification.

---

# 28. Staff Invoice Finalization UI

Staff sees invoice finalization only when the order has reached the approved finalization point.

## Staff review screen

Sections:

```text
Customer
Original Order
Received Laundry
Final Billable Items
Additional Charges
GST
Payment
Final Total
```

## Staff edit controls

If final quantity/rate/line/additional charge editing is required:

```text
Staff cannot edit these fields.
```

The UI should present the data as read-only and provide an Admin-required message/action path if such a navigation is approved.

Do not create a Staff "Request Admin" workflow unless the backend/UI contract explicitly defines one.

## GST

Staff may select/confirm:

```text
GST ON
GST OFF
```

only when the approved finalization conditions permit it.

## Finalization warning

Before final action:

```text
Review invoice carefully.

After finalization, the financial details become protected. Corrections require an authorized correction process.
```

Action:

```text
Finalize Invoice
```

---

# 29. Admin Navigation

Admin bottom navigation is fixed:

```text
Home
Orders
People
Reports
More
```

Do not add:

```text
Finance
Staff
Customers
Settings
```

as additional bottom tabs.

They belong inside the approved hierarchy.

---

# 30. Admin Home

## Layout

```text
Header
Business context
Sync status

Today's Operations
  Orders
  Pending
  Processing
  Ready
  Delivered
  Collected

Financial Snapshot
  Today's Revenue
  Unpaid

Quick Access
  Orders
  People
  Reports
```

Exact metrics must come from authoritative report/dashboard data.

Do not calculate revenue independently in the UI.

---

# 31. Admin Orders

Admin has business-scoped order access.

## Header

```text
Orders
[Search]
[Filter]
```

## Search

```text
Order ID
Customer name
Customer phone
Customer information
Invoice number
```

## Filter

Use approved order/payment states.

Do not invent extra business states.

## Order card

Show:

```text
Order ID
Customer
Business name if applicable
Order status
Payment status
Invoice status
Relevant date
```

---

# 32. Admin Order Detail

Admin can see the full approved business story:

```text
Order Header
Customer Snapshot
Original Order
Received Laundry
Final Billing
Additional Charges
GST
Invoice
Payments
Due
Status History
Cancellation / Return
Correction History
```

The UI must keep these concepts visually distinct.

Recommended section labels:

```text
Original Order
Actual Received
Final Bill
Payment
Invoice
History
```

Do not merge Original/Received/Final data into one generic editable card.

---

# 33. Original Order Section

Read-only historical representation.

Show:

```text
Original items
Original services
Ordered quantities
Collection method
Return method
Pickup information
Customer note
Estimated information
```

After order creation, the original request must not appear as a mutable current-order field.

---

# 34. Received Laundry Section

For applicable registered Customer orders:

```text
Ordered Quantity
Received Quantity
Difference
Received Notes where applicable
```

Make differences visually understandable.

Example:

```text
Ordered      10
Received      8
Difference   -2
```

Do not overwrite the ordered quantity.

## Staff

Read-only.

## Admin

Editable before invoice finalization where approved.

---

# 35. Final Billing Section

Show:

```text
Final/Billed Quantity
Final Rate
Billable Line
Line Subtotal
Additional Charges
GST
Final Amount
```

## Staff

Read-only if no edit is required.

If an edit is required, Staff must not receive editable controls.

## Admin

Editable before finalization according to permission contract.

---

# 36. Additional Charges UI

Admin-only before finalization.

Each charge:

```text
Description
Amount
Note
```

Display:

```text
Additional Charges
+ ₹X
```

The note is part of the financial/business record.

Staff does not get add/edit/remove controls.

Do not invent percentage charges or tax categories.

---

# 37. GST UI

Two conceptual options:

```text
GST ON
GST OFF
```

If GST ON, display the applicable rate and resulting GST amount according to authoritative data.

If GST OFF:

```text
GST ₹0
```

Do not allow Staff to change the business's GST configuration.

Order-level selection is separate from Admin GST settings.

---

# 38. Admin Invoice Finalization

Flow:

```text
Open Order
↓
Review Original
↓
Review Received
↓
Edit Final Quantity if needed
↓
Edit Final Rate if needed
↓
Edit Lines if needed
↓
Additional Charges
↓
GST
↓
Final Calculation
↓
Review
↓
Finalize Invoice
```

## Final review

Use a high-visibility financial summary:

```text
Subtotal
Additional Charges
GST
FINAL AMOUNT

Paid
Due
```

The final amount must be visually prominent.

## Confirmation

Require explicit confirmation.

Suggested:

```text
Finalize Invoice?

This will create the final invoice and protect the finalized financial details.

[Review Again] [Finalize Invoice]
```

Do not imply that finalization can be undone by a normal Edit button.

---

# 39. Finalized Invoice Screen

All permitted roles may view the finalized invoice.

## Header

```text
Invoice
INV-...
Finalized
```

For revised invoices:

```text
Revised Invoice
Revision information
```

Exact numbering format comes from backend contract.

## Required visible information

```text
Business identity
Invoice number
Invoice date
Order ID
Customer identity
Line items
Final quantities
Final rates
Line totals
Additional charges
GST
Final amount
Paid
Due
```

## Actions

Customer:

```text
View
Generate/Share PDF where enabled
```

Staff:

```text
View
Generate/Share PDF where enabled
```

Admin:

```text
View
Generate/Share PDF
View authorized history
Correction when eligible
```

Do not show edit controls for normal finalized invoice.

---

# 40. Invoice PDF UX

Before generating:

```text
Preparing invoice…
```

After successful local generation:

```text
Invoice ready
[Open] [Share]
```

If generation fails:

```text
Invoice could not be generated.
[Try Again]
```

The PDF must use the finalized snapshot.

The UI must never reconstruct old financial information from current settings.

---

# 41. Invoice Correction UI

Correction is Admin-only.

## Entry

On finalized order:

```text
Correction
```

must only appear when the authoritative eligibility check permits it.

The UI must not independently calculate the seven-day eligibility.

## Eligibility state

### Eligible

Show:

```text
Correction available
```

### Locked

Show:

```text
Financial correction is locked for this order.
```

Do not reveal implementation-specific timestamp calculations.

## Correction flow

```text
Start Correction
↓
Reason
↓
Review Original Invoice
↓
Modify approved financial data
↓
Recalculate
↓
Review Revision
↓
Confirm
↓
Revised Invoice
↓
Original Preserved
```

## Reason

Reason is required.

Do not allow blank reason.

## Revision review

Clearly distinguish:

```text
Original Invoice
Revised Invoice
```

Show changed values clearly.

Do not destroy the original.

---

# 42. Invoice History UI

Admin may access:

```text
Current Invoice
Original Invoice
Revision History
Correction Reason
Correction Actor
Correction Date
```

The current valid invoice must be obvious.

Original invoice remains available for authorized history.

Customer sees the current valid invoice, not internal correction controls.

---

# 43. Seven-Day Lock UI

The UI should reflect the authoritative result:

```text
Correction Available
```

or:

```text
Correction Locked
```

Do not expose a device-clock countdown as the authority.

If due remains pending, the UI may show:

```text
Controlled correction available
```

only when backend eligibility permits it.

---

# 44. Admin People

Fixed hierarchy:

```text
People
├── Staff
└── Customers
```

Exactly these two People sections are required.

---

# 45. Staff List

## Header

```text
Staff
[Search]
```

## Sections/filter

```text
Active
Inactive
```

## List item

```text
Staff Name
Staff Code
Status
```

Do not show password.

Do not show private authentication credentials.

## Add Staff

Primary action:

```text
+ Add Staff
```

---

# 46. Add Staff Screen

Fields:

```text
Name
Mobile Number
Email / Login
Staff Code
Temporary Password
```

The secure backend creates the authentication account.

The UI must clearly communicate that the credentials are temporary where applicable.

Do not expose the password after the secure creation flow unless the approved secure credential-delivery design requires it.

Do not provide role selection.

The created role is:

```text
STAFF
```

The UI must not offer:

```text
ADMIN
```

---

# 47. Staff Profile

Show:

```text
Name
Mobile
Email/Login
Staff Code
Status
Created Date
Updated Date
Last Login where available
```

Admin actions:

```text
Edit
Deactivate
Reactivate
```

Do not expose:

- password;
- authentication tokens;
- Admin promotion;
- business reassignment.

---

# 48. Staff Deactivation

Confirmation:

```text
Deactivate Staff?

This will prevent the Staff account from performing new protected work. Historical records will remain.

[Cancel] [Deactivate]
```

After success:

```text
Staff deactivated
```

Do not delete historical actor references.

---

# 49. Customer List

## Header

```text
Customers
[Search]
[Status Filter]
```

Search:

```text
Name
Mobile
Email
Business Name where applicable
```

## Customer item

Business customer:

```text
Customer Name
Business Name
```

Personal customer:

```text
Customer Name
Personal
```

This display behavior is mandatory.

---

# 50. Customer Detail

Sections:

```text
Profile
Contact
Address
Business Information
Account Status
Orders
Payment Summary
```

Only the selected customer's data may appear.

## Header

Business customer:

```text
Customer Name
Business Name
Business
```

Personal:

```text
Customer Name
Personal
```

## Actions

Admin:

```text
Edit
Deactivate / Reactivate
```

No Staff access to these controls.

---

# 51. Customer Deactivation

Confirmation:

```text
Deactivate Customer?

The customer will not be able to place new registered orders. Existing orders and history will remain.

[Cancel] [Deactivate]
```

After deactivation:

```text
Inactive
New Order unavailable
```

Existing orders remain visible to authorized Admin/Staff.

Do not delete the customer.

---

# 52. Customer Business-to-Personal Edit

When switching:

```text
BUSINESS → PERSONAL
```

show confirmation before clearing business-specific fields.

Suggested:

```text
Change customer type?

Business information will be removed from the current profile fields. Historical orders remain unchanged.

[Keep Business] [Change to Personal]
```

Do not silently clear data.

---

# 53. Admin Master Data Navigation

Master data belongs under the approved Admin configuration area.

Conceptual hierarchy:

```text
More
├── Items
├── Services
├── Prices
├── Business Settings
├── GST Settings
├── Invoice Settings
└── App Information
```

Exact nesting may be refined visually without changing access.

---

# 54. Items Screen

Admin can:

```text
View Items
Add Item
Edit Item
Deactivate Item
```

List item:

```text
Item Name
Status
Relevant summary
```

Used historical items must not be presented as deleted if historical records depend on them.

Use inactive/deactivated state.

---

# 55. Services Screen

Same general structure:

```text
Services
[Search]
+ Add Service
```

Item:

```text
Service Name
Status
```

Do not invent service pricing behavior beyond the approved master-data model.

---

# 56. Prices Screen

Admin-only.

List:

```text
Item / Service
Current Price
Status
Updated Date
```

Price editing must clearly communicate:

```text
This price applies to future applicable operations. Historical order/invoice values are unchanged.
```

Do not show a bulk "rewrite old invoices" function.

---

# 57. Business Settings

Business settings are separate from Admin profile.

Fields may include:

```text
Business Name
Business Type
Primary Mobile
Alternative Mobile
Email
Address
PIN Code
GSTIN
Default GST Rate
Invoice Prefix
Optional WhatsApp Number
```

## Save behavior

Use explicit:

```text
Save Changes
```

After save:

```text
Business settings updated
```

Historical invoices must remain unchanged.

## Validation

Use approved Indian:

- mobile;
- PIN;
- email;

formats.

Do not create additional business identity fields unless the schema contract approves them.

---

# 58. GST Settings

Admin-only.

Show current configuration.

Provide approved settings controls.

Clearly distinguish:

```text
Default GST configuration
```

from:

```text
Order-level GST choice
```

Changing default GST must be described as affecting future applicable operations, not historical invoices.

---

# 59. Invoice Settings

Admin-only.

At minimum, support approved:

```text
Invoice Prefix
```

Additional invoice presentation settings are only shown if approved by the backend/product contract.

Do not invent numbering rules in the UI.

---

# 60. Reports Navigation

Admin:

```text
Reports
```

Required periods:

```text
Weekly
Monthly
Yearly
```

The UI may use:

```text
Period selector
Date range where approved
Generate
Export XLSX
```

Exact report dataset comes from the backend/report contract.

---

# 61. Reports Dashboard

Suggested structure:

```text
Reporting Period
↓
Summary
  Total Orders
  Completed
  Cancelled
  Active

Financial
  Final Revenue
  GST
  Paid
  Due

Payment Methods
  Cash
  UPI
  Online

Operational
  Items
  Services
  Collection
  Return
```

Cancelled activity must remain distinguishable from normal revenue.

Estimated order amounts must not be presented as final revenue.

---

# 62. Report Empty State

If no data:

```text
No report data for this period.
Try another period.
```

Do not show ₹0 as if it necessarily means there were records with zero revenue unless the report contract defines that meaning.

---

# 63. Offline Report Warning

If local data is incomplete for the selected report:

```text
Offline report
Some cloud data may not yet be available on this device.
```

The UI must not present incomplete local data as authoritative complete reporting data.

Where the report contract requires online/cloud-complete data, make that requirement explicit.

---

# 64. XLSX Export

Flow:

```text
Generate Report
↓
Validate period/data
↓
Prepare XLSX
↓
File ready
↓
Share / Save
```

UI:

```text
Preparing report…
```

Then:

```text
Report ready
[Share]
```

Export must contain only authorized business data.

---

# 65. Admin More Screen

Possible approved sections:

```text
Business Settings
GST Settings
Invoice Settings
Items
Services
Prices
Sync / Diagnostics
App Information
Profile / Logout
```

The exact order may be optimized for frequency.

Do not add unrelated ERP modules.

---

# 66. App Information

May show:

```text
App Name
Version
Build Number
Environment information appropriate for the current user
Support information
```

Never show:

- Firebase private keys;
- service credentials;
- tokens;
- raw environment secrets.

---

# 67. Profile / Session

## Customer

May show:

```text
Name
Email
Mobile
Customer Type
Business Information where applicable
Address
PIN
Account status
Change Password
Logout
```

## Staff

Show approved Staff profile/session information.

Do not show Admin settings.

## Admin

Show Admin profile separately from Business Settings.

---

# 68. Logout UX

Use confirmation if required by the product interaction pattern:

```text
Log out?
[Stay] [Log Out]
```

After logout:

```text
Authenticated screens unavailable
```

Do not allow the next user on a shared device to see the previous user's protected information.

Pending work must not silently attach to a different user.

---

# 69. Sync / Diagnostics Screen

Where exposed, show:

```text
Network
Online / Offline

Sync
Synced / Syncing / Sync Error

Pending Operations
N

Last Successful Sync
date/time

Last Failed Sync
date/time where available

[Retry]
```

Do not expose raw:

- Firestore error;
- stack trace;
- SQL exception;
- token;
- internal server payload.

## Role visibility

Sync diagnostics may be appropriate for Staff/Admin.

Customer should not receive internal sync diagnostics.

---

# 70. Sync Error UI

Use:

```text
Sync needs attention

Your saved work is still stored on this device.
Try syncing again when the connection is available.

[Retry]
```

Only state "saved on this device" when the local transaction actually committed.

Do not promise cloud persistence before acknowledgement.

---

# 71. Local Commit Feedback

For permitted offline operations:

```text
Saved on this device
Will sync when online
```

This is different from:

```text
Saved to server
```

The second statement requires authoritative cloud acknowledgement.

---

# 72. Loading States

Every asynchronous screen must define:

```text
Initial loading
Refresh loading
Action submitting
File generation loading
Sync loading
```

Avoid replacing an existing list with a full-screen spinner during a small refresh.

Prefer preserving current content with a refresh indicator.

---

# 73. Empty States

Every major list must have a designed empty state.

Required examples:

```text
No Orders
No Customers
No Staff
No Items
No Services
No Prices
No Payments
No Report Data
No Search Results
```

Each empty state should explain:

```text
What is empty
Why it may be empty
What useful action is available
```

Do not invent actions unavailable to the current role.

---

# 74. Search Empty State

Example:

```text
No matching orders
Try a different order ID, customer name, or phone number.
```

Do not imply that search includes unauthorized data.

---

# 75. Form Validation System

## Inline validation

Errors should appear close to the affected field.

Example:

```text
Mobile Number
[98XXXXXXXX]

Enter a valid 10-digit Indian mobile number.
```

## Submission validation

If multiple fields are invalid:

- retain user-entered values;
- focus or scroll to the first invalid field;
- show all relevant field errors.

Do not erase the form.

## Server/domain validation

If backend rejects a valid-looking form:

```text
The information could not be saved.
Please review the highlighted fields or try again.
```

If the reason is known and safe, show the mapped domain message.

---

# 76. Keyboard Rules

Use appropriate Android keyboards:

```text
Email → email keyboard
Password → secure text
Mobile → numeric/phone
PIN → numeric
Money → decimal/numeric
Quantity → numeric
```

Do not permit alphabetic input where a numeric-only field is required.

Keyboard must not cover the active field.

---

# 77. Money Input UX

User-facing money format:

```text
₹ 1,200
₹ 590.50
```

Do not display internal paise representation.

Input should be intuitive:

```text
Amount
₹ [      ]
```

Do not let users type arbitrary currency symbols into a numeric amount field unless the input component intentionally supports it.

---

# 78. Date and Time UX

Business timezone:

```text
Asia/Kolkata
```

Display style:

```text
14 September 2026
```

Use clear time presentation where needed.

The UI must not use the device clock as the authoritative source for:

- seven-day correction eligibility;
- financial audit;
- server acceptance;
- authoritative status timing.

---

# 79. Address UX

Addresses may be long.

Use multiline input.

Display:

```text
Address
```

with sufficient vertical space.

Do not force an address into a one-line row.

On lists, truncate safely and allow full display in details.

---

# 80. Long Name / Business Name UX

Business customers:

```text
Customer Name
Business Name
```

Both must wrap.

Never:

```text
Hotel Paradise International Luxury Resort & Spa... 
```

without a way to inspect the complete value.

---

# 81. Order Timeline UX

Timeline should visually distinguish:

```text
Completed
Current
Upcoming
```

But text must communicate the state.

Example:

```text
✓ Order Created
✓ Picked Up
● Processing
○ Ready
○ Delivered
```

For Customer Pickup flows:

```text
○ Ready for Pickup
○ Collected
```

Do not display delivery steps for Customer Pickup.

For Delivery flows:

```text
○ Ready
○ Out for Delivery
○ Delivered
```

Do not display self-collection as the completion path.

---

# 82. Four Canonical Flow UI Rules

## Flow A

```text
Pickup by Us
+
Delivery by Us
```

Display:

```text
New
Pickup Pending
Picked Up
Processing
Ready
Out for Delivery
Delivered
```

## Flow B

```text
Pickup by Us
+
Customer Pickup
```

Display:

```text
New
Pickup Pending
Picked Up
Processing
Ready for Pickup
Collected
```

## Flow C

```text
Customer Drop-Off
+
Delivery by Us
```

Display:

```text
New
Received
Processing
Ready
Out for Delivery
Delivered
```

## Flow D

```text
Customer Drop-Off
+
Customer Pickup
```

Display:

```text
New
Received
Processing
Ready for Pickup
Collected
```

Only the selected flow's actions are actionable.

---

# 83. Cancellation + Return UX

If cancellation causes physical laundry to be returned:

```text
Cancelled
Return Required
Return Pending
Returned
```

Show return section only when applicable.

Return section may show:

```text
Return status
Returned date/time
Return note
```

Actor identity is internal unless approved for the viewer.

Do not treat cancellation as deletion.

---

# 84. Financial Summary Component

Reusable component:

```text
Financial Summary

Subtotal          ₹X
Additional        ₹Y
GST               ₹Z
----------------------
Final Amount      ₹A

Paid              ₹B
Due               ₹C
```

Before finalization, label the amount:

```text
Estimated Amount
```

not:

```text
Final Amount
```

After finalization:

```text
Final Amount
```

This component must receive authoritative view-model data.

It must not independently recalculate business totals.

---

# 85. Payment Summary Component

Reusable:

```text
Payment Status
Paid
Due
```

If not finalized and advance payment exists:

```text
Paid
₹X

Invoice
Not Finalized
```

Do not imply that paid amount means the invoice is finalized.

---

# 86. Permission-Aware UI

Use one capability model supplied by the application layer.

Conceptually:

```text
canCreateWalkIn
canRecordPayment
canFinalizeUnchangedInvoice
canEditReceivedQuantity
canEditFinalQuantity
canEditFinalRate
canEditAdditionalCharges
canCorrectInvoice
canManageStaff
canManageCustomers
canManagePrices
canManageGST
canManageBusiness
canExportReports
```

These are UI capability names, not new business permissions.

Their values must come from the approved role/operation permission contract.

Do not derive authorization from route names or UI state.

---

# 87. Staff Restricted Control Presentation

When Staff opens an order requiring Admin financial editing:

Do not show editable inputs.

Instead:

```text
Final billing changes require Admin access.
```

Show the current approved data read-only.

Do not create an undocumented "unlock with PIN" or "request override" mechanism.

---

# 88. Disabled vs Hidden Controls

Hide controls that have no relevance to the role.

Use disabled controls when the action is relevant but temporarily unavailable because of state.

Example:

```text
Finalize Invoice
```

may be unavailable because the order has not reached the finalization stage.

The reason should be understandable:

```text
Invoice finalization becomes available after the order reaches the finalization stage.
```

Do not expose internal state-machine terminology unnecessarily.

---

# 89. Destructive / High-Risk Actions

Require confirmation for:

- cancel order;
- deactivate Staff;
- deactivate Customer;
- finalize invoice;
- start invoice correction;
- create revised invoice;
- other irreversible/protected operations.

Confirmation must explain consequence.

Do not use generic:

```text
Are you sure?
```

for financial actions.

---

# 90. Invoice Finalization Confirmation

Minimum semantic content:

```text
This will finalize the invoice.
After finalization, financial details become protected.
```

Buttons:

```text
Review
Finalize
```

Do not use ambiguous:

```text
OK
Continue
Submit
```

as the only final action label.

---

# 91. Correction Confirmation

Minimum semantic content:

```text
This will create a revised invoice.
The original invoice will remain preserved in history.
```

Buttons:

```text
Cancel
Create Revision
```

The UI must not imply that the original will be edited.

---

# 92. Payment Confirmation

For payment:

```text
Record payment of ₹X using UPI?
```

Then:

```text
Cancel
Record Payment
```

If payment is invalid, do not allow the confirmation to proceed.

---

# 93. Network State UX

## Online

Normal operation.

## Offline

Show non-blocking status.

Core permitted local actions remain usable.

## Syncing

Show progress/status without blocking unrelated local reading.

## Sync error

Show retry and preserve committed work.

Do not turn a temporary sync problem into data deletion or rollback of a previously committed local operation.

---

# 94. Stale Data / Conflict UX

If a record changed elsewhere:

```text
This record was updated on another device.

Refresh to view the latest information.
```

Actions:

```text
[Refresh]
```

For financial conflict:

```text
This financial record has changed and cannot be merged automatically.
An authorized review is required.
```

Do not offer:

```text
Keep Mine
Keep Cloud
Merge Automatically
```

unless such behavior is explicitly approved.

---

# 95. Offline Finalization UX

If permitted finalization commits locally:

Show:

```text
Invoice finalized on this device.
Cloud sync pending.
```

Do not say:

```text
Invoice uploaded successfully
```

until cloud acknowledgement exists.

The finalized invoice may still be visible locally according to the approved local state.

---

# 96. Customer Invoice Visibility Rule

Before finalization:

```text
Invoice Locked
```

After finalization:

```text
Invoice Available
```

Advance payment alone must not change this.

---

# 97. Notification UX

FCM notifications are secondary.

When enabled, notifications may communicate relevant order events.

Lock-screen content must be non-sensitive.

Do not include:

- full addresses;
- payment secrets;
- internal notes;
- audit information;
- staff private data.

The user should be able to open the relevant order when authenticated and authorized.

---

# 98. Accessibility

## Required

- readable contrast;
- large enough touch targets;
- text labels for important actions;
- screen-reader-friendly labels;
- meaningful accessibility roles;
- logical focus order;
- no color-only state;
- scalable text;
- no clipped text;
- accessible modal/dialog behavior.

## Dynamic font size

At increased Android font scale:

- content must remain readable;
- buttons must wrap where appropriate;
- horizontal overflow must not appear;
- critical actions must remain reachable.

---

# 99. Accessibility Labels

Examples:

```text
"Decrease quantity for Shirt"
"Increase quantity for Shirt"
"Open order ORD-..."
"Payment status: Partially Paid"
"Order status: Processing"
```

Avoid:

```text
"Button 1"
"Icon"
```

for meaningful controls.

---

# 100. Screen Reader Semantics

Order cards should expose a coherent summary:

```text
Order [ID], customer [name], status [status], payment [status].
```

Financial summary should be read in a logical order.

Do not require a screen-reader user to interpret color or position alone.

---

# 101. Modal / Bottom Sheet Rules

Use bottom sheets for:

- filters;
- simple selections;
- non-destructive contextual choices.

Use full screens for:

- long forms;
- invoice finalization;
- customer profile;
- Staff profile;
- correction;
- complex report configuration.

Do not put a large multi-step financial form into a tiny modal.

---

# 102. Back Navigation

Android back behavior must be predictable.

For unsaved forms:

```text
Discard changes?
```

only when changes actually exist.

For multi-step order creation:

Back returns to the previous step while preserving current entered values.

Do not silently discard an in-progress order.

---

# 103. Draft Handling

A draft is UI/application state only until the approved create operation commits it.

Do not present an uncommitted draft as a real order.

If the application implements persistent drafts, that behavior must be explicitly approved before being treated as a business record.

---

# 104. Form Save States

Every save-capable form needs:

```text
Idle
Saving
Saved
Validation error
Permission error
Conflict error
Network/sync pending
Storage failure
```

Do not allow repeated rapid taps to submit the same mutation multiple times.

The use-case/idempotency layer is authoritative for duplicate prevention.

---

# 105. Button Interaction Rules

Primary actions:

- one obvious primary action per major screen;
- descriptive labels;
- loading state after tap;
- prevent accidental repeated submission.

Examples:

```text
Place Order
Create Walk-In
Record Payment
Finalize Invoice
Create Revision
Save Changes
```

Do not use multiple competing primary buttons.

---

# 106. Customer Profile Edit UX

Customer may edit permitted own fields.

Fields that must not be editable:

```text
Role
Business ID
Historical orders
Finalized invoices
Payment history
Status history
Pricing
GST settings
```

The UI should simply not expose these as editable controls.

---

# 107. Staff Profile UX

Staff can view permitted own session/profile information.

Staff cannot edit Admin-controlled identity/security fields unless specifically approved.

Do not show:

```text
Change Role
Change Business
```

---

# 108. Admin Profile vs Business Settings

Admin profile:

```text
Admin's personal account
```

Business settings:

```text
TREAT HOSPITALITY SERVICES business identity
```

Never combine them into one editable "Profile" object.

---

# 109. Privacy by Design in UI

Customer UI must not display:

```text
Other customers
Staff private information
Admin private information
Internal audit
Internal sync errors
Other business data
```

Staff UI must not display unnecessary:

```text
Staff management
Business configuration
Pricing administration
GST administration
Audit correction
Unrestricted reports
```

Admin UI is business-scoped.

---

# 110. Shared Device Protection

After logout:

```text
Protected screens → inaccessible
Protected cached data → not exposed to next session
```

Login as a different user must not reuse the previous user's protected view state.

Navigation state must reset to the new user's allowed root.

---

# 111. Performance UX

Target perceived responsiveness for:

- dashboard;
- order search;
- customer lookup;
- order open;
- status update;
- payment;
- walk-in creation.

For local operations:

```text
tap
↓
validate
↓
local commit
↓
UI update
```

Do not show an unnecessary network spinner before displaying a successfully committed offline operation.

---

# 112. Pagination UX

Large lists must support pagination.

When more data is available:

```text
Load more
```

or infinite scrolling.

Show:

```text
Loading more…
```

Do not load the entire business dataset into the screen.

---

# 113. Search Debouncing

Search inputs may debounce network/local queries.

Do not debounce the actual business mutation.

Search must remain scoped by:

```text
businessId
```

for Staff/Admin business data and by customer ownership for Customer data.

---

# 114. Refresh UX

Pull-to-refresh or explicit refresh may be used.

Refreshing must not discard:

- locally committed pending work;
- unsynced changes;
- current drafts.

When refresh discovers a stale/conflicting record, use the conflict UX rather than silently replacing local work.

---

# 115. Error Message Style

Use plain Indian English.

Prefer:

```text
Could not save the payment. Please try again.
```

over:

```text
Firestore PERMISSION_DENIED: write failed at /businesses/...
```

Prefer:

```text
This action is not available for this order.
```

over:

```text
INVALID_STATE_TRANSITION
```

The technical error code remains available to diagnostics, not the end user.

---

# 116. Offline Error Message Style

Prefer:

```text
No internet connection.
Your saved work is still on this device and will sync when you are online.
```

Only say "saved" after local transaction success.

---

# 117. Storage Error Message

If local persistence fails:

```text
We could not save this change.
No change was made.
Please try again.
```

Do not display SQLite internals.

---

# 118. Permission Error Message

Prefer:

```text
You do not have permission to perform this action.
```

Do not reveal backend authorization structure.

---

# 119. Session Error

If authentication expires:

```text
Your session has expired.
Please sign in again.
```

Do not expose tokens.

Queued work must remain protected according to the sync contract.

---

# 120. Version / Mandatory Upgrade UX

If the backend requires a minimum supported app version:

```text
Update required

A newer version of the app is required to continue.

[Update App]
```

Do not clear local pending work.

Do not present a destructive reinstall instruction as the normal update path.

---

# 121. Order Status + Payment Status Layout

Do not use one combined status label such as:

```text
Processing / Unpaid
```

Instead:

```text
Order
Processing

Payment
Partially Paid
```

This preserves independent state concepts.

---

# 122. Invoice Status + Sync Status Layout

Example:

```text
Invoice
Finalized

Sync
Pending
```

This is valid.

Do not display:

```text
Invoice Failed
```

just because cloud sync is pending.

---

# 123. Order Detail Information Hierarchy

Recommended order:

```text
1. Order identity
2. Current operational status
3. Next valid action
4. Customer identity
5. Collection/return
6. Original order
7. Received laundry
8. Final bill
9. Payment
10. Invoice
11. History
12. Cancellation/return information
```

This is a usability hierarchy, not a change to business data ownership.

---

# 124. Staff "Next Action" Principle

For Staff, the order detail should make the next valid operational action obvious.

Example:

```text
Processing

[Mark Ready]
```

The UI must receive the valid action from the domain/application layer.

It must not infer the next state by hard-coded screen logic.

---

# 125. Admin "Control vs View" Principle

Admin screens distinguish:

```text
View historical data
```

from:

```text
Edit current pre-finalization data
```

and:

```text
Controlled correction
```

Do not present all three as a generic Edit button.

---

# 126. Historical Data Visual Treatment

Use labels that clarify historical meaning:

```text
Original Order
Historical Rate
Finalized Invoice
Original Invoice
Revision 1
```

Do not show current price master beside historical invoice data in a way that implies the current price replaced the old price.

---

# 127. Customer Business Information Visual Treatment

Business customer:

```text
Rahul Sharma
Hotel Paradise
Business
```

Personal customer:

```text
Rahul Sharma
Personal
```

This pattern should be reused consistently in:

- customer list;
- order detail;
- invoice customer block where applicable;
- admin customer detail.

---

# 128. Invoice Customer Block

Show:

```text
Customer Name
Business Name where applicable
Phone
Address
```

Use historical snapshot data for finalized invoices.

---

# 129. Invoice Business Block

Show the historical business snapshot:

```text
TREAT HOSPITALITY SERVICES
Address
PIN
Phone
Alternative Phone where configured
Email
GSTIN where applicable
```

Do not substitute current business settings into old invoice views.

---

# 130. Invoice Line Item UX

Table/list columns conceptually:

```text
Item/Service
Qty
Rate
Amount
```

On narrow screens, use stacked rows:

```text
Shirt
Qty: 3
Rate: ₹30
Amount: ₹90
```

Do not force an unreadable wide table on small phones.

---

# 131. Invoice Additional Charge UX

Display:

```text
Additional Charges

Cleaning charge     ₹100
Note: ...
```

Keep notes readable.

Long notes wrap.

---

# 132. Invoice GST UX

Example:

```text
GST
18%
₹144
```

If off:

```text
GST
Not applied
₹0
```

Use the finalized snapshot.

---

# 133. Invoice Payment UX

Show:

```text
Total          ₹1,200
Paid           ₹300
Due            ₹900
```

For fully paid:

```text
Total          ₹1,200
Paid           ₹1,200
Due            ₹0
```

Never display a negative due.

---

# 134. Advance Payment UX

Before invoice:

```text
Payment
Advance paid ₹300

Invoice
Not finalized
```

After invoice:

```text
Final Amount ₹1,200
Paid ₹300
Due ₹900
```

The same payment history remains visible.

Do not create a separate "advance wallet" concept.

---

# 135. Cancelled Order UX

Cancelled order should remain in history.

Header:

```text
Cancelled
```

Show:

```text
Cancellation reason where viewer is permitted
Cancelled date/time
```

If physical return required:

```text
Return Required
Return Pending
Returned
```

Do not remove the order card from history.

---

# 136. Staff Walk-In vs Registered Customer UI

Walk-in:

```text
WALK-IN
Customer Name
Mobile
Address
Fixed Amount
```

Registered Customer:

```text
Customer Name
Business/Personal
Order ID
Normal order flow
```

Never make the Walk-In flow appear to be a hidden registered-account creation flow.

---

# 137. Dashboard Metric Integrity

Dashboard numbers must be clearly labelled.

For example:

```text
Today's Revenue
₹X
```

must come from the authoritative report/dashboard query.

Do not compute revenue from:

```text
estimated amounts
```

or incomplete local payment children without the approved completeness contract.

---

# 138. Reports Period UX

Use explicit labels:

```text
Weekly
Monthly
Yearly
```

Display the actual reporting period.

Business-facing date boundaries use Asia/Kolkata.

Do not rely on the device's arbitrary timezone for business reporting.

---

# 139. Report Revenue Labeling

Use:

```text
Final Revenue
```

not:

```text
Order Value
```

when the metric is the finalized financial report value.

Estimated amounts belong to order estimation, not final revenue.

---

# 140. Notification Deep Link UX

When a notification opens an order:

```text
Authenticate
↓
Verify role/ownership
↓
Load authorized order
↓
Open Order Detail
```

Do not trust a notification's order ID as authorization.

---

# 141. Deep-Link Security

A route such as:

```text
/order/ORD-123
```

does not authorize access.

If the current user cannot access the order:

```text
This order is not available.
```

Do not reveal whether the order exists for another customer/business.

---

# 142. Modal Security

Do not prefetch protected data merely because a modal may later be opened.

Load data through authorized application queries.

---

# 143. Data Refresh After Mutation

After a successful mutation:

```text
local state updates immediately
```

Then:

```text
sync state updates independently
```

The UI must not revert to stale server data after a local commit.

Repository/application state must reconcile authoritative cloud data when synchronization completes.

---

# 144. Sync Badge Placement

Recommended locations:

Customer:

```text
Top/header context only if needed
```

Staff:

```text
Header + Sync/Diagnostics
```

Admin:

```text
Header + Sync/Diagnostics
```

Do not make the sync badge dominate the business workflow.

---

# 145. Offline Mode Accessibility

Offline indicator must be accessible to screen readers.

Example:

```text
Network status: Offline. Saved changes will sync when connection returns.
```

Do not communicate offline only through a red/green dot.

---

# 146. Toast / Snackbar Rules

Use transient messages for:

- successful local save;
- sync completion;
- minor refresh feedback.

Do not use a toast as the only indication of:

- financial finalization;
- invoice correction;
- cancellation;
- deactivation.

High-risk operations require persistent review/confirmation.

---

# 147. Dialog Rules

Dialogs must:

- have clear title;
- explain consequence;
- have descriptive actions;
- support Android back behavior;
- not trap focus incorrectly.

Do not stack multiple dialogs.

---

# 148. Error Recovery Patterns

## Retryable

```text
Try Again
```

## Validation

```text
Fix fields
```

## Permission

```text
Close
```

## Stale

```text
Refresh
```

## Sync

```text
Retry Sync
```

## Permanent/protected

```text
View explanation / return
```

The UI must use the error classification supplied by the application layer.

---

# 149. Component Inventory

Reusable components should include at minimum:

```text
AppHeader
BottomNavigation
StatusBadge
PaymentStatusBadge
InvoiceStatusBadge
SyncStatusIndicator
OrderCard
CustomerIdentityBlock
BusinessIdentityBlock
OrderTimeline
SectionCard
MoneyRow
FinancialSummary
PaymentSummary
PaymentHistoryList
PrimaryButton
SecondaryButton
DangerButton
FormField
MoneyInput
QuantityStepper
SelectionCard
SearchBar
FilterSheet
EmptyState
LoadingState
ErrorState
OfflineBanner
SyncBanner
ConfirmationDialog
DateTimeDisplay
AddressBlock
InvoicePreview
ReportSummaryCard
```

Component names may vary, but equivalent reusable behavior is required.

---

# 150. Component Ownership Rules

Components are presentation-only.

They must not:

- write SQLite;
- call Firebase business mutations;
- calculate authoritative financial values;
- decide permissions;
- implement order state transitions;
- mutate payment history.

They receive view-model/application data and invoke approved callbacks/use cases.

---

# 151. View-Model Contract

A screen should consume presentation-safe data such as:

```text
OrderDetailViewModel
CustomerSummaryViewModel
PaymentSummaryViewModel
InvoiceViewModel
ReportViewModel
SyncStatusViewModel
```

The view model may transform domain data for presentation.

It must not create new business meaning.

---

# 152. Form State vs Business State

Distinguish:

```text
Draft form state
```

from:

```text
Persisted business state
```

Example:

A quantity currently typed into an Admin form is not the authoritative final quantity until the approved save/finalization operation commits.

The UI must not present unsaved values as historical facts.

---

# 153. Navigation Guards

Before leaving a form with unsaved changes:

```text
Unsaved changes
Discard?
```

Before leaving an invoice finalization review:

```text
Review still in progress
```

The guard must not create or finalize business data.

---

# 154. Customer Order Review Guard

If the customer has incomplete required fields:

```text
Complete required information before placing the order.
```

Do not silently fill required values with defaults unless the product contract defines them.

---

# 155. Admin Finalization Review Guard

Before finalization:

```text
Original
Received
Final
Charges
GST
Payment
```

must be reviewable.

The final action must remain explicit.

---

# 156. Staff Finalization Guard

Staff sees:

```text
All final billing information is ready.
```

If data is not ready for Staff finalization:

```text
Financial changes are required.
Admin access is required before this invoice can be finalized.
```

Do not expose editable financial controls.

---

# 157. Payment Eligibility UI

The UI should show payment action only when the order/payment contract allows recording.

If unavailable:

```text
Payment cannot be recorded for this order at this time.
```

Do not infer eligibility from a generic "order is open" rule.

---

# 158. Customer Payment Visibility

Customer sees permitted payment information.

Possible:

```text
Paid
Due
Payment history
```

Do not show internal actor/audit details unless explicitly customer-facing.

---

# 159. Staff Payment Visibility

Staff may see operational payment information needed for work.

Do not expose unnecessary financial administration or correction controls.

---

# 160. Admin Payment Visibility

Admin may see:

```text
Payment ledger
Paid
Due
Payment method
Actor attribution
Correction history
```

within assigned business scope.

---

# 161. Payment Correction UX

Admin-only.

Flow:

```text
Payment History
↓
Select payment
↓
Start Correction
↓
Reason
↓
Review original payment
↓
Create adjustment/reversal
↓
Review new ledger result
↓
Confirm
```

Do not edit the original payment row directly.

Original payment remains visible in history.

---

# 162. Customer Payment Correction Visibility

Customer should see the current permitted payment state.

Do not expose internal correction mechanics or audit controls.

---

# 163. Admin Customer Order History

Inside a Customer detail:

```text
Customer
↓
Orders
```

Only that customer's orders.

Summary may show:

```text
Total Orders
Completed
Cancelled
Total Billed
Total Paid
Pending Amount
```

Exact calculations come from authoritative data.

---

# 164. Staff Customer Information

Staff may see:

```text
Name
Relevant mobile
Relevant collection/delivery address
Relevant order information
Payment status
Finalized invoice
```

Do not expose unrelated profile administration.

---

# 165. Business Customer Address Handling

Where both personal and business address concepts exist, labels must be explicit.

Avoid a generic:

```text
Address
```

when context is ambiguous.

Use:

```text
Business Address
```

or:

```text
Customer Address
```

according to the approved data field.

---

# 166. Customer Type Selection

Use a clear segmented control/radio group:

```text
Personal
Business
```

When Business selected, reveal business fields.

Do not hide required business fields behind an obscure secondary screen.

---

# 167. Customer Type Confirmation

Switching:

```text
Business → Personal
```

must show a confirmation before clearing business-specific fields.

Do not require confirmation for harmless navigation between tabs.

---

# 168. Staff Search Speed

The Staff Orders screen should prioritize:

```text
Search field
```

with immediate focus option.

A common Staff workflow should not require opening several menus before search.

---

# 169. Order Search Result Ranking

The search layer decides result ordering.

UI must not invent a business ranking.

It may display exact/partial matching results supplied by the query contract.

---

# 170. Filter UX

Filters may use a bottom sheet:

```text
Status
Payment
Date where approved
```

Buttons:

```text
Clear
Apply
```

Do not add filters not supported by the backend/query contract.

---

# 171. Filter Persistence

Filter persistence is a usability decision.

If implemented, it must not create business state.

Clearing filters must not alter stored orders.

---

# 172. Order Card Action Rules

Cards should have one primary navigation action:

```text
Open
```

Secondary contextual actions only when approved.

Avoid putting many operational actions directly on every card.

The order detail is the authoritative action surface.

---

# 173. Staff Quick Actions

Approved quick action:

```text
+ Walk-In Order
```

Other quick actions may be shown only when they map to an already approved operation.

Do not add:

```text
+ Customer
+ Staff
+ Expense
+ Inventory
```

to Staff dashboard.

---

# 174. Admin Quick Actions

Useful approved actions:

```text
Orders
People
Reports
```

Master-data actions may be reachable from More.

Do not create a separate "Finance" module unless the approved navigation is updated.

---

# 175. Customer Quick Action

Primary:

```text
+ Place New Order
```

Do not add business administration shortcuts.

---

# 176. App Empty/First-Run Experience

Customer:

```text
Complete your profile
```

if required.

Admin:

```text
Complete Business Settings
```

when initial setup is incomplete.

Staff:

```text
Complete required first-login password change
```

where required.

The application must not expose incomplete configuration as if production-ready.

---

# 177. Initial Admin Setup UX

Admin setup is controlled.

If the account enters initial setup:

```text
Welcome
Complete Business Profile
↓
Business Settings
↓
Save
↓
Admin Home
```

Do not show public "Create Admin" UI.

---

# 178. First Staff Login UX

If temporary-password change is required:

```text
Temporary password detected
↓
Change Password
↓
Staff Dashboard
```

Do not show Staff a role selector.

---

# 179. Inactive Account UX

Customer:

```text
Your account is inactive.
You cannot place a new order.
```

Existing permitted history may remain accessible according to authorization.

Staff:

```text
Your Staff account is inactive.
Please contact an administrator.
```

Do not expose administrative internals.

---

# 180. Customer Registration Inactive Edge Case

If account creation succeeds but application profile is incomplete/unverified:

Route to the appropriate completion/verification state.

Do not silently create a usable order account before required authentication/profile conditions are satisfied.

---

# 181. Order Creation Offline UX

Customer:

```text
Review
↓
Place Order
↓
Saved on this device
↓
Order appears immediately
```

Staff Walk-In:

```text
Create Walk-In
↓
Saved on this device
↓
Order appears immediately
```

Do not block the UI waiting for cloud acknowledgement.

---

# 182. Payment Offline UX

After local commit:

```text
Payment recorded
Sync pending
```

The local payment should immediately affect the displayed permitted payment summary according to the local committed state.

Do not claim server synchronization.

---

# 183. Status Offline UX

After local commit:

```text
Status updated
Sync pending
```

The timeline updates immediately.

If sync later fails due to a conflict:

```text
The order changed elsewhere.
Refresh required.
```

Do not silently overwrite.

---

# 184. Invoice Offline UX

If permitted finalization is committed locally:

```text
Invoice finalized
Sync pending
```

The invoice may be viewed locally from the finalized snapshot.

Do not claim that the invoice exists in the cloud until acknowledgement.

---

# 185. App Restart UX

After restart:

```text
Restore local state
↓
Show permitted locally committed data
↓
Show pending sync
↓
Synchronize when possible
```

Pending work must not disappear from the UI.

---

# 186. Sync Queue User Experience

User-visible representation should be simplified:

```text
Saved
Sync pending
Synced
Needs attention
```

Do not expose internal queue states such as:

```text
PROCESSING
RETRY_BACKOFF
IDEMPOTENCY_CONFLICT
```

unless a diagnostics contract explicitly requires them.

---

# 187. Conflict Screen

For non-financial stale conflict:

```text
This record has newer changes.

Your device has an older version.
Refresh before continuing.
```

For financial conflict:

```text
Financial changes could not be merged automatically.

The latest record must be reviewed by an authorized user.
```

Do not offer automatic merge.

---

# 188. Invoice Correction Conflict

If Admin starts correction but another device changes the invoice:

```text
This invoice was updated elsewhere.
Your correction was not applied.
Refresh and review the latest invoice.
```

Never overwrite the newer invoice.

---

# 189. Customer Profile Conflict

If a customer profile is changed elsewhere:

```text
This profile has newer changes.
Refresh before saving.
```

Do not silently overwrite.

---

# 190. Master Price Conflict

If an Admin edits a price that changed elsewhere:

```text
This price was updated on another device.
Refresh before saving.
```

Do not silently replace the newer value.

---

# 191. Accessibility for Financial Data

Use explicit labels.

Avoid communicating:

```text
₹1200
```

without context.

Prefer:

```text
Final amount: ₹1,200
Paid: ₹300
Due: ₹900
```

This is better for both visual scanning and assistive technologies.

---

# 192. Accessibility for Status

Prefer:

```text
Order status: Processing
Payment status: Partially Paid
Invoice status: Finalized
Sync status: Pending
```

Do not rely on colored chips alone.

---

# 193. Accessibility for Destructive Actions

Buttons should announce their consequence:

```text
Deactivate Customer
Cancel Order
Finalize Invoice
Create Invoice Revision
```

Do not use only icons.

---

# 194. Localization Rules

V1 language:

```text
Indian English
```

Use consistent terminology.

Preferred:

```text
Mobile Number
PIN Code
Payment
Due
Invoice
Pickup
Drop-Off
Collected
Delivered
```

Avoid switching terminology randomly between screens.

---

# 195. Terminology Lock

The UI must preserve these distinctions:

```text
Order
Invoice
Payment
Due
Estimate
Final Amount
Received Quantity
Final Quantity
Price
Rate
GST
Additional Charge
Walk-In
Customer
Staff
Admin
```

Do not call an estimate an invoice.

Do not call payment status an order status.

Do not call a Walk-In a registered Customer.

---

# 196. Required UI Copy Concepts

The following concepts are mandatory even if final wording is refined:

```text
Estimated Order
Invoice Not Finalized
Finalized Invoice
Payment Pending
Partially Paid
Paid
Due
Sync Pending
Offline
Sync Error
Admin Required
Correction Locked
Original Invoice
Revised Invoice
Walk-In
```

---

# 197. No Technical Business Copy

Do not show customers or ordinary Staff:

```text
businessId
customerId
syncId
version
revision integer
Firestore
SQLite
Cloud Function
API
Security Rule
```

unless specifically required in a diagnostics surface.

---

# 198. UI Data Provenance

Where a value has historical significance, the UI should consume the historical snapshot.

Examples:

```text
Finalized invoice business name
Finalized invoice customer name
Historical rate
Historical GST
Historical additional charges
Historical payment entries
```

Do not replace these with current master/profile data.

---

# 199. Current vs Historical Visual Rule

For Admin screens where current and historical data coexist:

```text
CURRENT
```

and:

```text
HISTORICAL
```

must be distinguishable through section context.

Never imply that changing current settings updates historical records.

---

# 200. Invoice Revision Visual Rule

Current invoice:

```text
CURRENT VALID INVOICE
```

Original:

```text
ORIGINAL INVOICE
```

Revision:

```text
REVISION 1
```

The exact revision label comes from backend data.

---

# 201. Report Data Freshness

If data is known to be locally incomplete/offline:

```text
Data may be incomplete until synchronization finishes.
```

The UI must not present an incomplete report as authoritative complete reporting.

---

# 202. Report Calculation Ownership

The UI displays report values.

The UI must not independently calculate:

```text
Revenue
GST
Paid
Due
Payment totals
```

Those values come from the report application/domain contract.

---

# 203. Invoice Calculation Ownership

The UI may display live previews while Admin edits final billing.

However:

```text
UI preview ≠ authoritative financial mutation
```

The authoritative final amount comes from the approved domain/backend calculation.

---

# 204. Quantity Editing UX

Admin final quantity input:

```text
Final Quantity
[  8  ]
```

If original/received values exist:

```text
Ordered: 10
Received: 8
Final: 8
```

This helps the Admin understand the difference.

Do not overwrite the Ordered value.

---

# 205. Rate Editing UX

Admin:

```text
Final Rate
₹ [30.00]
```

Show the line impact:

```text
Quantity × Rate = Line Total
```

Do not let Staff edit the field.

---

# 206. Additional Charge Editing UX

Admin:

```text
Charge
Description
Amount
Note
```

Actions:

```text
Add Charge
Edit
Remove
```

Only before finalization where approved.

After finalization, normal edit controls disappear.

---

# 207. Invoice Locked State

After finalization:

```text
Financial details are protected.
```

Do not display an editable pencil icon.

Admin correction has its own controlled action if eligible.

---

# 208. Staff Invoice Locked State

If Staff opens finalized invoice:

```text
Finalized
Protected
```

No edit.

---

# 209. Customer Invoice Locked State

Before finalization:

```text
Invoice not available yet
```

Do not show editable invoice placeholders.

---

# 210. Customer Order Estimate UI

Use explicit wording:

```text
Estimated Amount
```

and supporting text:

```text
The final amount may be determined after the laundry is received and final billing is completed.
```

The exact copy may be refined, but the estimate/final distinction must remain.

---

# 211. Staff Walk-In Amount UI

Use:

```text
Fixed Amount
```

not:

```text
Estimate
```

This preserves the Walk-In product model.

---

# 212. Staff Walk-In Date UI

Do not show a fake pickup scheduling field.

An unscheduled Walk-In must not be visually treated as a scheduled Customer pickup.

---

# 213. Customer Pickup Scheduling UI

Only show scheduling information when the selected customer collection method requires it.

Do not show pickup date/time for:

```text
Customer Drop-Off
```

unless another approved requirement explicitly requires a separate date.

---

# 214. Delivery UI

For Delivery-by-Us flows:

At the appropriate stage show:

```text
Delivery address
Return method: Delivery by Us
```

Action:

```text
Out for Delivery
```

then:

```text
Delivered
```

Do not show delivery action for Customer Pickup flows.

---

# 215. Customer Pickup UI

For Customer Pickup flows:

```text
Ready for Pickup
```

then:

```text
Collected
```

Do not show:

```text
Out for Delivery
Delivered
```

as actionable stages.

---

# 216. Pickup-by-Us UI

For Pickup-by-Us:

```text
Pickup Pending
```

then:

```text
Picked Up
```

The pickup action must be available only at the correct state and role.

---

# 217. Customer Drop-Off UI

For Customer Drop-Off:

```text
Received
```

is the starting physical-operational stage after the order is created/received.

Do not create a fake:

```text
Pickup Pending
```

stage.

---

# 218. Received Verification UI

At the appropriate point:

```text
Received Laundry
```

Display comparison:

```text
Item
Ordered
Received
Difference
```

Admin can edit received quantities where allowed.

Staff cannot edit them.

---

# 219. Received Difference Warning

If:

```text
Received ≠ Ordered
```

show a clear informational state:

```text
Received quantity differs from the original order.
```

Do not automatically change the final billed quantity.

Do not automatically cancel.

The approved business flow determines what happens next.

---

# 220. Final Billing Difference Warning

If final quantity differs from received quantity:

```text
Final billed quantity differs from received quantity.
```

This is informational and review-oriented.

The UI must not invent an approval workflow unless defined.

---

# 221. Cancellation After Physical Receipt

If cancellation occurs while physical laundry is held:

Show:

```text
Order Cancelled
Return Required
```

Then the return status is separately visible.

Do not hide the cancellation behind the return state.

---

# 222. Return Completion UX

Return task:

```text
Return Pending
```

Action when authorized:

```text
Mark Returned
```

If note is required by backend contract, collect it.

After completion:

```text
Returned
```

The order remains:

```text
Cancelled
```

---

# 223. Audit Visibility

Customer:

```text
No internal audit
```

Staff:

```text
Only operational history permitted
```

Admin:

```text
Authorized history and correction information
```

The UI must not expose raw audit storage structures.

---

# 224. Actor Attribution UX

Where actor identity is allowed to be shown, use human-readable presentation:

```text
Recorded by: Staff Name
```

rather than:

```text
recordedBy: uid_abc...
```

Customer-facing views should omit internal actor data unless explicitly approved.

---

# 225. Business Isolation UX

The mobile UI operates in the authenticated business context.

Do not provide a Business Switcher in V1.

Do not provide:

```text
Change Business ID
```

Staff/Admin must not select arbitrary business IDs.

---

# 226. Role Isolation UX

No common navigation drawer should expose all three panels.

The root navigation is role-specific.

A Customer should never see:

```text
Staff
Admin
People
Reports
```

Staff should never see:

```text
People management
Business Settings
Prices
GST Settings
```

Admin sees the approved Admin navigation.

---

# 227. Deep Link Role Protection

Any deep link must first pass:

```text
Authentication
Role
Business scope
Ownership
Permission
State
```

Then render the destination.

---

# 228. UI Route Inventory

## Authentication

```text
/splash
/auth/login
/auth/register
/auth/verify-email
/auth/forgot-password
/auth/complete-profile
```

## Customer

```text
/customer/home
/customer/orders
/customer/orders/[orderId]
/customer/orders/new
/customer/orders/[orderId]/invoice
/customer/profile
```

## Staff

```text
/staff/home
/staff/orders
/staff/orders/[orderId]
/staff/walk-in
/staff/payments
/staff/profile
/staff/sync
```

## Admin

```text
/admin/home
/admin/orders
/admin/orders/[orderId]
/admin/people
/admin/people/staff
/admin/people/staff/[staffId]
/admin/people/staff/new
/admin/people/customers
/admin/people/customers/[customerId]
/admin/reports
/admin/more
/admin/more/items
/admin/more/services
/admin/more/prices
/admin/more/business-settings
/admin/more/gst-settings
/admin/more/invoice-settings
/admin/more/app-info
/admin/more/sync
```

Additional contextual routes:

```text
/admin/orders/[orderId]/finalize
/admin/orders/[orderId]/correction
/admin/orders/[orderId]/payment
```

These are route concepts; exact filesystem grouping may vary.

---

# 229. Route Naming Rules

Routes should:

- be role-scoped;
- use stable IDs;
- never contain authorization logic;
- not encode business ownership;
- not expose sensitive data in route strings.

A route parameter is only an identifier.

---

# 230. Screen-to-Use-Case Contract

Representative mapping:

```text
Customer New Order
→ CreateCustomerOrder

Staff Walk-In
→ CreateWalkInOrder

Order Status Action
→ TransitionOrderStatus

Received Laundry
→ RecordReceivedLaundry

Payment
→ RecordPayment

Staff Finalization
→ FinalizeInvoice

Admin Finalization
→ FinalizeInvoice

Correction
→ CorrectInvoice

Payment Correction
→ CorrectPayment

Staff Management
→ ManageStaff

Customer Management
→ ManageCustomer

Master Price
→ ManagePrice

Reports
→ GenerateReport

Sync
→ SyncPendingOperations
```

The UI must not bypass these boundaries.

---

# 231. Screen Data Loading Contract

Screen lifecycle:

```text
Route entered
↓
Authorization context available
↓
Load view-model data
↓
Render loading
↓
Render data / empty / error
↓
Subscribe to permitted local updates
↓
Reconcile cloud updates
```

Do not render unauthorized data while waiting for authorization resolution.

---

# 232. Optimistic UI Rules

Optimistic UI is allowed only where the local transaction has actually committed.

Valid:

```text
SQLite commit succeeded
→ show updated UI
```

Invalid:

```text
User tapped button
→ show success
→ database write later
```

Never show business success before the local operation has successfully committed.

---

# 233. Rollback UX

If local transaction fails:

```text
No business change was saved.
```

Do not leave the UI showing a change that was rolled back.

---

# 234. Cloud Reconciliation UX

After sync acknowledgement:

```text
Synced
```

If authoritative cloud data differs due to a valid server-side normalization or conflict path, the UI must reconcile to the authoritative result.

Do not silently preserve stale local financial truth.

---

# 235. Long Operation UX

For report/XLSX/PDF generation:

```text
Preparing…
```

For sync:

```text
Syncing…
```

For invoice finalization:

```text
Finalizing invoice…
```

Disable duplicate submission while the same operation is being processed locally.

---

# 236. Progress UX

Do not show fake progress percentages.

If actual progress is unavailable:

```text
Preparing…
```

not:

```text
73%
```

created from a timer.

---

# 237. No Fake Data UX

Production screens must never contain:

```text
Lorem ipsum
Demo Customer
Test Order
Sample Invoice
₹10,000 hard-coded
```

unless explicitly part of an isolated design fixture.

---

# 238. No Fake Success UX

Never show:

```text
Payment successful
Invoice created
Order synced
```

based only on a UI timer or mock response.

The message must correspond to actual application state.

---

# 239. No Silent Data Substitution

If a historical field is unavailable:

```text
Required historical data unavailable
```

or a safe error state must be shown.

Do not silently use:

```text
current customer name
current price
current GST
current business name
```

to fill historical invoice data.

---

# 240. Privacy-Safe Error UX

Error messages must not reveal:

- another customer;
- another business;
- internal record IDs beyond what is already appropriate;
- private Staff information;
- internal security details.

---

# 241. Confirmation Copy Governance

Final copy may be refined by the UI/UX owner without changing semantics.

However, copy must preserve:

```text
consequence
permission
financial protection
historical preservation
offline state
```

No copy change may imply a different business rule.

---

# 242. UI QA Requirements

Every production screen must be tested for:

```text
Small screen
Large screen
Long name
Long address
Long business name
Large font
Keyboard open
Keyboard closed
Loading
Empty
Error
Offline
Sync pending
Sync success
Sync error
Permission denied
Back navigation
Repeated tap
Restart
```

Financial screens additionally:

```text
No payment
Advance
Partial
Exact
Overpayment attempt
GST ON
GST OFF
Additional charges
Correction
Revision
```

---

# 243. Role QA Matrix

## Customer

Must never see:

```text
Admin People
Staff management
Prices
GST Settings
Business Settings
Invoice Correction
Other customers
```

## Staff

Must never see editable controls for:

```text
Received Quantity
Final Quantity
Final Rate
Billable Lines
Additional Charges
Master Prices
GST Settings
Business Settings
Customer Deactivation
Invoice Correction
Payment Correction
Staff Management
```

## Admin

Must see approved controls for:

```text
People
Master Data
Business
Received
Final Billing
Invoice
Payments
Corrections
Reports
```

---

# 244. Financial UX QA Matrix

Verify:

```text
₹0 / invalid payment blocked
₹300 advance
₹900 remaining due
₹1,200 exact paid
Overpayment rejected
GST ON
GST OFF
Additional charge
Final amount lower than advance
```

No negative due should be displayed.

---

# 245. Historical UX QA Matrix

Test:

```text
Change Customer name
→ old invoice unchanged

Change Price
→ old invoice unchanged

Change GST
→ old invoice unchanged

Change Business Settings
→ old invoice unchanged

Deactivate Staff
→ old actor attribution remains

Correct Invoice
→ original remains visible to Admin
```

---

# 246. Offline UX QA Matrix

Test:

```text
Offline order
Offline walk-in
Offline status
Offline payment
Permitted offline finalization
Kill app after local commit
Restart
Reconnect
Sync
Duplicate retry
Sync error
Conflict
```

The UI must accurately distinguish:

```text
Saved locally
vs
Synced
```

---

# 247. Screen Acceptance Criteria

A screen is accepted only when:

```text
Correct route
+
Correct role access
+
Correct data scope
+
Correct layout
+
Correct controls
+
Correct state handling
+
Correct validation
+
Correct offline behavior
+
Correct sync feedback
+
Correct error handling
+
Accessibility
+
No business-rule invention
```

---

# 248. Feature Acceptance Criteria

A feature is not complete merely because a screen renders.

It requires:

```text
UI
+
Use Case
+
Domain
+
Persistence
+
Authorization
+
Offline behavior
+
Sync
+
Tests
```

The UI/UX document defines presentation requirements only; downstream technical contracts must prove the rest.

---

# 249. Design Tokens That Must Be Approved Before Final Visual Freeze

The following visual items require explicit product/design approval if no existing approved brand system is supplied:

```text
Primary brand color
Secondary brand color
Accent color
Success color
Warning color
Error color
Background hierarchy
Card surface
Typography family
Logo treatment
App icon
Illustration style
```

Until approval:

```text
Do not invent a final brand identity.
```

Functional implementation may use temporary neutral tokens in a prototype branch only.

---

# 250. Recommended Visual Direction

The functional visual direction should be:

```text
Clean
Modern
Professional
Lightweight
High readability
Low visual noise
Operationally dense where needed
Comfortable for long Staff sessions
```

Avoid:

```text
Heavy gradients
Excessive glassmorphism
Large decorative illustrations
Tiny text
Excessive animation
Gaming-style UI
ERP-style dense tables everywhere
```

This is a visual recommendation and does not change product behavior.

---

# 251. Animation Rules

Animations must be purposeful.

Allowed:

- screen transition;
- list insertion;
- success feedback;
- status transition emphasis;
- loading shimmer where useful.

Avoid animation for:

- financial calculations;
- payment confirmation;
- invoice finalization;
- security decisions.

Never use animation to fake backend progress.

---

# 252. Haptic Feedback

Optional Android haptic feedback may be used for:

- successful action;
- confirmation;
- important status completion.

It must not be the only feedback.

---

# 253. Toast vs Inline Message

Use inline messages for:

- form validation;
- financial warnings;
- permission restrictions;
- invoice lock;
- conflict.

Use snackbar/toast for:

- successful local save;
- minor sync completion;
- non-critical feedback.

---

# 254. Critical Financial Warnings

The UI must clearly communicate:

```text
Finalization protects financial details.
```

and:

```text
Overpayment cannot be silently accepted.
```

and:

```text
Corrections use a controlled Admin process.
```

Do not promise refunds.

---

# 255. Customer Trust UX

Customer should always be able to distinguish:

```text
Estimate
Final Invoice
Paid
Due
Order Status
```

The interface must not create ambiguity about whether the amount is final.

---

# 256. Staff Efficiency UX

The Staff experience should favor:

```text
Search first
Next action first
Payment quick access
Walk-In quick access
Minimal typing
Large touch targets
Clear status
Clear sync state
```

Do not sacrifice audit/history or authorization for speed.

---

# 257. Admin Efficiency UX

Admin should favor:

```text
Business overview
Order lookup
People management
Final billing
Payment review
Reports
Settings
```

Financial editing should be explicit rather than hidden behind generic actions.

---

# 258. Customer Onboarding UX

Customer onboarding should be short:

```text
Create account
Verify email
Complete profile
Start order
```

Do not request unrelated information.

---

# 259. Staff Onboarding UX

Staff onboarding:

```text
Admin creates Staff
↓
Staff logs in
↓
Temporary password change
↓
Staff dashboard
```

No Staff self-registration.

---

# 260. Admin Onboarding UX

Controlled setup:

```text
Controlled Admin account
↓
Business profile
↓
Business settings
↓
Master data
↓
Operational use
```

Do not show public Admin registration.

---

# 261. UI Scope Exclusions

Do not design screens for:

```text
Super Admin
Multi-business switching
Multi-branch
Inventory
Payroll
Salary
Expenses
Advanced accounting
GPS
Route optimization
QR
Barcode
POS
Printer integration
Loyalty
Coupons
AI automation
Mandatory online gateway
Mandatory WhatsApp/SMS
Unrelated hospitality services
```

Future features require a new approved specification.

---

# 262. UI/UX Specification Blockers

The following are intentionally not invented.

## UI-SB-001 — Final brand visual tokens

**Missing:** owner-approved exact brand palette/logo treatment if not already separately approved.

**Required:** approve visual brand token set before final visual freeze.

## UI-SB-002 — Exact report screen dataset

**Missing:** final backend report schema/query contract.

**UI behavior:** display only fields returned by approved report contract.

## UI-SB-003 — Exact persisted status labels

**Missing:** final backend schema enum-to-display-label mapping.

**UI behavior:** use the canonical status enum and approved display labels; do not invent new stored states.

## UI-SB-004 — Exact invoice numbering display

**Missing:** final Backend Schema/Invoice Contract.

**UI behavior:** render the authoritative invoice number returned by the backend.

## UI-SB-005 — Exact sync operation presentation

**Missing:** final sync operation/result contract.

**UI behavior:** expose simplified user states only; do not invent internal queue semantics.

These blockers are technical/documentation gates. They do not authorize an agent to invent business behavior.

---

# 263. AI Coding Agent UI Control Protocol

An AI agent implementing UI must treat this document as a contract.

Before coding a screen, the agent must identify:

```text
Screen ID
Route
Role
SRS source
PRD source
TRD source
Use case
Data source
Allowed actions
Forbidden actions
States
Validation
Offline behavior
Sync behavior
Error behavior
Acceptance criteria
```

If any required business behavior is missing:

```text
STOP
→ Specification Blocker
```

---

# 264. AI Agent Must Not Invent UI Business Logic

The agent must not:

- add a new status;
- add a new role;
- add a new payment method;
- add a new permission;
- add a new cancellation rule;
- add an automatic refund;
- add a wallet;
- add a loyalty system;
- add a new customer-registration path;
- add Staff customer creation;
- add Staff financial editing;
- add invoice bypass;
- add automatic financial merge.

---

# 265. AI Agent Must Not Create Fake UI Behavior

Forbidden:

```text
fake API success
fake sync success
fake payment success
fake invoice creation
fake order IDs
fake report numbers
fake dashboard revenue
fake Firebase response
fake authorization
fake progress percentages
hard-coded production records
```

Temporary mocks are allowed only in explicitly isolated test/design fixtures.

---

# 266. AI Agent Connected-File Inspection Rule

Before modifying a screen, agent must inspect:

```text
route
screen
components
hooks
view-model
use case
domain model
repository
schema
security contract
sync handler
tests
```

A UI fix must not break:

```text
data model
permissions
sync
financial logic
historical data
```

---

# 267. UI Agent Change Report

Every meaningful UI implementation task must report:

```text
Screen:
Route:
Role:
Requirements:
SRS source:
PRD source:
TRD source:
Components changed:
Routes changed:
View-model changed:
Use cases touched:
Validation:
Offline behavior:
Sync behavior:
Error states:
Accessibility:
Tests:
Known limitations:
Specification Blockers:
```

---

# 268. UI/UX Definition of Done

A screen is complete only when:

```text
Route implemented
+
Role protected
+
Correct data scope
+
Correct layout
+
Correct components
+
Correct states
+
Correct validation
+
Correct error handling
+
Correct offline state
+
Correct sync state
+
Correct permissions presentation
+
Accessibility
+
Responsive behavior
+
No dummy production data
+
Tests
```

For financial screens additionally:

```text
Estimate/final distinction
+
Paid/due distinction
+
Invoice protection
+
Historical snapshot presentation
+
Correction/revision presentation
```

---

# 269. UI Regression Gates

Before release, test every role:

```text
Customer
Staff
Admin
```

Test:

```text
Login
Registration
Profile
Orders
Order detail
Status
Payment
Invoice
Walk-In
People
Master Data
Reports
Settings
Sync
Offline
Restart
Error
```

---

# 270. UI Security Regression

Verify:

```text
Customer cannot navigate to Staff/Admin routes
Staff cannot navigate to Admin routes
Staff cannot access protected financial controls
Customer cannot access another order
Deep links do not bypass authorization
Inactive users cannot access protected actions
Business scope cannot be changed from UI
```

Backend tests remain authoritative.

---

# 271. UI Financial Regression

Verify:

```text
Estimate ≠ Final Invoice
Advance ≠ Finalization
Payment status ≠ Order status
Due never negative
Overpayment rejected
Final invoice locked
Original invoice preserved
Revision visible correctly
Historical data not replaced by current data
```

---

# 272. UI Offline Regression

Verify:

```text
Local save immediately visible
Offline banner visible
Pending sync visible where appropriate
Restart preserves local work
Reconnect triggers sync
Synced state appears only after acknowledgement
Sync error does not delete local work
Financial conflicts are not silently merged
```

---

# 273. UI Accessibility Regression

Verify:

```text
Small screen
Large font
Screen reader
Keyboard
Long name
Long address
Long business name
Contrast
Touch target
No color-only state
No clipping
No horizontal overflow
```

---

# 274. UI Release Checklist

## Authentication

- [ ] Login
- [ ] Customer registration
- [ ] Email verification
- [ ] Forgot password
- [ ] Staff first-login password change
- [ ] Controlled Admin setup
- [ ] Role routing
- [ ] Inactive account handling

## Customer

- [ ] Dashboard
- [ ] Orders
- [ ] New Order
- [ ] Collection
- [ ] Return
- [ ] Estimate
- [ ] Tracking
- [ ] Cancellation
- [ ] Invoice
- [ ] Profile

## Staff

- [ ] Dashboard
- [ ] Search
- [ ] Filters
- [ ] Walk-In
- [ ] Operational statuses
- [ ] Payment
- [ ] Finalize unchanged invoice
- [ ] Profile/session
- [ ] Sync

## Admin

- [ ] Home
- [ ] Orders
- [ ] People
- [ ] Staff
- [ ] Customers
- [ ] Items
- [ ] Services
- [ ] Prices
- [ ] Business Settings
- [ ] GST Settings
- [ ] Invoice Settings
- [ ] Finalization
- [ ] Corrections
- [ ] Payments
- [ ] Reports
- [ ] XLSX
- [ ] More
- [ ] App Information

## States

- [ ] Loading
- [ ] Empty
- [ ] Error
- [ ] Offline
- [ ] Syncing
- [ ] Synced
- [ ] Sync Error
- [ ] Permission denied
- [ ] Stale/conflict
- [ ] Locked invoice

## Accessibility

- [ ] Small screen
- [ ] Large text
- [ ] Long names
- [ ] Long addresses
- [ ] Long business names
- [ ] Correct keyboards
- [ ] Touch targets
- [ ] Contrast
- [ ] Screen reader
- [ ] No clipping
- [ ] No horizontal overflow

---

# 275. Cross-Document Traceability

| UI/UX Area | Parent Source |
|---|---|
| Product scope | SRS Sections 1–4; PRD Sections 3–6 |
| Roles | SRS 9/218; PRD 7/106 |
| Authentication | SRS 10/197; PRD 8/114 |
| Customer | SRS 13–15/247–248; PRD 8–10/115 |
| Staff | SRS 16–17/249; PRD 16–17/116 |
| People | SRS 18–21/265; PRD 45–50/123 |
| Items/Services/Prices | SRS 23–26/240–241; PRD 69–70 |
| Collection | SRS 28/220; PRD 11–13 |
| Return | SRS 29/220; PRD 12–13 |
| Order lifecycle | SRS 30–33/220; PRD 13–15 |
| Original/Received/Final | SRS 36–38/222; PRD 16–18 |
| Invoice finalization | SRS 39–50/223; PRD 18–26 |
| Payments | SRS 51–55/224–226; PRD 31–37 |
| Walk-In | SRS 219; PRD 38–41 |
| Cancellation | SRS 221; PRD 51–53 |
| Reports | SRS 243; PRD 61–65 |
| Offline | SRS 230/252; PRD 72–76 |
| Privacy | SRS 235–236; PRD 87 |
| Error handling | SRS 246; PRD 86 |
| Accessibility | SRS 252; PRD 132 |
| Business settings | SRS 241–242; PRD 67–68 |
| Invoice correction | SRS 227; PRD 26–27/122 |
| AI controls | SRS 271; PRD 152–154 |
| Technical architecture | TRD 4–8, 22–31 |
| UI implementation boundary | TRD 5, 46–53 |

---

# 276. Final UI/UX Principles

The UI must always preserve:

```text
FAST
+
SIMPLE
+
OFFLINE
+
SECURE
+
ACCURATE
+
EASY TO USE
```

The UI must make these distinctions obvious:

```text
Customer vs Walk-In
Estimate vs Final Invoice
Original vs Received vs Final
Order Status vs Payment Status
Invoice Status vs Sync Status
Current Data vs Historical Data
Normal Finalization vs Correction
Staff View vs Admin Control
Saved Locally vs Synced
```

---

# 277. Final AI UI Rule

> **The UI presents and invokes the approved system. It does not decide what the business system should be.**

If a required UI behavior is missing from:

```text
SRS
+
PRD
+
TRD
+
approved backend/security contract
```

the agent must:

```text
STOP
↓
Report Specification Blocker
↓
Owner Decision
↓
Update Documentation
↓
Update UI/Backend/Test Contracts
↓
Implement
```

No UI shortcut may weaken:

- security;
- permission boundaries;
- financial integrity;
- historical integrity;
- offline integrity;
- customer isolation;
- business isolation.

---

# 278. Final UI/UX Authority Statement

This document is derived from:

```text
TREAT_HOSPITALITY_SERVICES_Laundry_App_Production_Master_SRS_V5.0.md
TREAT_HOSPITALITY_SERVICES_Laundry_App_PRD_V1.0.md
TREAT_HOSPITALITY_SERVICES_Laundry_App_TRD_V1.0.md
```

SRS V5.0 remains authoritative.

PRD V1.0 remains the product contract.

TRD V1.0 remains the technical implementation contract.

UI/UX V1.0 defines presentation and interaction behavior without changing the underlying business rules.

**No AI agent or developer may use UI convenience to weaken the approved system.**

Production UI is complete only when it is:

```text
Correctly routed
+
Correctly authorized
+
Correctly scoped
+
Correctly displayed
+
Correctly validated
+
Offline-safe where required
+
Sync-aware
+
Accessible
+
Tested
+
Free of dummy production behavior
```

**End of UI/UX Specification V1.0**
