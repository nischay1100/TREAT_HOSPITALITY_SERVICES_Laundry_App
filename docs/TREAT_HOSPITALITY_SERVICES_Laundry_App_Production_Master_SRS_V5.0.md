# TREAT HOSPITALITY SERVICES --- Laundry Management App

## Complete Software Requirements Specification (SRS) --- Production Master V5.0

**Document status:** Production Master / Binding Engineering Contract
**Supersedes:** V4.0 for all conflicting requirements
**Authority rule:** Sections 217 onward are binding clarifications and final decisions. Where any earlier V4.0 wording conflicts with these sections, V5.0 controls. Where V5.0 does not change a V4.0 requirement, the V4.0 requirement remains in force.

**Document status:** Final consolidated product specification\
**Product:** Laundry Management App\
**Target business:** TREAT HOSPITALITY SERVICES\
**Application scope:** Laundry services only\
**Primary platform:** Android mobile application\
**Future platform:** Admin Web Application\
**Architecture:** Offline-first\
**Mobile stack:** Expo + React Native + TypeScript + Expo Router\
**Local database:** SQLite / Expo SQLite\
**Cloud backend:** Firebase Authentication + Cloud Firestore + Cloud
Functions\
**Notifications:** Firebase Cloud Messaging (FCM), optional for V1 but
designed in\
**Local documents:** A4 PDF invoices and XLSX reports\
**V1 business model:** One laundry business, serving B2B, B2C, and
walk-in customers\
**Primary roles:** ADMIN, STAFF, CUSTOMER

------------------------------------------------------------------------

# 1. Purpose

The Laundry Management App is an Android-first operational application
for **TREAT HOSPITALITY SERVICES**.

TREAT HOSPITALITY SERVICES may provide multiple hospitality-related
services as a business, but this application is intentionally limited to
the **laundry service operation**.

The application must support:

-   Laundry orders from registered customers.
-   Personal customers.
-   Business customers.
-   Walk-in customers.
-   Phone/manual orders created by staff.
-   Laundry collection by the business.
-   Customer drop-off at the laundry.
-   Delivery by the business.
-   Customer self-collection.
-   Processing and received-laundry verification.
-   Final invoice preparation after actual laundry is received and
    processed.
-   Editable final quantities and prices before invoice finalization.
-   Additional charges with amount and explanatory note.
-   GST or non-GST selection per order at finalization.
-   Payment collection and payment history.
-   Customer order tracking.
-   Staff operational management.
-   Admin management of staff, customers, items, services, prices,
    orders, GST and business settings.
-   Weekly, monthly and yearly reports.
-   Offline operation for core business workflows.
-   Automatic synchronization with Firebase when connectivity returns.
-   Reliable preservation of original, received, final, invoice and
    payment history.

The application must remain a **simple laundry operating system**, not a
general ERP.

------------------------------------------------------------------------

# 2. Business Scope

## 2.1 Business Identity

The legal/business identity represented by the application is:

**TREAT HOSPITALITY SERVICES**

The business provides laundry services for:

-   Hotels
-   Resorts
-   B&Bs
-   Guest Houses
-   Local/personal customers
-   Walk-in customers

The application must display and operate only the laundry-service side
of the business.

## 2.2 Application Scope

The V1 application is for:

> Laundry Services only.

Other hospitality services offered by TREAT HOSPITALITY SERVICES must
not be added as application modules merely because they exist in the
wider business.

## 2.3 Customer Segments

The system supports:

1.  Personal customers.
2.  Business customers.
3.  Walk-in customers.

Business customers may include:

-   Hotel
-   Resort
-   B&B
-   Guest House
-   Other business

The system should not require a separate Business User account in V1.

------------------------------------------------------------------------

# 3. Product Goals

The product must prioritize:

1.  FAST
2.  SIMPLE
3.  OFFLINE
4.  SECURE
5.  ACCURATE
6.  EASY TO USE

The application should minimize taps for staff while preserving complete
historical records.

It must work well for non-technical staff.

------------------------------------------------------------------------

# 4. Non-Goals / V1 Exclusions

V1 must not become an ERP.

The following are excluded from V1:

-   Super Admin
-   Multi-business SaaS UI
-   Multi-branch UI
-   Multiple processing centres
-   Separate delivery application
-   Separate manager application
-   Inventory management
-   Salary
-   Payroll
-   Expense management
-   Advanced accounting
-   GPS route optimization
-   Garment QR tracking
-   Barcode tracking
-   POS hardware integration
-   Printer integration
-   Loyalty system
-   Coupons
-   Advanced CRM
-   AI features
-   Mandatory online payment gateway
-   Mandatory WhatsApp/SMS infrastructure
-   Customer-to-multiple-business association management
-   Complex accounting ledger
-   Full fleet management

These may be considered later without changing the core data
architecture.

------------------------------------------------------------------------

# 5. Technology Stack

## 5.1 Android

-   Expo
-   React Native
-   TypeScript
-   Expo Router
-   Expo SQLite
-   Firebase Authentication
-   Cloud Firestore
-   Firebase Cloud Functions where privileged operations are required
-   Firebase Cloud Messaging where enabled

Use the current stable Expo SDK supported at implementation time and
lock dependency versions.

## 5.2 Cloud

Firebase is the cloud backend.

Use:

-   Firebase Authentication
-   Cloud Firestore
-   Cloud Functions
-   Firebase Cloud Messaging

Firestore is the long-term cloud/master business record store.

## 5.3 Firebase Client Configuration

Expo client configuration may use public Firebase Web App configuration
values such as:

``` text
EXPO_PUBLIC_FIREBASE_API_KEY
EXPO_PUBLIC_FIREBASE_AUTH_DOMAIN
EXPO_PUBLIC_FIREBASE_PROJECT_ID
EXPO_PUBLIC_FIREBASE_MESSAGING_SENDER_ID
EXPO_PUBLIC_FIREBASE_APP_ID
```

These values are application configuration, not Firebase Admin
credentials.

Firebase Admin SDK service-account credentials/private keys must never
be included in:

-   Android source
-   APK
-   React Native bundle
-   Git repository
-   public configuration
-   `.env` committed to source control

------------------------------------------------------------------------

# 6. High-Level Architecture

``` text
                         ANDROID APP
                  Expo + React Native + TS
                           |
             +-------------+-------------+
             |                           |
          SQLite                     Firebase
       Local Database              Authentication
             |                     Firestore
             |                     Cloud Functions
             |                     FCM
         Sync Queue
             |
             +---------- Internet --------> Firebase Cloud
```

Future:

``` text
Android App --------+
                    |
                    +---- Firebase Backend
                    |
Admin Web ---------+
```

The future Admin Web must use the same Firebase backend and must not
require a separate database.

------------------------------------------------------------------------

# 7. Offline-First Architecture

SQLite is the local operational database.

For core operational actions:

``` text
User Action
   ↓
Validate
   ↓
SQLite Transaction
   ↓
Immediate UI Update
   ↓
Sync Queue
   ↓
Internet Available
   ↓
Firebase
```

The UI must not unnecessarily wait for Firebase before showing a
successfully committed local operation.

SQLite is the local source of truth for active mobile workflows.

Firestore is the cloud/master business record.

------------------------------------------------------------------------

# 8. Data Ownership Model

## 8.1 SQLite

SQLite stores local operational/cache copies of:

-   User/profile data
-   Staff
-   Customers
-   Items
-   Services
-   Prices
-   Orders
-   Order items
-   Received/final order details
-   Additional charges
-   Payments
-   Status history
-   Business settings
-   Sync queue
-   Application metadata
-   Notification tokens where required

SQLite enables:

-   Offline search
-   Fast dashboard
-   Fast customer lookup
-   Fast order lookup
-   Local invoice generation
-   Local report generation
-   Offline operational work

## 8.2 Firestore

Firestore stores:

-   Long-term business data
-   Central business records
-   Cross-device data
-   Cloud backup
-   Future Admin Web data
-   Auth-linked user/application profiles

## 8.3 Local PDF/XLSX

Generated invoices and reports are local files in V1.

Firebase Storage is not required for V1 documents.

------------------------------------------------------------------------

# 9. Roles

Exactly three application roles exist:

``` text
CUSTOMER
STAFF
ADMIN
```

No client may self-assign ADMIN or STAFF.

------------------------------------------------------------------------

# 10. Authentication

## 10.1 Customer Authentication

Customer registration uses:

``` text
Email + Password
```

V1 does not require:

-   Phone OTP
-   OTP on every login

Email verification is required.

## 10.2 Customer Registration Flow

``` text
Open App
 ↓
Register
 ↓
Email
Password
Confirm Password
 ↓
Firebase Authentication Account
 ↓
Verification Email
 ↓
Email Verified
 ↓
Complete Profile
 ↓
Customer Dashboard
```

## 10.3 Password

Recommended minimum:

-   8 characters

The exact Firebase/application password policy must be enforced
consistently.

Passwords must never be stored as plaintext in Firestore or SQLite.

## 10.4 Email Verification

The system must check Firebase Authentication's `emailVerified`.

An unverified customer must not receive full customer functionality.

If verification is incomplete, the user remains in the verification
flow.

## 10.5 Phone Verification

V1 does not cryptographically verify phone numbers.

Therefore:

``` text
emailVerified = true
phoneVerified = not required
```

The mobile number is contact information, not an authentication
identity.

## 10.6 Login

``` text
Email + Password
 ↓
Firebase Authentication
 ↓
Authenticated UID
 ↓
Trusted User Profile
 ↓
Role + Business
 ↓
Correct Dashboard
```

Role routing:

``` text
CUSTOMER → Customer Panel
STAFF    → Staff Panel
ADMIN    → Admin Panel
```

UI hiding alone is never security.

## 10.7 Forgot Password

Use Firebase's password-reset flow.

``` text
Forgot Password
 ↓
Enter Email
 ↓
Password Reset Email
 ↓
Secure Reset Link
 ↓
New Password
 ↓
Login
```

Do not build a custom OTP reset system in V1.

------------------------------------------------------------------------

# 11. Initial Admin Setup

Admin must not be created through public registration.

The first Admin must be created through a **controlled setup process**.

Conceptual setup:

``` text
Controlled Project Setup
 ↓
Create Firebase Authentication Admin Account
 ↓
Create trusted application user profile
 ↓
role = ADMIN
businessId = configured business ID
 ↓
Create business identity/settings
 ↓
Admin Login
 ↓
Business Settings
 ↓
Complete business profile
```

The setup mechanism must not expose a client-side way to promote an
account to ADMIN.

The application must never allow:

``` text
Customer → Admin
Staff → Admin
Client → Admin
```

through a normal mobile request.

------------------------------------------------------------------------

# 12. Admin Business Profile

The first Admin is responsible for completing business settings.

Recommended fields:

-   Business Name
-   Business Type
-   Primary Mobile Number
-   Alternative Mobile Number
-   Email
-   Address
-   PIN Code
-   GSTIN
-   Default GST Rate
-   Invoice Prefix
-   Optional WhatsApp Number
-   Optional business logo reference for future use

`Alternative Mobile Number` is a deliberate addition to the original SRS
because the business requirement explicitly needs an alternative contact
number.

The business profile is central application data.

Business information may appear in:

-   Dashboard/context where appropriate
-   Orders
-   Invoice
-   Reports
-   Customer-facing information where appropriate
-   Business settings

Changing current business settings must never rewrite historical
invoice/order snapshots.

------------------------------------------------------------------------

# 13. Customer Role

## 13.1 Customer Capabilities

Customer can:

-   Register
-   Verify email
-   Complete profile
-   Edit permitted own profile fields
-   Change password
-   Reset password
-   Place laundry order
-   See estimated order information
-   See own orders
-   Track own order status
-   See final invoice when finalized
-   See permitted payment information
-   See order history

Customer cannot:

-   Access Staff panel
-   Access Admin panel
-   Create Staff
-   Create Admin
-   Change role
-   Change businessId
-   View another customer
-   View another customer's orders
-   Modify protected financial fields
-   Modify payment history
-   Modify status history
-   Modify final invoice after finalization
-   Modify GST settings
-   Modify pricing

------------------------------------------------------------------------

# 14. Customer Profile

Fields:

``` text
customerId
userId
name
email
phone
customerType
businessName
businessType
businessAddress
businessPhone
address
pinCode
status
profileCompleted
createdAt
updatedAt
```

## 14.1 Customer Type

``` text
PERSONAL
BUSINESS
```

Default:

``` text
PERSONAL
```

## 14.2 Personal Customer

Example:

``` text
Rahul Sharma
Personal
98XXXXXXXX
Address...
```

Business fields should be null/not applicable.

## 14.3 Business Customer

Example:

``` text
Rahul Sharma
Hotel Paradise
Business
98XXXXXXXX
```

Business fields:

-   Business Name
-   Business Type
-   Business Address, if collected
-   Business Phone, if collected

Business Type may include:

-   HOTEL
-   RESORT
-   B_AND_B
-   GUEST_HOUSE
-   OTHER

The final UI may use human-readable labels.

## 14.4 Business-to-Person Display

For Admin customer list:

Business customer:

``` text
Rahul Sharma
Hotel Paradise
```

Personal customer:

``` text
Rahul Sharma
Personal
```

This is a required usability behavior.

## 14.5 Switching Business to Personal

When changing:

``` text
BUSINESS → PERSONAL
```

the app must confirm before clearing business-specific fields.

Historical order snapshots remain unchanged.

------------------------------------------------------------------------

# 15. Customer Deactivation

Customer status:

``` text
ACTIVE
INACTIVE
```

Admin can:

-   Activate
-   Deactivate
-   Reactivate

When a customer is deactivated:

-   The customer must not be allowed to create new laundry orders.
-   Existing orders remain.
-   Existing invoices remain.
-   Existing payment records remain.
-   Historical records remain in reports.
-   The customer record remains available to Admin.
-   Admin may reactivate the customer later.

Deactivation is not deletion.

Existing operational orders should continue to be manageable by
authorized Staff/Admin.

The exact authentication blocking mechanism must also prevent an
inactive account from bypassing the restriction through direct API
calls.

------------------------------------------------------------------------

# 16. Staff Role

Staff is an operational role. Staff permissions are intentionally narrow
and are defined as an allow-list below. The client UI must never be used
as the security boundary; the same permissions must be enforced by trusted
backend authorization.

Staff can:

-   Login.
-   View the Staff dashboard.
-   Search and filter orders belonging to the Staff's business.
-   Create **walk-in orders only**.
-   Process operational steps of eligible orders.
-   Update only the statuses explicitly assigned to Staff in the order
    state machine.
-   Record a payment received from a customer.
-   View the payment ledger for operational purposes.
-   View finalized invoices.
-   Generate/finalize an invoice **only when no financial/order edits are
    required**, subject to the exact V5.0 invoice-finalization rule.
-   Work offline for permitted operations.
-   Synchronize pending data.

Staff cannot:

-   Publicly register.
-   Create Admin.
-   Create or manage another Staff account.
-   Change role or businessId.
-   Create or edit registered Customer profiles.
-   Deactivate/reactivate Customers.
-   Create a new registered Customer account.
-   Edit Received Quantity.
-   Edit Final/Billed Quantity.
-   Edit Final Rate/Price.
-   Add, remove, or edit billable line items.
-   Add or edit Additional Charges.
-   Change master pricing.
-   Change GST settings.
-   Change Business Settings.
-   Edit a finalized invoice.
-   Correct/reissue an invoice.
-   Delete or rewrite historical payments.
-   Delete or rewrite status history.
-   Delete protected financial records.
-   Access another business.
-   Bypass allowed status transitions.

### 16.1 Staff Walk-In Order

A Staff-created V1 order must be a **WALK_IN** order.

Staff collects and stores:

-   Customer Name
-   Mobile Number
-   Address
-   Selected item/service information
-   Fixed amount at the time the walk-in order is created
-   Staff identity (`createdBy`)

A walk-in order does not require Customer app registration and does not
create a registered Customer account.

The walk-in amount is fixed at creation. It is not an estimated customer
app order and does not enter the normal received-laundry verification
workflow.

The customer may return later to make payment/collect the completed
walk-in service. The payment ledger records the payment and the Staff
member who entered it.

### 16.2 Staff Payment Recording

When Staff receives money:

1. Staff opens the eligible order.
2. Staff selects payment method.
3. Staff enters amount.
4. System validates that the amount does not cause overpayment.
5. System records an append-only payment.
6. `recordedBy` identifies the Staff account.
7. Paid/Due/Payment Status are recalculated.
8. The operation is synchronized when online.

Staff must never edit an old payment. A correction, if needed, follows
the Admin-only controlled correction policy.

### 16.3 Staff Invoice Finalization

Staff may generate/finalize an invoice only when:

-   The order has reached the invoice-finalization stage.
-   No Received Quantity change is required.
-   No Final/Billed Quantity change is required.
-   No Final Rate/Price change is required.
-   No item/service line change is required.
-   No Additional Charge needs to be added, removed, or changed.
-   Staff only needs to select/confirm GST ON or OFF according to the
    permitted order rules.
-   All other final invoice data is already correct.

If any financial/order edit is required, Staff must not make that edit.
The order is routed to Admin for finalization.


------------------------------------------------------------------------

# 17. Staff Account Management

Only Admin creates Staff.

Flow:

``` text
Admin
 ↓
People
 ↓
Staff
 ↓
Add Staff
 ↓
Name
Mobile Number
Email/Login
Staff Code
Temporary Password
 ↓
Secure Backend Function
 ↓
Firebase Authentication Account
 ↓
Staff Profile
 ↓
Staff Login
 ↓
Force Password Change
 ↓
Staff Dashboard
```

The final implementation must use a secure server-side privileged
operation for creating the Authentication account.

Temporary credentials must be handled securely.

## 17.1 Staff Profile

Admin can open an individual Staff profile.

Profile can display:

-   Staff Name
-   Mobile Number
-   Email/Login
-   Staff Code
-   Status
-   Created Date
-   Last Updated
-   Last Login, if available
-   Other non-sensitive operational profile information

Admin can:

-   Edit profile
-   Deactivate
-   Reactivate

Historical `createdBy`, `updatedBy`, `changedBy`, and `recordedBy`
references must remain valid.

------------------------------------------------------------------------

# 18. Admin Navigation

Bottom navigation:

``` text
Home
Orders
People
Reports
More
```

## 18.1 People

``` text
People
├── Staff
└── Customers
```

------------------------------------------------------------------------

# 19. Admin People --- Staff

Staff screen:

``` text
Staff
├── Active Staff
├── Inactive Staff
└── Add Staff
```

Opening a Staff member shows their profile.

Admin can edit the profile and change active status.

Deactivation does not delete history.

------------------------------------------------------------------------

# 20. Admin People --- Customers

Customer screen supports:

-   Search
-   Customer list
-   Customer detail
-   Customer status
-   Customer order history
-   Payment summary
-   Business information

Search should support:

-   Name
-   Mobile Number
-   Email
-   Business Name where applicable

Customer list display:

### Business

``` text
Amit Kumar
Hotel Paradise
```

### Personal

``` text
Amit Kumar
Personal
```

------------------------------------------------------------------------

# 21. Admin Customer Detail

When Admin opens a customer, the page must show **only that customer's
information**.

Sections:

