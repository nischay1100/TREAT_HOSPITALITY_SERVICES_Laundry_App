# TREAT HOSPITALITY SERVICES — Laundry Management App

# Backend Schema + Security Contract — Production Master V1.0

**Document ID:** THS-LAUNDRY-BACKEND-V1.0  
**Product:** TREAT HOSPITALITY SERVICES Laundry Management App  
**Scope:** V1 Android Laundry Operations  
**Status:** Production Backend Persistence + Security Contract  
**Parent:** SRS V5.0  
**Product Contract:** PRD V1.0  
**Technical Contract:** TRD V1.0  
**UI/UX Contract:** UI/UX V1.0  
**Primary Platform:** Android  
**Cloud:** Firebase Authentication + Cloud Firestore + Cloud Functions  
**Local Database:** SQLite / Expo SQLite  
**Timezone:** Asia/Kolkata  
**Currency:** INR / integer paise  
**Roles:** CUSTOMER / STAFF / ADMIN  
**Business:** TREAT HOSPITALITY SERVICES

---

# 0. Purpose

This document is the implementation contract for the persistent backend/data layer derived from SRS V5.0, PRD V1.0, TRD V1.0 and UI/UX V1.0.

It defines:

- canonical domain entities;
- Firestore collections/documents;
- SQLite tables;
- field names and meanings;
- field types;
- nullability;
- defaults;
- enums;
- relationships;
- indexes;
- uniqueness;
- immutable/protected fields;
- audit metadata;
- historical snapshots;
- invoice revisions;
- payment ledger and corrections;
- order/status history;
- cancellation/physical-return data;
- sync queue;
- idempotency;
- version/concurrency handling;
- Security Rules boundaries;
- Cloud Function boundaries;
- transaction boundaries;
- migration requirements;
- data retention/deactivation behavior;
- schema-level invariants.

This document MUST NOT introduce new business behavior. Where the four source documents do not provide an exact implementation detail, this document records a **Specification Blocker** instead of inventing a business rule.

---

# 1. Authority and Precedence

The binding hierarchy is:

```text
SRS V5.0
    ↓
PRD V1.0
    ↓
TRD V1.0
    ↓
UI/UX V1.0
    ↓
THIS BACKEND SCHEMA + SECURITY CONTRACT
    ↓
Implementation / Tests
```

The SRS remains authoritative.

The SRS explicitly states that sections 217 onward are binding V5.0 clarifications/final decisions. Therefore, when an earlier conceptual model differs from a later binding decision, the V5.0 decision controls.

The backend contract must implement the approved business behavior and must not silently choose between conflicting interpretations.

---

# 2. Source-Driven Backend Principles

## 2.1 Three distinct business data stages

The schema MUST preserve the distinction:

```text
Original Customer Request
        ↓
Actual Received Laundry
        ↓
Final Billing Data
        ↓
Final Invoice Snapshot
```

These are different meanings even when values happen to be equal.

## 2.2 Historical snapshot rule

The authoritative historical rule is:

```text
Current settings → future operations
Historical snapshots → historical records
```

Therefore:

- current customer profile MUST NOT rewrite old order snapshots;
- current item/service names MUST NOT rewrite old order items;
- current price MUST NOT rewrite `priceAtOrderTime`;
- current price MUST NOT rewrite `finalRate`;
- current GST settings MUST NOT rewrite historical GST;
- current business settings MUST NOT rewrite finalized invoice identity;
- old payments MUST remain historical ledger events;
- old status history MUST remain append-only.

## 2.3 Offline-first rule

SQLite is the local operational source of truth for currently committed offline work.

Firestore is the cloud/master business record after successful synchronization.

A successful local transaction MUST NOT be represented as successful cloud synchronization until cloud acknowledgement actually exists.

## 2.4 Financial rule

All persisted monetary values MUST use integer paise.

Example:

```text
₹590.50 = 59050
```

Floating-point numbers MUST NOT be the authoritative persisted representation.

## 2.5 Security rule

UI visibility is not authorization.

Authorization MUST be enforced through:

```text
Firebase Authentication
+
Firestore Security Rules
+
Cloud Functions for privileged operations
```

where appropriate.

---

# 3. V1 Scope Boundary

The backend supports:

- registered personal customers;
- registered business customers;
- walk-in customers/orders;
- staff operational work;
- admin management;
- collection methods;
- return methods;
- order lifecycle;
- received-laundry verification;
- final billing;
- GST;
- additional charges;
- invoice finalization;
- invoice correction/reissue;
- payments;
- payment correction/reversal;
- due tracking;
- status history;
- cancellation;
- physical return tracking after cancellation;
- reports;
- offline synchronization;
- audit/history;
- notifications where enabled.

The backend MUST NOT create V1 entities/workflows for:

- Super Admin;
- multi-business UI;
- multi-branch UI;
- inventory;
- payroll;
- salary;
- expense management;
- advanced accounting;
- GPS route optimization;
- QR/barcode garment tracking;
- POS hardware;
- loyalty/coupons;
- advanced CRM;
- mandatory online payment gateway;
- mandatory WhatsApp/SMS infrastructure;
- full fleet management.

The schema may remain structurally future-extensible through `businessId`, but V1 UI/business behavior remains one business.

---

# 4. Canonical Roles

Exactly:

```text
CUSTOMER
STAFF
ADMIN
```

No other production role may be introduced.

Role is security-sensitive.

`users.role` MUST NOT be client-self-assignable.

---

# 5. Canonical Enums

## 5.1 User/account role

```text
CUSTOMER
STAFF
ADMIN
```

## 5.2 Customer type

```text
PERSONAL
BUSINESS
```

Default:

```text
PERSONAL
```

## 5.3 Customer/staff/item/service account status

Customer:

```text
ACTIVE
INACTIVE
```

Item:

```text
ACTIVE
INACTIVE
```

Service:

```text
ACTIVE
INACTIVE
```

Staff uses active/inactive lifecycle. The exact stored Staff status enum should remain the same active/inactive concept used by the product.

## 5.4 Business type

Approved business categories:

```text
HOTEL
RESORT
B_AND_B
GUEST_HOUSE
OTHER
```

## 5.5 Order source

Normal registered customer order:

```text
CUSTOMER
```

Staff-created walk-in:

```text
WALK_IN
```

Do not introduce another V1 order source.

## 5.6 Collection method

```text
PICKUP_BY_US
CUSTOMER_DROP_OFF
```

## 5.7 Return method

```text
DELIVERY_BY_US
CUSTOMER_PICKUP
```

## 5.8 Normal order status

Canonical normal-order state set:

```text
NEW
PICKUP_PENDING
PICKED_UP
RECEIVED
PROCESSING
READY
READY_FOR_PICKUP
OUT_FOR_DELIVERY
DELIVERED
COLLECTED
CANCELLED
```

Not every status is valid for every flow.

### Pickup by us + delivery by us

```text
NEW
→ PICKUP_PENDING
→ PICKED_UP
→ PROCESSING
→ READY
→ OUT_FOR_DELIVERY
→ DELIVERED
```

### Pickup by us + customer pickup

```text
NEW
→ PICKUP_PENDING
→ PICKED_UP
→ PROCESSING
→ READY_FOR_PICKUP
→ COLLECTED
```

### Customer drop-off + delivery by us

```text
NEW
→ RECEIVED
→ PROCESSING
→ READY
→ OUT_FOR_DELIVERY
→ DELIVERED
```

### Customer drop-off + customer pickup

```text
NEW
→ RECEIVED
→ PROCESSING
→ READY_FOR_PICKUP
→ COLLECTED
```

Invalid jumps MUST be rejected.

## 5.9 Walk-in operational status

The SRS identifies walk-in as a distinct operational model and permits the following conceptual states:

```text
READY_FOR_COLLECTION
COLLECTED
CANCELLED
```

A walk-in MUST NOT be forced through `PROCESSING` when no normal laundry-processing workflow exists.

## 5.10 Payment method

Exactly:

```text
CASH
UPI
ONLINE
```

`ONLINE` is recordable where product/backend rules permit it; V1 does not require an online gateway.

## 5.11 Payment status

```text
PENDING
PARTIALLY_PAID
PAID
```

Presentation labels may use:

```text
Payment Pending
Partially Paid
Paid
```

## 5.12 Invoice status

Canonical persisted invoice lifecycle:

```text
NOT_FINALIZED
FINALIZED
REVISED_FINALIZED
```

Correction workflow state is represented by correction records, not by mutating the original invoice.

## 5.13 Return status

```text
NOT_REQUIRED
RETURN_PENDING
RETURNED
```

## 5.14 Sync status

The implementation needs a persisted queue state. The source documents establish pending/failed/retry/synced behavior.

Canonical backend queue states:

```text
PENDING
PROCESSING
FAILED
SYNCED
```

The UI may map these to:

```text
Offline
Syncing
Sync Error
Synced
```

A failed operation MUST remain recoverable.

## 5.15 Sync operation

The SRS explicitly permits:

```text
CREATE
UPDATE
DELETE
```

However, `DELETE` MUST NOT be used to casually destroy protected historical financial data.

## 5.16 Error category

The SRS defines:

```text
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

These are application/backend error categories, not business entity states.

---

# 6. Identifier Contract

## 6.1 Required durable identifiers

Every persisted major entity MUST have a stable immutable ID:

```text
userId
businessId
staffId
customerId
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

## 6.2 ID requirements

IDs MUST be:

- durable;
- collision-resistant for the required scope;
- stable after local creation;
- safe for offline creation;
- deterministic for validation;
- valid across sync retries.

A pending sync operation MUST never silently replace an already-committed local ID.

## 6.3 Prohibited ID strategies

Never use:

```text
process-local counters
array indexes
timestamp-only IDs
UI-only temporary IDs that are later silently replaced
```

The SRS explicitly prohibits process-local durable order-item counters such as:

```text
OI-1
OI-2
OI-3
```

## 6.4 Specification Blocker — exact ID alphabet/regex

The SRS says the final implementation MUST use the approved exact ID alphabet and regex consistently in generator, validator, tests, database constraints and backend validation.

However, the supplied SRS does not expose the actual final regex/alphabet value in a concrete schema definition.

Therefore:

```text
BLOCKER: SB-ID-001
```

The implementation MUST NOT invent a business-specific ID regex.

Until the owner supplies/approves the exact pattern, schema fields use a durable opaque `TEXT/STRING` identifier with uniqueness constraints and the generator/validator must be isolated behind one implementation contract.

This blocker affects:

- SQLite `CHECK` constraints;
- backend ID validators;
- test fixtures;
- ID generators;
- Firestore validation;
- any regex-based security validation.

---

# 7. Timestamp Contract

## 7.1 Business timezone

```text
Asia/Kolkata
```

## 7.2 Stored timestamps

Authoritative timestamps MUST use a consistent server-compatible timestamp representation.

Recommended Firestore representation:

```text
Firestore Timestamp
```

Recommended SQLite representation:

```text
INTEGER epoch milliseconds
```

or another single consistent UTC-compatible representation approved by the implementation.

The representation MUST be consistent across repositories.

## 7.3 Offline events

Where audit/debugging requires both values, preserve:

```text
clientCreatedAt
serverAcceptedAt
```

Client time MUST NOT be trusted for:

- seven-day invoice correction lock;
- payment ordering;
- authoritative audit chronology;
- security decisions.

---

# 8. Common Audit Metadata

Where applicable, records MUST use:

```text
createdAt
updatedAt
createdBy
lastUpdatedBy
version
```

Specialized actor fields:

```text
recordedBy
changedBy
invoiceCreatedBy
requestedBy
approvedBy
cancelledBy
returnedBy
```

Audit references should remain valid even after an actor is deactivated.

Historical actor references MUST NOT be replaced with NULL merely because the account becomes inactive.

---

# 9. Firestore Top-Level Architecture

Use the following canonical structure:

```text
users/{userId}

businesses/{businessId}

businesses/{businessId}/staff/{staffId}

businesses/{businessId}/customers/{customerId}

businesses/{businessId}/items/{itemId}

businesses/{businessId}/services/{serviceId}

businesses/{businessId}/prices/{priceId}

businesses/{businessId}/orders/{orderId}

businesses/{businessId}/orders/{orderId}/payments/{paymentId}

businesses/{businessId}/orders/{orderId}/statusHistory/{statusHistoryId}

businesses/{businessId}/orders/{orderId}/additionalCharges/{chargeId}

businesses/{businessId}/orders/{orderId}/invoices/{invoiceId}

businesses/{businessId}/orders/{orderId}/corrections/{correctionId}

businesses/{businessId}/settings/general

businesses/{businessId}/idempotency/{syncId}
```

The last five supporting subcollections are required by the data relationships and integrity model of this contract. They do not introduce new business behavior; they persist data already required by SRS/TRD.

---

# 10. Firestore Document: users/{userId}

## Purpose

Auth-linked application profile and trusted role/business context.

## Fields

| Field | Type | Required | Mutable | Notes |
|---|---|---:|---:|---|
| userId | string | yes | no | Must equal Firebase Auth UID |
| name | string | yes for completed profile | yes, permitted roles | Application identity |
| email | string | yes | controlled | Auth-linked email |
| phone | string | profile-dependent | yes, permitted | Contact information, not auth identity |
| role | enum | yes | protected | CUSTOMER / STAFF / ADMIN |
| businessId | string | yes for business users | protected | V1 business scope |
| status | enum | yes | Admin/server-controlled | ACTIVE / INACTIVE |
| profileCompleted | boolean | yes | controlled | Default false |
| forcePasswordChange | boolean | yes for Staff flow | server-controlled | Default false |
| createdAt | timestamp | yes | no | Audit |
| updatedAt | timestamp | yes | yes | Audit |

## Security

Client MUST NOT change:

```text
role
businessId
status
forcePasswordChange
```

A user cannot self-promote.

---

# 11. Firestore Document: businesses/{businessId}

## Fields

| Field | Type | Required | Mutable | Notes |
|---|---|---:|---:|---|
| businessId | string | yes | no | Stable ID |
| businessName | string | yes | controlled | Business identity |
| businessType | enum/string | yes | controlled | Business category |
| createdAt | timestamp | yes | no | Audit |
| updatedAt | timestamp | yes | yes | Audit |

V1 contains one business.

---

# 12. Firestore Document: businesses/{businessId}/settings/general

This is the central current business settings record.

## Fields

| Field | Type | Required | Mutable | Notes |
|---|---|---:|---:|---|
| businessId | string | yes | no | Must equal parent path |
| businessName | string | yes | yes | Current business name |
| businessType | string/enum | yes | yes | Current business type |
| primaryPhone | string | yes | yes | Current primary number |
| alternativePhone | string | nullable | yes | Explicit SRS requirement |
| email | string | nullable | yes | Business email |
| address | string | yes | yes | Current address |
| pinCode | string | yes | yes | Current PIN |
| GSTIN | string | nullable | yes | Current GSTIN |
| defaultGSTRate | integer/decimal policy | yes | yes | Configuration only; final invoice stores snapshot |
| invoicePrefix | string | yes | yes | Current invoice prefix |
| invoiceFooter | string | nullable | yes | Invoice presentation/footer |
| updatedAt | timestamp | yes | yes | Audit |
| updatedBy | string | yes | yes | Actor |

### Historical rule

Changing this document MUST NOT rewrite historical invoice snapshots.

---

# 13. Firestore Document: Staff

Path:

```text
businesses/{businessId}/staff/{staffId}
```

## Fields

| Field | Type | Required | Mutable | Notes |
|---|---|---:|---:|---|
| staffId | string | yes | no | Stable ID |
| userId | string | yes | no | Firebase Auth UID |
| businessId | string | yes | no | Parent business |
| name | string | yes | yes | Profile |
| email | string | yes | controlled | Login |
| phone | string | yes | yes | Contact |
| staffCode | string | yes | controlled | Staff identifier |
| status | enum | yes | Admin only | ACTIVE / INACTIVE |
| createdAt | timestamp | yes | no | Audit |
| updatedAt | timestamp | yes | yes | Audit |
| lastLoginAt | timestamp | nullable | system | If available |

Passwords MUST NOT be stored here.

Staff creation MUST use the trusted server-side privileged operation described by TRD/SRS.

---

# 14. Firestore Document: Customer

Path:

```text
businesses/{businessId}/customers/{customerId}
```

## Fields

| Field | Type | Required | Mutable | Notes |
|---|---|---:|---:|---|
| customerId | string | yes | no | Stable ID |
| userId | string | nullable | controlled | NULL for walk-in without account |
| businessId | string | yes | no | Parent business |
| name | string | yes | permitted | Current profile |
| email | string | nullable | permitted | Walk-in may have no email |
| phone | string | nullable | permitted | Contact |
| customerType | enum | yes | permitted/Admin | PERSONAL / BUSINESS |
| businessName | string | nullable | permitted | Required when BUSINESS |
| businessType | enum/string | nullable | permitted | Required when BUSINESS |
| businessAddress | string | nullable | permitted | Business-specific |
| businessPhone | string | nullable | permitted | Business-specific |
| address | string | yes/conditional | permitted | Customer address |
| pinCode | string | yes/conditional | permitted | PIN |
| status | enum | yes | Admin only | ACTIVE / INACTIVE |
| profileCompleted | boolean | yes | controlled | Default false |
| createdAt | timestamp | yes | no | Audit |
| updatedAt | timestamp | yes | yes | Audit |

## Rules

For `PERSONAL`, business-specific fields should be NULL/not applicable.

For `BUSINESS`, required business fields must be validated according to the approved profile requirements.

Changing BUSINESS → PERSONAL must preserve historical order snapshots and may clear current business-specific fields only after confirmation.

---

# 15. Firestore Document: Item

Path:

```text
businesses/{businessId}/items/{itemId}
```

Fields:

```text
itemId
businessId
name
status
createdAt
updatedAt
```

Status:

```text
ACTIVE
INACTIVE
```

Referenced historical order items retain their historical item name even if the Item is later changed or deactivated.

---

# 16. Firestore Document: Service

Path:

```text
businesses/{businessId}/services/{serviceId}
```

Fields:

```text
serviceId
businessId
name
status
createdAt
updatedAt
```

Status:

```text
ACTIVE
INACTIVE
```

Referenced historical order items retain their historical service name.

---

# 17. Firestore Document: Price

Path:

```text
businesses/{businessId}/prices/{priceId}
```

Fields:

| Field | Type | Required | Mutable | Notes |
|---|---|---:|---:|---|
| priceId | string | yes | no | Stable ID |
| businessId | string | yes | no | Parent |
| itemId | string | yes | controlled | Referenced Item |
| serviceId | string | yes | controlled | Referenced Service |
| pricePaise | integer | yes | versioned | Authoritative price |
| status | enum | yes | Admin | ACTIVE / INACTIVE |
| createdAt | timestamp | yes | no | Audit |
| updatedAt | timestamp | yes | yes | Audit |
| version | integer | yes | incremented | Concurrency |

The SRS conceptual field is named `price`; the technical contract normalizes it to `pricePaise` to enforce the mandatory integer-paise rule.

Master price updates are version-aware.

A historical order item MUST NOT depend on the current Price document for historical billing.

---

# 18. Firestore Order Document

Path:

```text
businesses/{businessId}/orders/{orderId}
```

The order is the central operational aggregate.

## 18.1 Identity

```text
orderId
businessId
customerId
userId
source
```

Rules:

- `businessId` must equal parent path;
- `customerId` may be NULL for a walk-in;
- `userId` may be NULL for an unregistered walk-in;
- `source` is `CUSTOMER` or `WALK_IN`;
- Staff-created V1 orders MUST use `WALK_IN`.

## 18.2 Historical customer snapshot

```text
customerNameAtOrder
customerPhoneAtOrder
customerAddressAtOrder
businessNameAtOrder
businessTypeAtOrder
```

These are historical values.

They MUST NOT be refreshed from the current Customer profile.

## 18.3 Collection and return

```text
collectionMethod
returnMethod
```

Allowed collection:

```text
PICKUP_BY_US
CUSTOMER_DROP_OFF
```

Allowed return:

```text
DELIVERY_BY_US
CUSTOMER_PICKUP
```

## 18.4 Pickup data

```text
pickupAddressAtOrder
pickupDate
pickupTime
```

For a normal scheduled Customer pickup, these contain the approved pickup details.

For an unscheduled Staff WALK_IN:

```text
pickupDate = NULL
pickupTime = NULL
```

Do not insert fake dates/times.

## 18.5 Customer note

```text
customerNote
```

This is the customer/order note.

Internal-only notes must not be exposed to Customer.

## 18.6 Original estimated financial data

```text
estimatedSubtotalPaise
estimatedGSTRate
estimatedGSTPaise
estimatedTotalPaise
estimatedGSTApplied
```

