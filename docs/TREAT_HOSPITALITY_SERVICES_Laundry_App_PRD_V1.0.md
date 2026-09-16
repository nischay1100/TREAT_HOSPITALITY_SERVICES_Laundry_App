# TREAT HOSPITALITY SERVICES — Laundry Management App
# Product Requirements Document (PRD) — Production Master V1.0

**Document ID:** THS-LAUNDRY-PRD-V1.0  
**Product:** TREAT HOSPITALITY SERVICES Laundry Management App  
**Product Scope:** Laundry Services only  
**Primary Platform:** Android mobile application  
**Future Platform:** Admin Web Application  
**Source of Truth:** `TREAT_HOSPITALITY_SERVICES_Laundry_App_Production_Master_SRS_V5.0.md`  
**Document Status:** Production Product Requirements Contract  
**Audience:** Product owner, UI/UX designer, developers, QA, security reviewer, AI coding agents  
**Authority:** The SRS V5.0 is the parent specification. This PRD translates its product/business requirements into a product-level contract.  
**Implementation Principle:** AI/developer agents implement this PRD and the downstream technical documents; they must not invent or change business behavior.

---

# 1. Document Purpose

This PRD defines exactly what the TREAT HOSPITALITY SERVICES Laundry Management App must do from a product and user-behavior perspective.

It converts the approved SRS V5.0 into:

- Product goals
- User roles
- Product boundaries
- User journeys
- Feature requirements
- Business behavior
- Permission behavior
- Order lifecycle behavior
- Invoice behavior
- Payment behavior
- People management behavior
- Reporting behavior
- Offline behavior from a product perspective
- Product-level security expectations
- Acceptance criteria
- Release priorities
- Traceability to SRS V5.0

This PRD is intentionally product-focused.

It must not become a substitute for:

- Technical Requirements Document (TRD)
- UI/UX specification
- Backend schema specification
- Firestore Security Rules specification
- Cloud Functions contract
- SQLite schema/migration specification
- Sync protocol specification
- Automated test implementation

Those documents will be created separately and must remain consistent with this PRD and SRS V5.0.

---

# 2. Authority and Precedence

## 2.1 Parent Specification

The authoritative parent document is:

`TREAT_HOSPITALITY_SERVICES_Laundry_App_Production_Master_SRS_V5.0.md`

SRS V5.0 explicitly establishes itself as the Production Master / Binding Engineering Contract and supersedes V4.0 where requirements conflict.

Therefore:

```text
SRS V5.0
    ↓
PRD V1.0
    ↓
TRD
    ↓
UI/UX Specification
    ↓
Backend Schema / Security Contract
    ↓
Implementation
```

## 2.2 Conflict Rule

If this PRD accidentally conflicts with SRS V5.0:

1. SRS V5.0 wins.
2. The conflict must be reported.
3. The affected PRD requirement must be corrected before implementation.
4. An AI agent must not silently choose one interpretation.

## 2.3 Non-Invention Rule

No developer, designer, QA engineer, or AI agent may invent:

- A new role
- A new permission
- A new order status
- A new payment method
- A new cancellation rule
- A new invoice rule
- A new financial correction behavior
- A new customer-registration path
- A new business workflow

when the SRS/PRD already defines the behavior.

If an affected requirement is genuinely missing:

```text
STOP
↓
Report Specification Blocker
↓
Owner Decision
↓
Update authoritative documentation
↓
Continue implementation
```

---

# 3. Product Identity

## 3.1 Business

**TREAT HOSPITALITY SERVICES**

The business may operate multiple hospitality-related services, but this application is specifically and intentionally for:

> **Laundry Services only.**

## 3.2 Product Name

Working product name:

**TREAT HOSPITALITY SERVICES Laundry Management App**

## 3.3 Product Type

An offline-first laundry operations application for:

- Registered personal customers
- Registered business customers
- Walk-in customers
- Laundry Staff
- Laundry Admin

## 3.4 Primary Platform

Android mobile application.

Technology choices are governed by SRS V5.0 and later TRD documentation.

## 3.5 Future Platform

A future Admin Web Application will use the same Firebase backend and business data model.

The future web application must not require a second independent business database.

---

# 4. Product Vision

The product should operate as a simple digital laundry operating system.

The product must make everyday laundry operations:

- Fast
- Simple
- Offline-capable
- Secure
- Accurate
- Easy to use

The product must reduce manual operational work while preserving complete historical records.

The product is not intended to become a general ERP.

---

# 5. Product Goals

## P0 Goals

The following goals are non-negotiable:

1. Protect customer data.
2. Protect business data.
3. Enforce role boundaries.
4. Prevent cross-business access.
5. Preserve historical financial records.
6. Prevent duplicate financial records.
7. Prevent overpayment.
8. Prevent unauthorized invoice changes.
9. Preserve committed offline work.
10. Maintain reliable synchronization.
11. Keep customer order information understandable.
12. Make Staff workflows fast.
13. Keep Admin financial control explicit.
14. Keep product behavior deterministic.

## P1 Goals

1. Customer self-service ordering.
2. Walk-in order management.
3. Pickup and drop-off support.
4. Delivery and customer pickup support.
5. Actual received-laundry verification.
6. Final invoice generation.
7. GST handling.
8. Payment recording.
9. Due tracking.
10. Customer and Staff management.
11. Reports.
12. Offline operational capability.

## P2 Goals

1. Reliable notifications where enabled.
2. Sync diagnostics.
3. Operational dashboard summaries.
4. Polished invoice presentation.

---

# 6. Product Non-Goals

V1 must not add:

- Super Admin
- Multi-business UI
- Multi-branch UI
- Multiple processing centres
- Separate delivery application
- Separate manager application
- Inventory management
- Salary management
- Payroll
- Expense management
- Advanced accounting
- GPS route optimization
- Garment QR tracking
- Barcode tracking
- POS hardware integration
- Printer integration
- Loyalty
- Coupons
- Advanced CRM
- AI features
- Mandatory online payment gateway
- Mandatory WhatsApp/SMS infrastructure
- Customer-to-multiple-business associations
- Complex accounting ledger
- Full fleet management

These are outside V1 product scope.

---

# 7. Product Users

Exactly three application roles exist:

```text
CUSTOMER
STAFF
ADMIN
```

## 7.1 Customer

A registered laundry customer using the mobile application.

Customer types:

```text
PERSONAL
BUSINESS
```

## 7.2 Staff

A laundry operational employee.

Staff is intentionally restricted to operational work.

Staff is not a customer manager, pricing manager, GST manager, or financial correction authority.

## 7.3 Admin

The authorized business administrator.

Admin controls:

- Staff
- Customers
- Items
- Services
- Prices
- Orders
- Final financial data
- GST settings
- Business settings
- Invoice settings
- Reports
- Controlled corrections

---

# 8. Customer Product Requirements

## 8.1 Customer Registration

Customer registration is self-service.

Required authentication model:

```text
Email
+
Password
```

Customer must verify email.

Phone OTP is not required in V1.

## 8.2 Customer Registration Flow

```text
Open App
↓
Register
↓
Email
Password
Confirm Password
↓
Account Created
↓
Verification Email
↓
Email Verified
↓
Complete Profile
↓
Customer Dashboard
```

## 8.3 Customer Profile

Customer profile supports:

- Name
- Email
- Mobile Number
- Customer Type
- Business Name when Business
- Business Type when Business
- Business Address when applicable
- Business Phone when applicable
- Personal/customer address
- PIN Code
- Account status

## 8.4 Customer Type

Allowed values:

```text
PERSONAL
BUSINESS
```

Default:

```text
PERSONAL
```

## 8.5 Business Customer Display

A business customer should be visually represented as:

```text
Customer Name
Business Name
```

Example:

```text
Rahul Sharma
Hotel Paradise
```

## 8.6 Personal Customer Display

A personal customer should be represented as:

```text
Customer Name
Personal
```

Example:

```text
Rahul Sharma
Personal
```

## 8.7 Customer Profile Editing

A Customer may edit permitted own profile information.

Customer cannot modify:

- Role
- Business ID
- Historical order snapshots
- Finalized invoice data
- Payment history
- Status history
- Pricing configuration
- GST settings

## 8.8 Business-to-Personal Change

Changing:

```text
BUSINESS → PERSONAL
```

requires confirmation before business-specific fields are cleared.