1.  Profile
2.  Contact
3.  Address
4.  Business information where applicable
5.  Account status
6.  Orders
7.  Payment summary where appropriate

The customer page must not accidentally show another customer's data.

Admin actions:

-   Edit Profile
-   Activate
-   Deactivate
-   Open individual order

## 21.1 Customer Order History

The customer's page must show only orders whose `customerId` matches
that customer.

Summary may include:

-   Total Orders
-   Completed/Delivered Orders
-   Cancelled Orders
-   Total Billed
-   Total Paid
-   Pending Amount

Each order can be opened separately.

------------------------------------------------------------------------

# 22. Historical Customer Snapshot

Changing a current customer profile must not rewrite old order/invoice
identity.

At order creation, preserve relevant snapshots:

``` text
customerNameAtOrder
customerPhoneAtOrder
businessNameAtOrder
businessTypeAtOrder
pickupAddressAtOrder
```

If the customer later changes:

``` text
Hotel Paradise
```

to:

``` text
Hotel Royal
```

old historical documents must continue to use the original snapshot
where applicable.

------------------------------------------------------------------------

# 23. Items

Items represent laundry articles/garments.

Examples:

-   Shirt
-   Pant
-   Jeans
-   T-Shirt
-   Saree
-   Bedsheet
-   Blanket
-   Towel
-   Other configured laundry article

Admin can:

-   Add
-   Edit
-   Deactivate
-   Reactivate

Referenced items should generally be marked `INACTIVE`, not
hard-deleted.

Inactive items must not appear as normal new-order choices, but
historical orders must retain their snapshots.

------------------------------------------------------------------------

# 24. Services

Services represent laundry work performed.

Examples:

-   Wash
-   Wash + Iron
-   Iron
-   Dry Clean

Admin can:

-   Add
-   Edit
-   Deactivate
-   Reactivate

Referenced services should generally be marked `INACTIVE`, not
hard-deleted.

------------------------------------------------------------------------

# 25. Pricing

Pricing is based on:

``` text
Item + Service
```

Example:

``` text
Shirt + Wash = ₹30
Shirt + Wash + Iron = ₹50
Pant + Wash = ₹40
```

Prices must be stored in Firebase and cached in SQLite.

Prices must never be hardcoded into application logic.

Staff may use current prices operationally but may not change master
pricing.

Admin manages master pricing.

------------------------------------------------------------------------

# 26. Historical Price Integrity

When an order is created, store the applicable price snapshot.

At minimum:

``` text
priceAtOrderTime
```

The final invoice process must also store the final billed rate.

Changing a current master price must not alter:

-   Original order price snapshot
-   Received/final historical values
-   Final invoice

Reports must use stored historical/final values.

------------------------------------------------------------------------

# 27. Order Sources

Orders must support:

``` text
CUSTOMER_APP
STAFF_CREATED
WALK_IN
PHONE
```

For V1, `CUSTOMER_APP`, `STAFF_CREATED`, and `WALK_IN` are required.
`PHONE` is supported as a staff-created/manual source.

A walk-in customer does not need a customer-app account.

Staff/Admin can create a walk-in order and store the customer/contact
details needed for the transaction.

A walk-in may have no email.

------------------------------------------------------------------------

# 28. Order Collection Method

Every order must specify how laundry reaches the business.

Required values:

``` text
PICKUP_BY_US
CUSTOMER_DROP_OFF
```

## 28.1 Pickup by Business

The laundry business collects the customer's laundry.

Required/appropriate data:

-   Pickup address
-   Pickup date
-   Pickup time
-   Customer note

Flow:

``` text
NEW
 ↓
PICKUP_PENDING
 ↓
PICKED_UP
 ↓
PROCESSING
```

## 28.2 Customer Drop-Off

Customer personally brings laundry to the business.

Flow:

``` text
NEW / RECEIVED
 ↓
PROCESSING
```

No business pickup step should be shown.

The UI must not ask for unnecessary pickup scheduling information for
customer drop-off orders.

------------------------------------------------------------------------

# 29. Order Return Method

Every order must specify how completed laundry is returned.

Required values:

``` text
DELIVERY_BY_US
CUSTOMER_PICKUP
```

## 29.1 Delivery by Business

After ready:

``` text
READY
 ↓
OUT_FOR_DELIVERY
 ↓
DELIVERED
```

Customer can see delivery progress.

## 29.2 Customer Pickup

After ready:

``` text
READY_FOR_PICKUP
 ↓
COLLECTED
```

The delivery step is skipped.

Customer must not see an unnecessary `OUT_FOR_DELIVERY` stage for a
self-collection order.

------------------------------------------------------------------------

# 30. Combined Order Flows

## 30.1 Business Pickup + Business Delivery

``` text
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

## 30.2 Business Pickup + Customer Pickup

``` text
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

## 30.3 Customer Drop-Off + Business Delivery

``` text
NEW
 ↓
PROCESSING
 ↓
READY
 ↓
OUT_FOR_DELIVERY
 ↓
DELIVERED
```

## 30.4 Customer Drop-Off + Customer Pickup

``` text
NEW
 ↓
PROCESSING
 ↓
READY_FOR_PICKUP
 ↓
COLLECTED
```

The system must select valid status transitions according to the order's
collection and return methods.

------------------------------------------------------------------------

# 31. Order Statuses

Canonical statuses:

``` text
NEW
PICKUP_PENDING
PICKED_UP
PROCESSING
READY
READY_FOR_PICKUP
OUT_FOR_DELIVERY
DELIVERED
COLLECTED
CANCELLED
```

The application may use human-readable labels:

-   New
-   Pickup Pending
-   Picked Up
-   Processing
-   Ready
-   Ready for Pickup
-   Out for Delivery
-   Delivered
-   Collected
-   Cancelled

Invalid jumps must be rejected.

------------------------------------------------------------------------

# 32. Status Rules

Status changes are operational events and must be controlled.

Examples:

-   `PICKUP_PENDING → PICKED_UP`
-   `PICKED_UP → PROCESSING`
-   `PROCESSING → READY` or `READY_FOR_PICKUP`
-   `READY → OUT_FOR_DELIVERY`
-   `OUT_FOR_DELIVERY → DELIVERED`
-   `READY_FOR_PICKUP → COLLECTED`

The final implementation must define a domain state machine.

A Staff member must not bypass it by modifying Firestore directly.

------------------------------------------------------------------------

# 33. Status History

Every status change must create an append-only history record.

Fields:

``` text
historyId
orderId
businessId
fromStatus
toStatus
changedAt
changedBy
note
```

Initial status:

``` text
fromStatus = null
toStatus = NEW
```

Later transitions require a non-null `fromStatus`.

History records must not be silently overwritten or deleted.

------------------------------------------------------------------------

# 34. Order Creation --- Customer

Customer flow:

``` text
Customer Login
 ↓
Home
 ↓
Place Order
 ↓
Select Items
 ↓
Select Service
 ↓
Enter Quantity
 ↓
Select Collection Method
 ↓
Enter pickup details if required
 ↓
Select Return Method
 ↓
GST preference
 ↓
Estimated Amount
 ↓
Review
 ↓
Confirm
 ↓
Save to SQLite
 ↓
Sync Queue
 ↓
Firebase
```

The customer is shown an **estimated** amount only.

The final invoice is created later after actual laundry is received and
the business finalizes the order.

------------------------------------------------------------------------

# 35. Order Creation --- Staff/Admin

### 35.1 Staff

Staff may create **WALK_IN** orders only.

Staff must not create registered Customer profiles and must not create
phone/manual/customer-app orders in V1.

### 35.2 Admin

Admin may create operational/manual orders where required by the business,
including walk-in orders and orders for an existing registered Customer.

Admin-created orders must still follow all source, collection, return,
pricing, GST, audit, and historical-snapshot rules.

### 35.3 Walk-In Creation Flow

``` text
Staff/Admin
 ↓
Create Walk-In
 ↓
Enter:
Customer Name
Mobile Number
Address
 ↓
Select Item/Service
 ↓
Set Fixed Amount
 ↓
Review
 ↓
Create WALK_IN Order
 ↓
Store Staff/Admin creator identity
```

A V1 walk-in order does not require customer registration.

The walk-in amount is fixed at creation. No later Received Quantity or
Final Rate editing workflow is required for a walk-in order.

When the customer later pays, Staff or Admin records the payment. The
payment status is updated from the ledger and the Staff/Admin identity is
stored in `recordedBy`.

### 35.4 Registered Customer Order Creation

Customer-created orders follow the Customer flow in Section 34.

Staff must not create or edit the registered Customer profile merely to
place an order. If a customer needs an account, registration occurs
through the Customer registration flow.


------------------------------------------------------------------------

# 36. Original Order Data

The system must preserve what the customer originally requested.

At minimum:

``` text
orderId
businessId
customerId
source

customerNameAtOrder
customerPhoneAtOrder
businessNameAtOrder
businessTypeAtOrder

collectionMethod
returnMethod

pickupAddressAtOrder
pickupDate
pickupTime
customerNote

orderedItems[]
estimatedSubtotal
estimatedGST
estimatedTotal
estimatedGSTRate
estimatedGSTApplied

orderStatus
createdAt
updatedAt
createdBy
lastUpdatedBy
```

Original order data must not be replaced by later received/final data.

------------------------------------------------------------------------

# 37. Received Laundry Details

A key V1 workflow is the **actual received laundry verification**.

The customer's ordered quantity may differ from the laundry actually
received.

Example:

``` text
Ordered:
Shirt = 10
Pant = 5
Bedsheet = 8

Received:
Shirt = 12
Pant = 4
Bedsheet = 8
```

The system must preserve both.

Received data must not overwrite the original order.

------------------------------------------------------------------------

# 38. Received Item Fields

Each received/final item should preserve:

``` text
orderItemId
itemId
itemName
serviceId
serviceName

orderedQuantity
receivedQuantity

finalQuantity
finalRate
finalSubtotal

priceAtOrderTime
receivedNote
```

`finalQuantity` is the quantity used for final billing.

In the normal flow it may equal `receivedQuantity`, but the data model
must distinguish:

-   Ordered quantity
-   Received quantity
-   Final/billed quantity

This prevents loss of operational history.

------------------------------------------------------------------------

# 39. Invoice Finalization Window

After the laundry has been received and processed, Staff/Admin must have
an **Invoice Finalization** screen.

This is a critical V1 workflow.

Flow:

``` text
Existing Order
 ↓
Open Order
 ↓
Received / Processing completed
 ↓
Finalize Invoice
 ↓
Show complete order information
 ↓
Edit received/final line items
 ↓
Edit final quantities
 ↓
Edit final prices/rates
 ↓
Add/remove permitted final billable lines
 ↓
Add additional charges
 ↓
Select GST ON/OFF
 ↓
Calculate final invoice
 ↓
Review
 ↓
Save Final Invoice
 ↓
Generate Invoice Number
 ↓
Invoice Finalized
 ↓
Customer can see invoice
```

The finalization screen must show enough information for Staff/Admin to
verify the actual laundry before billing.

------------------------------------------------------------------------

# 40. Invoice Finalization Editable Data

Before finalization, authorized Staff/Admin can edit:

-   Received quantity
-   Final/billed quantity
-   Final rate
-   Final line subtotal
-   Applicable service/item line
-   GST ON/OFF
-   GST rate where authorized by the configured rules
-   Additional charges

The original customer order must remain intact.

Any important correction must be recorded through controlled
audit/history.

------------------------------------------------------------------------

# 41. Additional Charges

The finalization screen must allow additional charges.

Multiple charges may be added.

Each charge:

``` text
chargeId
name
amount
note
createdBy
createdAt
```

Example:

``` text
Extra Stain Treatment
₹100
Heavy stain treatment requested during processing.
```

Another:

``` text
Urgent Processing
₹200
Same-day processing charge.
```

Additional charges must appear in the final financial calculation and
invoice.

The note/message explains why the charge exists.

------------------------------------------------------------------------

# 42. Final Invoice Calculation

Recommended stored values:

``` text
estimatedSubtotal
estimatedGST
estimatedTotal

actualSubtotal
additionalChargesTotal

gstApplied
gstRate
gstAmount

finalAmount
```

Calculation:

``` text
actualSubtotal
+
additionalChargesTotal
=
taxableSubtotal

If GST applied:
gstAmount = taxableSubtotal × gstRate / 100

finalAmount = taxableSubtotal + gstAmount
```

If GST is not applied:

``` text
gstAmount = 0
finalAmount = taxableSubtotal
```

The exact rounding policy must be deterministic and consistent.

Money must be stored as integer minor units (paise) rather than
floating-point persisted currency.

------------------------------------------------------------------------

# 43. GST

GST is selected per order.

The application must not rely only on one global ON/OFF switch.

Order-level choice:

``` text
GST ON
GST OFF
```

Business settings contain:

``` text
GSTIN
defaultGSTRate
```

The default rate is only a default.

It must not automatically force GST on every order.

------------------------------------------------------------------------

# 44. GST Finalization Rule

The final GST decision used for the invoice must be captured in the
final invoice snapshot.

Once the invoice is finalized, changing the business default GST rate
must not alter the old invoice.

The historical invoice retains:

``` text
gstApplied
gstRate
gstAmount
```

The user's requirement is that once the received/final invoice data is
finalized, later normal setting changes must not alter that finalized
invoice.

------------------------------------------------------------------------

# 45. Estimated vs Final Financial Data

The system must clearly separate:

## Estimate

Based on customer/staff order request and current applicable pricing at
order creation.

``` text
estimatedSubtotal
estimatedGST
estimatedTotal
```

## Final

Based on actual received/finalized laundry:

``` text
actualSubtotal
additionalChargesTotal
gstApplied
gstRate
gstAmount
finalAmount
```

The customer may initially see the estimate.

After finalization, the customer sees the final invoice/amount.

------------------------------------------------------------------------

# 46. Invoice Finalization Lock

Once final invoice data is finalized:

``` text
invoiceStatus = FINALIZED
```

The financial snapshot becomes protected.

Normal Staff must not freely edit the finalized invoice.

Recommended correction policy:

``` text
Admin correction
 ↓
Controlled correction/reversal
 ↓
Audit record
 ↓
Re-finalization / replacement invoice where required
```

No silent modification of old financial history is permitted.

If an invoice correction feature is implemented in V1, it must preserve
the original finalized version and record who made the correction and
why.

------------------------------------------------------------------------

# 47. Invoice Status

Recommended:

``` text
NOT_FINALIZED
FINALIZED
CANCELLED
```

Invoice creation is separate from order creation.

An order can exist before an invoice exists.

------------------------------------------------------------------------

# 48. Invoice Number

Invoice number must be separate from Order ID.

Example:

``` text
Order ID: ORDXXXX
Invoice: INVXXXX
```

Invoice number must be unique within the business.

Invoice number generation must be safe against:

-   Offline retries
-   Duplicate operations
-   Multiple devices
-   Concurrent finalization
-   Collision

A business-scoped uniqueness check must be enforced authoritatively.

------------------------------------------------------------------------

# 49. Invoice Contents

A4 invoice must contain:

## Business

-   TREAT HOSPITALITY SERVICES
-   Address
-   Primary phone
-   Alternative phone where configured
-   Email where configured
-   GSTIN where applicable

## Invoice

-   Invoice number
-   Order ID
-   Invoice date

## Customer

-   Customer name
-   Mobile
-   Address
-   Business name where applicable

## Laundry lines

-   Item
-   Service
-   Quantity
-   Rate
-   Amount

## Additional Charges

-   Charge name
-   Amount
-   Note where appropriate

## Totals

-   Subtotal
-   Additional charges
-   GST rate
-   GST amount
-   Grand total
-   Paid amount
-   Due amount
-   Payment status

## Footer

-   Thank-you note / configured invoice footer

------------------------------------------------------------------------

# 50. Invoice Formatting

Invoice must support:

-   ₹ symbol
-   Indian number formatting
-   Long customer names
-   Long business names
-   Long addresses
-   Many line items
-   Additional charges
-   Text wrapping
-   Multiple pages if required
-   Correct A4 layout
-   No clipping
-   No overlap
-   No broken characters

Date formats:

``` text
14 September 2026
```

or:

``` text
14 Sep 2026
```

------------------------------------------------------------------------

# 51. Payment

Payment status:

``` text
PENDING
PARTIALLY_PAID
PAID
```

Payment methods:

``` text
CASH
UPI
ONLINE
```

Online payment gateway is not required for V1.

Manual recording of an online payment method is permitted only according
to the business's operational policy; this does not mean a payment
gateway is implemented.

------------------------------------------------------------------------

# 52. Payment Records

Each payment:

``` text
paymentId
orderId
businessId
amount
paymentMethod
paymentDate
recordedBy
note
createdAt
syncId
```

Multiple payments may exist.

Example:

``` text
Final Amount = ₹590

Cash = ₹200
UPI = ₹200

Paid = ₹400
Due = ₹190
```

------------------------------------------------------------------------

# 53. Payment Integrity

Payment records are append-only.

Normal clients must not:

-   Change old payment amount
-   Change old payment method
-   Change old payment date
-   Change recordedBy
-   Delete old payment

A correction/reversal process may be added later or implemented as an
Admin-only controlled operation.

Every payment must:

-   Have positive amount
-   Use a valid method
-   Belong to the correct order
-   Belong to the same business
-   Not cause paid amount to exceed final amount

Payment totals must be derived/validated from the payment ledger.

------------------------------------------------------------------------

# 54. Payment and Order Status Independence

Order status and payment status are independent.

Valid example:

``` text
Order Status: DELIVERED
Payment Status: PENDING
```

Therefore, delivery does not automatically mean paid.

The system must never infer payment solely from order status.

------------------------------------------------------------------------

# 55. Due Amount

Recommended:

``` text
dueAmount = finalAmount - paidAmount
```

The system must reject a payment that makes:

``` text
paidAmount > finalAmount
```

unless a separately documented credit/refund model is introduced.

------------------------------------------------------------------------

# 56. Order Detail Screen

Opening an order should show a complete history.

Recommended sections:

1.  Customer
2.  Original Order
3.  Collection Method
4.  Pickup information where applicable
5.  Received Laundry
6.  Final Bill
7.  Additional Charges
8.  GST
9.  Payment
10. Due
11. Invoice
12. Status Timeline
13. Audit information where appropriate

The original order and final invoice must be visibly distinguishable.

------------------------------------------------------------------------

# 57. Complete Order History Requirement

The database must preserve:

``` text
Original Customer Request
        +
Actual Received Laundry
        +
Final Billed Data
        +
GST Snapshot
        +
Additional Charges
        +
Invoice
        +
Payment Ledger
        +
Due
        +
Status History
        +
Audit Metadata
```

No later routine change may erase this history.

This is one of the highest-priority requirements of the system.

------------------------------------------------------------------------

# 58. Customer Order Tracking

Customer sees only their own orders.

Order summary:

-   Order ID
-   Invoice Number when available
-   Order Date
-   Collection method
-   Return method
-   Items
-   Estimated amount
-   Final amount when finalized
-   Payment status
-   Order status

For business delivery orders, the customer can see:

``` text
Ready
 ↓
Out for Delivery
 ↓
Delivered
```

For customer pickup orders:

``` text
Ready for Pickup
 ↓
Collected
```

Delivery stages must not be shown for self-collection orders.