The estimate is never the final invoice.

## 18.7 Final financial summary

```text
actualSubtotalPaise
additionalChargesTotalPaise
gstApplied
gstRate
gstAmountPaise
finalAmountPaise
paidAmountPaise
dueAmountPaise
paymentStatus
```

These summary values are derived from the authoritative financial datasets and must not be trusted merely because a client sends them.

## 18.8 Invoice summary

```text
invoiceStatus
currentInvoiceId
invoiceNumber
invoiceCreatedAt
invoiceCreatedBy
```

`invoiceNumber` is a convenience/current reference; the immutable invoice document is authoritative.

## 18.9 Operational state

```text
orderStatus
```

## 18.10 Audit/concurrency

```text
createdAt
updatedAt
createdBy
lastUpdatedBy
version
```

## 18.11 Walk-in fixed amount

For WALK_IN orders:

```text
fixedAmountPaise
```

The fixed amount is set at creation and is not an estimated normal Customer amount.

Walk-in orders do not enter normal received-quantity verification.

---

# 19. Firestore Order Items

Recommended path:

```text
businesses/{businessId}/orders/{orderId}/items/{orderItemId}
```

The TRD also permits an embedded `items[]` representation. The cloud contract must choose exactly one authoritative Firestore representation. This contract uses a subcollection because the order item is independently identified, audited, synchronized and queried while SQLite remains normalized.

## Fields

```text
orderItemId
orderId
businessId

itemId
itemName

serviceId
serviceName

orderedQuantity
receivedQuantity
finalQuantity

priceAtOrderTimePaise
finalRatePaise

orderedSubtotalPaise
finalSubtotalPaise

receivedNote

createdAt
updatedAt
```

## Required historical fields

`itemName` and `serviceName` are snapshots.

They MUST NOT be populated later from current Item/Service masters when rendering a historical record.

## Quantity semantics

```text
orderedQuantity = customer-requested quantity
receivedQuantity = physically received quantity
finalQuantity = quantity used for billing
```

`orderedQuantity` MUST never be overwritten.

`receivedQuantity` and `finalQuantity` are Admin-controlled before finalization.

Staff cannot edit either.

## Rate semantics

```text
priceAtOrderTimePaise = original applicable order-time price
finalRatePaise = final billed rate
```

Changing master prices MUST NOT change either historical value.

---

# 20. Additional Charges

Path:

```text
businesses/{businessId}/orders/{orderId}/additionalCharges/{chargeId}
```

Fields:

```text
chargeId
orderId
businessId
name
amountPaise
note
createdBy
createdAt
updatedAt
```

Rules:

- amount must not be negative;
- name/description must be meaningful;
- note may be optional;
- Staff cannot create/edit charges;
- Admin can create/edit before finalization;
- finalized charges become part of the immutable invoice snapshot.

---

# 21. Received Laundry Dataset

The source model requires received laundry to remain distinct from original request data.

The normalized representation is the `receivedQuantity`/`receivedNote` fields on order items, with the order-level `receivedAt`/audit metadata below.

Recommended order-level fields:

```text
receivedAt
receivedBy
```

These identify the receiving event when applicable.

The system MUST preserve:

```text
orderedQuantity
receivedQuantity
finalQuantity
```

as separate meanings.

If received quantity differs from ordered quantity, the difference must remain historically visible.

The system must not automatically:

- change final quantity;
- cancel the order;
- rewrite the original quantity.

---

# 22. Cancellation Data

Cancellation does not delete an order.

Recommended order-level fields:

```text
cancelledAt
cancelledBy
cancellationReason
returnRequired
returnStatus
```

## Cancellation eligibility

Customer:

```text
PICKUP_BY_US → before PICKED_UP
CUSTOMER_DROP_OFF → before PROCESSING
```

Staff/Admin:

- only allowed cancellable states;
- also allowed for received-vs-ordered mismatch when the approved business condition is met.

Backend MUST enforce these conditions.

## Payment after cancellation

Cancellation MUST NOT erase payments.

If refund/reversal is needed, the payment correction model must be used.

The backend MUST NOT invent an automatic refund amount or method.

---

# 23. Physical Return Task

When cancelled laundry is physically held by the business:

```text
returnRequired = true
returnStatus = RETURN_PENDING
```

When returned:

```text
returnStatus = RETURNED
```

When no physical laundry is held:

```text
returnRequired = false
returnStatus = NOT_REQUIRED
```

Fields:

```text
returnStatus
returnedAt
returnedBy
returnNote
```

The order remains:

```text
orderStatus = CANCELLED
```

The return task does not reopen or delete the order.

---

# 24. Invoice Architecture

## 24.1 Invoice identity

Invoice ID and invoice number are distinct.

```text
invoiceId
invoiceNumber
```

`invoiceId` is a stable internal entity identifier.

`invoiceNumber` is the business-facing invoice identity.

Invoice numbers MUST be unique within the business.

## 24.2 Invoice document

Path:

```text
businesses/{businessId}/orders/{orderId}/invoices/{invoiceId}
```

## 24.3 Required invoice fields

```text
invoiceId
orderId
businessId

invoiceNumber
invoiceDate

status
revisionNumber

originalInvoiceId
revisedFromInvoiceId

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

lineItems[]
additionalCharges[]

subtotalPaise
gstApplied
gstRate
gstAmountPaise
finalAmountPaise

paidAmountPaise
dueAmountPaise
paymentStatus

createdAt
createdBy
```

## 24.4 Immutable snapshot

The invoice snapshot MUST contain the actual values used for billing.

It must not be reconstructed later from:

- current Customer;
- current Item;
- current Service;
- current Price;
- current GST settings;
- current Business Settings.

## 24.5 Invoice line item snapshot

Each `lineItems[]` entry must preserve:

```text
orderItemId
itemId
itemName
serviceId
serviceName
quantity
ratePaise
lineSubtotalPaise
```

The exact persisted array/object shape is part of this contract and must map losslessly to SQLite.

## 24.6 Additional charge snapshot

Each invoice additional charge snapshot must preserve:

```text
chargeId
name
amountPaise
note
```

## 24.7 Business snapshot

```text
businessName
businessAddress
businessPhone
businessAlternativePhone
businessEmail
businessGSTIN
```

## 24.8 Customer snapshot

```text
customerName
customerPhone
customerAddress
customerBusinessName
```

---

# 25. Invoice Finalization

Finalization is atomic.

The trusted operation must validate:

1. authenticated identity;
2. role;
3. business;
4. target order;
5. current order state;
6. original/received/final data;
7. Staff/Admin edit authority;
8. GST validity;
9. final line items;
10. additional charges;
11. financial arithmetic;
12. payment reconciliation;
13. invoice uniqueness;
14. concurrency/version;
15. idempotency.

Then atomically persist:

```text
invoice snapshot
+
invoice identity/number
+
final item values
+
additional charges
+
GST snapshot
+
payment reconciliation
+
invoice/order state
+
audit
+
sync/idempotency result
```

If any mandatory part fails, the transaction MUST fail as one logical operation.

---

# 26. Staff Finalization Constraint

Staff may finalize only when:

```text
received quantities already correct
AND
final quantities already correct
AND
final rates already correct
AND
billable lines already correct
AND
no additional charge change required
AND
no financial/order edit required
AND
GST ON/OFF is the only remaining final choice
AND
order reached finalization gate
```

The backend MUST enforce this, regardless of UI state.

If an edit is required, Admin finalization is required.

---

# 27. Seven-Day Financial Edit Rule

After delivery/collection:

### Due still pending

Admin controlled correction remains available according to the approved correction policy.

### Fully paid

Admin financial edit/correction is available only during the first seven calendar days after the delivery/collection completion timestamp.

After that:

```text
financialEdit = LOCKED
```

The backend MUST calculate the authoritative time window using trusted timestamps.

Client device time MUST NOT control this lock.

---

# 28. Invoice Correction / Reissue

The original finalized invoice is never directly mutated.

Correction record path:

```text
businesses/{businessId}/orders/{orderId}/corrections/{correctionId}
```

Fields:

```text
correctionId
orderId
businessId

originalInvoiceId
revisedInvoiceId
revisionNumber

reason
requestedBy
approvedBy

createdAt
```

## Relationship

Example:

```text
INV-000123
INV-000123-R1
INV-000123-R2
```

A revision MUST NOT reuse the original immutable invoice number.

The exact prefix is controlled by Invoice Settings.

## Correction invariants

- original invoice remains available;
- revised invoice is a new immutable snapshot;
- current valid invoice reference points to the currently valid revision;
- revision relationship is explicit;
- reason is mandatory;
- authorization is mandatory;
- audit history remains append-only;
- normal operational order status is not reopened merely because financial data is corrected.

---

# 29. Invoice Number Reservation

Invoice numbers must be unique within a business.

Because offline devices may finalize locally, authoritative cloud reservation MUST prevent two devices from creating the same business invoice number.

The trusted backend must provide idempotent invoice identity handling.

A retry of the same finalized operation must return the existing authoritative result rather than create a second invoice.

---

# 30. Payment Ledger

Path:

```text
businesses/{businessId}/orders/{orderId}/payments/{paymentId}
```

Payments are append-only financial events.

## Fields

```text
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

entryType
adjustsPaymentId
adjustmentReason
```

### Original payment

```text
entryType = PAYMENT
adjustsPaymentId = NULL
adjustmentReason = NULL
```

### Correction/reversal

The exact adjustment semantics must follow the approved append-only model.

The original payment remains immutable.

`adjustsPaymentId` links an adjustment to the original payment where applicable.

`adjustmentReason` records the reason.

---

# 31. Payment Validation

A payment mutation must validate:

```text
authorization
+
same business
+
valid order
+
valid payment method
+
amount > 0
+
financial state
+
remaining payable amount
+
idempotency
+
concurrency
```

For ordinary positive payment entry:

```text
newValidPaidAmount <= finalAmount
```

Overpayment MUST be rejected.

Before final invoice exists, advance payment is allowed and remains linked to the order.

After finalization:

```text
paidAmountPaise = sum(valid ledger entries)
dueAmountPaise = finalAmountPaise - paidAmountPaise
```

---

# 32. Advance Payments

Advance payment is a valid payment before final invoice finalization.

It MUST NOT:

- finalize the invoice;
- expose the invoice to Customer;
- bypass processing;
- change operational status;
- become an orphan payment.

After final invoice:

```text
paidAmountPaise = sum(valid payment ledger entries)
dueAmountPaise = finalAmountPaise - paidAmountPaise
```