Historical orders remain unchanged.

---

# 9. Customer Deactivation

Customer account status:

```text
ACTIVE
INACTIVE
```

Only Admin can deactivate/reactivate a Customer.

When inactive:

- New customer-created orders are blocked.
- Existing orders remain.
- Existing invoices remain.
- Existing payments remain.
- Historical records remain.
- Admin can still access the customer record.
- Admin can reactivate the customer.

Deactivation is not deletion.

The restriction must also be enforced outside the UI.

---

# 10. Customer Ordering

A registered Customer can create a laundry order.

A Customer order supports:

- Laundry items
- Laundry services
- Quantity
- Collection method
- Return method
- Pickup information where applicable
- Customer note
- Estimated order information

The Customer sees the estimate before the final invoice.

The estimate is not the final invoice.

---

# 11. Collection Methods

Two V1 collection methods exist:

```text
PICKUP_BY_US
CUSTOMER_DROP_OFF
```

## 11.1 Pickup by Us

The laundry business collects the customer's laundry.

This creates a pickup stage.

## 11.2 Customer Drop-Off

The customer personally brings laundry to the business.

The order starts with the laundry received at the business and does not require a pickup stage.

---

# 12. Return Methods

Two V1 return methods exist:

```text
DELIVERY_BY_US
CUSTOMER_PICKUP
```

## 12.1 Delivery by Us

The business returns the processed laundry to the customer's delivery address.

## 12.2 Customer Pickup

The customer collects processed laundry from the business.

No delivery stage is required.

---

# 13. Order Combination Matrix

The product must support exactly these four normal Customer order combinations.

| Collection | Return | Product Flow |
|---|---|---|
| Pickup by us | Delivery by us | Pickup → Processing → Ready → Delivery → Delivered |
| Pickup by us | Customer pickup | Pickup → Processing → Ready for Pickup → Collected |
| Customer drop-off | Delivery by us | Received → Processing → Ready → Delivery → Delivered |
| Customer drop-off | Customer pickup | Received → Processing → Ready for Pickup → Collected |

The UI must only expose actions valid for the selected flow.

---

# 14. Normal Order Lifecycle

## 14.1 Pickup + Delivery

```text
NEW
↓
PICKUP_PENDING
↓
PICKED_UP
↓
PROCESSING
↓
READY
↓
OUT_FOR_DELIVERY
↓
DELIVERED
```

## 14.2 Pickup + Customer Pickup

```text
NEW
↓
PICKUP_PENDING
↓
PICKED_UP
↓
PROCESSING
↓
READY_FOR_PICKUP
↓
COLLECTED
```

## 14.3 Drop-Off + Delivery

```text
NEW
↓
RECEIVED
↓
PROCESSING
↓
READY
↓
OUT_FOR_DELIVERY
↓
DELIVERED
```

## 14.4 Drop-Off + Customer Pickup

```text
NEW
↓
RECEIVED
↓
PROCESSING
↓
READY_FOR_PICKUP
↓
COLLECTED
```

---

# 15. Order Status Rules

The product must not allow arbitrary status jumps.

Invalid examples:

- NEW directly to DELIVERED
- PROCESSING directly to DELIVERED
- READY directly to DELIVERED when delivery is required
- READY_FOR_PICKUP to OUT_FOR_DELIVERY
- Final state back to PROCESSING
- Cancellation after a prohibited cutoff

Same-status repeated operations must not create duplicate status history.

Final operational states include:

```text
DELIVERED
COLLECTED
CANCELLED
```

---

# 16. Original Order vs Final Order

The product must clearly distinguish:

## 16.1 Original Order Data

What the customer initially requested.

Examples:

- Original items
- Original services
- Ordered quantities
- Collection method
- Return method
- Pickup information
- Customer note
- Estimated financial information

## 16.2 Received Laundry Data

What was actually received by the laundry.

Examples:

- Received quantity
- Received item information
- Received notes

## 16.3 Final Invoice Data

What is finally billed.

Examples:

- Final/billed quantity
- Final rate
- Final line items
- Additional charges
- GST
- Final subtotal
- Final invoice amount

These datasets must not be silently merged into one mutable value.

---

# 17. Received Laundry Verification

For normal registered Customer orders, actual received laundry is recorded separately from the original order.

Relevant quantities are:

```text
Ordered Quantity
Received Quantity
Final/Billed Quantity
```

Received quantity is controlled by Admin.

Staff cannot edit received quantity.

The system must preserve the original requested quantity even if actual received quantity differs.

---

# 18. Invoice Finalization Window

A normal Customer order reaches a stage where final invoice data can be prepared.

Before finalization:

- Original order remains visible.
- Received data is visible.
- Final financial data can be prepared.
- Authorized Admin can edit required financial information.
- Staff may finalize only when no financial/order edit is required.

The invoice must not be treated as final before finalization.

---

# 19. Staff Invoice Rule

Staff may finalize/generate the invoice only when all financial/order data is already correct.

Staff may proceed when:

- Received quantity is already correct.
- Final quantity is already correct.
- Final rates are already correct.
- Billable lines are already correct.
- No additional charge needs to be added/changed/removed.
- GST ON/OFF selection is the only remaining required decision.
- The order has reached the invoice-finalization stage.

If any financial/order edit is required:

```text
Staff
↓
Cannot edit
↓
Admin required
```

Staff must not work around this restriction.

---

# 20. Admin Invoice Finalization

Admin may:

- Edit received quantity.
- Edit final/billed quantity.
- Edit final rate.
- Add/remove/edit billable lines.
- Add/edit additional charges.
- Select GST ON/OFF.
- Finalize the invoice.
- Perform controlled correction/reissue after finalization under the defined rules.

---

# 21. Additional Charges

Additional charges are supported for final billing.

Each charge must contain:

- Charge name/description
- Amount
- Explanatory note
- Creator
- Creation time

A Staff member cannot create or edit additional charges.

Admin can create/edit them before final invoice finalization.

Additional charges become part of the final financial record.

---

# 22. GST Product Behavior

GST can be selected at finalization.

Allowed invoice state:

```text
GST ON
GST OFF
```

GST configuration is controlled by Admin.

Staff cannot change GST settings.

Staff may select/confirm GST ON/OFF during allowed invoice finalization when no other financial edit is needed.

Historical GST data is preserved on the invoice.

Changing future GST settings must not alter historical invoices.

---

# 23. Final Invoice Calculation

The final invoice is calculated from final billing data, not from the original estimate.

Conceptually:

```text
Final Line Subtotal
+
Additional Charges
=
Taxable/Pre-GST Final Subtotal
```

When GST is enabled:

```text
Final Subtotal
+
GST
=
Final Amount
```

When GST is disabled:

```text
Final Subtotal
=
Final Amount
```

The exact precision and rounding contract is defined by SRS V5.0 and the future TRD/backend specification.

Money must be treated as exact financial values, not approximate floating-point values.

---

# 24. Final Invoice Visibility

Before finalization:

```text
Customer invoice access = LOCKED
```

After successful finalization:

```text
Customer invoice access = OPEN
Staff invoice access = OPEN
Admin invoice access = OPEN
```

The customer must see the finalized invoice data.

The generated invoice must represent the finalized snapshot.

---

# 25. Invoice Immutability

After normal finalization:

- Staff cannot edit the invoice.
- Customer cannot edit the invoice.
- The system must not silently change the invoice.
- Current prices must not rewrite the invoice.
- Current GST settings must not rewrite the invoice.
- Current business settings must not rewrite historical invoice data.

Finalized invoice data is a protected financial snapshot.

---

# 26. Invoice Correction and Reissue

The normal invoice is not directly editable after finalization.

If an authorized correction is required:

```text
FINALIZED
↓
Admin Correction
↓
Admin Reason
↓
Original Invoice Preserved
↓
Revised Invoice
↓
Audit Trail
```

The original invoice must remain available in authorized history.

The customer sees the currently valid invoice after a valid revision.

The Staff and Admin views use the current valid invoice data while preserving the original audit history.

---

# 27. Seven-Day Financial Edit Rule

After an order is delivered:

### If due is still pending

Admin's controlled correction/edit path remains available according to the SRS correction rules.

### If delivered and no due is pending

Admin financial edit capability remains available for the defined seven-calendar-day period.

After more than seven calendar days:

```text
Delivered
+
Due = 0
+
More than 7 calendar days
=
Financial edit locked
```

Client-side device time must not be trusted to bypass this rule.

The backend must enforce the authoritative time rule.

---

# 28. Invoice Numbering

Invoice number is separate from Order ID.

Invoice identity must remain stable for the original finalized invoice.

A revised invoice must have an explicit relationship with the original invoice.

Invoice numbering/reissue rules must be implemented exactly as defined in the backend contract derived from SRS V5.0.

No developer may invent a different numbering behavior.

---

# 29. Payment Product Model

Payment is a separate financial activity from order status.

Allowed V1 payment methods:

```text
CASH
UPI
ONLINE
```

Online is recordable where the product/backend permits it; V1 does not require a mandatory online payment gateway.

---

# 30. Payment Before Final Invoice

Advance payment is allowed before final invoice finalization.

Example:

```text
Order
↓
Advance Payment
↓
Payment Ledger
↓
Processing continues
↓
Final Invoice
↓
Advance automatically applied
↓
Remaining Due
```

The customer must not receive final invoice access merely because an advance was paid.

Final invoice visibility opens only after finalization.

---

# 31. Advance Payment Example

Example:

```text
Final Amount = ₹1,200
Advance Paid = ₹300
```

After finalization:

```text
Paid = ₹300
Due = ₹900
Payment Status = PARTIALLY_PAID
```

The ₹300 must automatically count toward the finalized invoice.

---

# 32. Fully Paid Example

```text
Final Amount = ₹1,200
Paid = ₹1,200
```

Result:

```text
Due = ₹0
Payment Status = PAID
```

---

# 33. Payment Cannot Create Silent Overpayment

Example:

```text
Final Amount = ₹250
Paid = ₹300
```

The system must not silently accept the extra ₹50 as valid settlement.

An overpayment must be rejected or routed to the explicitly defined Admin resolution process.

No automatic refund behavior may be invented.

---

# 34. Staff Payment Recording

Staff may record money received from a customer.

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
Validate
↓
Save
```

The payment record must identify:

```text
recordedBy = Staff UID
```

Therefore the business can determine which Staff member entered the payment.

Staff cannot edit an old payment.

Staff cannot delete an old payment.

Staff cannot rewrite payment history.

---

# 35. Payment Ledger

Payment history is append-only during normal operation.

Each payment must remain individually traceable.

The product must support:

- Multiple payments
- Cash
- UPI
- Online where permitted
- Paid total
- Due total
- Payment status
- Payment history
- Staff/Admin actor attribution

---

# 36. Payment Status

Payment status is independent from order status.

Allowed payment states:

```text
PENDING
PARTIALLY_PAID
PAID
```

Conceptual rules:

```text
Paid = 0 and positive final amount
→ PENDING

0 < Paid < Final Amount
→ PARTIALLY_PAID