------------------------------------------------------------------------

# 59. Customer Notifications

FCM may notify customers about:

-   Order received
-   Pickup completed
-   Processing
-   Ready
-   Ready for Pickup
-   Out for Delivery
-   Delivered
-   Collected
-   Invoice finalized

Notification failure must never cause an order or invoice operation to
fail.

------------------------------------------------------------------------

# 60. Staff Notifications

Staff may receive:

-   New customer order
-   Pickup request
-   Important operational updates
-   Relevant status tasks

Notifications are secondary to the database transaction.

------------------------------------------------------------------------

# 61. Reports --- Core Rule

Reports must use **finalized historical financial/order data**, not the
current order estimate.

This is a critical requirement.

If:

``` text
Ordered Quantity = 10
Received Quantity = 12
Final Billed Quantity = 12
```

reports must use the finalized/billed quantity where the metric is
financial/final operational revenue.

Original ordered data remains available separately for historical
analysis.

------------------------------------------------------------------------

# 62. Report Historical Integrity

Reports must never recalculate old orders using current:

-   Item price
-   Service price
-   GST rate
-   Customer profile
-   Business name
-   Current settings

Reports use stored historical/final values.

Therefore:

``` text
Old Order
 ↓
Old snapshots
 ↓
Finalized invoice data
 ↓
Historical report
```

not:

``` text
Old Order
 ↓
Current catalog
 ↓
Recalculate
```

------------------------------------------------------------------------

# 63. Report Periods

Available:

``` text
Weekly
Monthly
Yearly
```

## Weekly

Select:

-   Month
-   Year
-   From Date
-   To Date

Example:

``` text
September 2026
07 Sep 2026
13 Sep 2026
```

Filename:

``` text
Laundry_Report_07-09-2026_to_13-09-2026.xlsx
```

## Monthly

Select:

-   Month
-   Year

Filename:

``` text
Laundry_Report_September_2026.xlsx
```

## Yearly

Select:

-   Year

Filename:

``` text
Laundry_Report_2026.xlsx
```

------------------------------------------------------------------------

# 64. Report Metrics

Reports should include:

-   Total Orders
-   Completed/Delivered/Collected Orders
-   Cancelled Orders
-   Active/Pending Orders
-   Final Subtotal
-   Additional Charges
-   GST
-   Grand Total
-   Paid Amount
-   Pending Amount
-   Cash
-   UPI
-   Online
-   Orders by Status
-   Final service quantity
-   Final service revenue
-   Final item quantity
-   Final item revenue
-   Collection method breakdown
-   Return method breakdown
-   Customer type breakdown where useful

Cancelled orders must not accidentally inflate completed revenue.

------------------------------------------------------------------------

# 65. Yearly Report

Yearly report additionally contains:

## Monthly Breakdown

``` text
January
February
...
December
```

## Status Summary

-   New
-   Processing
-   Ready
-   Out for Delivery
-   Delivered
-   Ready for Pickup
-   Collected
-   Cancelled

## Payment Summary

-   Cash
-   UPI
-   Online
-   Paid
-   Partially Paid
-   Pending

## Service Performance

For each service:

-   Quantity
-   Revenue

## Item Performance

For each item:

-   Quantity
-   Revenue

------------------------------------------------------------------------

# 66. Report Finalization Eligibility

The report must clearly define what counts as financial revenue.

Recommended rule:

-   Finalized invoices contribute to final revenue metrics.
-   Orders without a finalized bill are not treated as finalized
    revenue.
-   Cancelled orders do not contribute to completed revenue.
-   Payments remain represented through the payment ledger.
-   Pending due remains outstanding until paid.

The implementation must use a single consistent reporting policy.

------------------------------------------------------------------------

# 67. Offline Reports

Reports can be generated offline when the required data exists in
SQLite.

If local data is not fully synchronized:

``` text
Report generated from data available on this device.
Some newer data may still be waiting to sync.
```

The report must not falsely claim to represent the complete cloud
dataset if the device has pending sync.

------------------------------------------------------------------------

# 68. Excel Generation

Flow:

``` text
SQLite
 ↓
Select Period
 ↓
Read Finalized Historical Data
 ↓
Calculate Metrics
 ↓
Generate XLSX
 ↓
Save Locally
 ↓
Open / Share
```

No Firebase Storage is required for V1 XLSX reports.

------------------------------------------------------------------------

# 69. Dashboards

## 69.1 Customer Dashboard

Keep simple:

``` text
Welcome
Active Order
Recent Orders
Place New Order
```

Primary action:

``` text
Place New Order
```

## 69.2 Staff Dashboard

Focus on operations:

-   New Orders
-   Pickup Pending
-   Processing
-   Ready
-   Ready for Pickup
-   Out for Delivery
-   Unpaid

## 69.3 Admin Dashboard

Show:

-   Today's Orders
-   Pending Orders
-   Processing
-   Ready
-   Ready for Pickup
-   Out for Delivery
-   Delivered
-   Collected
-   Unpaid
-   Today's Revenue

Do not turn the dashboard into a complex ERP dashboard.

------------------------------------------------------------------------

# 70. Admin More Section

Admin More can contain:

``` text
Business Settings
GST Settings
Invoice Settings
Items
Services
Prices
Sync Status
App Information
Logout
```

Only Admin functionality must be shown.

------------------------------------------------------------------------

# 71. Business Settings

Central settings:

``` text
Business Name
Business Type
Primary Mobile Number
Alternative Mobile Number
Email
Address
PIN Code
GSTIN
Default GST Rate
Invoice Prefix
```

Optional:

``` text
WhatsApp Number
Invoice Footer
Logo reference
```

Business settings are used for future operations.

Historical records preserve their own snapshots.

------------------------------------------------------------------------

# 72. Invoice Settings

Invoice settings may include:

-   Invoice prefix
-   Footer text
-   Business contact display preferences
-   GST display behavior
-   Date display format
-   Other non-financial presentation preferences

Financial historical snapshots must not depend on current presentation
settings.

------------------------------------------------------------------------

# 73. Data Model --- User

Conceptual user:

``` text
userId
name
email
phone
role
businessId
status
profileCompleted
forcePasswordChange
createdAt
updatedAt
```

Protected:

-   role
-   businessId
-   account status where Admin-controlled
-   forcePasswordChange where server-controlled

------------------------------------------------------------------------

# 74. Data Model --- Staff

``` text
staffId
userId
businessId
name
email
phone
staffCode
status
createdAt
updatedAt
```

Staff login credential remains managed by Firebase Authentication.

Do not store passwords in Staff documents.

------------------------------------------------------------------------

# 75. Data Model --- Customer

``` text
customerId
userId
businessId

name
email
phone

customerType

businessName
businessType
businessAddress
businessPhone

address
pinCode

status
profileCompleted

createdAt
updatedAt
```

Email may be nullable for walk-in customers where no account exists.

------------------------------------------------------------------------

# 76. Data Model --- Business

``` text
businessId
businessName
businessType
createdAt
updatedAt
```

V1 has one business.

The architecture still carries `businessId` on business-related records
to prepare for future expansion.

------------------------------------------------------------------------

# 77. Data Model --- Business Settings

``` text
businessId
businessName
businessType
primaryPhone
alternativePhone
email
address
pinCode
GSTIN
defaultGSTRate
invoicePrefix
invoiceFooter
createdAt
updatedAt
updatedBy
```

SQLite may use `business_settings` as the local representation.

Avoid maintaining two competing local sources of truth for editable
business settings.

------------------------------------------------------------------------

# 78. Data Model --- Item

``` text
itemId
businessId
name
status
createdAt
updatedAt
```

Status:

``` text
ACTIVE
INACTIVE
```

------------------------------------------------------------------------

# 79. Data Model --- Service

``` text
serviceId
businessId
name
status
createdAt
updatedAt
```

Status:

``` text
ACTIVE
INACTIVE
```

------------------------------------------------------------------------

# 80. Data Model --- Price

``` text
priceId
businessId
itemId
serviceId
price
status
createdAt
updatedAt
```

Price is stored as integer paise.

------------------------------------------------------------------------

# 81. Data Model --- Order

Conceptual final order model:

``` text
orderId
businessId
customerId
source

customerNameAtOrder
customerPhoneAtOrder
businessNameAtOrder
businessTypeAtOrder

collectionMethod
returnMethod

pickupAddressAtOrder
pickupDate
pickupTime
customerNote

orderedItems[]

estimatedSubtotal
estimatedGSTRate
estimatedGST
estimatedTotal
estimatedGSTApplied

receivedItems[]

actualSubtotal
additionalCharges[]
additionalChargesTotal

gstApplied
gstRate
gstAmount
finalAmount

invoiceStatus
invoiceNumber
invoiceCreatedAt
invoiceCreatedBy

paidAmount
dueAmount
paymentStatus

orderStatus

createdAt
updatedAt
createdBy
lastUpdatedBy
version
```

The final implementation may normalize some fields into SQLite tables
while retaining the same logical model.

------------------------------------------------------------------------

# 82. Firestore Order Representation

For Firestore, the documented architecture uses an order document with
embedded `items[]` where appropriate.

SQLite uses normalized `order_items`.

Do not introduce a second incompatible data architecture.

Firestore order documents must preserve:

-   Original order information
-   Received/final information
-   Financial snapshots
-   Additional charges
-   Invoice state
-   Customer snapshots
-   Status
-   Audit metadata

------------------------------------------------------------------------

# 83. Order Item Data

Logical item:

``` text
orderItemId
itemId
itemName
serviceId
serviceName

orderedQuantity
receivedQuantity
finalQuantity

priceAtOrderTime
finalRate

orderedSubtotal
finalSubtotal

receivedNote
createdAt
```

Historical item/service names must not be refreshed from current master
data.

------------------------------------------------------------------------

# 84. Additional Charge Data

``` text
chargeId
orderId
businessId
name
amount
note
createdBy
createdAt
```

Amount stored in paise.

Charges become part of the finalized financial snapshot.

------------------------------------------------------------------------

# 85. Invoice Snapshot

The finalized invoice must contain a durable snapshot of:

``` text
invoiceNumber
invoiceDate

businessName
businessAddress
businessPhone
businessAlternativePhone
businessEmail
businessGSTIN

customerName
customerPhone
customerAddress
customerBusinessName

lineItems
additionalCharges

subtotal
gstApplied
gstRate
gstAmount
finalAmount

paidAmount
dueAmount
paymentStatus
```

Changing current business/customer/master settings must not change this
snapshot.

------------------------------------------------------------------------

# 86. Payment Data

SQLite:

``` text
payment_id
order_id
business_id
amount
payment_method
payment_date
recorded_by
note
created_at
sync_id
```

Constraints:

-   amount \> 0
-   valid payment method
-   same business
-   valid order
-   no overpayment
-   append-only

------------------------------------------------------------------------

# 87. Status History Data

SQLite:

``` text
status_history_id
order_id
business_id
from_status
to_status
changed_at
changed_by
note
```

Append-only.

------------------------------------------------------------------------

# 88. Sync Queue

SQLite:

``` text
syncId
entityType
entityId
operation
status
createdAt
updatedAt
retryCount
lastError
```

Possible operations:

``` text
CREATE
UPDATE
DELETE
```

Hard deletion should be avoided for protected historical data.

------------------------------------------------------------------------

# 89. Sync Requirements

Sync must support:

-   Retry
-   Backoff
-   Restart recovery
-   Dependency ordering
-   Duplicate prevention
-   Idempotency
-   Error retention
-   Pending status
-   Conflict handling

A failed operation must remain recoverable.

Data must never silently disappear.

------------------------------------------------------------------------

# 90. Sync Dependency Example

If a local operation creates:

``` text
Customer
 ↓
Order
 ↓
Payment
```

the sync engine must not upload a dependent child before its required
parent is available.

Real dependencies must be represented explicitly.

The sync engine must not invent fake dependencies.

------------------------------------------------------------------------

# 91. Duplicate Prevention

Offline retries must not create duplicate records.

Use:

-   Client-generated IDs
-   Sync IDs
-   Idempotent operations
-   Authoritative uniqueness checks

Example:

``` text
Create Order ORD-XXXX
 ↓
Network failure
 ↓
Retry
```

The backend must recognize the existing operation rather than create a
duplicate order.

Invoice creation must have business-scoped uniqueness protection.

------------------------------------------------------------------------

# 92. IDs

Major entities require unique IDs:

``` text
businessId
userId
staffId
customerId
itemId
serviceId
priceId
orderId
orderItemId
paymentId
historyId
chargeId
syncId
invoiceNumber
```

Client-generated UUID/random IDs are recommended for offline entities.

IDs must be unique across offline devices.

Do not use unsafe process-local counters as durable identifiers.

------------------------------------------------------------------------

# 93. Money Representation

Persisted money must use integer paise.

Example:

``` text
₹500.00 = 50000 paise
```

Do not persist financial amounts as floating-point values.

All calculations must use deterministic integer arithmetic.

Display converts paise to Indian currency format.

------------------------------------------------------------------------

# 94. Transactions

Important local operations must use SQLite transactions.

Order creation should atomically include:

``` text
Order
+
Order Items
+
Initial Status History
+
Sync Queue Entry
```

Invoice finalization should atomically include:

``` text
Received/final item changes
+
Additional charges
+
GST selection
+
Final totals
+
Invoice state
+
Invoice number reservation/operation metadata
+
Sync Queue Entry
```

If any required part fails, the transaction must roll back.

------------------------------------------------------------------------

# 95. Financial Finalization Integrity

The finalization operation must be atomic.

The system must not produce:

-   Final amount without final items
-   Invoice number without a valid invoice
-   Payment against a nonexistent final state
-   Partially saved additional charges
-   GST amount inconsistent with GST rate
-   Paid amount greater than final amount

Local and backend validation must both exist.

------------------------------------------------------------------------

# 96. Historical Immutability

After finalization, the following are protected:

-   Original order snapshots
-   Received/final snapshot
-   Invoice number
-   Final item rates
-   Final quantities
-   GST snapshot
-   Final amount
-   Payment history
-   Status history

A controlled Admin correction process may create a new version/reversal,
but old history must remain traceable.

------------------------------------------------------------------------

# 97. Customer Isolation

A Customer can access only:

``` text
Own profile
Own customer record
Own orders
Own permitted invoices
Own permitted payment information
```

The backend must ensure:

``` text
order.customerId == authenticated customer's customerId
```

A customer must not gain access by changing:

-   URL/path
-   orderId
-   customerId
-   businessId
-   local SQLite data
-   request payload

------------------------------------------------------------------------

# 98. Staff Isolation

Staff must belong to the same business.

Staff may access operational records only within their assigned
business.

A Staff client must not be able to change:

``` text
role
businessId
pricing
GST configuration
protected financial snapshots
payment history
status history
```

------------------------------------------------------------------------

# 99. Admin Isolation

Admin has business-level administrative access only.

Admin can manage the assigned business but must not access another
business by manipulating `businessId`.

No Super Admin exists in V1.

------------------------------------------------------------------------

# 100. Backend Security Boundary

The mobile client is not trusted for authorization.

Firebase Security Rules / Cloud Functions must independently verify:

-   Authentication
-   User identity
-   Role
-   Business
-   Target resource
-   Ownership
-   Allowed fields
-   Allowed state transitions
-   Financial constraints
-   Protected fields
-   Account status

UI restrictions are not security.

Client-side validation is not security.

SQLite validation is not security.

------------------------------------------------------------------------

# 101. Protected Fields

At minimum protect:

``` text
role
businessId

finalAmount
gstRate
gstAmount
paidAmount
dueAmount
invoiceNumber

priceAtOrderTime
customer snapshots
business snapshots

payment history
status history

createdBy
updatedBy
recordedBy
changedBy

audit metadata
financial timestamps
```

Rules must reject unauthorized writes to protected fields.

------------------------------------------------------------------------

# 102. Firebase Security Structure

Recommended Firestore structure:

``` text
users/{userId}

businesses/{businessId}

businesses/{businessId}/staff/{staffId}

businesses/{businessId}/customers/{customerId}

businesses/{businessId}/items/{itemId}

businesses/{businessId}/services/{serviceId}

businesses/{businessId}/prices/{priceId}

businesses/{businessId}/orders/{orderId}

businesses/{businessId}/orders/{orderId}/payments/{paymentId}

businesses/{businessId}/orders/{orderId}/statusHistory/{historyId}

businesses/{businessId}/settings/general
```

Additional supporting subcollections may be introduced only where
justified by the finalized backend contract.

------------------------------------------------------------------------

# 103. Firestore Rules

Rules must enforce:

-   Authentication
-   Role
-   Business isolation
-   Customer ownership
-   Staff restrictions
-   Admin permissions
-   Protected field restrictions
-   Inactive account restrictions
-   Valid resource ownership

A user must never access another business by changing the path or
payload.

------------------------------------------------------------------------

# 104. Cloud Functions

Use Cloud Functions for privileged operations such as:

-   Staff Authentication account creation
-   Controlled role/account administration where required
-   Authoritative invoice uniqueness/reservation
-   Sensitive financial validation
-   Other operations that cannot safely be performed only by client
    rules

Admin SDK credentials must remain server-side.

------------------------------------------------------------------------

# 105. Authentication State and Account Status

The application must handle:

-   Signed out
-   Authenticated
-   Email not verified
-   Profile incomplete
-   Active
-   Inactive
-   Force password change
-   Revoked/invalid session where applicable

An inactive account must not bypass restrictions using an already-issued
session/token.

The backend must enforce account status.

------------------------------------------------------------------------

# 106. Data Consistency

For records that can be modified by Android and future Admin Web, use:

``` text
updatedAt
updatedBy
version
```

where useful.

Important financial and status changes must not be silently overwritten.

------------------------------------------------------------------------

# 107. Conflict Handling

For non-financial operational fields, the implementation may use a
documented conflict policy.

For:

-   Payments
-   Final invoice
-   GST snapshot
-   Status history
-   Protected financial data

use append-only or controlled transactional operations rather than blind
last-write-wins overwrites.

Conflicts must be detectable and recoverable.

------------------------------------------------------------------------

# 108. Deletion Policy

Prefer:

``` text
INACTIVE
```

instead of hard deletion for referenced master records:

-   Item
-   Service
-   Staff
-   Customer

Orders, finalized invoices, payment history and status history must not
be casually deleted.

------------------------------------------------------------------------

# 109. Customer Deactivation vs Historical Orders

Deactivating a customer:

``` text
ACTIVE → INACTIVE
```

does not mean:

``` text
DELETE CUSTOMER
DELETE ORDERS
DELETE INVOICES
DELETE PAYMENTS
```

It means:

``` text
Block new customer-initiated orders
Preserve historical records
Allow authorized Admin/Staff operational access
```

------------------------------------------------------------------------

# 110. Order Cancellation

Cancellation is a controlled operation and is permitted to CUSTOMER,
STAFF, or ADMIN only under the exact rules below.

## 110.1 Customer Cancellation

A Customer may cancel:

-   A pickup order **before pickup by the business**.
-   A customer-drop-off order **before processing starts**.

Customer cancellation is rejected after the applicable cutoff.

## 110.2 Staff/Admin Cancellation

Staff and Admin may cancel an order when:

-   The order is still before its irreversible processing stage; or
-   The laundry received by the business does not match the order and the
    customer does not agree to proceed.