If the advance causes an excess relative to final amount, finalization MUST NOT silently accept the overpayment. The approved Admin correction/refund process must resolve it.

---

# 33. Payment Status Reconciliation

If:

```text
paidAmountPaise < finalAmountPaise
```

then:

```text
paymentStatus = PARTIALLY_PAID
dueAmountPaise > 0
```

If:

```text
paidAmountPaise = finalAmountPaise
```

then:

```text
paymentStatus = PAID
dueAmountPaise = 0
```

The product's unpaid/pending presentation may map the pre-payment state to `PENDING`.

Payment status is independent of order status.

A delivered order may still be partially paid.

---

# 34. Payment Correction

No user directly edits:

```text
amountPaise
paymentMethod
paymentDate
recordedBy
paymentId
```

If a payment is wrong:

```text
Original Payment
      ↓
Admin Correction
      ↓
Reason
      ↓
Append-only Adjustment/Reversal
      ↓
New Valid Ledger Total
```

The original entry remains in audit history.

---

# 35. Status History

Path:

```text
businesses/{businessId}/orders/{orderId}/statusHistory/{statusHistoryId}
```

Fields:

```text
statusHistoryId
orderId
businessId

fromStatus
toStatus

changedAt
changedBy
reason

clientCreatedAt
serverAcceptedAt
syncId
```

The history is append-only.

Same committed transition retried with the same idempotency identity must not create duplicate logical history.

Invalid transitions are rejected.

Final states:

```text
DELIVERED
COLLECTED
CANCELLED
```

Normal Staff/Customer operations cannot reopen them.

---

# 36. Order State Machine Security

The backend must evaluate:

```text
current orderStatus
+
requested status
+
role
+
collectionMethod
+
returnMethod
+
required preconditions
+
version
+
idempotency
```

A valid state transition must be performed atomically with its status-history entry.

Examples of prohibited operations:

```text
NEW → DELIVERED
PROCESSING → DELIVERED
READY → DELIVERED when delivery stage requires OUT_FOR_DELIVERY
READY_FOR_PICKUP → OUT_FOR_DELIVERY
final → PROCESSING
```

---

# 37. Order Search Index Strategy

Staff/Admin search is local-first on the device and business-scoped in the backend.

Search fields include:

```text
orderId
invoiceNumber
customerNameAtOrder
customerPhoneAtOrder
businessNameAtOrder
```

Firestore query/index planning must support business-scoped access.

SQLite must support offline search.

Large cloud collections MUST be paginated.

The backend must never rely on loading the entire order collection into memory.

---

# 38. Customer Search Index Strategy

Admin customer search supports:

```text
name
phone
email
businessName
```

All Admin queries MUST be scoped to:

```text
businessId == authenticated user's businessId
```

Customer users do not receive arbitrary customer search capability.

---

# 39. Master Data Index Strategy

Recommended Firestore indexes/lookup patterns:

```text
items:
    businessId + status + name

services:
    businessId + status + name

prices:
    businessId + itemId + serviceId + status

customers:
    businessId + status + name
    businessId + phone
    businessId + email
    businessId + businessName

staff:
    businessId + status + name

orders:
    businessId + orderStatus + updatedAt
    businessId + paymentStatus + updatedAt
    businessId + createdAt
    businessId + customerId + createdAt
    businessId + invoiceNumber
```

Exact Firestore composite-index files must be generated and tested during implementation.

---

# 40. SQLite Architecture

SQLite is normalized for local operational querying.

Canonical tables:

```text
users
businesses
business_settings
staff
customers
items
services
prices
orders
order_items
additional_charges
order_returns
invoices
invoice_line_items
invoice_additional_charges
invoice_corrections
payments
payment_adjustments
status_history
sync_queue
idempotency_records
app_metadata
notification_tokens
```

All business tables that are business-owned must contain `business_id`.

---

# 41. SQLite: users

```sql
users (
  user_id TEXT PRIMARY KEY,
  name TEXT,
  email TEXT,
  phone TEXT,
  role TEXT NOT NULL,
  business_id TEXT,
  status TEXT NOT NULL,
  profile_completed INTEGER NOT NULL DEFAULT 0,
  force_password_change INTEGER NOT NULL DEFAULT 0,
  created_at INTEGER NOT NULL,
  updated_at INTEGER NOT NULL,
  version INTEGER NOT NULL DEFAULT 1
)
```

Constraints:

```text
role ∈ CUSTOMER, STAFF, ADMIN
status ∈ ACTIVE, INACTIVE
profile_completed ∈ 0,1
force_password_change ∈ 0,1
```

Never store passwords.

---

# 42. SQLite: businesses

```sql
businesses (
  business_id TEXT PRIMARY KEY,
  business_name TEXT NOT NULL,
  business_type TEXT NOT NULL,
  created_at INTEGER NOT NULL,
  updated_at INTEGER NOT NULL,
  version INTEGER NOT NULL DEFAULT 1
)
```

---

# 43. SQLite: business_settings

```sql
business_settings (
  business_id TEXT PRIMARY KEY,
  business_name TEXT NOT NULL,
  business_type TEXT NOT NULL,
  primary_phone TEXT NOT NULL,
  alternative_phone TEXT,
  email TEXT,
  address TEXT NOT NULL,
  pin_code TEXT NOT NULL,
  gstin TEXT,
  default_gst_rate TEXT NOT NULL,
  invoice_prefix TEXT NOT NULL,
  invoice_footer TEXT,
  created_at INTEGER NOT NULL,
  updated_at INTEGER NOT NULL,
  updated_by TEXT NOT NULL,
  version INTEGER NOT NULL DEFAULT 1
)
```

Do not maintain another competing local current-business-settings table.

---

# 44. SQLite: staff

```sql
staff (
  staff_id TEXT PRIMARY KEY,
  user_id TEXT NOT NULL UNIQUE,
  business_id TEXT NOT NULL,
  name TEXT NOT NULL,
  email TEXT NOT NULL,
  phone TEXT NOT NULL,
  staff_code TEXT NOT NULL,
  status TEXT NOT NULL,
  created_at INTEGER NOT NULL,
  updated_at INTEGER NOT NULL,
  last_login_at INTEGER,
  version INTEGER NOT NULL DEFAULT 1
)
```

Recommended uniqueness:

```text
UNIQUE(business_id, staff_code)
```

---

# 45. SQLite: customers

```sql
customers (
  customer_id TEXT PRIMARY KEY,
  user_id TEXT,
  business_id TEXT NOT NULL,
  name TEXT NOT NULL,
  email TEXT,
  phone TEXT,
  customer_type TEXT NOT NULL,
  business_name TEXT,
  business_type TEXT,
  business_address TEXT,
  business_phone TEXT,
  address TEXT,
  pin_code TEXT,
  status TEXT NOT NULL,
  profile_completed INTEGER NOT NULL DEFAULT 0,
  created_at INTEGER NOT NULL,
  updated_at INTEGER NOT NULL,
  version INTEGER NOT NULL DEFAULT 1
)
```

Walk-in-only records may have:

```text
user_id = NULL
email = NULL
```

A Staff walk-in order does not automatically create a registered Customer account.

---

# 46. SQLite: items

```sql
items (
  item_id TEXT PRIMARY KEY,
  business_id TEXT NOT NULL,
  name TEXT NOT NULL,
  status TEXT NOT NULL,
  created_at INTEGER NOT NULL,
  updated_at INTEGER NOT NULL,
  version INTEGER NOT NULL DEFAULT 1
)
```

Recommended:

```text
INDEX(business_id, status, name)
```

---

# 47. SQLite: services

```sql
services (
  service_id TEXT PRIMARY KEY,
  business_id TEXT NOT NULL,
  name TEXT NOT NULL,
  status TEXT NOT NULL,
  created_at INTEGER NOT NULL,
  updated_at INTEGER NOT NULL,
  version INTEGER NOT NULL DEFAULT 1
)
```

Recommended:

```text
INDEX(business_id, status, name)
```

---

# 48. SQLite: prices

```sql
prices (
  price_id TEXT PRIMARY KEY,
  business_id TEXT NOT NULL,
  item_id TEXT NOT NULL,
  service_id TEXT NOT NULL,
  price_paise INTEGER NOT NULL,
  status TEXT NOT NULL,
  created_at INTEGER NOT NULL,
  updated_at INTEGER NOT NULL,
  version INTEGER NOT NULL DEFAULT 1
)
```

Constraints:

```text
price_paise >= 0
```

Recommended active lookup:

```text
UNIQUE(business_id, item_id, service_id, status)
```

If the business later needs multiple simultaneous historical versions, versioning must be handled through explicit rows/revisions rather than overwriting historical order data.

---

# 49. SQLite: orders

```sql
orders (
  order_id TEXT PRIMARY KEY,
  business_id TEXT NOT NULL,

  customer_id TEXT,
  user_id TEXT,

  source TEXT NOT NULL,

  customer_name_at_order TEXT NOT NULL,
  customer_phone_at_order TEXT,
  customer_address_at_order TEXT,
  business_name_at_order TEXT,
  business_type_at_order TEXT,

  collection_method TEXT,
  return_method TEXT,

  pickup_address_at_order TEXT,
  pickup_date INTEGER,
  pickup_time TEXT,

  customer_note TEXT,

  estimated_subtotal_paise INTEGER,
  estimated_gst_rate TEXT,
  estimated_gst_paise INTEGER,
  estimated_total_paise INTEGER,
  estimated_gst_applied INTEGER,

  actual_subtotal_paise INTEGER,
  additional_charges_total_paise INTEGER NOT NULL DEFAULT 0,

  gst_applied INTEGER,
  gst_rate TEXT,
  gst_amount_paise INTEGER,
  final_amount_paise INTEGER,

  paid_amount_paise INTEGER NOT NULL DEFAULT 0,
  due_amount_paise INTEGER,

  payment_status TEXT,
  invoice_status TEXT,
  current_invoice_id TEXT,
  invoice_number TEXT,
  invoice_created_at INTEGER,
  invoice_created_by TEXT,

  fixed_amount_paise INTEGER,

  order_status TEXT NOT NULL,

  received_at INTEGER,
  received_by TEXT,

  cancelled_at INTEGER,
  cancelled_by TEXT,
  cancellation_reason TEXT,

  return_required INTEGER NOT NULL DEFAULT 0,
  return_status TEXT,
  returned_at INTEGER,
  returned_by TEXT,
  return_note TEXT,

  created_at INTEGER NOT NULL,
  updated_at INTEGER NOT NULL,
  created_by TEXT NOT NULL,
  last_updated_by TEXT,
  version INTEGER NOT NULL DEFAULT 1
)
```