Paid = Final Amount
→ PAID
```

Overpayment is not a valid normal state.

---

# 37. Due Amount

Due amount is derived from:

```text
Final Amount - Total Valid Payments
```

with no negative due.

The product must never display an incorrect negative customer due.

Payment and order completion are separate concepts.

An order can be operationally completed while a due amount remains.

---

# 38. Walk-In Customer Product Model

A walk-in is a separate operational source.

```text
source = WALK_IN
```

A walk-in does not require Customer app registration.

Staff creates walk-in orders.

Staff may collect:

- Customer Name
- Mobile Number
- Address
- Item/service information
- Fixed amount
- Staff identity

A walk-in does not create a registered Customer account.

---

# 39. Walk-In Amount

The walk-in amount is fixed at the moment the walk-in order is created.

It is not treated as a normal registered Customer estimate.

No later received-quantity verification workflow is required for a V1 walk-in.

No normal final-rate editing workflow is required for a V1 walk-in.

---

# 40. Walk-In Return / Completion

A walk-in can remain in the system until the customer returns for collection/payment.

The product must support an operational completion/collection behavior appropriate to the walk-in workflow.

Payment status remains independent:

```text
PENDING
PARTIALLY_PAID
PAID
```

The Staff member who records a later payment is stored in the payment history.

---

# 41. Staff Order Creation Boundary

Staff may create:

```text
WALK_IN orders only
```

Staff must not create a registered Customer account.

Staff must not convert a walk-in into a registered account as part of normal Staff order creation.

Admin remains responsible for registered Customer management.

---

# 42. Staff Operational Capabilities

Staff may:

- Login
- View Staff dashboard
- Search orders
- Filter orders
- Create walk-in orders
- Process permitted operational steps
- Update permitted statuses
- Record payments
- View operational order data
- View finalized invoices
- Generate/finalize unchanged invoices
- Work offline for permitted operations
- Synchronize pending work

---

# 43. Staff Restrictions

Staff may not:

- Create Admin
- Create Staff
- Change role
- Change business ID
- Create registered Customer
- Edit registered Customer profile
- Deactivate Customer
- Reactivate Customer
- Edit received quantity
- Edit final quantity
- Edit final rate
- Edit billable lines
- Add/edit/remove additional charges
- Change master pricing
- Change GST configuration
- Change Business Settings
- Edit finalized invoices
- Correct/reissue invoices
- Edit old payments
- Delete payment history
- Rewrite status history
- Delete protected financial records
- Access another business

---

# 44. Admin Product Capabilities

Admin controls:

## People

- Staff
- Customers

## Master Data

- Items
- Services
- Prices

## Business

- Business Settings
- GST Settings
- Invoice Settings

## Operations

- Orders
- Received data
- Final billing data
- Invoice finalization
- Payments
- Controlled corrections

## Reporting

- Weekly
- Monthly
- Yearly
- XLSX export

---

# 45. People Section

Admin navigation must contain:

```text
People
├── Staff
└── Customers
```

Exactly these two People sections are required.

---

# 46. Staff People Experience

Admin opens:

```text
People
↓
Staff
↓
Staff Member
```

The Staff profile may display:

- Name
- Mobile
- Email/login
- Staff code
- Status
- Created date
- Updated date
- Last login where available

Admin may:

- Edit Staff profile
- Activate Staff
- Deactivate Staff
- Reactivate Staff

Deactivation is not deletion.

Historical Staff actor references must remain valid.

---

# 47. Customer People Experience

Admin opens:

```text
People
↓
Customers
↓
Customer
```

Customer list must support:

- Search
- Customer status
- Customer details
- Customer order history
- Payment summary where appropriate
- Business information where applicable

Search fields:

- Name
- Mobile
- Email
- Business Name where applicable

---

# 48. Customer Detail Isolation

When Admin opens one Customer:

> Only that Customer's information must be shown.

Sections may include:

1. Profile
2. Contact
3. Address
4. Business information
5. Account status
6. Orders
7. Payment summary

The page must not leak another customer's information.

---

# 49. Customer Order History

Customer detail must show only orders belonging to that Customer.

The product may show:

- Total Orders
- Completed/Delivered Orders
- Cancelled Orders
- Total Billed
- Total Paid
- Pending Amount

Each order can be opened separately.

---

# 50. Customer Deactivation from People

Admin can:

- Deactivate Customer
- Reactivate Customer

Deactivation blocks new registered Customer orders.

Historical orders remain accessible.

Existing operational orders remain manageable by authorized Staff/Admin.

---

# 51. Customer Cancellation

Customer cancellation is limited by collection state.

## Pickup by Us

Customer can cancel before:

```text
PICKED_UP
```

## Customer Drop-Off

Customer can cancel before:

```text
PROCESSING
```

After the defined cutoff, Customer cancellation is rejected.

---

# 52. Staff/Admin Cancellation

Staff/Admin can cancel an order only where the defined cancellation rules allow it.

A common operational case is:

```text
Actual received quantity does not match expected quantity
+
Customer does not agree to continue
```

The order can be cancelled according to the allowed state/cutoff.

Cancellation does not erase the order.

---

# 53. Physical Return After Cancellation

Cancellation and physical return are separate concepts.

If laundry is already physically with the business:

```text
Cancellation
↓
Return Required
↓
Return Pending
↓
Returned
```

The system must preserve:

- Return status
- Returned time
- Returned by
- Return note

The cancelled order remains in history.

---

# 54. Order Search

Staff/Admin need fast order lookup.

Search should support the fields defined by the SRS/backend contract, including appropriate:

- Order ID
- Customer name
- Customer phone
- Customer information
- Invoice number where available

Search must remain business-scoped.

Customer search must remain customer-data isolated.

---

# 55. Order Filters

Operational filters should support the product's defined status and payment states.

Examples:

- New
- Pickup pending
- Picked up
- Received
- Processing
- Ready
- Ready for pickup
- Out for delivery
- Delivered
- Collected
- Cancelled
- Payment pending
- Partially paid
- Paid

Only statuses applicable to the order flow should be shown as actionable states.

---

# 56. Customer Order Tracking

Customer can see:

- Own order
- Current order status
- Order timeline
- Collection method
- Return method
- Finalized invoice after finalization
- Permitted payment information

Customer cannot see internal-only operational or audit information.

---

# 57. Customer Privacy

Customer must not see:

- Other customers
- Staff private information
- Admin private information
- Internal notes not intended for customers
- Internal audit information
- Internal sync errors
- Other business data

Only customer-authorized order, invoice, payment, and business-facing information is shown.

---

# 58. Staff Privacy

Staff sees only operational customer information required to perform their work.

Staff must not receive unnecessary:

- Admin-only information
- Internal security information
- Sensitive audit information
- Unrestricted financial administration data

---

# 59. Admin Visibility

Admin can see complete records for the assigned business, subject to security and privacy rules.

Admin visibility includes:

- Customers
- Staff
- Orders
- Final invoices
- Payments
- Status history
- Reports
- Business settings
- Master data

---

# 60. Dashboard Product Requirements

## 60.1 Customer Dashboard

Customer dashboard should provide:

- Welcome/context
- Active order
- Recent orders
- Place New Order
- Current order status where applicable
- Final invoice access when available

## 60.2 Staff Dashboard

Staff dashboard should prioritize operational work.

Examples:

- New work
- Pickup pending
- Processing
- Ready
- Out for delivery
- Ready for pickup
- Unpaid/partially paid operational orders
- Walk-in operational work

## 60.3 Admin Dashboard

Admin dashboard should provide operational and financial overview.

Examples:

- Today's orders
- Pending
- Processing
- Ready
- Delivered
- Collected
- Unpaid
- Today's revenue

Exact dashboard calculations must use the authoritative data rules.

---

# 61. Reports

Admin reports must support:

- Weekly
- Monthly
- Yearly

Reports use finalized financial/order data.

Estimated values must not be treated as final revenue.

---

# 62. Report Source of Truth

Example:

```text
Estimated Amount = ₹500
Final Amount = ₹700
```

Revenue reports must use:

```text
₹700
```

not:

```text
₹500
```

Historical rates, GST, additional charges, payments, and due values must remain tied to the historical records that existed for the relevant order/invoice.

---

# 63. Report Metrics

Product reports may include:

- Total orders
- Delivered/completed orders
- Collected orders
- Cancelled orders
- Active orders
- Final revenue
- GST
- Paid amount
- Due amount
- Payment method totals
- Service quantities/revenue
- Item quantities/revenue
- Collection method breakdown
- Return method breakdown

The exact report dataset and calculation contract will be specified in the backend/schema documents.

---

# 64. Cancelled Orders in Reports

Cancelled-order reporting must follow the SRS-approved cancelled-order policy.

Cancelled orders must not silently become normal revenue.

The report must distinguish cancelled activity from finalized financial business.

---

# 65. XLSX Export

Admin can generate XLSX reports.

The exported report must match the authoritative report data.

The product must not expose unauthorized customer/business data through exports.

Exports must be generated with the correct reporting period and historical financial values.

---

# 66. Invoice PDF

Invoices are generated as local A4 PDF documents in V1.

The invoice must represent the finalized invoice snapshot.

It must include the required business identity and invoice information.

It must not reconstruct historical information from current master data.

Firebase Storage is not required for V1 invoice files.

---

# 67. Business Identity

The business represented by the app is:

**TREAT HOSPITALITY SERVICES**

Business settings may contain:

- Business Name
- Business Type
- Primary Mobile
- Alternative Mobile
- Email
- Address
- PIN Code
- GSTIN
- Default GST Rate
- Invoice Prefix
- Optional WhatsApp Number
- Optional future logo reference

Business identity is separate from an individual Admin's personal profile.

---

# 68. Historical Business Identity

When a business setting changes:

```text
Current Business Setting
→ Future operations
```

It must not rewrite:

```text
Historical order
Historical invoice
Historical report
```

Historical business snapshots remain fixed.

---

# 69. Price Product Rule

Admin manages:

- Items
- Services
- Prices

Current price applies to future operations.

Historical order/invoice pricing remains unchanged.

Changing a master price must not rewrite old invoices.

---

# 70. Item and Service Product Rules

Items and services are business master data.

They can be managed by Admin.

Historical orders preserve the item/service identity and relevant historical display information required by the SRS.

Master-data deactivation is preferred over destructive deletion when historical records depend on the data.

---

# 71. Notifications

Notifications may be enabled using Firebase Cloud Messaging.

Notifications can inform users about relevant order events.

Notification failure must not prevent a valid order or financial operation from being committed.

Customer-facing notifications must not expose internal information.

---

# 72. Offline Product Experience

The product is offline-first.

For permitted operational actions:

```text
User Action
↓
Validate
↓
Local Commit
↓
Immediate UI Update
↓
Pending Sync
↓
Network Available
↓
Cloud Synchronization
```

The product must not unnecessarily wait for Firebase before showing a locally committed operation.

---

# 73. Offline User Feedback

The product should clearly communicate:

```text
ONLINE
OFFLINE
SYNCING
SYNCED
SYNC ERROR
```

Where appropriate, show:

- Pending operation count
- Last successful sync
- Last failed sync
- Retry action

Raw backend errors or secrets must never be shown.

---

# 74. Offline Security Boundary

Offline mode must not become a privilege-escalation mechanism.

The product must not allow:

- Role escalation
- Business switching
- New privileged permissions
- Unauthorized financial editing

because the device is offline.

---

# 75. Sync Product Expectations

The product must preserve locally committed work.

Synchronization must prevent:

- Duplicate orders
- Duplicate payments
- Duplicate invoice creation
- Duplicate status history
- Cross-business data leakage

If a conflict affects financial data, it must not be silently auto-merged.

---

# 76. Cross-Device Experience

Staff/Admin may use more than one device.

The product must support:

- Pulling newer cloud data
- Pushing local changes
- Avoiding duplicates
- Preserving append-only histories
- Detecting stale writes
- Maintaining business isolation

---

# 77. Application Startup

Product startup should safely establish:

```text
SQLite initialization
↓
Authentication state
↓
Local trusted profile
↓
Correct dashboard
↓
Network state
↓
Synchronization
```

A previously authenticated user may operate within the permitted offline boundary.

---

# 78. Logout

Logout must:

- Sign out Firebase authentication
- Clear sensitive session/auth state
- Prevent protected screens
- Preserve only safe local data according to the security policy

No protected operation may remain accessible after logout.

---

# 79. Authentication Product Rules

Customer:

- Self-registers.
- Uses email/password.
- Must verify email.
- Can reset password through Firebase's password reset flow.

Staff:

- Does not publicly register.
- Is created by Admin through controlled privileged functionality.
- Changes temporary password on first login where required.

Admin:

- Is created only through controlled setup.
- Cannot be created through normal public registration.

---

# 80. Role Routing

After authentication:

```text
CUSTOMER → Customer Panel
STAFF    → Staff Panel
ADMIN    → Admin Panel
```

UI routing is not the security boundary.

Backend authorization must independently enforce the role.

---

# 81. Business Isolation

Every business-owned record is scoped to its business.

A user must not access another business.

The product must never trust a client-provided business ID by itself.

The authoritative security chain is conceptually:

```text
Authenticated UID
↓
Trusted User Profile
↓
Role
↓
Business ID
↓
Target Record Business ID
↓
Permission
↓
Record State
↓
Allowed Operation
```

---

# 82. Financial Safety

Financial product invariants include:

- No overpayment.
- No silent payment deletion.
- No silent invoice mutation.
- No historical price rewrite.
- No historical GST rewrite.
- No historical business snapshot rewrite.
- No unauthorized finalization.
- No invoice visibility before finalization.
- No duplicate financial operation.
- No financial conflict auto-merge.

---

# 83. Historical Data Principle

The product follows:

> Current settings control future operations; historical snapshots control historical records.

Therefore:

```text
Current Customer Profile
→ Future orders