Staff and Admin may perform the operational cancellation; the backend
must enforce the same state/permission rules.

## 110.3 Return After Cancellation

If laundry/garments are physically held by the business when the order is
cancelled, the system must create/track a return-to-customer requirement.

Cancellation does not mean the physical laundry is discarded.

The cancellation record must preserve:

-   Original order
-   Received details, if any
-   Payment history
-   Cancellation reason
-   Cancelled by
-   Cancelled timestamp
-   Return-required flag
-   Return status

Recommended return status:

``` text
NOT_REQUIRED
RETURN_PENDING
RETURNED
```

The order remains financially/historically preserved while the physical
return is tracked.

## 110.4 Cancellation After Payment

A cancellation must never silently erase a payment.

If a refund/reversal is required, it must be recorded through a controlled
financial correction process. The original payment record remains part of
the audit history.

V1 must not invent an automatic refund amount or payment method.


------------------------------------------------------------------------

# 111. Audit Metadata

Important records should include:

``` text
createdAt
updatedAt
createdBy
lastUpdatedBy
```

Payments:

``` text
recordedBy
```

Status history:

``` text
changedBy
changedAt
```

Additional charges:

``` text
createdBy
createdAt
```

Invoice finalization:

``` text
invoiceCreatedBy
invoiceCreatedAt
```

Audit metadata must identify the actor wherever practical.

------------------------------------------------------------------------

# 112. Local Search

SQLite should support:

-   Customer search
-   Order search
-   Item search
-   Service lookup
-   Business name search
-   Order ID search
-   Invoice number search
-   Mobile number search

This must continue to work offline using locally available data.

------------------------------------------------------------------------

# 113. Order Search

Admin and Staff can search by:

-   Order ID
-   Invoice Number
-   Customer Name
-   Mobile Number
-   Business Name where applicable

Example:

``` text
ORDXXXX
INVXXXX
Rahul
9876543210
Hotel Paradise
```

Search should be local-first.

------------------------------------------------------------------------

# 114. Order Filters

Filters:

## Order Status

-   New
-   Pickup Pending
-   Picked Up
-   Processing
-   Ready
-   Ready for Pickup
-   Out for Delivery
-   Delivered
-   Collected
-   Cancelled

## Payment Status

-   Paid
-   Partially Paid
-   Unpaid

Filters should be combinable.

Example:

``` text
Delivered + Unpaid
```

means:

``` text
orderStatus = DELIVERED
AND
paymentStatus != PAID
```

------------------------------------------------------------------------

# 115. Network States

Application should distinguish:

``` text
ONLINE
OFFLINE
SYNCING
SYNCED
SYNC_ERROR
```

A pending count should be visible where appropriate.

Examples:

``` text
✓ Synced
↻ Syncing...
! 3 items waiting to sync
```

------------------------------------------------------------------------

# 116. Sync Error Handling

If sync fails:

``` text
Data remains in SQLite
Sync state = FAILED/PENDING
Retry later
```

User-friendly message:

> Internet connection is unavailable. Your data has been saved on this
> device and will sync automatically.

If pending work exists:

> Some data is waiting to sync. Please keep the app open when internet
> is available.

Do not show raw Firebase error details.

------------------------------------------------------------------------

# 117. Background Sync

Use an Expo-compatible network-aware/background synchronization
approach.

Sync logic must be independent of UI.

If guaranteed native background execution is later required,
WorkManager/native scheduling can be added without replacing the core
repository/database architecture.

Sync must never block normal UI usage.

------------------------------------------------------------------------

# 118. Application Startup

``` text
Open App
 ↓
Initialize SQLite
 ↓
Check Firebase Auth
 ↓
Load local trusted profile/cache
 ↓
Load local dashboard
 ↓
Check network
 ↓
Sync if required
 ↓
Update UI
```

The user should not unnecessarily wait for the cloud.

------------------------------------------------------------------------

# 119. Logout

Logout must:

-   Sign out Firebase Authentication.
-   Clear sensitive authentication state.
-   Prevent protected screen access.
-   Handle pending work according to the sync policy.
-   Avoid exposing another user's private local data on a shared device.

Non-sensitive cached business data may be retained where safe.

Sensitive user-specific data must be handled carefully on shared Staff
devices.

------------------------------------------------------------------------

# 120. Session Security

Firebase Authentication manages the authentication session.

Before protected operations, the application/backend must validate the
current authenticated identity.

Client-stored role alone must never be trusted.

------------------------------------------------------------------------

# 121. Customer Registration Profile Completion

After verification:

``` text
Complete Your Profile
```

Fields:

-   Full Name
-   Mobile Number
-   Customer Type
-   Address
-   PIN Code
-   Business Name if Business
-   Business Type if Business

Completion state:

``` text
profileCompleted = false
```

then:

``` text
profileCompleted = true
```

An interrupted profile flow must resume safely.

------------------------------------------------------------------------

# 122. Staff First Login

After Admin creates Staff:

``` text
Staff Login
 ↓
Force Password Change
 ↓
New Password
 ↓
Staff Dashboard
```

`forcePasswordChange` must be trusted/server-controlled.

------------------------------------------------------------------------

# 123. Customer Profile Editing

Customer may edit:

-   Name
-   Mobile Number
-   Address
-   PIN Code
-   Business details where applicable

Customer cannot edit:

-   role
-   businessId
-   account status controlled by Admin
-   financial data
-   order ownership
-   payment history
-   status history

------------------------------------------------------------------------

# 124. Admin Profile Editing

Admin can edit their permitted profile/business settings.

The Admin's personal identity and business settings should be
conceptually separated so that changing a business contact detail
updates central business settings, not historical records.

------------------------------------------------------------------------

# 125. Input and Keyboard Requirements

Email:

``` text
email keyboard
```

Mobile:

``` text
numeric keyboard
```

PIN Code:

``` text
numeric keyboard
```

Quantity:

``` text
numeric keyboard
```

Price/amount:

``` text
numeric/decimal-compatible input
```

Password:

``` text
secure password input
```

------------------------------------------------------------------------

# 126. Validation

## Mobile

-   Required for completed customer profile.
-   Validate Indian mobile format according to business requirement.
-   Store consistently.

## PIN Code

-   Numeric.
-   Appropriate Indian PIN format.

## Quantity

-   Must be greater than 0 for billable line items.
-   Final quantity must satisfy the finalization rules.

## Price

-   Must not be negative.

## GST Rate

-   Must be valid according to configured business requirements.

## Additional Charge

-   Amount must not be negative.
-   Name should be meaningful.
-   Note may be optional but should be available.

## Payment

-   Amount \> 0.
-   Must not exceed remaining payable amount.

------------------------------------------------------------------------

# 127. Indian English

Use:

-   Mobile Number
-   PIN Code
-   GST
-   Pending Amount
-   Pickup
-   Delivery
-   Customer
-   Staff
-   Admin
-   Collected
-   Ready for Pickup

Avoid:

-   Zip Code
-   Sales Tax
-   Fulfilled

------------------------------------------------------------------------

# 128. Currency

Display:

``` text
₹
```

Examples:

``` text
₹500
₹1,250
₹5,900
```

Do not display:

``` text
Rs
$
```

unless explicitly required in another context.

------------------------------------------------------------------------

# 129. Date Format

Preferred:

``` text
14 September 2026
```

Short:

``` text
14 Sep 2026
```

Report filename date:

``` text
07-09-2026_to_13-09-2026
```

Timestamps must be stored consistently and displayed in the
business/user's intended local timezone.

------------------------------------------------------------------------

# 130. UI Quality

The application must avoid:

-   Text clipping
-   Broken characters
-   Broken ₹ symbol
-   Overlapping buttons
-   Text overflow
-   Long address clipping
-   Long business name clipping
-   Long customer name clipping
-   Incorrect keyboard
-   Tiny touch targets
-   Unclear status
-   Ambiguous GST selection
-   Hidden finalization state

Long text must wrap.

Buttons must remain usable on smaller Android screens.

------------------------------------------------------------------------

# 131. Customer UI Navigation

Bottom navigation:

``` text
Home
Orders
Profile
```

------------------------------------------------------------------------

# 132. Staff UI Navigation

Bottom navigation:

``` text
Home
Orders
Profile
```

------------------------------------------------------------------------

# 133. Admin UI Navigation

Bottom navigation:

``` text
Home
Orders
People
Reports
More
```

------------------------------------------------------------------------

# 134. Customer Journey

``` text
Install App
 ↓
Register
 ↓
Email + Password
 ↓
Email Verification
 ↓
Complete Profile
 ↓
Personal / Business
 ↓
Customer Dashboard
 ↓
Place Order
 ↓
Items
 ↓
Services
 ↓
Ordered Quantity
 ↓
Collection Method
 ├─ Pickup by Us → Pickup details
 └─ Customer Drop-Off
 ↓
Return Method
 ├─ Delivery by Us
 └─ Customer Pickup
 ↓
GST Preference
 ↓
Estimated Amount
 ↓
Review
 ↓
Confirm
 ↓
Order Created
 ↓
Operational Status Tracking
 ↓
Laundry Received
 ↓
Processing
 ↓
Final Invoice Finalization by Staff/Admin
 ↓
Invoice Available
 ↓
Payment / Due
 ↓
Delivery or Customer Collection
 ↓
Completed
```

------------------------------------------------------------------------

# 135. Staff Journey

``` text
Admin Creates Staff
 ↓
Staff Receives Login
 ↓
Staff Login
 ↓
First Password Change
 ↓
Staff Dashboard
 ↓
New Orders
 ↓
Pickup / Receive
 ↓
Processing
 ↓
Ready / Ready for Pickup
 ↓
Delivery / Collection
 ↓
Payment
 ↓
Delivered / Collected
```

Manual order:

``` text
Staff
 ↓
Create Order
 ↓
Existing Customer / Walk-in
 ↓
Collection Method
 ↓
Return Method
 ↓
Items
 ↓
Services
 ↓
Ordered Quantity
 ↓
Estimated GST
 ↓
Create
 ↓
Receive Laundry
 ↓
Process
 ↓
Finalize Invoice
 ↓
Payment
 ↓
Delivery / Collection
```

------------------------------------------------------------------------

# 136. Admin Journey

``` text
Admin Login
 ↓
Dashboard
 ↓
People
 ├── Staff
 │    ├── List
 │    ├── Open Profile
 │    ├── Edit
 │    └── Activate/Deactivate
 │
 └── Customers
      ├── List
      ├── Search
      ├── Open Customer
      ├── View Details
      ├── View Only That Customer's Orders
      ├── Open Individual Order
      └── Activate/Deactivate
 ↓
Orders
 ↓
Search / Filter
 ↓
Open Order
 ↓
View Original + Received + Final + Payment + History
 ↓
Finalize Invoice
 ↓
Payment
 ↓
More
 ├── Business Settings
 ├── GST
 ├── Invoice Settings
 ├── Items
 ├── Services
 ├── Prices
 └── Sync Status
 ↓
Reports
 ├── Weekly
 ├── Monthly
 └── Yearly
```

------------------------------------------------------------------------

# 137. Complete Final Order Lifecycle

``` text
ORDER REQUEST
    ↓
Original Order Saved
    ↓
Collection
    ↓
Laundry Received
    ↓
Actual Received Quantities Recorded
    ↓
Processing
    ↓
Invoice Finalization Window
    ↓
Final Quantities / Rates Reviewed
    ↓
Additional Charges Added if needed
    ↓
GST ON/OFF Selected
    ↓
Final Totals Calculated
    ↓
Invoice Finalized
    ↓
Invoice Number Assigned
    ↓
Customer Can See Invoice
    ↓
Payment(s)
    ↓
Due Tracked
    ↓
Delivery OR Customer Collection
    ↓
Completed
```

------------------------------------------------------------------------

# 138. Final Data Lifecycle Example

Example:

## Original

``` text
Shirt = 10
Pant = 5
Bedsheet = 8
Estimated = ₹2,000
```

## Received

``` text
Shirt = 12
Pant = 4
Bedsheet = 8
```

## Finalization

``` text
Shirt final qty = 12
Pant final qty = 4
Bedsheet final qty = 8

Final rates entered/confirmed
Additional Charge = ₹100
GST = ON
```

## Final Invoice

``` text
Final subtotal
+ Additional charges
+ GST
= Final amount
```

## Payment

``` text
Cash ₹500
UPI ₹1,000
Paid = ₹1,500
Due = Final Amount - ₹1,500
```

All stages remain stored.

------------------------------------------------------------------------

# 139. Customer Order Data vs Final Invoice Data

The system must never treat these as the same thing.

``` text
CUSTOMER REQUEST
        ≠
ACTUAL RECEIVED
        ≠
FINAL BILL
```

They may have equal values, but the database must retain their distinct
meaning.

This requirement prevents loss of operational truth.

------------------------------------------------------------------------

# 140. Historical Business Snapshot

When an order/invoice is finalized, preserve relevant business details.

Example:

``` text
businessNameAtInvoice
businessAddressAtInvoice
businessPhoneAtInvoice
businessAlternativePhoneAtInvoice
businessEmailAtInvoice
GSTINAtInvoice
```

If Admin later changes business information, old invoices remain
historically correct.

------------------------------------------------------------------------

# 141. Historical Price Snapshot

Preserve:

``` text
priceAtOrderTime
finalRate
```

The first represents the original applicable order price.

The second represents the final billed rate.

Changing master prices must not change either historical value.

------------------------------------------------------------------------

# 142. Historical GST Snapshot

Preserve:

``` text
estimatedGSTApplied
estimatedGSTRate
estimatedGST

gstApplied
gstRate
gstAmount
```

Final invoice values are authoritative for final reporting.

------------------------------------------------------------------------

# 143. Reports and Payments

Reports must distinguish:

-   Final billed amount
-   Amount actually paid
-   Amount still due

Example:

``` text
Final Bill = ₹10,000
Paid = ₹7,000
Due = ₹3,000
```

Revenue/billing and collection metrics must not be confused.

------------------------------------------------------------------------

# 144. Report Source of Truth

For financial/reporting metrics:

``` text
Finalized Invoice Snapshot
+
Payment Ledger
+
Finalized Operational Quantities
+
Historical Status Data
```

not:

``` text
Current Price Master
+
Current GST Setting
+
Current Customer Profile
```

------------------------------------------------------------------------

# 145. Local vs Cloud Historical Safety

A local offline finalization must be preserved until successfully
synchronized.

If the network fails after local finalization:

-   Final data remains in SQLite.
-   Invoice state remains pending sync.
-   Sync retries.
-   Duplicate invoice creation must be prevented.
-   The user must not be told that cloud sync succeeded unless it
    actually did.

------------------------------------------------------------------------

# 146. Sync Status and Financial Data

A locally finalized invoice may have:

``` text
invoiceStatus = FINALIZED
syncStatus = PENDING
```

This is valid.

The UI should distinguish:

-   Financial finalization state
-   Cloud synchronization state

They are not the same.

------------------------------------------------------------------------

# 147. Firebase Suitability

Firebase/Firestore is suitable for this application as the long-term
cloud data store because the architecture requires:

-   Authentication
-   Central business records
-   Cross-device synchronization
-   Future Admin Web access
-   Cloud persistence/backup
-   Security Rules
-   Server-side Functions

Firestore should remain the cloud/master record while SQLite remains the
offline operational store.

The application should not assume that SQLite alone is the permanent
backup.

------------------------------------------------------------------------

# 148. No Firebase Storage in V1

Firebase Storage is not required for:

-   Normal business records
-   Invoice PDF
-   XLSX reports

V1 documents are generated locally.

Future Storage may support:

-   Business logo upload
-   Clothing photos
-   Order photos
-   Documents

------------------------------------------------------------------------

# 149. Future Admin Web

Future Admin Web:

``` text
React / Next.js
TypeScript
```

It uses the same Firebase backend.

Potential functions:

-   Dashboard
-   Orders
-   Customers
-   Staff
-   Items
-   Services
-   Prices
-   Reports
-   Business Settings
-   GST
-   Invoice management

Exact Web UI is outside Android V1.

------------------------------------------------------------------------

# 150. Multiple Business / Branch Future Scope

V1:

``` text
One business
One businessId
```

The schema should still carry `businessId`.

Future versions may introduce:

-   Multiple businesses
-   Multiple branches
-   Multiple processing centres
-   Multiple customer-business associations

No multi-business UI is required in V1.

------------------------------------------------------------------------

# 151. Future Customer Multiple Business Associations

V1 customer has one current business identity:

``` text
customerType = BUSINESS
businessName = ...
```

A future version may support:

``` text
Customer
 ├── Business A
 ├── Business B
 └── Business C
```

This is not V1.

------------------------------------------------------------------------

# 152. Delivery Scope

V1 stores:

-   Delivery method
-   Delivery status
-   Relevant address
-   Delivery progression

V1 does not implement:

-   GPS
-   Live delivery tracking
-   Route optimization
-   Driver fleet management

Customer can still see order delivery status.

------------------------------------------------------------------------

# 153. Walk-In Scope

Walk-in orders are first-class operational orders.

A walk-in customer may not have:

-   Firebase Auth account
-   Email
-   Customer mobile app profile

Staff/Admin must still be able to capture the necessary customer
identity/contact information.

Walk-in records must be handled securely and linked to their orders.

------------------------------------------------------------------------

# 154. Walk-In and Customer Records

If a walk-in customer later becomes a registered customer, the
implementation may provide a future controlled record-linking/merge
feature.

V1 must not silently merge records.

------------------------------------------------------------------------

# 155. Notifications Reliability

Notification failure must not block:

-   Order creation
-   Status update
-   Invoice finalization
-   Payment recording

Database operation succeeds independently of notification delivery.

------------------------------------------------------------------------

# 156. Error Handling

Never show raw technical errors.

Bad:

``` text
FirebaseError: PERMISSION_DENIED
```

Good:

> You do not have permission to perform this action.

Bad:

``` text
Network request failed
```

Good:

> Internet connection is unavailable. Your data has been saved on this
> device and will sync automatically.

Backend should return safe application-level errors.

Server logs may retain technical diagnostics where appropriate.

Do not expose:

-   Stack traces
-   Admin SDK internals
-   Service-account details
-   Private implementation details

------------------------------------------------------------------------

# 157. Performance Requirements

Application should:

-   Launch quickly.
-   Load local data quickly.
-   Search locally.
-   Avoid unnecessary Firebase reads.
-   Avoid blocking UI during sync.
-   Generate invoices locally.
-   Generate reports locally.
-   Use efficient queries.
-   Use pagination as data grows.
-   Keep lists responsive.

------------------------------------------------------------------------

# 158. Data Retention

Firestore:

``` text
Long-term business data
```

SQLite:

``` text
Local operational/cache data
```

PDF/XLSX:

``` text
Local generated files
```

Historical orders, invoices, payments and status histories should not be
casually deleted.

------------------------------------------------------------------------

# 159. Security Regression Requirements

Tests must verify that a malicious client cannot:

-   Change role
-   Change businessId
-   Create Admin
-   Create Staff directly
-   Access another business
-   Access another customer
-   Access another customer's orders
-   Modify invoice number
-   Modify GST amount
-   Modify GST rate snapshot
-   Modify final amount
-   Modify paid amount
-   Modify due amount
-   Modify priceAtOrderTime
-   Rewrite customer snapshots
-   Rewrite payment history
-   Rewrite status history
-   Bypass inactive account
-   Bypass email verification
-   Force invalid status transitions
-   Cause overpayment