## Critical constraints

For WALK_IN:

```text
source = WALK_IN
pickup_date IS NULL
pickup_time IS NULL
fixed_amount_paise IS NOT NULL
```

For normal CUSTOMER orders:

```text
source = CUSTOMER
fixed_amount_paise IS NULL
```

The exact enforcement may be implemented at the domain/use-case layer plus SQLite CHECK constraints.

---

# 50. SQLite: order_items

```sql
order_items (
  order_item_id TEXT PRIMARY KEY,
  order_id TEXT NOT NULL,
  business_id TEXT NOT NULL,

  item_id TEXT NOT NULL,
  item_name TEXT NOT NULL,

  service_id TEXT NOT NULL,
  service_name TEXT NOT NULL,

  ordered_quantity INTEGER NOT NULL,
  received_quantity INTEGER,
  final_quantity INTEGER,

  price_at_order_time_paise INTEGER NOT NULL,
  final_rate_paise INTEGER,

  ordered_subtotal_paise INTEGER NOT NULL,
  final_subtotal_paise INTEGER,

  received_note TEXT,

  created_at INTEGER NOT NULL,
  updated_at INTEGER NOT NULL,
  version INTEGER NOT NULL DEFAULT 1
)
```

Constraints:

```text
ordered_quantity > 0
received_quantity IS NULL OR received_quantity >= 0
final_quantity IS NULL OR final_quantity >= 0
price_at_order_time_paise >= 0
final_rate_paise IS NULL OR final_rate_paise >= 0
```

`order_item_id` MUST be durable and collision-resistant.

---

# 51. SQLite: additional_charges

```sql
additional_charges (
  charge_id TEXT PRIMARY KEY,
  order_id TEXT NOT NULL,
  business_id TEXT NOT NULL,
  name TEXT NOT NULL,
  amount_paise INTEGER NOT NULL,
  note TEXT,
  created_by TEXT NOT NULL,
  created_at INTEGER NOT NULL,
  updated_at INTEGER NOT NULL,
  version INTEGER NOT NULL DEFAULT 1
)
```

Constraint:

```text
amount_paise >= 0
```

Only Admin can create/edit before finalization.

---

# 52. SQLite: order_returns

```sql
order_returns (
  order_id TEXT PRIMARY KEY,
  business_id TEXT NOT NULL,
  return_status TEXT NOT NULL,
  return_required INTEGER NOT NULL,
  returned_at INTEGER,
  returned_by TEXT,
  return_note TEXT,
  updated_at INTEGER NOT NULL,
  version INTEGER NOT NULL DEFAULT 1
)
```

Allowed return status:

```text
NOT_REQUIRED
RETURN_PENDING
RETURNED
```

---

# 53. SQLite: invoices

```sql
invoices (
  invoice_id TEXT PRIMARY KEY,
  order_id TEXT NOT NULL,
  business_id TEXT NOT NULL,

  invoice_number TEXT NOT NULL,
  invoice_date INTEGER NOT NULL,

  status TEXT NOT NULL,
  revision_number INTEGER NOT NULL DEFAULT 0,

  original_invoice_id TEXT,
  revised_from_invoice_id TEXT,

  business_name TEXT NOT NULL,
  business_address TEXT NOT NULL,
  business_phone TEXT,
  business_alternative_phone TEXT,
  business_email TEXT,
  business_gstin TEXT,

  customer_name TEXT NOT NULL,
  customer_phone TEXT,
  customer_address TEXT,
  customer_business_name TEXT,

  subtotal_paise INTEGER NOT NULL,
  gst_applied INTEGER NOT NULL,
  gst_rate TEXT NOT NULL,
  gst_amount_paise INTEGER NOT NULL,
  final_amount_paise INTEGER NOT NULL,

  paid_amount_paise INTEGER NOT NULL,
  due_amount_paise INTEGER NOT NULL,
  payment_status TEXT NOT NULL,

  created_at INTEGER NOT NULL,
  created_by TEXT NOT NULL,

  immutable_at INTEGER NOT NULL
)
```

Recommended uniqueness:

```text
UNIQUE(business_id, invoice_number)
```

The original invoice snapshot is immutable.

---

# 54. SQLite: invoice_line_items

```sql
invoice_line_items (
  invoice_line_item_id TEXT PRIMARY KEY,
  invoice_id TEXT NOT NULL,
  order_item_id TEXT NOT NULL,

  item_id TEXT NOT NULL,
  item_name TEXT NOT NULL,

  service_id TEXT NOT NULL,
  service_name TEXT NOT NULL,

  quantity INTEGER NOT NULL,
  rate_paise INTEGER NOT NULL,
  line_subtotal_paise INTEGER NOT NULL
)
```

This is a historical invoice snapshot.

It MUST NOT be recomputed from current masters.

---

# 55. SQLite: invoice_additional_charges

```sql
invoice_additional_charges (
  invoice_charge_id TEXT PRIMARY KEY,
  invoice_id TEXT NOT NULL,
  charge_id TEXT,
  name TEXT NOT NULL,
  amount_paise INTEGER NOT NULL,
  note TEXT
)
```

The invoice snapshot remains stable even if the current order charge record is later represented differently.

---

# 56. SQLite: invoice_corrections

```sql
invoice_corrections (
  correction_id TEXT PRIMARY KEY,
  order_id TEXT NOT NULL,
  business_id TEXT NOT NULL,

  original_invoice_id TEXT NOT NULL,
  revised_invoice_id TEXT,

  revision_number INTEGER NOT NULL,

  reason TEXT NOT NULL,
  requested_by TEXT NOT NULL,
  approved_by TEXT NOT NULL,

  created_at INTEGER NOT NULL
)
```

The original invoice MUST remain immutable.

---

# 57. SQLite: payments

```sql
payments (
  payment_id TEXT PRIMARY KEY,
  order_id TEXT NOT NULL,
  business_id TEXT NOT NULL,

  amount_paise INTEGER NOT NULL,
  payment_method TEXT NOT NULL,
  payment_date INTEGER NOT NULL,

  recorded_by TEXT NOT NULL,
  note TEXT,

  created_at INTEGER NOT NULL,
  sync_id TEXT NOT NULL,

  entry_type TEXT NOT NULL DEFAULT 'PAYMENT',
  adjusts_payment_id TEXT,
  adjustment_reason TEXT
)
```

Constraints:

```text
amount_paise > 0
```

Original payment fields are immutable.

---

# 58. SQLite: status_history

```sql
status_history (
  status_history_id TEXT PRIMARY KEY,
  order_id TEXT NOT NULL,
  business_id TEXT NOT NULL,

  from_status TEXT,
  to_status TEXT NOT NULL,

  changed_at INTEGER NOT NULL,
  changed_by TEXT NOT NULL,
  reason TEXT,

  client_created_at INTEGER,
  server_accepted_at INTEGER,

  sync_id TEXT
)
```

Append-only.

---

# 59. SQLite: sync_queue

```sql
sync_queue (
  sync_id TEXT PRIMARY KEY,

  business_id TEXT NOT NULL,

  entity_type TEXT NOT NULL,
  entity_id TEXT NOT NULL,

  operation TEXT NOT NULL,

  status TEXT NOT NULL,

  created_at INTEGER NOT NULL,
  updated_at INTEGER NOT NULL,

  attempt_count INTEGER NOT NULL DEFAULT 0,
  last_attempt_at INTEGER,

  last_error_code TEXT,
  last_error_message TEXT
)
```

Required behavior:

```text
PENDING
→ PROCESSING
→ SYNCED
```

or:

```text
PENDING
→ PROCESSING
→ FAILED
→ PENDING
```

Failed work MUST remain recoverable.

---

# 60. Sync Dependency Model

If local work creates:

```text
Customer
  ↓
Order
  ↓
Payment
```

then sync MUST preserve dependency ordering.

The backend must never receive a child that references a parent that does not yet exist unless the operation is designed to create the full parent/child atomically.

Sync dependencies should be represented explicitly in queue metadata where required.

Do not invent fake dependencies.

---

# 61. Idempotency Records

Recommended Firestore path:

```text
businesses/{businessId}/idempotency/{syncId}
```

SQLite:

```sql
idempotency_records (
  sync_id TEXT PRIMARY KEY,
  business_id TEXT NOT NULL,
  entity_type TEXT NOT NULL,
  entity_id TEXT NOT NULL,
  operation TEXT NOT NULL,
  request_hash TEXT NOT NULL,
  state TEXT NOT NULL,
  result_reference TEXT,
  created_at INTEGER NOT NULL,
  completed_at INTEGER,
  error_code TEXT
)
```

## Invariant

The same `syncId` + same operation represents one logical operation.

A repeated retry MUST return/reconcile the existing authoritative result.

If the same `syncId` is reused with a materially different payload, the backend MUST reject it as an idempotency conflict.

---

# 62. Application Metadata

SQLite:

```sql
app_metadata (
  key TEXT PRIMARY KEY,
  value TEXT,
  updated_at INTEGER NOT NULL
)
```

Suitable values include:

```text
schema_version
last_successful_sync_at
current_business_id
last_auth_user_id
sync_engine_version
```

Sensitive authentication secrets MUST NOT be stored here.

---

# 63. Notification Tokens

FCM is optional for V1 but the model must support it.

SQLite:

```sql
notification_tokens (
  token_id TEXT PRIMARY KEY,
  user_id TEXT NOT NULL,
  business_id TEXT,
  token TEXT NOT NULL,
  platform TEXT NOT NULL,
  active INTEGER NOT NULL DEFAULT 1,
  created_at INTEGER NOT NULL,
  updated_at INTEGER NOT NULL
)
```

Notification data must be:

- role-aware;
- business-scoped;
- safe for lock screens;
- non-authoritative;
- retryable.

Notification failure MUST NOT roll back a business transaction.

---

# 64. SQLite Foreign-Key Relationships

Required logical relationships:

```text
users.business_id → businesses.business_id

staff.user_id → users.user_id
staff.business_id → businesses.business_id

customers.user_id → users.user_id
customers.business_id → businesses.business_id

items.business_id → businesses.business_id
services.business_id → businesses.business_id
prices.business_id → businesses.business_id

orders.business_id → businesses.business_id
orders.customer_id → customers.customer_id

order_items.order_id → orders.order_id
order_items.business_id → businesses.business_id

additional_charges.order_id → orders.order_id
additional_charges.business_id → businesses.business_id

order_returns.order_id → orders.order_id

invoices.order_id → orders.order_id
invoices.business_id → businesses.business_id

invoice_line_items.invoice_id → invoices.invoice_id
invoice_additional_charges.invoice_id → invoices.invoice_id

invoice_corrections.order_id → orders.order_id
invoice_corrections.original_invoice_id → invoices.invoice_id
invoice_corrections.revised_invoice_id → invoices.invoice_id

payments.order_id → orders.order_id
payments.business_id → businesses.business_id

status_history.order_id → orders.order_id
status_history.business_id → businesses.business_id

sync_queue.business_id → businesses.business_id
```

SQLite foreign keys MUST be enabled.

Protected historical records MUST NOT be cascaded into accidental deletion.

---

# 65. Deletion Policy

Prefer:

```text
ACTIVE / INACTIVE
```

over physical deletion for referenced:

- Item;
- Service;
- Staff;
- Customer.

Never casually hard-delete:

- orders;
- finalized invoices;
- payment history;
- status history;
- audit/correction history.

Account deletion requests that legally require personal-data removal must use a documented anonymization/deletion policy while retaining minimum legally/business-required financial history.

---

# 66. Customer Isolation

Customer authorization invariant:

```text
targetOrder.customerId == authenticatedCustomer.customerId
```

Customer can access only:

```text
own profile
own customer record
own orders
own permitted invoices
own permitted payment information
```

Customer MUST NOT gain access by modifying:

```text
orderId
customerId
businessId
document path
request payload
local SQLite data
```

Customer invoice access is locked before finalization.

---

# 67. Staff Isolation

Staff must:

```text
authenticated user
→ Staff profile
→ same business
→ allowed operational action
```

Staff cannot:

- manage Staff accounts;
- create registered Customer accounts;
- edit registered Customer profiles;
- deactivate Customers;
- edit received quantity;
- edit final quantity;
- edit final rate;
- edit billable lines;
- edit additional charges;
- change master prices;
- change GST settings;
- change Business Settings;
- edit/correct/reissue finalized invoices;
- rewrite payment history;
- rewrite status history;
- access another business.

Staff can:

- create WALK_IN orders;
- perform permitted operational status transitions;
- record payments;
- view operational payment ledger;
- view finalized invoices;
- finalize only unchanged financial/order data;
- select/confirm GST ON/OFF where allowed;
- sync permitted work.

---

# 68. Admin Isolation

Admin has administrative access only to the assigned business.

Admin can manage:

```text
Staff
Customers
Items
Services
Prices
Orders
Finalization
GST
Business Settings
Invoice Settings
Reports
Controlled corrections
```

Admin MUST NOT access another business by changing `businessId`.

No Super Admin exists in V1.

---

# 69. Protected Fields

At minimum, backend security must protect:

```text
role
businessId

finalAmountPaise
gstRate
gstAmountPaise
paidAmountPaise
dueAmountPaise
invoiceNumber

priceAtOrderTimePaise
finalRatePaise

customerNameAtOrder
customerPhoneAtOrder
customerAddressAtOrder
businessNameAtOrder
businessTypeAtOrder

invoice snapshots
business snapshots
customer snapshots

payment history
status history

createdBy
updatedBy
lastUpdatedBy
recordedBy
changedBy

financial timestamps
audit metadata
```

Protected-field rules must reject unauthorized writes.

---

# 70. Firestore Rules Strategy

Rules MUST be deny-by-default.

Every protected request must establish:

```text
authenticated UID
+
trusted user profile
+
role
+
businessId
+
target record businessId
+
ownership where applicable
+
allowed operation
+
allowed fields
```

For mutable business records, version/concurrency validation is required in the application/trusted backend layer.

Security Rules alone must not be treated as a substitute for Cloud Function validation of sensitive financial operations.

---

# 71. Firestore Path Security

A user MUST NOT gain access by changing:

```text
/businesses/{businessId}/...
```

The authenticated user's trusted business context must match the path business.

For Customer order access:

```text
order.customerId == authenticated customer's customerId
```

For Staff/Admin:

```text
resource.businessId == authenticated user's businessId
```

The same business boundary must be checked for nested payments, invoices, status history and corrections.

---

# 72. Firestore Field-Level Security

## Customer self-service writes

Customer may update only permitted profile fields:

```text
name
phone
address
pinCode
business-specific profile fields where applicable
```

Customer may NOT update:

```text
role
businessId
status
payment history
status history
pricing
GST settings
finalized financial data
invoice snapshots
order ownership
```

## Staff writes

Staff writes must be allow-listed.

Do not use broad "Staff can update order" rules.

Allowed Staff mutation must be limited to the exact operational fields and state transitions defined by the business contract.

## Admin writes

Admin may mutate approved business-management records within the assigned business.

Financial corrections must use controlled backend operations rather than unrestricted direct document mutation.

---

# 73. Cloud Functions Contract

Privileged Cloud Functions are required for operations where the client cannot safely be trusted.

At minimum:

```text
createStaffAccount
reserveInvoiceIdentity
finalizeInvoice
correctInvoice
correctPayment
performProtectedFinancialOperation
```

The implementation may split these into smaller functions.

## Function responsibilities

Every privileged function must:

1. authenticate;
2. load trusted user profile;
3. verify role;
4. verify business;
5. verify target resource;
6. validate current state;
7. validate allowed fields;
8. validate business rules;
9. validate version/concurrency;
10. validate idempotency;
11. execute atomic mutation;
12. return authoritative result;
13. avoid logging sensitive payloads.

---

# 74. Staff Account Creation Function

Only Admin can create Staff.

Input must contain only approved Staff creation information:

```text
name
mobile number
email/login
staffCode
temporary password
```

The trusted backend:

```text
verify Admin
→ verify business
→ create Firebase Auth account
→ create trusted user profile
→ create Staff profile
→ set forcePasswordChange = true
```

The client must never receive or control a privilege-escalation path.

Passwords are never stored in Firestore/SQLite.

---

# 75. Invoice Finalization Function

The trusted finalization operation is the authoritative financial mutation.

It must be idempotent.

It must reject:

- invalid state;
- invalid role;
- invalid business;
- invalid quantities;
- invalid rates;
- invalid charges;
- invalid GST;
- stale version;
- duplicate invoice identity;
- overpayment;
- duplicate retry with changed payload.

It must atomically create the immutable invoice snapshot and update the relevant order summary/state.

---

# 76. Invoice Correction Function

Only authorized Admin can initiate/approve the controlled correction path.

It must validate:

```text
Admin role
+
business
+
order
+
current invoice
+
correction eligibility
+
seven-day rule where applicable
+
reason
+
version
+
idempotency
```

It must create a new immutable invoice snapshot.

It must never mutate/destroy the original invoice.

---

# 77. Payment Function

Payment recording must validate:

```text
Staff/Admin role
+
same business
+
valid order
+
valid method
+
amount > 0
+
not overpaying
+
idempotency
+
concurrency
```

Then:

```text
append payment
→ reconcile
→ persist summary
→ return authoritative result
```

The original payment remains immutable.

---

# 78. Payment Correction Function

Only Admin controlled correction.

It must create an append-only adjustment/reversal record.

It must never rewrite the original payment row/document.

The exact refund mechanism is not invented here because the SRS explicitly says V1 must not invent an automatic refund amount or payment method.

---

# 79. Order Status Transition Function

For sensitive/online authoritative transitions, trusted backend validation must apply:

```text
current status
+
requested status
+
role
+
flow
+
preconditions
+
version
+
idempotency
```

Then atomically:

```text
order status update
+
status history append
+
audit
+
sync/idempotency result
```

Two devices attempting the same logical committed transition must not produce duplicate business events.

---

# 80. Financial Calculation Contract

## 80.1 Line subtotal

```text
lineSubtotalPaise = quantity × ratePaise
```

## 80.2 Taxable subtotal

```text
taxableSubtotalPaise =
    finalLineSubtotalPaise
    + additionalChargesTotalPaise
```

## 80.3 GST

```text
gstAmountPaise =
    taxableSubtotalPaise × gstRate / 100
```

Rounding:

```text
HALF-UP
```

to nearest paise.

## 80.4 GST OFF

```text
gstApplied = false
gstRate = 0
gstAmountPaise = 0
```

## 80.5 Final amount

```text
finalAmountPaise =
    taxableSubtotalPaise
    + gstAmountPaise
```

The same calculation contract must be used for:

- local previews;
- domain calculation;
- backend validation;
- invoice rendering;
- reports;
- tests.

UI calculation is never authoritative.

---

# 81. Financial Invariants

The backend MUST guarantee:

```text
money is integer paise
final amount derives from final billing data
additional charges are included
GST follows approved rule
payments are append-only
overpayment is rejected
due never becomes a silent negative value
advance payments remain linked to order
finalized financial data is protected
historical financial data is never rebuilt from current settings
corrections require Admin authority
```

---

# 82. Historical Integrity Invariants

Never silently rewrite:

```text
customer snapshot
business snapshot
item name snapshot
service name snapshot
priceAtOrderTimePaise
finalRatePaise
orderedQuantity
receivedQuantity
finalQuantity
invoice snapshot
payment history
status history
correction history
```

Current master-data edits apply only to future operations.

---

# 83. Local Transaction Boundaries

## Order creation

Atomically:

```text
orders
+
order_items
+
initial status_history
+
sync_queue
+
idempotency record where required
```

## Payment

Atomically:

```text
payment
+
payment summary/reconciliation
+
sync_queue
+
idempotency record
```

## Status transition

Atomically:

```text
order status
+
status_history
+
sync_queue
+
idempotency record
```

## Invoice finalization

Atomically:

```text
final invoice snapshot
+
invoice line items
+
invoice additional charges
+
invoice identity metadata
+
financial summary
+
invoice/order state
+
sync_queue
+
idempotency record
```

If a mandatory component fails, the local transaction rolls back.

---

# 84. Sync Transaction Boundary

A local offline mutation MUST follow:

```text
Validate
  ↓
SQLite transaction
  ├── domain data
  ├── audit/history/financial data
  ├── sync queue
  └── idempotency identity
  ↓
Commit
  ↓
UI reflects committed local state
```

A sync failure MUST NOT erase the local committed operation.

---

# 85. Sync Retry Rules

Retry must be:

- safe;
- idempotent;
- dependency-aware;
- restart-safe;
- recoverable;
- observable.

Do not create duplicate:

- orders;
- payments;
- invoice identities;
- status history;
- corrections.

---

# 86. Conflict Policy

## Non-financial catalog

Use:

```text
version-aware server-authoritative update
```

## Operational status

Use:

```text
state-machine validation
+
idempotent transition
```

## Customer profile

Use:

```text
authoritative version check
```

## Final invoice

```text
NO AUTOMATIC MERGE
```

## Payment

```text
append-only ledger
+
authoritative validation
```

## Historical audit

```text
append-only
+
never destructive merge
```

---

# 87. Concurrency / Version Contract

Mutable records that may be changed by Android and future Admin Web should include:

```text
version
updatedAt
updatedBy
```

Initial:

```text
version = 1
```

Every successful authoritative mutation increments the version.

A mutation based on a stale version MUST be rejected or routed to an explicit conflict path.

Financial conflicts MUST NOT be auto-merged.

---

# 88. Offline Authentication Boundary

When authentication is unavailable:

```text
queued work pauses
new privileges are not granted
already committed safe local work remains
sync resumes only after trusted authentication is restored
```

Offline mode MUST NOT grant privileges that were not already known and authorized.

A deactivated user must not bypass restrictions through stale local role data.

---

# 89. Logout / Shared Device Safety

Logout must:

- sign out Firebase;
- clear sensitive session state;
- prevent protected screen access;
- safely handle pending work;
- prevent another user from seeing the previous user's protected data.

Do not silently attach pending work to another user.

---

# 90. Data Retention / Deactivation

## Customer deactivation

Preserve:

```text
customer
orders
invoices
payments
status history
reports
```

Block:

```text
new customer-created orders
```

## Staff deactivation

Block:

```text
new authentication
new orders
new payments
status changes
```

Preserve:

```text
createdBy
recordedBy
changedBy
historical audit references
```

---

# 91. Report Source of Truth

Financial/reporting metrics must derive from:

```text
Finalized Invoice Snapshot
+
Payment Ledger
+
Finalized Operational Quantities
+
Historical Status Data
```

Never reconstruct financial history from:

```text
current price master
current GST setting
current customer profile
current business settings
```

Reports must distinguish:

```text
Final billed amount
Amount actually paid
Amount still due
```

Payment method totals must use the complete payment ledger.

Partially populated child datasets must not cause undercounting.

---

# 92. Report Periods

Supported report periods:

```text
WEEKLY
MONTHLY
YEARLY
```

Period calculations use:

```text
Asia/Kolkata
```

Report generation must be based on finalized/historical data.

If local data is incomplete because the device is offline, the application must warn that the report may be incomplete until synchronization finishes.

---

# 93. Document Generation Data Contract

V1 invoice PDFs and XLSX reports are local files.

Firebase Storage is not required for these documents.

Invoice PDF MUST use the finalized invoice snapshot.

Report XLSX MUST use the report contract/source-of-truth data.

Documents must never silently substitute current data for missing historical values.

---

# 94. UI Data Provenance Requirements

Backend DTO/repository mappings must preserve enough provenance for UI to distinguish:

```text
CURRENT
HISTORICAL
FINALIZED
ESTIMATED
SYNC_PENDING
SYNCED
```

The UI must be able to display:

- historical business name;
- historical customer name;
- historical rate;
- historical GST;
- historical additional charges;
- historical payment entries;
- invoice revision state.

Do not return only current master references where historical snapshots are required.

---

# 95. Security-Sensitive Query Rules

Every business query must apply:

```text
businessId = authenticatedBusinessId
```

before exposing business data.

Customer queries must additionally apply:

```text
customerId = authenticatedCustomerId
```

Large queries must use pagination.

Never download all Firestore collections into memory.

---

# 96. Firestore Security Rules: High-Level Contract

Rules should follow this conceptual model:

```text
isSignedIn()
AND
trustedProfileExists()
AND
sameBusiness(resource)
AND
roleAllowed()
AND
ownershipAllowed()
AND
fieldsAllowed()
```

Rules must deny by default.

Rules must reject:

```text
cross-business access
customer-to-customer access
Staff management by Staff
unauthorized price/GST changes
direct finalized invoice mutation
payment deletion
role escalation
businessId reassignment
inactive-account protected actions
```

---

# 97. Firestore Rules: Users

### Customer

Can read/write only permitted own profile fields.

Cannot change:

```text
role
businessId
status
forcePasswordChange
```

### Staff

Can read trusted own profile and permitted operational context.

Cannot self-change:

```text
role
businessId
status
```

### Admin

Can manage approved business records within their assigned business.

No Admin can change their own role to another role.

---

# 98. Firestore Rules: Customers

### Customer

Can access:

```text
own customer document
own permitted fields
```

Cannot access another customer.

### Staff

Staff can read operational customer information required by approved workflows, but cannot create/edit registered Customer profiles.

### Admin

Admin can manage Customer records in the assigned business, including activate/deactivate.

---

# 99. Firestore Rules: Orders

### Customer

Can:

```text
create own normal CUSTOMER order
read own orders
read permitted own order data
request permitted cancellation
```

Cannot:

```text
create WALK_IN
change businessId
change customer ownership
mutate protected financial data
mutate payment history
mutate status history
mutate finalized invoice
```

### Staff

Can:

```text
create WALK_IN
read business operational orders
perform allowed operational status changes
record payments
perform allowed finalization
```

Cannot:

```text
financial editing
registered customer management
protected invoice mutation
```

### Admin

Can manage assigned-business orders under approved business rules.

---

# 100. Firestore Rules: Payments

Payment documents are append-only.

Direct update/delete of original payment entries is prohibited.

New payment:

```text
Staff/Admin
+
same business
+
valid order
+
valid method
+
amount validation
```

Payment correction must use the controlled correction function.

---

# 101. Firestore Rules: Status History

Status history is append-only.

Client must not directly rewrite or delete history.

Trusted transition operation creates history.

---

# 102. Firestore Rules: Invoices

Finalized invoice snapshots are immutable.

Customer:

```text
read only after finalization
```

Staff:

```text
read
```

Admin:

```text
read
```

Correction/reissue:

```text
controlled Admin backend operation
```

No direct client update of finalized financial fields.

---

# 103. Firestore Rules: Master Data

Items, Services, Prices and Business Settings are Admin-controlled.

Staff and Customer cannot mutate them.

Master-data updates are version-aware.

A stale client MUST NOT silently overwrite a newer cloud version.

---

# 104. Firestore Rules: Inactive Accounts

An inactive Customer:

```text
cannot create new customer orders
```

An inactive Staff:

```text
cannot create orders
cannot record payments
cannot change operational status
```

These restrictions must be enforced server-side and cannot be bypassed by a stale session.

---

# 105. Cloud Function Transaction Rules

Where Firestore transaction/batched-write semantics are insufficient for a multi-step privileged operation, the trusted backend must orchestrate the operation so that partial financial success is impossible.

Invoice finalization must not produce:

```text
invoice number without invoice
final amount without final items
partial additional charges
GST inconsistent with GST rate
paid amount > final amount
```

---

# 106. Audit Trail

Audit data must capture, where applicable:

```text
createdAt
createdBy
updatedAt
lastUpdatedBy

changedAt
changedBy
fromStatus
toStatus
reason

cancelledAt
cancelledBy
cancellationReason

recordedBy

requestedBy
approvedBy
correctionId
```

Logs MUST NOT contain:

- passwords;
- authentication tokens;
- Firebase Admin credentials;
- unnecessary customer addresses;
- unnecessary phone/email;
- private internal notes;
- complete sensitive financial payloads unless explicitly required and secured.

---

# 107. Error Contract

Every backend/application error should expose:

```text
errorCode
safeUserMessage
diagnosticContext
retryable
```

Error categories:

```text
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

The UI must be able to distinguish:

```text
rejected
saved locally
pending sync
authoritatively succeeded
rolled back
```

Never report success for a rolled-back transaction.

---

# 108. Data Integrity Checks

Production validation must verify:

## Orders

```text
businessId valid
source valid
flow valid
status valid for flow
customer ownership valid where applicable
historical snapshot present
version valid
```

## Order items

```text
orderedQuantity > 0
rates non-negative
historical item/service names present
```

## Payments

```text
amountPaise > 0
valid method
valid order
same business
no overpayment
append-only
```

## Invoice

```text
unique invoice number
immutable snapshot
valid financial calculation
valid GST
valid payment reconciliation
valid revision relationship
```

---

# 109. SQLite Migration Contract

Every schema change requires a numbered migration:

```text
v1
v2
v3
...
```

Migrations must be:

- deterministic;
- tested;
- transactional where supported;
- safe against business-data loss.

Must preserve:

```text
orders
original quantities
received quantities
final quantities
historical rates
GST
invoice numbers
payments
status history
customer snapshots
invoice revisions
correction history
sync queue
```

Migration failure MUST NOT silently destroy business data.

---

# 110. Environment Separation

Use separate Firebase environments:

```text
DEVELOPMENT
STAGING / TEST
PRODUCTION
```

Each must have separate Firebase configuration.

Production MUST NOT contain:

- test customers;
- fake orders;
- development Staff accounts;
- test payments;
- staging credentials.

---

# 111. Backup and Recovery

Production Firestore must have automated backup.

Required before launch:

```text
scheduled backup
+
documented retention period
+
documented restore procedure
```

Restore procedure must validate:

- record counts;
- financial integrity;
- Security Rules;
- critical financial records;
- incident/recovery timestamp.

RPO/RTO values are operations decisions and must be approved before production launch.

---

# 112. Backend Observability

Safe diagnostic context may include:

```text
environment
appVersion
platform
operationName
syncId
entityType
non-sensitive entity identifier
errorCode
timestamp
networkState
```

Never log:

```text
passwords
auth tokens
Admin credentials
unnecessary customer PII
payment secrets
private internal notes
```

---

# 113. Schema-Level Business Invariants

The implementation MUST enforce all of the following:

1. Exactly three roles.
2. No client role escalation.
3. Every business-owned record has business scope.
4. Customer access is ownership-scoped.
5. Staff access is business-scoped.
6. Original order quantity is immutable historical truth.
7. Received quantity is distinct.
8. Final quantity is distinct.
9. Original rate is distinct from final rate.
10. Historical item/service names are preserved.
11. Estimates are distinct from final invoice.
12. Finalized invoice is immutable.
13. Corrections create revisions.
14. Original invoice remains preserved.
15. Invoice numbers are unique within business.
16. Payment history is append-only.
17. Payment corrections are append-only adjustments/reversals.
18. Overpayment is rejected.
19. Order status and payment status are independent.
20. Status history is append-only.
21. Invalid status transitions are rejected.
22. Cancelled orders remain historical records.
23. Physical return after cancellation is separately tracked.
24. Current settings never rewrite historical records.
25. Offline committed work survives restart.
26. Sync retries are idempotent.
27. Duplicate financial records are prevented.
28. Financial conflicts are never silently merged.
29. Client time cannot bypass financial/security windows.
30. Staff cannot perform Admin-only financial edits.
31. Inactive accounts cannot bypass restrictions.
32. Protected financial fields cannot be directly client-mutated.
33. Production/test data is separated.
34. No sensitive secrets are stored in client persistence.

---

# 114. Backend-to-UI Contract

The backend/repository DTO layer must provide the UI enough information to render:

```text
order status
payment status
invoice status
sync status
return status
estimate vs final
ordered vs received vs final quantities
historical snapshots
current valid invoice
original invoice history where authorized
payment ledger
due amount
```

The backend must not force UI code to reconstruct historical truth from current master data.

---

# 115. Backend-to-TRD Contract

This schema implements the TRD requirements for:

```text
repository persistence
SQLite
Firestore
Cloud Functions
authorization
offline sync
idempotency
concurrency
financial calculation
invoice protection
audit
pagination
migration
backup/recovery
observability
```

The application/domain layer remains responsible for reusable business policies such as state-machine validation; the backend must enforce the same rules authoritatively.

---

# 116. AI Agent Implementation Rules

AI coding agents MUST:

- use this schema as the persistence contract;
- preserve exact field semantics;
- preserve historical snapshots;
- use integer paise;
- preserve durable IDs;
- implement migrations;
- implement authorization server-side;
- implement idempotency;
- implement version/concurrency handling;
- add tests for protected fields;
- add cross-business tests;
- add customer-isolation tests;
- add payment overpayment tests;
- add duplicate-finalization tests;
- add duplicate-payment tests;
- add offline restart tests;
- add sync retry tests;
- add invoice correction tests.

AI agents MUST NOT:

- invent a role;
- invent a status;
- invent a payment method;
- invent a refund policy;
- merge financial conflicts;
- mutate finalized invoices directly;
- overwrite payment history;
- overwrite status history;
- infer missing historical values from current master data;
- create fake pickup times for walk-ins;
- use process-local durable IDs;
- bypass backend authorization.

---

# 117. Specification Blockers

## SB-ID-001 — Exact ID alphabet/regex

The SRS mandates an exact approved alphabet/regex but the supplied documents do not state the concrete final pattern.

**Required owner action:** approve the exact durable-ID alphabet and regex.

Until approved, implementation must not invent a business-specific pattern.

## SB-BACKEND-001 — Exact Firestore composite-index manifest

The source documents require indexes/query safety but do not provide an executable final `firestore.indexes.json`.

**Required implementation action:** generate and test the manifest from the exact implemented queries without changing business behavior.

## SB-RULES-001 — Executable Firestore Rules

The source documents define the security contract but do not provide the final executable rules file.

**Required implementation action:** generate rules and security tests from this contract.

## SB-OPS-001 — Backup retention / RPO / RTO

Exact retention, RPO and RTO are explicitly operations decisions.

**Required owner action:** approve values before production launch.

## SB-PAYMENT-001 — Exact refund execution mechanism

The SRS deliberately does not define an automatic refund amount/method.

**Rule:** do not invent one in backend code.

The append-only payment adjustment model is mandatory; the actual refund mechanism requires owner decision if implemented.

---

# 118. Recommended Firestore Index Families

The implementation should validate the following query families:

```text
customers:
  businessId + status + name
  businessId + phone
  businessId + email
  businessId + businessName

staff:
  businessId + status + name

orders:
  businessId + orderStatus + updatedAt
  businessId + paymentStatus + updatedAt
  businessId + createdAt
  businessId + customerId + createdAt
  businessId + invoiceNumber

items:
  businessId + status + name

services:
  businessId + status + name

prices:
  businessId + itemId + serviceId + status

payments:
  businessId + orderId + paymentDate
```

Do not create broad indexes merely for speculative future features.

---

# 119. Recommended SQLite Indexes

```sql
CREATE INDEX idx_users_business
ON users(business_id);

CREATE INDEX idx_staff_business_status_name
ON staff(business_id, status, name);

CREATE INDEX idx_customers_business_status_name
ON customers(business_id, status, name);

CREATE INDEX idx_customers_business_phone
ON customers(business_id, phone);

CREATE INDEX idx_customers_business_email
ON customers(business_id, email);

CREATE INDEX idx_customers_business_business_name
ON customers(business_id, business_name);

CREATE INDEX idx_items_business_status_name
ON items(business_id, status, name);

CREATE INDEX idx_services_business_status_name
ON services(business_id, status, name);

CREATE INDEX idx_prices_lookup
ON prices(business_id, item_id, service_id, status);

CREATE INDEX idx_orders_business_status_updated
ON orders(business_id, order_status, updated_at);

CREATE INDEX idx_orders_business_payment_status
ON orders(business_id, payment_status, updated_at);

CREATE INDEX idx_orders_business_customer
ON orders(business_id, customer_id, created_at);

CREATE INDEX idx_orders_business_created
ON orders(business_id, created_at);

CREATE INDEX idx_orders_invoice_number
ON orders(business_id, invoice_number);

CREATE INDEX idx_order_items_order
ON order_items(order_id);

CREATE INDEX idx_additional_charges_order
ON additional_charges(order_id);

CREATE INDEX idx_invoices_order
ON invoices(order_id);

CREATE INDEX idx_invoices_business_number
ON invoices(business_id, invoice_number);

CREATE INDEX idx_payments_order_date
ON payments(order_id, payment_date);

CREATE INDEX idx_status_history_order_changed
ON status_history(order_id, changed_at);

CREATE INDEX idx_sync_queue_status_created
ON sync_queue(status, created_at);

CREATE INDEX idx_sync_queue_entity
ON sync_queue(entity_type, entity_id);
```

These are implementation indexes, not new business behavior.

---

# 120. Production Readiness Test Matrix for This Contract

Before persistence is considered complete, test at minimum:

## Authentication

```text
customer registration
email verification
staff public registration rejected
admin setup
role routing
inactive account
force password change
```

## Isolation

```text
customer A cannot read customer B
customer cannot change businessId
staff cannot read another business
admin cannot read another business
deep-link ID manipulation rejected
```

## Order

```text
all four normal flows
walk-in creation
walk-in NULL pickup date/time
invalid transition rejection
duplicate transition retry
cancellation cutoff
physical return after cancellation
```

## Received/final

```text
ordered quantity preserved
received quantity differs
final quantity differs
staff edit rejected
admin edit allowed
historical names preserved
historical rates preserved
```

## Invoice

```text
GST ON
GST OFF
GST rounding
additional charges
multiple additional charges
final amount
invoice uniqueness
duplicate finalization retry
invoice immutability
revision relationship
seven-day lock
```

## Payments

```text
cash
UPI
online where allowed
advance payment
multiple payments
partial payment
full payment
overpayment rejection
append-only payment
payment correction
```

## Offline

```text
offline order
offline walk-in
offline payment
offline status
offline finalization
restart with pending queue
retry after network loss
duplicate retry
dependency ordering
failed queue retention
conflict detection
```

## Persistence

```text
transaction rollback
migration
foreign keys
version increment
stale write rejection
```

## Security

```text
Rules deny-by-default
protected field rejection
cross-business rejection
role escalation rejection
inactive-account bypass rejection
```

---

# 121. Final Backend Contract

The production backend must preserve the following model:

```text
USER
 ├── role
 └── business scope

BUSINESS
 ├── settings
 ├── staff
 ├── customers
 ├── items
 ├── services
 ├── prices
 └── orders

ORDER
 ├── original snapshot
 ├── order items
 ├── received quantities
 ├── final billing data
 ├── additional charges
 ├── invoice(s)
 ├── payments
 ├── status history
 ├── cancellation
 ├── physical return
 ├── payment summary
 └── audit/version

INVOICE
 ├── immutable snapshot
 ├── line snapshots
 ├── charge snapshots
 ├── GST snapshot
 ├── payment reconciliation
 └── revision relationship

PAYMENT
 └── append-only ledger event

SYNC
 ├── durable operation ID
 ├── dependency
 ├── retry state
 └── idempotent cloud result
```

The fundamental invariants are:

```text
CURRENT DATA ≠ HISTORICAL DATA
CUSTOMER REQUEST ≠ RECEIVED LAUNDRY ≠ FINAL BILL
ORDER STATUS ≠ PAYMENT STATUS
ESTIMATE ≠ FINAL INVOICE
ORIGINAL INVOICE ≠ REVISED INVOICE
PAYMENT ENTRY ≠ PAYMENT SUMMARY
LOCAL COMMIT ≠ CLOUD SYNC
UI VISIBILITY ≠ AUTHORIZATION
```

The backend implementation is production-ready only when these distinctions remain true across:

```text
online operation
offline operation
app restart
sync retry
duplicate retry
multiple devices
stale versions
role changes
account deactivation
invoice correction
payment correction
database migration
```

---

# 122. Final AI-Agent Instruction

Do not start coding from UI screens alone.

Implementation order for persistence/security:

```text
1. Canonical enums
2. Durable ID abstraction
3. SQLite migration v1
4. SQLite repositories
5. Domain state machine
6. Financial calculation module
7. Firestore mapping
8. Security Rules
9. Cloud Functions
10. Idempotency
11. Sync engine
12. Concurrency/version checks
13. Invoice/payment protection
14. Repository/use-case integration
15. Security tests
16. Offline tests
17. Financial tests
18. Migration tests
19. Real-device validation
```

Any genuine missing business decision must become:

```text
SPECIFICATION BLOCKER
```

It must not be hidden as a "reasonable default."

---

# 123. Closure

This document is derived from the supplied SRS V5.0, PRD V1.0, TRD V1.0 and UI/UX V1.0.

The implementation must preserve the authority chain:

```text
SRS V5.0
→ PRD V1.0
→ TRD V1.0
→ UI/UX V1.0
→ Backend Schema + Security Contract
→ Code
```

No downstream implementation choice may weaken a P0 security, financial-integrity, historical-integrity, offline-preservation or business-isolation requirement.

**END OF BACKEND SCHEMA + SECURITY CONTRACT V1.0**