Historical Customer Snapshot
→ Old orders/invoices

Current Price
→ Future orders

Historical Price
→ Old invoices/reports

Current GST Default
→ Future applicable operations

Historical GST Snapshot
→ Old invoices/reports

Current Business Settings
→ Future invoices

Historical Business Snapshot
→ Old invoices
```

---

# 84. Order History

Opening a completed order should preserve the full business story.

The order history should allow authorized users to understand:

1. What was originally requested.
2. What was actually received.
3. What was finally billed.
4. What GST was applied.
5. What additional charges were added.
6. What payments were received.
7. What amount remains due.
8. What status transitions occurred.
9. Who performed relevant actions.
10. What invoice was issued.
11. Whether a correction/reissue occurred.
12. Whether a cancelled order required physical return.

---

# 85. Actor Attribution

Important operations must identify the responsible user.

Examples:

- Order creator
- Order updater
- Status actor
- Payment recorder
- Invoice finalizer
- Invoice correction actor
- Return actor
- Staff manager

This is required for accountability.

---

# 86. Product Error Behavior

Errors should be:

- Clear
- Actionable
- Non-sensitive
- Understandable to non-technical Staff/Customers
- Consistent

The product must not expose:

- Service-account credentials
- Security-rule internals
- Stack traces
- Raw database errors
- Sensitive customer information
- Sensitive payment information

---

# 87. Data Privacy

Customer information may include:

- Name
- Phone
- Email
- Address
- Business information

The product must apply data minimization.

Only required information should be displayed to each role.

Customer and financial information must not be written to diagnostic logs.

---

# 88. Data Retention Product Rule

Protected historical business records must remain available according to the retention policy.

Deactivation is not deletion.

Historical order, invoice, payment, and audit information must not be casually deleted.

Account deletion/export/retention behavior must follow the approved data-governance and backend contract.

---

# 89. Product Performance Goals

The application should feel fast for ordinary Staff operations.

Priority areas:

- Dashboard opening
- Order search
- Customer lookup
- Order opening
- Status update
- Payment recording
- Walk-in creation
- Invoice opening
- Local PDF generation
- Local report generation
- Sync queue processing

Offline local operations should not depend on network latency.

---

# 90. UI Product Quality

The UI must:

- Avoid clipping.
- Avoid text overflow.
- Support long names.
- Support long addresses.
- Use correct keyboard types.
- Validate Indian mobile numbers.
- Validate Indian PIN codes.
- Use Indian English.
- Display currency as `₹`.
- Display dates in the approved Indian format.
- Remain understandable to non-technical Staff.

Exact layouts, components, spacing, typography, colors, navigation structure, and interaction specifications will be defined in the dedicated UI/UX document.

---

# 91. Product Navigation

## Customer

Customer navigation must support the customer journey without exposing Staff/Admin functionality.

Core destinations:

- Dashboard
- Orders
- Order details
- Profile
- New order
- Invoice

## Staff

Staff navigation must prioritize operations.

Core destinations:

- Dashboard
- Orders
- Order detail
- Walk-in order
- Payments
- Profile/session controls
- Sync/diagnostics where appropriate

## Admin

Admin bottom navigation:

```text
Home
Orders
People
Reports
More
```

People:

```text
People
├── Staff
└── Customers
```

More includes administrative configuration and app information according to SRS.

---

# 92. Customer Journey — New Order

```text
Customer Login
↓
Dashboard
↓
Place New Order
↓
Select Items
↓
Select Services
↓
Enter Quantities
↓
Select Collection Method
↓
Select Return Method
↓
Enter Pickup Information if required
↓
Review Estimated Order
↓
Submit
↓
Order Created
↓
Track Order
```

The customer receives an estimated amount, not a final invoice.

---

# 93. Customer Journey — Pickup

```text
Order Created
↓
Pickup Pending
↓
Business Collects
↓
Picked Up
↓
Processing
↓
Ready
```

The final next stage depends on return method.

---

# 94. Customer Journey — Drop-Off

```text
Order Created
↓
Customer Drops Laundry
↓
Received
↓
Processing
↓
Ready
```

The final next stage depends on return method.

---

# 95. Customer Journey — Delivery

```text
Ready
↓
Out for Delivery
↓
Delivered
```

Finalized invoice must already exist before the order reaches the final invoice-dependent customer-facing completion flow where required by the SRS.

---

# 96. Customer Journey — Self Pickup

```text
Ready for Pickup
↓
Customer Collects
↓
Collected
```

No delivery stage is used.

---

# 97. Staff Journey — Walk-In

```text
Staff Login
↓
Walk-In Order
↓
Customer Name
↓
Mobile
↓
Address
↓
Item/Service
↓
Fixed Amount
↓
Create Walk-In
↓
Staff Attribution Stored
```

No registered Customer account is created.

---

# 98. Staff Journey — Operational Order

Staff can process only the operational steps assigned to Staff by the state machine and permission contract.

Staff must not use an operational action to bypass a financial permission.

---

# 99. Staff Journey — Payment

```text
Open Order
↓
Payment
↓
Select Method
↓
Enter Amount
↓
Validate
↓
Save
↓
recordedBy = Current Staff
↓
Paid/Due Updated
```

Old payment records remain unchanged.

---

# 100. Staff Journey — Invoice

```text
Order Reaches Finalization Stage
↓
Staff Reviews Final Data
↓
If No Edit Required
    ↓
    Select/Confirm GST
    ↓
    Finalize
Else
    ↓
    Admin Required
```

Staff cannot edit financial values to make the invoice final.

---

# 101. Admin Journey — Final Invoice

```text
Open Order
↓
Review Original Order
↓
Review Received Laundry
↓
Edit Final Quantity if required
↓
Edit Final Rate if required
↓
Edit Line Items if required
↓
Add/Edit Additional Charges if required
↓
Select GST
↓
Review Final Amount
↓
Finalize
↓
Invoice Generated
↓
Invoice Locked
```

The actual UI must prevent unauthorized Staff access to these edit controls.

---

# 102. Admin Journey — Correction

```text
Open Finalized Order
↓
Check Correction Eligibility
↓
Start Correction
↓
Enter Reason
↓
Modify Authorized Financial Data
↓
Recalculate
↓
Create Revised Invoice
↓
Preserve Original
↓
Write Audit Trail
↓
Current Invoice = Revised Invoice
```

No original invoice destruction is allowed.

---

# 103. Admin Journey — Customer

```text
People
↓
Customers
↓
Search
↓
Open Customer
↓
Customer Profile
↓
Customer Orders
↓
Open Individual Order
```

Only the selected customer's data may be displayed.

---

# 104. Admin Journey — Staff

```text
People
↓
Staff
↓
Open Staff
↓
Profile
↓
Edit / Activate / Deactivate
```

Historical Staff attribution remains valid.

---

# 105. Product-Level Security Contract

Security is a product requirement, not merely a technical feature.

The product must enforce:

1. Authentication.
2. Role isolation.
3. Business isolation.
4. Customer isolation.
5. Staff permission allow-list.
6. Admin-only privileged actions.
7. Protected financial data.
8. Protected historical data.
9. Offline security boundaries.
10. Backend enforcement.

UI hiding alone is insufficient.

---

# 106. Staff Permission Summary

| Capability | Customer | Staff | Admin |
|---|---:|---:|---:|
| Register account | Yes | No | No |
| Create registered customer | No | No | No |
| Create walk-in | No | Yes | Yes |
| Edit received quantity | No | No | Yes |
| Edit final quantity | No | No | Yes |
| Edit final rate | No | No | Yes |
| Edit final lines | No | No | Yes |
| Additional charges | No | No | Yes |
| Change master prices | No | No | Yes |
| Change GST settings | No | No | Yes |
| Record payment | No | Yes | Yes |
| Edit old payment | No | No | No |
| Payment correction | No | No | Yes |
| Finalize unchanged invoice | No | Yes | Yes |
| Finalize with financial edits | No | No | Yes |
| Correct/reissue invoice | No | No | Yes |
| Customer deactivate | No | No | Yes |
| Staff management | No | No | Yes |
| Business settings | No | No | Yes |
| Reports | Own permitted data | Operational only | Yes |

This table is product-level behavior. The backend security document must implement it without weakening it.

---

# 107. Product State Separation

The product must conceptually keep these separate:

```text
Customer Account Status
        ≠