------------------------------------------------------------------------

# 160. Authorization Tests

Test:

-   Unauthenticated access
-   Customer own data
-   Customer other-data rejection
-   Staff own-business access
-   Staff cross-business rejection
-   Admin own-business management
-   Cross-business Admin rejection
-   Role escalation rejection
-   Business ID tampering rejection
-   Protected field rejection
-   Inactive account rejection

------------------------------------------------------------------------

# 161. Financial Tests

Test:

-   Correct subtotal
-   Correct GST
-   GST OFF
-   GST ON
-   Additional charges
-   Multiple additional charges
-   Final quantity changes
-   Final rate changes
-   Paid amount
-   Due amount
-   Partial payment
-   Full payment
-   Overpayment rejection
-   Payment append-only behavior
-   Invoice uniqueness
-   Duplicate finalization retry
-   Historical price preservation
-   Historical GST preservation
-   Historical customer snapshot preservation

------------------------------------------------------------------------

# 162. Order Flow Tests

Test every supported combination:

1.  Pickup by us + Delivery by us
2.  Pickup by us + Customer pickup
3.  Customer drop-off + Delivery by us
4.  Customer drop-off + Customer pickup

Verify invalid status transitions are rejected.

Verify the correct customer-facing timeline is displayed.

------------------------------------------------------------------------

# 163. Offline Tests

Test:

-   Order creation offline
-   Customer creation offline where permitted
-   Staff operational work offline
-   Received data offline
-   Invoice finalization offline
-   Payment recording offline
-   Sync retry
-   App restart with pending queue
-   Network loss during sync
-   Duplicate retry
-   Dependency ordering
-   Queue corruption/error recovery
-   Cloud conflict
-   Incomplete sync state

No committed local data may disappear silently.

------------------------------------------------------------------------

# 164. Migration Requirements

SQLite schema changes must use explicit migrations.

Migrations must preserve:

-   Orders
-   Original quantities
-   Received quantities
-   Final quantities
-   Historical rates
-   GST
-   Invoice numbers
-   Payments
-   Status history
-   Customer snapshots

Migrations must be tested for rollback/failure safety where practical.

------------------------------------------------------------------------

# 165. Repository Architecture

The UI must not directly manipulate SQLite or Firebase.

Recommended layers:

``` text
UI
 ↓
Screen/View Model
 ↓
Use Cases
 ↓
Domain Layer
 ↓
Repository Interfaces
 ↓
SQLite Adapter / Firebase Adapter
 ↓
Sync Engine
```

Domain logic must remain independent of Expo/Firebase/SQLite where
practical.

------------------------------------------------------------------------

# 166. Domain Layer

Domain layer should contain:

-   Order state machine
-   Price calculations
-   GST calculations
-   Payment calculations
-   Finalization validation
-   Status transition rules
-   Business rules

Do not put critical business rules only in UI components.

------------------------------------------------------------------------

# 167. Repository Layer

Repositories should abstract:

-   Customer data
-   Staff data
-   Items
-   Services
-   Prices
-   Orders
-   Payments
-   Status history
-   Business settings
-   Sync queue

UI must not directly call Firebase SDK for business mutations.

------------------------------------------------------------------------

# 168. Sync Engine

Sync engine should:

-   Be UI-independent.
-   Process pending work.
-   Respect dependencies.
-   Retry safely.
-   Use idempotency.
-   Preserve failed work.
-   Record diagnostics.
-   Avoid duplicate writes.
-   Wake on connectivity changes.
-   Resume after app restart.

------------------------------------------------------------------------

# 169. Firestore / SQLite Mapping

Conceptual:

``` text
Firestore:
orders/{orderId}
  items[]

SQLite:
orders
order_items
```

Firestore is document-oriented.

SQLite is normalized for local operational querying.

Both represent the same business model.

------------------------------------------------------------------------

# 170. No Direct Client Trust

The following must never be accepted as authoritative solely because the
client supplied them:

``` text
role
businessId
finalAmount
gstAmount
paidAmount
dueAmount
invoiceNumber
createdBy
recordedBy
changedBy
```

The backend must validate them.

------------------------------------------------------------------------

# 171. Admin People Data Safety

Admin's People section must be correctly scoped.

When opening:

``` text
People → Customers → Customer A
```

the screen must load:

``` text
Customer A
Customer A's orders
Customer A's payment summary
```

and never:

``` text
All customers' orders mixed together
```

The customer ID must be the authoritative filter.

------------------------------------------------------------------------

# 172. Staff Profile Safety

When Admin edits Staff:

-   Do not allow changing Staff to ADMIN through ordinary profile
    editing.
-   Do not allow changing businessId to another business.
-   Do not expose Authentication private credentials.
-   Do not expose password.
-   Preserve staff historical actor references.

------------------------------------------------------------------------

# 173. Customer Deactivation Safety

When Admin selects Deactivate:

``` text
Confirm
 ↓
Customer status = INACTIVE
 ↓
Record audit metadata
 ↓
Prevent new customer order creation
 ↓
Preserve existing data
```

Reactivation reverses the account availability state without rewriting
history.

------------------------------------------------------------------------

# 174. Invoice Finalization UX

The finalization screen should make it obvious that the displayed values
are being finalized.

Recommended sections:

``` text
Customer
Original Order
Received Laundry
Final Billable Items
Additional Charges
GST
Payment
Final Total
```

Use explicit action:

``` text
Finalize Invoice
```

Before committing, show a final review/confirmation.

------------------------------------------------------------------------

# 175. Finalization Warning

Before finalization, show a clear warning such as:

> After the invoice is finalized, the financial details become
> protected. Corrections require an authorized correction process.

This is a UX requirement to prevent accidental finalization.

------------------------------------------------------------------------

# 176. Payment Timing

Payments may occur:

-   At order creation
-   During processing
-   At invoice finalization
-   Before delivery/collection
-   At delivery/collection
-   After delivery/collection

But the system must always validate payments against the current
finalized payable amount.

If a payment is recorded before final invoice finalization, the
implementation must define how it is held/associated and how the final
invoice is reconciled.

Recommended V1 approach:

-   Allow advance/partial payment only if explicitly supported.
-   Preserve the payment ledger.
-   Reconcile against the finalized amount.
-   Never lose the original payment timestamp/actor.

------------------------------------------------------------------------

# 177. Order Completion and Payment

An order can reach:

``` text
DELIVERED
```

or:

``` text
COLLECTED
```

while payment is:

``` text
PENDING
```

The business may therefore collect outstanding payment later.

The dashboard/report must show unpaid completed orders.

------------------------------------------------------------------------

# 178. Business Settings Historical Rule

If Admin changes:

``` text
Business Name
Phone
Alternative Phone
Address
GSTIN
Default GST Rate
Invoice Prefix
```

the change affects future operations only.

Old finalized invoices retain their historical values.

------------------------------------------------------------------------

# 179. Price Settings Historical Rule

If Admin changes:

``` text
Shirt + Wash = ₹30
```

to:

``` text
Shirt + Wash = ₹40
```

future orders use ₹40.

Old orders retain their original historical price and final invoice
data.

------------------------------------------------------------------------

# 180. GST Settings Historical Rule

If:

``` text
Default GST = 18%
```

changes to:

``` text
Default GST = 12%
```

old finalized invoices remain at their captured GST snapshot.

New applicable orders may use 12% as the default.

Order-level GST choice remains authoritative.

------------------------------------------------------------------------

# 181. Customer Settings Historical Rule

If customer changes:

``` text
Name
Business Name
Phone
Address
```

future orders use the current profile.

Old orders/invoices retain historical snapshots.

------------------------------------------------------------------------

# 182. Invoice and Report Relationship

The invoice is the final financial document.

Reports aggregate finalized historical data.

Therefore:

``` text
Order Estimate
       ↓
Actual Received
       ↓
Final Invoice
       ↓
Reports
```

Reports must not bypass final invoice data for financial totals.

------------------------------------------------------------------------

# 183. Operational vs Financial Status

These are separate concepts.

Operational:

``` text
orderStatus
```

Financial:

``` text
invoiceStatus
paymentStatus
```

Synchronization:

``` text
syncStatus
```

They must not be collapsed into one status field.

------------------------------------------------------------------------

# 184. Suggested Status Separation

Example:

``` text
Order:
PROCESSING

Invoice:
FINALIZED

Payment:
PARTIALLY_PAID

Sync:
SYNCED
```

All four can coexist.

------------------------------------------------------------------------

# 185. Customer Account Status vs Order Status

Customer:

``` text
ACTIVE / INACTIVE
```

Order:

``` text
NEW / PROCESSING / ...
```

Deactivating a customer must not automatically cancel existing orders.

------------------------------------------------------------------------

# 186. Business Account Status

The business itself is V1-controlled.

If a future business status is introduced, it must be separate from
individual user status.

------------------------------------------------------------------------

# 187. App Information

Admin More → App Information may show:

-   App name
-   Version
-   Build number
-   Backend/environment information appropriate for the user
-   Support information

Do not expose secrets.

------------------------------------------------------------------------

# 188. Support / Diagnostics

Sync screen may provide:

-   Current network state
-   Pending operations count
-   Last successful sync time
-   Last failed sync time
-   Retry action

Raw server secrets/errors must not be shown.

------------------------------------------------------------------------

# 189. Local Cache Freshness

Master data such as:

-   Items
-   Services
-   Prices
-   Business settings

must have a documented freshness/invalidation strategy.

After a successful cloud update, relevant local caches must be
invalidated/refreshed.

Stale catalog data must not silently overwrite newer authoritative data.

------------------------------------------------------------------------

# 190. Cross-Device Data

Because Staff/Admin may use more than one device, synchronization must
support:

-   Pulling newer cloud data
-   Pushing local changes
-   Avoiding duplicate records
-   Preserving append-only histories
-   Detecting stale writes
-   Maintaining business isolation

------------------------------------------------------------------------

# 191. Cold Offline Start

The application should define a safe policy for starting offline.

If the user has previously authenticated and locally cached trusted
session/profile data exists, the app may allow appropriate offline
operation.

Privileged/account-sensitive actions must not rely indefinitely on stale
authorization state.

The implementation must not grant new privileges while offline.

------------------------------------------------------------------------

# 192. Security of Local Data

Sensitive local data should be minimized.

Use secure platform storage for:

-   Authentication/session secrets where needed
-   Sensitive tokens

Do not put Firebase Admin credentials in local app storage.

Shared Staff devices require careful handling of local cached customer
data.

------------------------------------------------------------------------

# 193. Customer Privacy

Customers must not see:

-   Other customers
-   Staff private information
-   Admin information
-   Internal notes not intended for customers
-   Internal financial audit details
-   Internal sync errors
-   Other business data

Only permitted customer-facing order/invoice/payment information is
shown.

------------------------------------------------------------------------

# 194. Staff Privacy

Staff sees only operational customer information required for work.

Internal Admin-only data should not be exposed unnecessarily.

------------------------------------------------------------------------

# 195. Admin Visibility

Admin can see complete business records within the assigned business,
including:

-   Customers
-   Staff
-   Orders
-   Final invoices
-   Payments
-   Status history
-   Reports
-   Business settings

------------------------------------------------------------------------

# 196. Data Integrity Principles

The system must follow:

1.  Never silently overwrite history.
2.  Never use current master data to rewrite historical data.
3.  Never trust client authorization.
4.  Never delete protected financial history casually.
5.  Never lose offline data silently.
6.  Never allow duplicate financial records.
7.  Never allow overpayment.
8.  Never allow cross-business access.
9.  Never let inactive customers create new orders.
10. Never let customers alter finalized financial data.

------------------------------------------------------------------------

# 197. V1 Acceptance Criteria --- Authentication

V1 is complete when:

-   Customer registration works.
-   Email verification works.
-   Login works.
-   Forgot password works.
-   Staff cannot public-register.
-   Admin can create Staff.
-   Staff first-login password change works.
-   Initial Admin setup is controlled.
-   Role routing works.
-   Inactive account handling works.
-   Profile completion works.

------------------------------------------------------------------------

# 198. V1 Acceptance Criteria --- Customer

-   Customer profile works.
-   Personal/Business works.
-   Business information works.
-   Customer can place an order.
-   Customer can choose collection method.
-   Customer can choose return method.
-   Customer sees correct status flow.
-   Customer sees own orders.
-   Customer cannot see other customers.
-   Customer sees finalized invoice.
-   Customer sees permitted payment information.
-   Deactivated customer cannot place new orders.

------------------------------------------------------------------------

# 199. V1 Acceptance Criteria --- Staff

-   Staff login works.
-   Staff can change temporary password.
-   Staff can create manual/walk-in orders.
-   Staff can search orders.
-   Staff can filter orders.
-   Staff can process pickup/drop-off flows.
-   Staff can record received laundry.
-   Staff can update permitted statuses.
-   Staff can finalize invoice if granted that operational permission.
-   Staff can record payments.
-   Staff can work offline.
-   Staff cannot manage pricing/GST/staff.

------------------------------------------------------------------------

# 200. V1 Acceptance Criteria --- Admin

-   Admin dashboard works.
-   Staff list works.
-   Staff profile opens.
-   Staff profile can be edited.
-   Staff can be activated/deactivated.
-   Customer list works.
-   Business/personal customer display works.
-   Customer profile opens.
-   Only that customer's details are shown.
-   Only that customer's orders are shown.
-   Individual customer orders open separately.
-   Customer can be activated/deactivated.
-   Items work.
-   Services work.
-   Prices work.
-   Business Settings work.
-   GST settings work.
-   Invoice settings work.
-   Order management works.
-   Reports work.

------------------------------------------------------------------------

# 201. V1 Acceptance Criteria --- Order Lifecycle

All four collection/return combinations work:

-   Pickup by us + delivery by us
-   Pickup by us + customer pickup
-   Customer drop-off + delivery by us
-   Customer drop-off + customer pickup

The correct statuses are shown.

Invalid transitions are rejected.

------------------------------------------------------------------------

# 202. V1 Acceptance Criteria --- Received and Finalization

-   Original ordered quantity is preserved.
-   Received quantity can differ.
-   Final quantity can be set.
-   Final rate can be edited by authorized user.
-   Additional charges can be added.
-   Charge amount and note are stored.
-   GST can be ON/OFF at finalization.
-   Final subtotal is correct.
-   GST is correct.
-   Final amount is correct.
-   Invoice is finalized atomically.
-   Final invoice becomes protected.
-   Customer sees invoice after finalization.
-   Historical data remains intact.

------------------------------------------------------------------------

# 203. V1 Acceptance Criteria --- Payments

-   Multiple payments work.
-   Cash works.
-   UPI works.
-   Online method can be recorded where allowed.
-   Paid amount is correct.
-   Due amount is correct.
-   Payment status is correct.
-   Overpayment is rejected.
-   Payment history is append-only.
-   Order status and payment status remain independent.

------------------------------------------------------------------------

# 204. V1 Acceptance Criteria --- Reports

-   Weekly report works.
-   Monthly report works.
-   Yearly report works.
-   XLSX generation works.
-   Finalized data is used.
-   Historical rates are correct.
-   Historical GST is correct.
-   Additional charges are included.
-   Paid/due values are correct.
-   Payment method totals are correct.
-   Item/service performance is correct.
-   Collection/return breakdown works where implemented.
-   Offline report warning works when local data is incomplete.

------------------------------------------------------------------------

# 205. V1 Acceptance Criteria --- Offline

-   Core operations work without internet.
-   SQLite stores changes.
-   Sync queue stores pending work.
-   UI updates immediately after local commit.
-   Sync resumes after connectivity returns.
-   Failed syncs retry.
-   App restart preserves pending work.
-   Duplicate retries do not create duplicates.
-   Financial finalization remains safe offline.
-   Cloud sync status is visible.
-   Data never silently disappears.

------------------------------------------------------------------------

# 206. V1 Acceptance Criteria --- Security

-   Role-based access works.
-   Customer isolation works.
-   Business isolation works.
-   Staff restrictions work.
-   Admin-only operations are protected.
-   Inactive accounts are enforced.
-   Protected fields cannot be modified by unauthorized clients.
-   Payment history cannot be rewritten.
-   Status history cannot be rewritten.
-   Firebase Admin credentials are never exposed.
-   Cross-business path manipulation fails.

------------------------------------------------------------------------

# 207. V1 Acceptance Criteria --- Invoice

-   A4 PDF works.
-   Business information is correct.
-   Customer information is correct.
-   Invoice number is unique.
-   Order ID is shown.
-   Final line items are correct.
-   Additional charges are shown.
-   GST is correct.
-   Paid/due is correct.
-   ₹ displays correctly.
-   Long text wraps.
-   Multi-page invoice works when necessary.
-   Historical business/customer data remains correct.

------------------------------------------------------------------------

# 208. Final Product Flow

``` text
                         OPEN APP
                            ↓
                    Firebase Authentication
                            ↓
                     Authenticated UID
                            ↓
                  Trusted User Application Profile
                            ↓
                    Role + Business ID
                            ↓
           +----------------+----------------+
           |                |                |
        CUSTOMER          STAFF            ADMIN
           |                |                |
        Customer         Staff            Admin
          Home            Home             Home
           |                |                |
        Orders           Orders           Orders
           |                |                |
       Own Orders      Operations       Full Business
           |                |             Control
           +----------------+----------------+
                            ↓
                         SQLite
                            ↓
                       Sync Queue
                            ↓
                         Firebase
                            ↓
                        Firestore
                            ↓
                    Future Admin Web
```

------------------------------------------------------------------------

# 209. Final Order/Financial Flow

``` text
Customer Request
      ↓
Original Order Snapshot
      ↓
Collection / Drop-Off
      ↓
Laundry Received
      ↓
Received Quantity Snapshot
      ↓
Processing
      ↓
Invoice Finalization
      ↓
Final Quantity + Final Rate
      ↓
Additional Charges
      ↓
GST ON/OFF
      ↓
Final Invoice Snapshot
      ↓
Invoice Number
      ↓
Payment Ledger
      ↓
Paid / Due
      ↓
Delivery / Customer Collection
      ↓
Reports
```

------------------------------------------------------------------------

# 210. Final Architecture Summary

``` text
                 React Native + Expo + TypeScript
                              |
             +----------------+----------------+
             |                                 |
          SQLite                           Firebase
       Local Source                     Cloud Backend
             |                                 |
       Repositories                    Authentication
             |                         Firestore
        Sync Queue                     Functions
             |                         FCM
             |
        Offline Work
```

Cloud:

``` text
Firebase Auth
     +
Firestore
     +
Cloud Functions
     +
FCM
```

Future:

``` text
Admin Web → Same Firebase Backend
```

------------------------------------------------------------------------

# 211. Final Product Philosophy

The final application must feel like a:

> **Simple, fast and reliable laundry business operating app.**

It must not feel like a large enterprise ERP.

The highest-priority product principles are:

``` text
FAST
SIMPLE
OFFLINE
SECURE
ACCURATE
EASY TO USE
```

The application must let TREAT HOSPITALITY SERVICES operate laundry
orders efficiently while preserving a complete and trustworthy
historical record.