Order Status
        ≠
Payment Status
        ≠
Invoice Status
        ≠
Sync Status
        ≠
Return Status
```

Changing one state must not silently modify another unrelated state.

---

# 108. Customer Account Status

Allowed:

```text
ACTIVE
INACTIVE
```

Inactive Customer:

- Cannot create new registered orders.
- Existing records remain.

---

# 109. Operational Order Status

Order status represents physical/operational progress.

It must follow the selected collection/return flow.

---

# 110. Payment Status

Payment status represents money settlement.

It is derived from valid payment records and final amount.

---

# 111. Invoice Status

Invoice status represents invoice lifecycle.

Conceptually:

```text
NOT_FINALIZED
FINALIZED
```

A correction/reissue must be represented through the controlled invoice history rather than silently mutating a finalized invoice.

---

# 112. Sync Status

Sync status represents local/cloud synchronization.

Conceptually:

```text
ONLINE
OFFLINE
SYNCING
SYNCED
SYNC_ERROR
```

This does not replace order or payment status.

---

# 113. Return Status

Return tracking is relevant when a cancelled order still has physical laundry held by the business.

Conceptually:

```text
NOT_REQUIRED
RETURN_PENDING
RETURNED
```

---

# 114. Product Acceptance Criteria — Authentication

V1 passes when:

- Customer registration works.
- Email verification works.
- Login works.
- Forgot password works.
- Staff cannot publicly register.
- Admin can create Staff.
- Staff first-login password change works where required.
- Initial Admin setup is controlled.
- Correct role dashboard is shown.
- Inactive accounts are restricted.
- Profile completion works.

---

# 115. Product Acceptance Criteria — Customer

V1 passes when:

- Customer can create an account.
- Customer can complete profile.
- Customer can choose Personal/Business.
- Business fields appear when required.
- Customer can place an order.
- Customer can choose collection method.
- Customer can choose return method.
- Customer can track own order.
- Customer cannot see another customer's order.
- Customer cannot edit protected financial data.
- Customer sees final invoice only after finalization.
- Deactivated Customer cannot create a new order.

---

# 116. Product Acceptance Criteria — Staff

V1 passes when:

- Staff can log in.
- Staff can create walk-in orders.
- Staff cannot create registered Customers.
- Staff cannot edit received quantity.
- Staff cannot edit final quantity.
- Staff cannot edit final rate.
- Staff cannot edit additional charges.
- Staff can update permitted operational statuses.
- Staff can record payments.
- Payment records identify the Staff actor.
- Staff can finalize an invoice only when no financial/order edit is required.
- Staff can select/confirm GST ON/OFF under the allowed finalization rule.
- Staff cannot correct/reissue invoices.
- Staff cannot deactivate Customers.
- Staff can operate offline for permitted actions.

---

# 117. Product Acceptance Criteria — Admin

V1 passes when:

- Admin dashboard works.
- Staff list works.
- Staff profile works.
- Staff activation/deactivation works.
- Customer list works.
- Business/personal display works.
- Customer detail isolation works.
- Customer-specific orders work.
- Customer activation/deactivation works.
- Items work.
- Services work.
- Prices work.
- Business Settings work.
- GST Settings work.
- Invoice Settings work.
- Order management works.
- Reports work.
- Financial correction rules work.

---

# 118. Product Acceptance Criteria — Order Lifecycle

All four normal collection/return combinations work.

The product must:

- Show correct status.
- Show only valid next actions.
- Reject invalid transitions.
- Prevent final-state reopening.
- Preserve status history.
- Preserve cancellation information.
- Track physical return where required.

---

# 119. Product Acceptance Criteria — Received and Finalization

The product passes when:

- Original quantity remains unchanged.
- Received quantity can differ.
- Received quantity is Admin-controlled.
- Final quantity can be set by Admin.
- Final rate can be set by Admin.
- Additional charges can be added by Admin.
- GST can be ON/OFF at finalization.
- Staff can finalize unchanged data.
- Staff cannot perform prohibited financial edits.
- Final amount is correct.
- Invoice finalization is atomic.
- Final invoice becomes protected.
- Customer sees invoice after finalization.
- Historical data remains intact.

---

# 120. Product Acceptance Criteria — Advance Payment

Example:

```text
Final Amount = ₹1,200
Advance = ₹300
```

Before finalization:

```text
Payment = ₹300
Invoice access = Locked
```

After finalization:

```text
Paid = ₹300
Due = ₹900
```

The advance must automatically count toward the finalized invoice.

---

# 121. Product Acceptance Criteria — Payment

The product passes when:

- Multiple payments work.
- Cash works.
- UPI works.
- Online recording works where allowed.
- Paid amount is correct.
- Due amount is correct.
- Payment status is correct.
- Overpayment is rejected.
- Zero-value invalid payment records are rejected.
- Payment history is append-only.
- Payment actor is retained.
- Order and payment statuses remain independent.

---

# 122. Product Acceptance Criteria — Invoice Correction

The product passes when:

- Finalized invoice cannot be edited by Staff.
- Finalized invoice cannot be edited by Customer.
- Admin correction requires authorization.
- Admin correction requires a reason.
- Original invoice remains preserved.
- Revised invoice is generated according to the correction contract.
- Customer sees the current valid invoice.
- Authorized Staff/Admin see the current valid invoice.
- Audit history preserves the original.
- Seven-day lock is enforced.
- Due-pending correction eligibility follows the SRS rule.

---

# 123. Product Acceptance Criteria — People

Staff:

- Admin can open a Staff profile.
- Admin can edit Staff profile.
- Admin can activate/deactivate Staff.
- History remains intact.

Customers:

- Admin can search customers.
- Business customers show name + business.
- Personal customers show name + Personal.
- Admin can open one Customer.
- Only that customer's data is shown.
- Only that customer's orders are shown.
- Admin can activate/deactivate Customer.
- Historical records remain.

---

# 124. Product Acceptance Criteria — Reports

Reports pass when:

- Weekly report works.
- Monthly report works.
- Yearly report works.
- Finalized data is used.
- Historical rates remain correct.
- Historical GST remains correct.
- Additional charges are included.
- Paid/due values are correct.
- Payment-method totals are correct.
- Item/service performance is correct.
- Cancelled orders follow the approved policy.
- XLSX export matches report data.

---

# 125. Product Acceptance Criteria — Offline

The product passes when:

- Permitted offline order operations work.
- Permitted offline walk-in operations work.
- Permitted offline payment operations work.
- Permitted offline status operations work.
- Local committed data survives restart.
- Pending operations are visible.
- Reconnection triggers synchronization.
- Failed synchronization can retry.
- Duplicate retry does not duplicate business data.
- Financial conflicts are not silently auto-merged.

---

# 126. Product Acceptance Criteria — Security

The product passes when:

- Customer cannot access Staff/Admin functions.
- Staff cannot access Admin functions.
- Staff cannot create registered Customers.
- Staff cannot edit protected financial data.
- Staff cannot deactivate Customers.
- Customer cannot see another Customer.
- Users cannot access another business.
- Client-side role manipulation fails.
- Backend rules enforce the same permissions.
- Admin creation is controlled.
- Production secrets are not shipped in the app.
- Sensitive information is absent from logs.

---

# 127. Product Acceptance Criteria — Historical Integrity

The product passes when:

- Current customer changes do not change old orders.
- Current prices do not change old invoices.
- Current GST settings do not change old invoices.
- Current business settings do not change old invoices.
- Old payments remain traceable.
- Old status history remains traceable.
- Original invoices remain preserved through corrections.
- Reports reproduce historical values correctly.

---

# 128. Product Observability Requirements

Production operation must support:

- Crash reporting
- Non-sensitive error logging
- Sync diagnostics
- Cloud Function logs
- Performance monitoring
- Critical-operation monitoring

Logs must not contain:

- Customer personal data
- Payment details beyond approved non-sensitive diagnostics
- Full addresses
- Authentication credentials
- Tokens
- Secrets
- Service-account credentials
- Raw sensitive payloads

---

# 129. Backup and Recovery Product Requirement

Production business data must have:

- Automated backups
- Defined retention
- Restore procedure
- Accidental-deletion recovery
- Disaster recovery process
- Recovery validation

Backup/recovery is a production-system requirement even though the end user does not interact with it directly.

---

# 130. Environment Separation

The product must have separate environments:

```text
Development
↓
Staging/Test
↓
Production
```

Development and testing data must not be placed into the Production Firebase project.

The mobile application's environment configuration must select the correct Firebase project.

Production credentials must never be embedded in source control or shared development configuration.

---

# 131. Production Data Safety

Production release must not occur until:

- Production Firebase project is verified.
- Security Rules are verified.
- Cloud Functions are verified.
- Backup schedule is verified.
- Restore procedure has been tested.
- Monitoring is active.
- Production configuration is validated.

---

# 132. Product Accessibility and Usability

The product must be usable by non-technical Staff.

Requirements:

- Clear labels.
- Clear status names.
- Clear action buttons.
- No unnecessary technical terminology.
- No ambiguous financial wording.
- Confirmation before destructive-looking operations.
- Clear validation messages.
- Appropriate keyboards.
- Accessible touch targets.
- No important information hidden behind confusing interaction patterns.

Exact UI accessibility standards will be defined in UI/UX documentation.

---

# 133. Product Localization

V1 uses:

- Indian English
- Indian currency formatting
- Indian date presentation

Currency:

```text
₹
```

Date presentation:

```text
14 September 2026
```

Stored timestamps and backend time rules follow the technical contract.

---

# 134. Product Data Boundaries

The following must remain distinct:

```text
Registered Customer
Walk-In Customer Details
Staff
Admin
Business
Business Settings
Item
Service
Price
Order
Order Item
Received Data
Final Invoice
Additional Charge
Payment
Status History
Return Record
Sync Operation
Audit Record
```

Technical schemas will define exact storage structures.

---

# 135. Product Lifecycle — Complete

The normal registered Customer lifecycle is:

```text
Customer
↓
Order Created
↓
Collection
↓
Laundry Received
↓
Processing
↓
Received Verification
↓
Final Invoice Preparation
↓
Invoice Finalization
↓
Invoice Visible
↓
Ready
↓
Delivery / Customer Pickup
↓
Delivered / Collected
↓
Payment Settlement
↓
Historical Record
```

Advance payment may occur before final invoice.

Cancellation may terminate the operational flow before completion.

---

# 136. Product Lifecycle — Financial

```text
Estimated Order
↓
Optional Advance Payment
↓
Actual Received Data
↓
Final Quantity/Rate/Charges
↓
GST Decision
↓
Final Invoice
↓
Advance Applied
↓
Remaining Due
↓
Additional Payments
↓
Paid
```

The estimate must never overwrite the final financial record.

---

# 137. Product Lifecycle — Correction

```text
Finalized
↓
Correction Eligible
↓
Admin Reason
↓
Original Preserved
↓
Revision
↓
Current Invoice
↓
Audit History
```

If correction is no longer eligible, the UI and backend must reject the operation.

---

# 138. Product Lifecycle — Cancellation

```text
Active Order
↓
Cancellation Allowed?
    ├── NO → Reject
    └── YES
          ↓
       Cancel
          ↓