The most important historical chain is:

``` text
WHAT CUSTOMER ORDERED
        ↓
WHAT LAUNDRY WAS ACTUALLY RECEIVED
        ↓
WHAT WAS FINALLY BILLED
        ↓
WHAT GST WAS APPLIED
        ↓
WHAT WAS PAID
        ↓
WHAT REMAINS DUE
        ↓
HOW THE ORDER WAS COMPLETED
```

None of these historical facts should be silently lost or rewritten.

------------------------------------------------------------------------

# 212. Final V1 Boundary

V1 builds exactly the operational capabilities required for TREAT
HOSPITALITY SERVICES' laundry business:

-   Authentication
-   Controlled Admin
-   Staff management
-   Customer management
-   Personal/business customers
-   Customer deactivation
-   Items
-   Services
-   Pricing
-   Customer orders
-   Walk-in orders
-   Pickup by business
-   Customer drop-off
-   Delivery by business
-   Customer self-collection
-   Status tracking
-   Received laundry verification
-   Invoice finalization
-   Editable final quantities
-   Editable final prices
-   Additional charges
-   GST ON/OFF
-   Final invoices
-   Payment ledger
-   Paid/due tracking
-   Weekly/monthly/yearly reports
-   Offline SQLite operation
-   Firebase cloud synchronization
-   Security and business isolation
-   Historical data integrity

Anything outside this boundary requires an explicit product decision
before being added.

------------------------------------------------------------------------

# 213. Final Definition of Done

The product is ready for V1 only when the implementation satisfies all
applicable requirements in this document and passes:

``` text
TypeScript typecheck
Lint
Unit tests
Domain tests
SQLite/repository tests
Sync tests
Firebase Rules tests
Cloud Function tests
Financial integrity tests
Authorization tests
Invoice tests
Report/XLSX tests
Offline/restart tests
Android build/export
Real-device or emulator validation
```

Where an environment prevents a test from running, the limitation must
be explicitly documented rather than treated as a pass.

------------------------------------------------------------------------

# 214. Final Requirement Priority

## P0 --- Must Never Fail

-   Authentication security
-   Role/business isolation
-   Customer isolation
-   Historical financial integrity
-   Invoice finalization integrity
-   Payment integrity
-   No overpayment
-   Offline data preservation
-   Duplicate prevention
-   Protected-field enforcement

## P1 --- Core V1

-   Orders
-   Pickup/drop-off
-   Delivery/customer collection
-   Received laundry
-   Finalization
-   Invoice
-   Staff
-   Customers
-   Items/services/prices
-   GST
-   Reports
-   Sync

## P2 --- Supporting V1

-   Notifications
-   Sync diagnostics
-   Additional dashboard summaries
-   Optional invoice presentation settings

## Future

-   Web Admin
-   Online gateway
-   WhatsApp/SMS
-   Storage/photos
-   GPS
-   Route optimization
-   QR/barcode
-   Multi-business/branch
-   Loyalty/coupons
-   Advanced analytics/accounting
-   Separate delivery module

------------------------------------------------------------------------

# 215. Final Consolidated Data Integrity Rule

The system must always preserve this principle:

> **Current settings control future operations; historical snapshots
> control historical records.**

Therefore:

-   Current customer profile → future orders.
-   Historical customer snapshot → old orders/invoices.
-   Current price → future orders.
-   Historical order/final rate → old orders/invoices/reports.
-   Current GST default → future applicable orders.
-   Historical GST snapshot → old invoices/reports.
-   Current business settings → future invoices.
-   Historical business snapshot → old invoices.
-   New payment → payment ledger.
-   Old payment → immutable history.
-   New status → append-only status history.
-   Final invoice → protected financial snapshot.

This is the authoritative business-data rule for the application.

------------------------------------------------------------------------

# 216. Final Consolidated Scope Statement

**TREAT HOSPITALITY SERVICES Laundry Management App V1** is an
offline-first Android laundry operations application backed by Firebase
and powered locally by SQLite.

It supports registered customers, personal customers, business
customers, walk-in orders, staff operations, controlled Admin
management, collection and return choices, actual received-laundry
verification, final invoice preparation, GST, additional charges,
payments, due tracking, customer order tracking, reporting,
synchronization, and strong historical data integrity.

The system deliberately avoids unrelated ERP features and is designed so
that a future Admin Web application can use the same backend without
rebuilding the business data layer.

**End of Final SRS --- V4.0**


------------------------------------------------------------------------

# 217. V5.0 Binding Authority and Decision Register

This section converts all previously open/recommended implementation
language into mandatory V1 requirements.

The following principles are binding:

1.  The V4.0 requirements remain valid unless explicitly superseded by
    V5.0.
2.  V5.0 decisions override any earlier conflicting wording.
3.  `Recommended`, `Suggested`, `May`, or `Where appropriate` language
    in earlier sections is treated as optional only when V5.0 does not
    explicitly make the behavior mandatory.
4.  No developer, AI coding tool, or implementation agent may invent
    business rules where this SRS is silent. Such a case is a
    **Specification Blocker** and must be resolved before implementation
    of the affected behavior.
5.  Security rules and backend authorization are authoritative over the
    mobile UI.
6.  Server/cloud records are authoritative after successful
    synchronization; local SQLite is authoritative for the user's
    currently committed offline work until synchronization succeeds.
7.  Historical financial records are immutable except through the
    explicitly defined Admin correction/reissue process.
8.  No feature may weaken an existing P0 requirement in order to make an
    operation succeed.

## 217.1 Final Business Decisions

The V1 business decisions are:

| Area | Final V1 Decision |
|---|---|
| Staff order creation | WALK_IN only |
| Staff registered customer creation | Not allowed |
| Staff registered customer editing | Not allowed |
| Staff customer deactivation | Not allowed |
| Staff received quantity editing | Not allowed |
| Staff final quantity editing | Not allowed |
| Staff final rate editing | Not allowed |
| Staff additional-charge editing | Not allowed |
| Staff master pricing | Not allowed |
| Staff GST settings | Not allowed |
| Staff payment recording | Allowed |
| Staff payment editing | Not allowed |
| Staff invoice finalization | Allowed only when no financial/order edit is required |
| Staff GST ON/OFF at finalization | Allowed, subject to order/business GST rules |
| Admin financial editing | Allowed before finalization and through controlled correction after finalization |
| Customer cancellation | Allowed only at defined pre-processing/pre-pickup cutoffs |
| Staff/Admin cancellation | Allowed under the defined cancellation rules |
| Customer account creation | Customer self-registration only |
| Walk-in account | No registered Customer account required |
| Walk-in amount | Fixed when walk-in order is created |
| Advance payment | Allowed before final invoice |
| Final invoice visibility | Customer/Staff/Admin after invoice finalization |
| Final invoice correction | Admin controlled only |
| Historical invoice | Original version preserved |
| Customer deactivation | Soft deactivation; history retained |
| Staff deactivation | Soft deactivation; history retained |
| Firebase environments | Separate Development, Staging/Test, Production configuration |
| Production backups | Mandatory |
| Sensitive logging | Prohibited |

------------------------------------------------------------------------

# 218. Final Role Permission Contract

The implementation must maintain a single authoritative permission matrix.

## 218.1 Permission Matrix

| Operation | CUSTOMER | STAFF | ADMIN |
|---|---:|---:|---:|
| Register own account | YES | NO | NO |
| Login | YES | YES | YES |
| Edit own profile | YES | NO | YES for own Admin profile |
| Create registered Customer | NO | NO | NO |
| Deactivate Customer | NO | NO | YES |
| Reactivate Customer | NO | NO | YES |
| Create Staff | NO | NO | YES |
| Edit Staff | NO | NO | YES |
| Deactivate Staff | NO | NO | YES |
| Create WALK_IN order | NO | YES | YES |
| Create registered Customer order | YES | NO | YES where operationally required |
| Edit received quantity | NO | NO | YES |
| Edit final quantity | NO | NO | YES |
| Edit final rate | NO | NO | YES |
| Add/remove final billable line | NO | NO | YES |
| Add/edit additional charge | NO | NO | YES |
| Select GST ON/OFF at invoice finalization | YES for preference only | YES within finalization rule | YES |
| Change GST settings | NO | NO | YES |
| Change master price | NO | NO | YES |
| Process operational statuses | Own permitted order/business scope | YES | YES |
| Cancel order | Limited | YES | YES |
| Record payment | NO | YES | YES |
| Edit old payment | NO | NO | NO |
| Controlled payment reversal/correction | NO | NO | YES |
| Finalize invoice without edits | NO | YES | YES |
| Finalize invoice with financial edits | NO | NO | YES |
| View own orders | YES | NO | YES |
| View business operational orders | NO | YES | YES |
| View all customer records | NO | NO | YES |
| View another customer's data | NO | Only operational minimum required | YES within business |
| Export reports | NO | No unrestricted financial export | YES |
| Change Business Settings | NO | NO | YES |
| Change Invoice Settings | NO | NO | YES |
| Change role/businessId | NO | NO | NO through client UI |
| Access another business | NO | NO | NO |

## 218.2 Permission Enforcement

Every protected mutation must validate:

``` text
Authenticated UID
    ↓
Trusted user profile
    ↓
Role
    ↓
Business ID
    ↓
Target record business ID
    ↓
Specific permission
    ↓
Record state
    ↓
Allowed transition
    ↓
Mutation
```

A hidden/disabled button is never considered authorization.

------------------------------------------------------------------------

# 219. Walk-In Order Contract

A walk-in order is a distinct V1 source:

``` text
source = WALK_IN
```

It is not equivalent to a Customer-created order.

## 219.1 Required Walk-In Data

At minimum:

``` text
orderId
businessId
source
customerNameAtOrder
customerPhoneAtOrder
customerAddressAtOrder
createdBy
createdAt
item/service details
fixedAmount
paymentStatus
orderStatus
```

`customerId` may be NULL for a walk-in.

`userId` must remain NULL unless the walk-in is explicitly associated
with an already registered Customer under an authorized Admin workflow.

## 219.2 Walk-In Operational Behavior

A walk-in:

- does not require account registration;
- does not enter Customer pickup scheduling;
- does not require the normal laundry collection workflow;
- does not require received-quantity verification;
- has its amount fixed at creation;
- retains the Staff/Admin creator identity;
- can later receive payment entries;
- remains in history after payment.

The exact UI may use a simple operational status such as:

``` text
READY_FOR_COLLECTION
COLLECTED
CANCELLED
```

The implementation must not reuse a processing status such as
`PROCESSING` for a walk-in when no laundry-processing workflow exists.

Payment status remains independent:

``` text
PENDING
PARTIALLY_PAID
PAID
```

A walk-in may be considered financially settled when the ledger equals
the fixed amount.

------------------------------------------------------------------------

# 220. Final Order State Machines

Order state is determined by the selected collection and return methods.

## 220.1 Pickup by Us + Delivery by Us

``` text
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

## 220.2 Pickup by Us + Customer Pickup

``` text
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

## 220.3 Customer Drop-Off + Delivery by Us

``` text
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

## 220.4 Customer Drop-Off + Customer Pickup

``` text
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

The implementation must use one canonical enum set. Display labels may be
localized, but stored states must not be ambiguous.

## 220.5 Allowed Transitions

Only explicitly defined next transitions are allowed.

The system must reject:

- forward jumps;
- backward jumps;
- same-status mutations where the operation is not idempotent;
- transitions from final states;
- delivery when the order is not ready;
- collection when the order is not ready for collection;
- processing before the laundry has been received/picked up as required.

Idempotent retry of the exact same committed transition may return the
existing result rather than creating a duplicate history row.

## 220.6 Final States

For normal orders:

``` text
DELIVERED
COLLECTED
CANCELLED
```

A final state cannot be changed by ordinary Staff/Customer actions.

Admin correction of financial data does not reopen the operational order
state.

------------------------------------------------------------------------

# 221. Cancellation and Physical Return Contract

Cancellation is separate from physical return.

## 221.1 Customer Cancellation Cutoffs

Customer may cancel:

``` text
Pickup by us:
    before PICKED_UP

Customer drop-off:
    before PROCESSING
```

After the cutoff, Customer cancellation is rejected.

## 221.2 Staff/Admin Cancellation

Staff/Admin may cancel when the order is in an allowed cancellable state
or when a received-vs-ordered mismatch is identified and the customer
does not agree to proceed.

The backend must enforce the cutoff.

## 221.3 Physical Return

If garments/laundry are already physically with the business, cancellation
must preserve a return task:

``` text
returnRequired = true
returnStatus = RETURN_PENDING
```

After return:

``` text
returnStatus = RETURNED
```

If no physical laundry is held:

``` text
returnRequired = false
returnStatus = NOT_REQUIRED
```

The return task must record:

``` text
returnStatus
returnedAt
returnedBy
returnNote
```

The return record does not delete or alter the cancelled order.

------------------------------------------------------------------------

# 222. Final Received-Laundry Contract

For normal laundry orders, actual received laundry is a separate dataset
from the original request.

For every relevant line:

``` text
orderedQuantity
receivedQuantity
finalQuantity
finalRate
```

The following rules are mandatory:

1.  `orderedQuantity` is never overwritten.
2.  `receivedQuantity` is the actual physical quantity accepted by the
    laundry.
3.  `finalQuantity` is the quantity used for billing.
4.  Staff cannot edit received/final quantities.
5.  Admin may edit them before invoice finalization.
6.  Finalization must preserve the values used for the invoice.
7.  Current Item/Service master changes must never rewrite historical
    names or prices.

If received quantity differs from ordered quantity, the difference must
remain visible in the order history.

------------------------------------------------------------------------

# 223. Final Invoice Finalization Contract

## 223.1 Invoice Gate

A normal laundry invoice cannot be finalized until the order has reached
the business-defined finalization point after the laundry is received and
the operational processing requirement has been satisfied.

The UI must not expose a working invoice-generation action before this
gate.

## 223.2 Staff Finalization

Staff may finalize only if:

- all received quantities are already correct;
- all final quantities are already correct;
- all final rates are already correct;
- all billable lines are already correct;
- no additional charge needs to be added, removed, or edited;
- no financial edit is required;
- GST ON/OFF selection is the only final choice needed, if applicable.

Staff may then:

``` text
Open order
 ↓
Review finalization data
 ↓
Confirm GST ON/OFF
 ↓
Generate/finalize invoice
 ↓
Invoice becomes FINALIZED
```

## 223.3 Admin Finalization

Admin may finalize with financial edits:

``` text
Open order
 ↓
Review original + received data
 ↓
Edit final quantity/rate/lines
 ↓
Add/remove additional charges
 ↓
Select GST
 ↓
Review final calculation
 ↓
Finalize
```

Finalization must be atomic.

No state may exist where the invoice is marked finalized but only part of
its financial snapshot was saved.

## 223.4 Customer Visibility

Before finalization:

- Customer may see permitted order/status information.
- Customer must not see a finalized invoice because it does not yet
  exist.
- Customer may see an estimate where applicable.

Immediately after successful finalization:

- Customer can see the finalized invoice.
- Customer can access the invoice document/download action where the
  document generation flow is enabled.
- Staff and Admin can also view the same finalized financial snapshot.

The same invoice snapshot must be used for all three roles.

------------------------------------------------------------------------

# 224. Advance Payment Before Final Invoice

Advance payment is explicitly supported.

The financial sequence is:

``` text
Advance Payment
 ↓
Order remains operationally incomplete
 ↓
Laundry processing/finalization gate
 ↓
Final Invoice
 ↓
Advance automatically applied
 ↓
Remaining Due
```

## 224.1 Advance Payment Visibility

A payment may be recorded before the final invoice exists.

That payment:

- belongs to the order;
- is append-only;
- identifies the Staff/Admin who recorded it;
- does not finalize the order;
- does not make the final invoice visible to Customer;
- does not bypass processing/finalization status requirements.

## 224.2 Final Reconciliation

After final invoice:

``` text
paidAmount = sum(valid payment ledger entries)
dueAmount = finalAmount - paidAmount
```

If:

``` text
paidAmount < finalAmount
```

then:

``` text
paymentStatus = PARTIALLY_PAID
dueAmount > 0
```

If:

``` text
paidAmount = finalAmount
```

then:

``` text
paymentStatus = PAID
dueAmount = 0
```

If an advance causes:

``` text
paidAmount > finalAmount
```

the invoice finalization must not silently accept the overpayment.

Admin must resolve the excess through the controlled payment correction/
refund process. No automatic deletion or silent offset is permitted.

## 224.3 No Invoice Bypass

This is mandatory:

``` text
Advance payment ≠ Invoice finalized
Payment status ≠ Order completion
```

A payment must never unlock the Customer invoice before finalization.

------------------------------------------------------------------------

# 225. Payment Ledger and Correction Contract

Payments are append-only financial events.

Each payment contains:

``` text
paymentId
orderId
businessId
amountPaise
paymentMethod
paymentDate
recordedBy
note
createdAt
syncId
```

## 225.1 Payment Recording

Staff/Admin may add a new payment.

The system must atomically:

1. validate authorization;
2. validate amount;
3. validate order/business relationship;
4. validate payment method;
5. validate financial state;
6. write payment;
7. update/recalculate payment summary;
8. create sync work;
9. commit the transaction.

## 225.2 No Direct Edit

No user may directly edit:

- amount;
- method;
- payment date;
- recorder;
- original payment identity.

## 225.3 Admin Correction

If a payment is wrong, Admin must use a controlled correction process.

The preferred model is an append-only adjustment/reversal event rather than
mutating the original payment.

Example:

``` text
Original Payment
      ↓
Admin Correction
      ↓
Reason required
      ↓
Reversal / Adjustment entry
      ↓
New valid ledger total
```

The original payment remains visible in audit history.

The net financial calculation must use valid ledger entries and controlled
adjustments.

------------------------------------------------------------------------

# 226. Financial Calculation, Money and GST Precision

All persisted financial values must use integer paise.

Example:

``` text
₹590.50 = 59050 paise
```

JavaScript/TypeScript floating-point values must never be the authoritative
persisted representation of money.

## 226.1 Multiplication

Line subtotal:

``` text
quantity × ratePaise
```

The result must be an integer paise value.

## 226.2 GST

For GST:

``` text
taxableSubtotal = finalLineSubtotal + additionalChargesTotal
gstAmount = taxableSubtotal × gstRate / 100
```

GST rounding must use **half-up rounding** to the nearest paise.

The exact same domain calculation must be used by:

- SQLite-side application calculations;
- cloud/backend validation;
- invoice rendering;
- reports;
- tests.

## 226.3 GST OFF

When GST is OFF:

``` text
gstApplied = false
gstRate = 0
gstAmount = 0
```

## 226.4 Final Amount

``` text
finalAmount = taxableSubtotal + gstAmount
```

The stored invoice snapshot must contain the values actually used for
billing.

------------------------------------------------------------------------

# 227. Invoice Immutability, Correction and Reissue

The invoice lifecycle is:

``` text
NOT_FINALIZED
      ↓
FINALIZED
      ↓
(optional) CORRECTION_REQUEST
      ↓
ADMIN_REVIEW
      ↓
REVISED_FINALIZED
```

## 227.1 Normal Edit Window

Before finalization:

- Admin may edit financial fields.
- Staff may only finalize if no such edit is required.

After finalization:

- Staff cannot edit.
- Customer cannot edit.
- Admin cannot silently mutate the finalized snapshot.

## 227.2 Admin Post-Delivery Edit Rule

Admin may have a controlled edit/correction action after delivery/collection
under these conditions:

### If due is still pending

Admin correction remains available, subject to authorization and audit
requirements.

### If due is fully paid

Admin correction is available only during the first **7 calendar days**
after the delivery/collection completion timestamp.

After 7 calendar days and with no outstanding due:

``` text
financialEdit = LOCKED
```

The lock is enforced by backend rules/functions, not merely by hiding the
button.

## 227.3 Correction Requirements

Every post-finalization correction requires:

``` text
correctionId
orderId
originalInvoiceId
reason
requestedBy
approvedBy
createdAt
```

The original invoice snapshot remains preserved.

A revised invoice receives its own immutable invoice identity and a
relationship to the original:

``` text
originalInvoiceId
revisedInvoiceId
revisionNumber
```

The UI must clearly identify the current valid invoice and retain access
to the historical original for authorized Admin audit purposes.

## 227.4 Invoice Numbering

Invoice numbers must be unique within the business.

A revision must never reuse the original immutable invoice number.

Recommended relationship:

``` text
INV-000123
INV-000123-R1
INV-000123-R2
```

The exact prefix is controlled by Invoice Settings, but the uniqueness
and revision relationship are mandatory.

The implementation must reserve invoice identities transactionally so
two offline/online devices cannot create the same authoritative invoice
number.

------------------------------------------------------------------------

# 228. Order and Financial Audit Trail

Audit data must be append-only wherever historical accountability matters.

At minimum record:

``` text
createdAt
createdBy
updatedAt
lastUpdatedBy
```

For status:

``` text
fromStatus
toStatus
changedBy
changedAt
reason
```

For cancellation:

``` text
cancelledBy
cancelledAt
cancellationReason
```

For invoice correction:

``` text
correctionId
reason
requestedBy
approvedBy
createdAt
```

For payments:

``` text
recordedBy
createdAt
```

For additional charges:

``` text
createdBy
createdAt
note
```

Audit logs must not contain passwords, authentication tokens, or
unnecessary sensitive data.

------------------------------------------------------------------------

# 229. Concurrency, Versioning and Stale Writes

The application must support multiple Staff/Admin devices.

Mutable records must carry a version/revision mechanism.

Example:

``` text
version = 1
```

Every successful authoritative mutation increments the version.

A write based on a stale version must be rejected or routed into an
explicit conflict path.

## 229.1 Financial Records

Financial records must never be automatically merged when the merge
could change:

- final amount;
- GST;
- final quantity;
- final rate;
- payment total;
- invoice identity.

Such conflicts require authoritative resolution.

## 229.2 Status History

Status history is append-only.

Two devices attempting the same valid transition must result in one
logical transition, not duplicate business events.

## 229.3 Master Data

Items, Services, Prices, Business Settings, and GST Settings must use
version-aware synchronization.

A stale local catalog must never silently overwrite a newer authoritative
cloud version.

------------------------------------------------------------------------

# 230. Offline Sync Contract

Offline operation is mandatory for core Staff/Admin workflows.

## 230.1 Local Commit Rule

For an offline mutation:

``` text
Validate
 ↓
SQLite transaction
    ├── domain record change
    ├── audit/status/payment data
    └── sync queue entry
 ↓
Commit
 ↓
UI updates immediately
```

If any part fails, the whole local transaction rolls back.

## 230.2 Sync Queue

Every synchronizable mutation requires an idempotency identity.

At minimum:

``` text
syncId
entityId
operation
businessId
createdAt
attemptCount
status
lastErrorCode
lastAttemptAt
```

A retry must not create a duplicate financial/order record.

## 230.3 Idempotency

Cloud processing must treat a repeated `syncId` for the same operation as
the same logical operation.

The backend must return the existing authoritative result when a committed
operation is retried.

## 230.4 Auth State

When authentication is unavailable:

- queued work is paused;
- the app must not grant new privileges;
- already committed safe local work remains preserved;
- synchronization resumes after trusted authentication is restored.

## 230.5 Conflict Policy

The final conflict policy is:

``` text
Non-financial catalog:
    version-aware server-authoritative update

Operational status:
    state-machine validation + idempotent transition

Customer profile:
    authoritative version check

Final invoice:
    no automatic merge

Payment:
    append-only ledger + authoritative validation

Historical audit:
    append-only, never merged destructively
```

------------------------------------------------------------------------

# 231. Firebase Architecture and Backend Contract

Firebase remains the cloud backend.

The mobile application must use:

``` text
Firebase Authentication
Cloud Firestore
Cloud Functions
FCM where enabled
```

Firebase Admin/service-account credentials must never be bundled into the
Expo application.

## 231.1 Client vs Trusted Backend

Client may:

- authenticate;
- read permitted data;
- write permitted offline operations;
- request trusted backend operations.

Trusted backend must enforce:

- role;
- businessId;
- ownership;
- state transitions;
- financial calculations/validation;
- invoice uniqueness;
- idempotency;
- sensitive mutations.

## 231.2 Firestore Rules

Rules must be deny-by-default.

Rules must prevent:

- cross-business access;
- customer access to another customer's data;
- Staff management;
- unauthorized pricing/GST changes;
- direct final invoice mutation;
- payment deletion;
- role escalation;
- businessId reassignment.

Rules tests are release-blocking.

## 231.3 Cloud Functions

Functions are required where privileged logic cannot safely be performed
directly by the client.

Examples:

- Staff account creation;
- controlled invoice identity reservation;
- privileged financial operations;
- controlled correction/reissue;
- server-authoritative operations;
- notification triggers where implemented.

------------------------------------------------------------------------

# 232. Firebase Environment Separation

The development workflow must support three logical environments:

``` text
DEVELOPMENT
     ↓
STAGING / TEST
     ↓
PRODUCTION
```

Each environment must have its own Firebase configuration.

## 232.1 Development

The developer may use the currently created Firebase project for
development.

Development data must never be treated as production data.

## 232.2 Staging

Staging is used for:

- integration testing;
- Firestore Rules tests;
- Cloud Function tests;
- sync tests;
- release candidate validation.

## 232.3 Production

Production uses the dedicated production Firebase project/configuration.

The production app must not contain:

- test customer records;
- fake orders;
- development Staff accounts;
- test payments;
- staging credentials.

Environment values must be injected through build configuration.

The application must fail safely if required production configuration is
missing or malformed.

------------------------------------------------------------------------

# 233. Backup, Recovery and Disaster Recovery

Production data must have an explicit recovery strategy.

## 233.1 Firestore Backup

Production Firestore must be backed up automatically using an appropriate
Firebase/Google Cloud backup mechanism.

Backup operations must be monitored.

## 233.2 Retention

The production owner must define a retention period before launch.

The minimum release requirement is:

``` text
Automated scheduled backup
+
Documented retention period
+
Documented restore procedure
```

The retention value must be configured as an environment/operations
decision rather than hidden in application code.

## 233.3 Restore Procedure

A documented restore runbook must specify:

1. Identify incident.
2. Freeze affected mutation path if necessary.
3. Identify last known good backup.
4. Restore into a controlled recovery environment first.
5. Validate record counts and financial integrity.
6. Validate Security Rules/access.
7. Compare critical financial data.
8. Approve production recovery.
9. Record incident and recovery timestamp.

## 233.4 Accidental Deletion

Protected financial data must not rely on mobile deletion for recovery.

Accidental deletion must be recoverable through the backup/recovery
process.

## 233.5 Disaster Recovery

Production operations must document:

``` text
RPO = maximum acceptable data-loss window
RTO = maximum acceptable service-restoration window
```

Exact numeric RPO/RTO values are an operations decision and must be
approved before production launch.

------------------------------------------------------------------------

# 234. Observability and Diagnostics

Production must provide enough diagnostics to identify failures without
leaking customer data.

## 234.1 Required Observability

At minimum:

- crash reporting;
- application error reporting;
- sync failure diagnostics;
- Cloud Function logs;
- backend error monitoring;
- performance monitoring where available;
- critical-operation monitoring.

## 234.2 Required Diagnostic Context

Safe diagnostic fields may include:

``` text
environment
appVersion
platform
operationName
syncId
entityType
non-sensitive entity identifier where necessary
errorCode
timestamp
networkState
```

## 234.3 Prohibited Logs

Logs must never contain:

- passwords;
- authentication tokens;
- Firebase Admin credentials;
- full payment secrets;
- unnecessary customer address;
- unnecessary phone/email;
- private internal notes;
- complete financial payloads unless explicitly required and secured.

Errors shown to users must be safe and understandable.

Raw Firebase/server stack traces must not be exposed to customers.

------------------------------------------------------------------------

# 235. Privacy and Data Governance

The application processes customer identity/contact information and
business information.

## 235.1 Data Minimization

Collect only information required for:

- customer identity;
- contact;
- service fulfillment;
- order operations;
- billing;
- support;
- legal/business requirements.

Optional fields must not be made mandatory without a business reason.

## 235.2 Customer Privacy Boundary

Customer can access only:

``` text
Own profile
Own orders
Own order statuses
Own finalized invoices
Own permitted payment information
```

Customer cannot access:

``` text
Other customers
Staff private information
Admin information
Internal notes
Internal audit records
Sync diagnostics
Other business data
```

## 235.3 Privacy Notice

Production launch requires a customer-facing privacy notice describing:

- data collected;
- why it is collected;
- how it is used;
- who can access it;
- retention principles;
- support/contact route;
- applicable rights/processes.

## 235.4 Consent

Where consent is legally or operationally required, the UI must capture
and preserve the applicable consent record.

Consent text must be versioned so a later policy change does not rewrite
the historical fact that a user accepted an earlier version.

------------------------------------------------------------------------

# 236. Account Deletion, Deactivation, Retention and Export

## 236.1 Customer Deactivation

Deactivation is a soft state:

``` text
active = false
```

A deactivated Customer:

- cannot place a new order;
- retains historical orders;
- retains permitted historical invoice/payment visibility;
- cannot bypass the restriction through direct API calls.

## 236.2 Staff Deactivation

A deactivated Staff account:

- cannot authenticate for new protected work;
- cannot create new orders;
- cannot record new payments;
- cannot change operational status;
- retains historical `createdBy`/`recordedBy` references.

Historical actor identity must not be replaced with NULL merely because
the account was deactivated.

## 236.3 Account Deletion

Account deletion must not physically delete required financial history.

If a legal/business deletion request requires personal-data removal,
the implementation must use a documented anonymization/deletion policy
that preserves the minimum records legally/business-required.

## 236.4 Data Export

Customer export, where provided, must be limited to the Customer's own
permitted data.

Admin export is limited to the assigned business and must respect report
authorization.

------------------------------------------------------------------------

# 237. Local SQLite Security and Lifecycle

SQLite is an operational/offline store, not a second independent
financial authority.

## 237.1 Local Data

Minimize sensitive local storage.

Never store:

- passwords;
- Firebase Admin credentials;
- long-lived privileged secrets.

Authentication/session secrets that require persistence must use secure
platform storage.

## 237.2 Shared Device

The app must clear protected session state on logout and must prevent the
next user from seeing the previous user's protected data.

Cached business data may only remain if it cannot expose protected data
to an unauthorized session.

## 237.3 SQLite Migrations

Every schema change must use a numbered migration.

Example:

``` text
v1
v2
v3
...
```

Migrations must be:

- deterministic;
- tested;
- transactional where supported;
- backward-aware where required for the deployed app version.

A migration failure must not silently destroy existing business data.

------------------------------------------------------------------------

# 238. Durable Identity and ID Contract

All persisted IDs must be collision-resistant and suitable for offline
creation.

No process-local counter may be used as a durable persisted identity.

This explicitly prohibits designs such as:

``` text
OI-1
OI-2
OI-3
```

as authoritative persisted IDs.

## 238.1 ID Requirements

Each entity must have a stable immutable ID.

At minimum:

``` text
userId
staffId
customerId
businessId
itemId
serviceId
priceId
orderId
orderItemId
chargeId
invoiceId
paymentId
statusHistoryId
syncId
correctionId
```

The approved implementation must use the final documented ID alphabet
and validation pattern consistently in:

- generator;
- validator;
- tests;
- database constraints;
- backend validation.

Any previous mismatch between the documented alphabet and implementation
must be corrected before production.

------------------------------------------------------------------------

# 239. Search, Pagination and Query Safety

Search must be business-scoped.

At minimum:

``` text
businessId = currentBusinessId
```

must be applied before exposing Staff/Admin business data.

Customer search must support relevant fields without exposing other
businesses.

Large collections must use pagination.

The app must not assume that an entire Firestore collection can be
downloaded into memory.

Reports and lists must have bounded query sizes and documented pagination
behavior.

Search/index requirements must be documented for:

- orders;
- customers;
- staff;
- items;
- services;
- payments;
- reports.

------------------------------------------------------------------------

# 240. Master Data and Price Versioning

Items, Services, and Prices are master data.

## 240.1 Soft Deactivation

Used records must not be hard-deleted if historical records reference
them.

Use:

``` text
active = false
```

to stop future selection.

## 240.2 Historical Snapshot

At order/finalization time preserve:

``` text
itemName
serviceName
rate
```

where required for historical interpretation.

Changing a current price must not alter an old invoice.

## 240.3 Price Changes

A price change affects future applicable operations only.

The system must not rewrite:

- old order prices;
- final invoice rates;
- historical reports.

------------------------------------------------------------------------

# 241. Business Settings and Identity Separation

Two concepts must remain separate:

### Admin Profile

The personal account/profile of the Admin.

### Business Settings

The canonical business identity used by the application and invoices.

Business Settings include, as applicable:

``` text
Legal/Business Name
Display Name where configured
Address
PIN Code
Primary Phone
Alternative Phone
Email
GSTIN
Default GST Rate
Invoice Prefix
```

The business name used by an invoice must come from the appropriate
historical business snapshot.

Changing current Business Settings must never rewrite an old invoice.

The business's legal identity must not be inferred from a Customer's
Business Name.

------------------------------------------------------------------------

# 242. Invoice Document Contract

A generated invoice document must represent the exact finalized invoice
snapshot.

It must not recalculate using current:

- prices;
- GST settings;
- customer profile;
- business settings.

The invoice must use historical snapshots.

Minimum invoice identity:

``` text
invoiceNumber
invoiceId
orderId
invoiceCreatedAt
```

Business snapshot:

``` text
businessName
address
PIN
phone
alternativePhone where configured
email
GSTIN
```

Customer snapshot:

``` text
customerName
phone
address
businessName where applicable
```

Financial snapshot:

``` text
line items
final quantities
final rates
line subtotals
additional charges
GST
final amount
paid amount
due amount
```

Payment information must reflect the ledger state according to the
invoice-document rules defined for the release.

If a revised invoice exists, the document must clearly identify the
revision where required.

------------------------------------------------------------------------

# 243. Reports: Source of Truth and Reconciliation

Reports must use finalized financial data.

The authoritative source is:

``` text
Final Invoice Snapshot
+
Valid Payment Ledger
+
Status History
```

Reports must not use estimated order amounts as final revenue.

## 243.1 Cancelled Orders

Cancelled orders are excluded from revenue/final-sales totals unless a
specific report is explicitly a cancellation report.

Cancelled order counts remain available separately.

## 243.2 Payment Totals

Payment-method totals must be reconciled from the complete payment ledger.

The report engine must not silently undercount an order because a partial
child-payment dataset was supplied.

Repository/report boundaries must guarantee complete payment data or
perform explicit per-order reconciliation.

## 243.3 Periods

All report periods use:

``` text
Asia/Kolkata
```

for business-facing date boundaries.

Cloud/server timestamps may be stored in UTC, but display and business
period calculations use the configured India timezone.

------------------------------------------------------------------------

# 244. Date, Time and Clock Contract

The business timezone is:

``` text
Asia/Kolkata
```

Persist authoritative timestamps in a consistent server-compatible
timestamp representation.

Display dates using Indian English style:

``` text
14 September 2026
```

Client clock time must not be treated as authoritative for financial
audit timestamps.

The backend should provide authoritative timestamps for committed cloud
operations.

Client-generated offline events must preserve both:

``` text
clientCreatedAt
serverAcceptedAt
```

where needed for audit/debugging.

Clock skew must not allow a user to bypass:

- 7-day invoice correction window;
- payment ordering;
- status transition timing;
- audit chronology.

------------------------------------------------------------------------

# 245. Notification Contract

FCM is optional for V1 presentation but the data model must support
notifications.

Notification failure must never roll back a successful order, payment,
status, or invoice operation.

Notifications must be:

- role-aware;
- business-scoped;
- non-sensitive in lock-screen content;
- retryable where appropriate;
- safe to duplicate from a business perspective.

A notification is informational, not the source of truth.

The app must always derive actual order/invoice/payment state from the
authoritative data model.

------------------------------------------------------------------------

# 246. Error Handling Contract

Errors are categorized:

``` text
VALIDATION_ERROR
AUTH_ERROR
PERMISSION_ERROR
STATE_ERROR
CONFLICT_ERROR
NETWORK_ERROR
SYNC_ERROR
FINANCIAL_ERROR
SERVER_ERROR
UNKNOWN_ERROR
```

Each error must have:

``` text
stable errorCode
safe user message
developer diagnostic context
retryability classification
```

The UI must distinguish:

- action rejected;
- action pending sync;
- action succeeded locally;
- action succeeded authoritatively;
- action failed and was rolled back.

The app must never show "Success" for a transaction that was rolled back.

------------------------------------------------------------------------

# 247. Customer Account and Registration Contract

Customer registration is self-service.

Required authentication behavior:

``` text
Register
 ↓
Email verification
 ↓
Login
 ↓
Profile completion
 ↓
Active Customer account
```

The system must validate:

- email;
- password;
- Indian mobile number;
- PIN code;
- customer type;
- business fields where applicable.

## 247.1 Personal Customer

``` text
Customer Type = PERSONAL
```

Business-only fields must be empty/NULL.

## 247.2 Business Customer

``` text
Customer Type = BUSINESS
```

Business Name and Business Type are required according to the profile
validation rules.

Switching Business → Personal must require confirmation before clearing
business fields.

------------------------------------------------------------------------

# 248. Customer Data Isolation

Customer queries must be scoped by authenticated customer identity.

A Customer must never be able to request another customer's record by
changing:

``` text
customerId
orderId
businessId
```

in a client request.

The backend must derive/validate ownership from trusted authentication
context.

A customer URL/route parameter is not an authorization mechanism.

------------------------------------------------------------------------

# 249. Staff Operational Data Boundary

Staff must see only the operational information required to perform work.

Staff may view:

- customer name;
- relevant mobile number;
- relevant address for collection/delivery;
- order details;
- payment status;
- finalized invoice.

Staff must not receive Admin-only:

- staff management data;
- business configuration editing rights;
- pricing/GST administration;
- audit correction controls;
- unrestricted reports unless separately authorized.

------------------------------------------------------------------------

# 250. App Update and Compatibility Contract

Production releases must have an application version.

The backend may enforce a minimum supported version when a breaking
migration/security issue requires it.

Version policy:

``` text
currentVersion
minimumSupportedVersion
```

If a mandatory upgrade is required:

``` text
Authenticated user
 ↓
Safe upgrade screen
 ↓
No destructive local-data action
```

An app update must not silently erase pending offline work.

Before schema-breaking release:

- migration tested;
- pending sync behavior tested;
- rollback/recovery plan documented.

------------------------------------------------------------------------

# 251. Build and Secret Management

Production builds must:

- use production environment configuration;
- exclude development debugging tools;
- exclude test credentials;
- exclude service-account/private keys;
- have deterministic version/build numbers;
- pass TypeScript and lint gates.

The Expo client may contain Firebase public client configuration.

It must never contain Firebase Admin credentials.

Sensitive operational secrets belong only in trusted server-side
configuration.

------------------------------------------------------------------------

# 252. Accessibility and UI Quality Contract

All production screens must:

- support small Android screens;
- handle long customer names;
- handle long addresses;
- handle long business names;
- avoid text clipping;
- avoid horizontal overflow;
- provide correct keyboard types;
- provide visible validation errors;
- support readable touch targets;
- maintain sufficient contrast;
- avoid relying only on color for state;
- handle loading/empty/error/offline states.

Core workflows must remain usable without network connectivity where the
SRS defines them as offline-capable.

------------------------------------------------------------------------

# 253. Transaction Boundaries

The following operations must be atomic locally:

### Order creation

``` text
Order
+
Order Items
+
Initial Status History
+
Sync Queue
```

### Payment

``` text
Payment
+
Payment Summary/Reconciliation
+
Sync Queue
```

### Status transition

``` text
Order Status
+
Status History
+
Sync Queue
```

### Invoice finalization

``` text
Final Invoice Snapshot
+
Invoice Number Reservation
+
Final Item Data
+
Additional Charges
+
GST Snapshot
+
Initial Payment Reconciliation
+
Status/Invoice State
+
Sync Queue
```

A failure in any mandatory component must roll back the transaction.

------------------------------------------------------------------------

# 254. Firestore Data Integrity Contract

Firestore documents must contain enough authoritative data to validate
business boundaries.

Business-owned records must include:

``` text
businessId
```

where applicable.

Customer-owned records must include:

``` text
customerId
```

Order records must preserve both identity references and historical
snapshots.

Payment records must preserve:

``` text
businessId
orderId
recordedBy
```

Financial fields must be validated server-side.

The client must not be trusted to calculate a final financial amount
without backend validation.

------------------------------------------------------------------------

# 255. Schema Completeness Contract

Before M3 persistence implementation begins, the final schema must
explicitly define:

- table/collection name;
- primary key;
- foreign keys/references;
- nullability;
- default values;
- allowed enum values;
- indexes;
- uniqueness;
- immutable fields;
- version fields;
- audit fields;
- sync fields;
- migration number.

The local and cloud representations may differ structurally, but they must
map losslessly for every business-critical field.

No required field may exist only in UI state.

------------------------------------------------------------------------

# 256. Mandatory SQLite Corrections From Earlier Engineering Review

The previously identified implementation issues are release blockers and
are incorporated into V5.0.

The implementation must correct:

1.  Durable Order Item IDs must not use the process-local `OI-*` counter.
2.  The approved ID alphabet and regex must match exactly.
3.  Unscheduled walk-in orders must have both pickup date and pickup time
    stored as NULL.
4.  The validator/use-case boundary must distinguish Customer scheduled
    pickup from Staff WALK_IN.
5.  Payment history must be complete before aggregate payment assumptions
    are made.
6.  Payment correction/reversal behavior must follow the append-only
    adjustment model.
7.  Mutable master data must have version-aware updates.
8.  SQLite repositories must have real transaction rollback tests.
9.  Sync idempotency must be tested against duplicate retries.
10. Firestore Rules must be tested before production.
11. Cloud Functions must be tested before production.
12. Report payment aggregation must not undercount partially populated
    child datasets.
13. Client-side authorization must never replace backend authorization.
14. Financial calculations must use integer paise.

------------------------------------------------------------------------

# 257. Testing Contract --- Unit

Unit tests are mandatory for:

- money arithmetic;
- GST;
- rounding;
- order calculation;
- payment reconciliation;
- due calculation;
- status transitions;
- cancellation cutoffs;
- collection/return combinations;
- customer profile validation;
- business profile validation;
- Staff permissions;
- Admin permissions;
- invoice finalization rules;
- invoice correction rules;
- 7-day post-delivery correction lock;
- ID generation/validation;
- normalization;
- report mathematics.

Every business rule in V5.0 must have at least one positive and one
negative test where meaningful.

------------------------------------------------------------------------

# 258. Testing Contract --- Integration and Persistence

Mandatory tests:

- SQLite migrations;
- SQLite foreign keys;
- transaction commit;
- transaction rollback;
- repository mapping;
- offline create;
- offline payment;
- offline status update;
- restart with pending queue;
- duplicate sync retry;
- network recovery;
- stale version rejection;
- cross-device changes;
- incomplete payment history handling;
- invoice finalization atomicity;
- invoice number uniqueness;
- correction/reissue relationship;
- customer deactivation;
- Staff deactivation.

------------------------------------------------------------------------

# 259. Testing Contract --- Firebase Security

Rules tests must cover at least:

### Customer

- own customer document;
- another customer's document denied;
- own order;
- another customer's order denied;
- another business denied;
- finalized invoice mutation denied;
- payment deletion denied.

### Staff

- same business allowed;
- other business denied;
- Staff management denied;
- customer profile creation denied;
- received quantity edit denied;
- final rate edit denied;
- GST settings edit denied;
- price master edit denied;
- allowed payment creation allowed;
- old payment edit denied;
- unauthorized invoice correction denied.

### Admin

- same business full permitted access;
- other business denied;
- controlled correction allowed only under required conditions;
- role escalation denied;
- arbitrary businessId reassignment denied.

All Rules tests must run against the Firebase Emulator or an equivalent
authoritative security-test environment before release.

------------------------------------------------------------------------

# 260. Testing Contract --- Financial Integrity

Financial tests must include:

``` text
No payment
One payment
Multiple payments
Advance payment
Partial payment
Exact payment
Attempted overpayment
Final amount lower than advance
GST ON
GST OFF
Additional charges
Zero additional charges
Different rates
Different quantities
Received quantity != ordered quantity
Invoice finalization
Invoice correction
Invoice revision
Payment correction
Cancellation before payment
Cancellation after payment
```

Expected invariant:

``` text
No valid operation may silently lose money,
create money,
duplicate money,
or change historical financial truth.
```

------------------------------------------------------------------------

# 261. Testing Contract --- Offline and Restart

The following must be tested:

``` text
Create offline
Kill app
Restart app
Open order
Reconnect
Sync
Restart again
Verify exactly one authoritative record
```

Repeat for:

- order;
- status transition;
- payment;
- invoice finalization where offline support is allowed;
- cancellation.

A crash between local commit and sync must not lose the committed local
operation.

A crash during synchronization must not duplicate it.

------------------------------------------------------------------------

# 262. Acceptance Criteria --- Staff Finalization

The Staff invoice acceptance test is:

### Case A: No edits

``` text
Received = correct
Final quantity = correct
Rate = correct
Additional charges = none
GST selection = confirm
```

Result:

``` text
Staff may finalize
```

### Case B: Quantity edit required

Result:

``` text
Staff cannot edit
Admin required
```

### Case C: Rate edit required

Result:

``` text
Staff cannot edit
Admin required
```

### Case D: Additional charge required

Result:

``` text
Staff cannot edit
Admin required
```

### Case E: GST selection only

Result:

``` text
Staff may select GST ON/OFF
Staff may finalize
```

provided all other final financial data is already valid.

------------------------------------------------------------------------

# 263. Acceptance Criteria --- Advance Payment

Test:

``` text
Final expected amount = ₹1,000
Advance = ₹300
```

Before invoice:

``` text
Payment Status = PARTIALLY_PAID
Invoice = NOT_FINALIZED
Customer invoice access = LOCKED
```

After final invoice:

``` text
Final Amount = ₹1,200
Paid = ₹300
Due = ₹900
```

The ₹300 payment is automatically reflected against the final invoice.

If:

``` text
Final Amount = ₹300
Paid = ₹300
```

then:

``` text
Due = ₹0
Payment Status = PAID
```

If:

``` text
Final Amount = ₹250
Paid = ₹300
```

the system must not silently mark the extra ₹50 as valid invoice
settlement. Admin resolution is required.

------------------------------------------------------------------------

# 264. Acceptance Criteria --- Invoice Correction

Test:

``` text
Invoice finalized
Order delivered
Due = ₹0
```

For days 0 through 7:

``` text
Admin correction option = available
```

After more than 7 calendar days:

``` text
Admin financial edit = locked
```

If:

``` text
Due > ₹0
```

the controlled Admin correction path remains available, subject to
authorization and audit.

Any correction must:

``` text
Preserve original invoice
+
Create correction audit
+
Create revised invoice identity if reissued
+
Show current valid invoice to Customer
+
Retain original for authorized Admin history
```

------------------------------------------------------------------------

# 265. Acceptance Criteria --- People

## Staff

Admin opens:

``` text
People
 ↓
Staff
 ↓
Staff Member
```

Admin can:

- view profile;
- edit profile;
- activate;
- deactivate.

Deactivation does not delete history.

## Customers

Admin opens:

``` text
People
 ↓
Customers
 ↓
Customer
```

Business customer displays:

``` text
Customer Name
Business Name
```

Personal customer displays:

``` text
Customer Name
Personal
```

Customer detail shows only that Customer's:

- profile;
- contact;
- address;
- business information;
- status;
- orders;
- permitted payment summary.

No other Customer's data may appear.

Deactivation prevents new Customer-created orders.

------------------------------------------------------------------------

# 266. Acceptance Criteria --- Reports

Reports must use final financial data.

Test:

``` text
Estimated = ₹500
Final = ₹700
```

Revenue report must use:

``` text
₹700
```

not ₹500.

If:

``` text
Additional Charge = ₹100
GST = ₹144
```

the report must include the finalized stored values.

Historical rate changes must not change historical reports.

Payments must reconcile to the payment ledger.

------------------------------------------------------------------------

# 267. Production Release Gates

The application is **NOT production-ready** until all mandatory gates
pass.

## Gate 1 --- Documentation

- V5.0 SRS approved.
- Database schema approved.
- Firestore schema approved.
- Rules contract approved.
- Cloud Function contract approved.
- Sync protocol approved.
- Screen/route specification approved.
- Test matrix approved.
- Backup/recovery runbook approved.

## Gate 2 --- Code Quality

- TypeScript passes.
- Lint passes.
- Build succeeds.
- No debug credentials.
- No Admin secrets.
- No unsafe TODO business logic.

## Gate 3 --- Domain

- State machines pass.
- Financial calculations pass.
- Payment invariants pass.
- Invoice invariants pass.
- Permission matrix passes.

## Gate 4 --- Persistence

- SQLite migrations pass.
- Repository tests pass.
- Transaction tests pass.
- Restart tests pass.

## Gate 5 --- Backend

- Firebase Rules tests pass.
- Cloud Function tests pass.
- Idempotency tests pass.
- Cross-business tests pass.
- Concurrency tests pass.

## Gate 6 --- Offline

- Offline create works.
- Offline payment works.
- Offline status works.
- Queue retry works.
- Duplicate retry does not duplicate data.
- Crash/restart recovery works.

## Gate 7 --- Documents

- Invoice PDF matches finalized snapshot.
- Revised invoice behavior works.
- XLSX report matches report data.
- Sharing/export works without leaking unauthorized data.

## Gate 8 --- Real Device

At least one supported production-like Android device must validate:

- login;
- registration;
- order creation;
- walk-in;
- pickup;
- drop-off;
- processing;
- finalization;
- payment;
- cancellation;
- offline/reconnect;
- invoice;
- reports.

## Gate 9 --- Operations

- Production Firebase project verified.
- Production environment verified.
- Backup schedule verified.
- Restore test completed.
- Crash/error monitoring verified.
- Privacy notice published.
- Support path verified.

------------------------------------------------------------------------

# 268. Final V1 Definition of Done

V1 is complete only when:

``` text
SRS
 ↓
Schema
 ↓
Domain Rules
 ↓
Repositories
 ↓
Firebase
 ↓
Security Rules
 ↓
Cloud Functions
 ↓
Offline Sync
 ↓
Screens
 ↓
Invoice
 ↓
Reports
 ↓
Testing
 ↓
Backup/Recovery
 ↓
Security Audit
 ↓
Real Device Validation
 ↓
Production Release
```

has been completed and verified.

"Code exists" is not equivalent to "requirement is complete".

A requirement is complete only when:

``` text
Implemented
+
Persisted correctly
+
Authorized correctly
+
Offline-safe where required
+
Synced correctly
+
Tested
+
Historically safe
+
Validated on a production-like device
```

------------------------------------------------------------------------

# 269. Final P0 Invariants

The following invariants must never be violated:

1.  No cross-business access.
2.  No customer access to another customer.
3.  No Staff role escalation.
4.  No Staff financial editing beyond explicitly permitted operations.
5.  No Staff received-quantity editing.
6.  No Staff final-rate editing.
7.  No Staff additional-charge editing.
8.  No unauthorized invoice finalization.
9.  No customer invoice visibility before finalization.
10. No invoice generation before the finalization gate.
11. No overpayment.
12. No silent payment deletion.
13. No silent invoice mutation.
14. No historical price rewrite.
15. No historical GST rewrite.
16. No historical business snapshot rewrite.
17. No duplicate sync mutation.
18. No duplicate invoice identity.
19. No lost offline committed operation.
20. No deletion of protected financial history.
21. No customer creation by Staff.
22. No Staff customer deactivation.
23. No production database use for development/testing.
24. No production secrets in the mobile client.
25. No sensitive customer/financial data in diagnostic logs.
26. No privilege increase while offline.
27. No automatic financial conflict merge.
28. No post-delivery 7-day bypass using client clock manipulation.
29. No correction without Admin authorization and reason.
30. No revised invoice that destroys the original audit trail.

------------------------------------------------------------------------

# 270. Final Engineering Execution Contract

The implementation must proceed in this order:

``` text
PHASE 0
V5.0 SRS freeze
        ↓
PHASE 1
Domain enums + state machines + financial rules
        ↓
PHASE 2
SQLite schema + migrations + repositories
        ↓
PHASE 3
Firebase schema + indexes + Security Rules
        ↓
PHASE 4
Cloud Functions + trusted financial operations
        ↓
PHASE 5
Auth + session + role/business isolation
        ↓
PHASE 6
Offline sync + idempotency + conflict handling
        ↓
PHASE 7
Customer UI
        ↓
PHASE 8
Staff UI
        ↓
PHASE 9
Admin UI + People
        ↓
PHASE 10
Orders + received + finalization + invoice
        ↓
PHASE 11
Payments + correction + reports
        ↓
PHASE 12
Notifications + diagnostics + hardening
        ↓
PHASE 13
Full test matrix
        ↓
PHASE 14
Backup/restore + security audit
        ↓
PHASE 15
Production build + real-device release validation
```

No later UI implementation may redefine a domain rule already frozen in
this document.

------------------------------------------------------------------------

# 271. AI/Developer Non-Invention Rule

This document is intended to be used directly by human developers and AI
coding tools.

An implementation agent must:

- follow exact enums;
- follow exact permissions;
- follow exact state transitions;
- follow exact financial rules;
- follow exact historical snapshot rules;
- follow exact invoice correction rules;
- follow exact Staff limitations;
- follow exact walk-in behavior;
- follow exact payment behavior;
- follow exact offline/sync rules.

The implementation agent must not:

- invent a new Staff permission;
- invent a new payment method;
- invent a new order status;
- invent automatic refunds;
- invent automatic financial conflict resolution;
- change historical invoices silently;
- make Customer registration available to Staff;
- use client-side role checks as security;
- bypass the invoice finalization gate;
- treat an estimate as a final invoice;
- replace historical data with current master data.

If a requirement is genuinely missing, the implementation must stop at
the affected boundary and report a **Specification Blocker** instead of
choosing an undocumented business behavior.

------------------------------------------------------------------------

# 272. Final Production Master Statement

**TREAT HOSPITALITY SERVICES Laundry Management App V5.0** is the binding
production-oriented specification for the V1 Android laundry application.

It retains the complete V4.0 product scope while explicitly finalizing:

- granular Staff permissions;
- walk-in-only Staff order creation;
- registered Customer self-registration boundary;
- Staff payment recording with actor identity;
- Customer/Staff/Admin cancellation rules;
- physical laundry return after cancellation;
- received-versus-original laundry data;
- Admin-only financial edits;
- Staff invoice generation when no financial edit is required;
- GST selection at finalization;
- advance payment before invoice;
- automatic advance reconciliation against the final invoice;
- due tracking;
- immutable payment history;
- controlled payment correction;
- invoice correction/reissue;
- original invoice preservation;
- 7-day post-delivery paid-order correction lock;
- ongoing controlled Admin correction while due remains pending;
- invoice revision identity;
- business-scoped uniqueness;
- Firebase environment separation;
- automated backup and documented recovery;
- production observability;
- privacy/data governance;
- SQLite security;
- concurrency/version handling;
- offline idempotency;
- financial precision;
- exact release gates;
- AI/developer non-invention rules.

The application must be considered production-ready only after the
implementation satisfies this specification and passes all applicable
release gates.

**V5.0 is the authoritative engineering contract for V1.**

------------------------------------------------------------------------

# 273. Final Scope Boundary

V1 remains intentionally limited to laundry operations.

It does not introduce:

- Super Admin;
- multi-business SaaS;
- multi-branch;
- inventory;
- payroll;
- expenses;
- advanced accounting;
- GPS route optimization;
- QR/barcode;
- POS hardware;
- loyalty/coupons;
- AI automation;
- unrelated hospitality modules.

Future functionality must be introduced through a new versioned
specification and must not silently expand V1.

------------------------------------------------------------------------

# 274. Final Document Closure

This document must be treated as a version-controlled engineering
artifact.

Required metadata:

``` text
Document: TREAT HOSPITALITY SERVICES Laundry Management App
Version: V5.0
Status: Production Master
Scope: V1 Android Laundry Operations
Backend: Firebase
Local Database: SQLite
Primary Roles: CUSTOMER / STAFF / ADMIN
Business: TREAT HOSPITALITY SERVICES
Timezone: Asia/Kolkata
Currency: INR / Paise
```

Any future business-rule change must:

1. identify the affected V5.0 section;
2. define the new behavior explicitly;
3. update database/domain/backend/UI/test requirements together;
4. increment the SRS version;
5. update acceptance criteria;
6. update migration requirements if schema changes;
7. update security rules if authorization changes;
8. update reports/invoice logic if financial meaning changes.

No production implementation should depend on undocumented verbal
assumptions after V5.0 is frozen.

------------------------------------------------------------------------

# 275. Final Master Requirement

The single highest-level implementation rule is:

> **Build exactly the business system defined by this SRS, preserve every
> historical fact that has financial or operational meaning, make every
> privileged operation backend-authorized, make core operations safe
> offline, and never invent or silently change business logic.**

**End of Production Master SRS --- V5.0**