Physical Laundry Held?
    ├── NO → End
    └── YES
          ↓
     Return Pending
          ↓
        Returned
```

Cancellation never deletes the order.

---

# 139. Product Lifecycle — Walk-In

```text
Staff
↓
Walk-In
↓
Customer Details
↓
Laundry Details
↓
Fixed Amount
↓
Walk-In Order Created
↓
Operational Completion
↓
Customer Collection
↓
Payment / Settlement
```

No registered Customer account is required.

---

# 140. Product Rules for Current vs Historical Data

| Current Data | Historical Behavior |
|---|---|
| Customer profile | Old order snapshot remains |
| Price | Old order price remains |
| GST setting | Old invoice GST remains |
| Business settings | Old invoice business snapshot remains |
| Staff profile | Historical actor reference remains |
| Payment ledger | Old entries remain |
| Status | Old history remains |
| Invoice | Original remains through correction |

---

# 141. Product Security Invariants

The following must never happen:

1. Customer accesses another Customer.
2. Staff creates a registered Customer.
3. Staff edits received quantity.
4. Staff edits final quantity.
5. Staff edits final rate.
6. Staff edits additional charges.
7. Staff changes master pricing.
8. Staff changes GST settings.
9. Staff changes Business Settings.
10. Staff corrects/reissues an invoice.
11. Customer edits final financial data.
12. User accesses another business.
13. User changes their role.
14. User changes their business ID.
15. Invoice appears before finalization.
16. Payment exceeds valid settlement.
17. Historical invoice silently changes.
18. Historical price silently changes.
19. Historical GST silently changes.
20. Historical business identity silently changes.
21. Offline mode grants a new privilege.
22. Sync retry creates duplicate financial data.
23. Sensitive information is written to logs.
24. Production data is mixed with development/test data.
25. Original invoice is destroyed during correction.

---

# 142. Product Financial Invariants

1. Money uses exact financial representation.
2. Final amount is calculated from final billing data.
3. Additional charges are included in final financial data.
4. GST is calculated from the approved GST rule.
5. Payment records are append-only.
6. Overpayment is rejected.
7. Due cannot become a silent negative value.
8. Advance payments remain linked to the order.
9. Advance payments are automatically reflected after finalization.
10. Finalized financial data is protected.
11. Historical financial data is not rewritten by current settings.
12. Financial corrections require Admin authority.

---

# 143. Product Offline Invariants

1. A committed local operation must not disappear silently.
2. Restart must not erase committed pending work.
3. Retry must not duplicate the operation.
4. Offline operation must respect current known authorization boundaries.
5. Offline operation must not grant new privileges.
6. Financial conflicts must not be auto-merged.
7. Business isolation must remain enforced after synchronization.

---

# 144. Product Quality Gates

Before production release:

## Product

- All P0 requirements implemented.
- All P1 requirements implemented.
- P2 requirements implemented or explicitly accepted as deferred.

## Security

- Permission matrix verified.
- Cross-business isolation verified.
- Customer isolation verified.
- Admin-only actions verified.
- Offline privilege boundary verified.

## Financial

- Invoice calculations verified.
- GST verified.
- Payment reconciliation verified.
- Advance payment verified.
- Overpayment prevention verified.
- Correction/reissue verified.

## Operational

- Four collection/return flows verified.
- Walk-in verified.
- Cancellation verified.
- Physical return tracking verified.
- People section verified.

## Data

- Historical snapshots verified.
- Payment history verified.
- Status history verified.
- Audit data verified.

## Offline

- Offline create/update verified.
- Restart recovery verified.
- Sync retry verified.
- Duplicate prevention verified.
- Conflict handling verified.

---

# 145. Product Release Definition

The product is not considered production-ready merely because screens exist.

Production-ready means:

```text
Product Requirements
+
Technical Design
+
Backend/Data Contract
+
Security Rules
+
Domain Logic
+
Offline Sync
+
UI/UX
+
Testing
+
Backup/Recovery
+
Observability
+
Real Device Validation
```

all pass their required gates.

---

# 146. Product Priority Model

## P0 — Must Never Fail

- Authentication security
- Role isolation
- Business isolation
- Customer isolation
- Historical financial integrity
- Invoice finalization integrity
- Payment integrity
- No overpayment
- Offline preservation
- Duplicate prevention
- Protected-field enforcement

## P1 — Core V1

- Customer registration
- Customer orders
- Walk-in
- Pickup
- Drop-off
- Delivery
- Customer pickup
- Processing
- Received verification
- Finalization
- Invoice
- Payments
- Due
- Staff
- Customers
- Items
- Services
- Prices
- GST
- Reports
- Sync

## P2 — Supporting V1

- Notifications
- Sync diagnostics
- Additional dashboard summaries
- Optional invoice presentation enhancements

## Future

- Admin Web
- Online gateway
- WhatsApp/SMS
- Storage/photos
- GPS
- Route optimization
- QR/barcode
- Multi-business
- Multi-branch
- Loyalty/coupons
- Advanced analytics/accounting

---

# 147. Requirements Traceability to SRS V5.0

This PRD is derived from the following SRS V5.0 areas:

| PRD Area | SRS V5.0 Source |
|---|---|
| Product purpose/scope | Sections 1–4 |
| Technology/product architecture boundary | Sections 5–8 |
| Authentication | Section 10 |
| Initial Admin | Section 11 |
| Business profile | Section 12 |
| Customer | Sections 13–15 |
| Staff | Sections 16–17 and binding Section 218 |
| People | Sections 18–21 and binding Section 265 |
| Items/services/prices | Sections 23–26 |
| Order source | Section 27 and binding Section 219 |
| Collection | Section 28 |
| Return | Section 29 |
| Order flows | Section 30 and binding Section 220 |
| Status | Sections 31–33 and binding Section 220 |
| Customer order creation | Section 34 |
| Staff/Admin order creation | Section 35 and binding Section 219 |
| Original order data | Section 36 |
| Received laundry | Sections 37–38 and binding Section 222 |
| Invoice finalization | Sections 39–50 and binding Section 223 |
| Payments | Sections 51–55 and binding Sections 224–226 |
| Order details/history | Sections 56–58 |
| Notifications | Sections 59–60 and binding Section 245 |
| Reports | Sections 61–68 and binding Section 243 |
| Dashboards | Section 69 |
| Business/invoice settings | Sections 70–72 and binding Section 241 |
| Data model boundary | Sections 73–88 and binding Section 255 |
| Sync | Sections 88–91 and binding Section 230 |
| Security | Sections 97–105 and binding Section 231 |
| Cancellation | Section 110 and binding Section 221 |
| Audit | Section 111 and binding Section 228 |
| Search/filter | Sections 112–114 and binding Section 239 |
| Network/session | Sections 115–120 |
| UX/navigation | Sections 125–145 and binding Section 252 |
| Walk-in | Sections 153–154 and binding Section 219 |
| Privacy | Sections 193–196 and binding Sections 235–236 |
| Production operations | Sections 147–152 and binding Sections 232–234 |
| Testing | Sections 159–163 and binding Sections 257–267 |
| Release gates | Sections 213–214 and binding Sections 267–270 |
| AI/developer control | Binding Section 271 |

---

# 148. Downstream Document Contract

This PRD is intentionally the first product-level document after SRS V5.0.

The next documents must be produced in this order:

```text
SRS V5.0
    ↓
PRD V1.0
    ↓
TRD
    ↓
UI/UX Specification
    ↓
Backend Schema + Security Contract
    ↓
Test Matrix / Implementation Plan
    ↓
Code
```

Each downstream document must derive its requirements from the documents above it.

---

# 149. TRD Responsibility

The future TRD must define implementation architecture without changing product behavior.

It must cover, at minimum:

- Expo/React Native architecture
- TypeScript structure
- Domain layer
- Repository layer
- SQLite adapter
- Firebase integration
- Cloud Functions
- Authentication/session architecture
- Offline sync engine
- Idempotency
- Concurrency/versioning
- Error handling
- Observability
- Environment configuration
- Build/release architecture
- Backup/recovery implementation
- Testing architecture

---

# 150. UI/UX Responsibility

The future UI/UX document must define:

- Screen inventory
- Navigation
- Route protection
- Layout
- Components
- States
- Empty states
- Loading states
- Offline states
- Error states
- Confirmation dialogs
- Form behavior
- Validation presentation
- Role-specific UI
- Staff workflow optimization
- Customer workflow
- Admin People workflow
- Invoice screens
- Payment screens
- Reports
- Accessibility
- Responsive behavior

It must not invent new product permissions.

---

# 151. Backend Schema Responsibility

The future backend/schema document must define:

- Firestore collections
- Document structures
- SQLite tables
- Field types
- Required/nullable fields
- Immutable fields
- Indexes
- Relationships
- Business scoping
- Version fields
- Audit metadata
- Invoice revision relationships
- Payment ledger
- Sync queue
- Idempotency
- Security Rules
- Cloud Functions
- Transaction boundaries

It must implement the product rules defined here and in SRS V5.0.

---

# 152. AI Agent Execution Rule

AI coding agents are implementation tools.

They may:

- Write code.
- Refactor code without changing behavior.
- Implement approved schemas.
- Implement approved screens.
- Implement approved tests.
- Fix implementation defects.

They may not decide:

- What Staff should be allowed to do.
- What Admin should be allowed to do.
- Whether a payment should be accepted.
- Whether an invoice should be editable.
- Whether a Customer should be deactivated.
- Whether a status transition should exist.
- Whether an old invoice should be changed.
- Whether a financial conflict should be auto-merged.

Those decisions are already specified.

---

# 153. Specification Blocker Rule

If the PRD, SRS, or downstream approved contract does not define a required behavior:

```text
Do not guess.
Do not invent.
Do not silently implement.
```

Instead:

```text
SPECIFICATION BLOCKER
↓
Affected feature
↓
Missing decision
↓
Required owner decision
↓
Documentation update
↓
Implementation
```

---

# 154. Product Owner Control

The business owner retains control over:

- Product behavior
- Business rules
- Role permissions
- Financial rules
- Cancellation policy
- Invoice correction policy
- Pricing policy
- GST policy
- Customer policy
- Operational workflows

AI agents and developers do not own these decisions.

---

# 155. Final Product Contract

The product must behave as follows:

```text
CUSTOMER
    ↓
Self-register
    ↓
Create normal laundry order
    ↓
Choose collection
    ↓
Choose return
    ↓
Track order
    ↓
Receive finalized invoice
    ↓
Pay / view due
```

```text
STAFF
    ↓
Login
    ↓
Operational work
    ↓
Create WALK-IN only
    ↓
Process allowed statuses
    ↓
Record payments
    ↓
Finalize only unchanged invoices
```

```text
ADMIN
    ↓
Full controlled business management
    ↓
People
    ├── Staff
    └── Customers
    ↓
Master data
    ↓
Orders
    ↓
Received verification
    ↓
Financial editing
    ↓
Invoice
    ↓
Payments
    ↓
Correction
    ↓
Reports
```

---

# 156. Final Product Principles

The application must always follow these principles:

1. Simple is preferred over unnecessary complexity.
2. Offline-first is mandatory for core permitted operations.
3. Security is enforced by the backend, not only the UI.
4. Staff permissions are allow-listed.
5. Financial control remains with Admin where defined.
6. Customer self-service remains isolated.
7. Walk-in operations remain separate from registered Customer accounts.
8. Original order information is preserved.
9. Actual received information is preserved.
10. Final invoice information is preserved.
11. Payment history is preserved.
12. Historical records are immutable except through controlled correction.
13. Current settings affect future operations only.
14. Reports use final authoritative data.
15. No silent financial changes are allowed.
16. No duplicate financial operations are allowed.
17. No cross-business access is allowed.
18. No privilege escalation is allowed.
19. No AI/developer may invent business rules.
20. A missing requirement is a Specification Blocker.

---

# 157. Final Product Definition of Done

The Product Requirements are considered implemented only when all applicable requirements have:

```text
Implemented
+
Correctly persisted
+
Correctly authorized
+
Correctly synchronized
+
Correctly displayed
+
Correctly tested
+
Historically preserved
```

The application is considered production-ready only after the full engineering and release gates defined by SRS V5.0 and the downstream technical documents pass.

---

# 158. Final Authority Statement

This PRD is a product-level contract derived from:

**TREAT HOSPITALITY SERVICES Laundry App — Production Master SRS V5.0**

The PRD does not replace SRS V5.0.

The SRS remains the authoritative parent specification.

Where this PRD summarizes a rule, the exact binding rule remains governed by SRS V5.0.

Where the PRD and SRS appear inconsistent, implementation must stop and the conflict must be resolved before coding.

No AI agent or developer may use implementation convenience as a reason to weaken:

- Security
- Permission boundaries
- Financial integrity
- Historical integrity
- Offline integrity
- Customer isolation
- Business isolation

---

# 159. Final Product Statement

**TREAT HOSPITALITY SERVICES Laundry Management App V1** is an offline-first Android laundry operations product serving registered personal customers, registered business customers, and walk-in customers.

It provides controlled:

- Customer ordering
- Walk-in operations
- Laundry collection
- Customer drop-off
- Processing
- Received-laundry verification
- Final invoice preparation
- GST
- Additional charges
- Payment recording
- Due tracking
- Delivery
- Customer pickup
- Staff operations
- Admin management
- People management
- Reporting
- Offline synchronization
- Historical financial preservation

The product deliberately remains a focused laundry operating system rather than an ERP.

**End of PRD V1.0**
