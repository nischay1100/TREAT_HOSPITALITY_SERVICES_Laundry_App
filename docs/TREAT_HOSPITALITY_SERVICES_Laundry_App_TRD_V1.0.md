# TREAT HOSPITALITY SERVICES — Laundry Management App
# Technical Requirements Document (TRD) — Production Master V1.0

**Document ID:** THS-LAUNDRY-TRD-V1.0  
**Product:** TREAT HOSPITALITY SERVICES Laundry Management App  
**Scope:** V1 Android Laundry Operations  
**Status:** Production Technical Contract  
**Parent Specification:** SRS V5.0  
**Product Contract:** PRD V1.0  
**Platform:** Android  
**Mobile Stack:** Expo + React Native + TypeScript + Expo Router  
**Local Database:** SQLite / Expo SQLite  
**Cloud:** Firebase Authentication + Cloud Firestore + Cloud Functions  
**Notifications:** FCM where enabled  
**Documents:** Local A4 PDF invoices + XLSX reports  
**Timezone:** Asia/Kolkata  
**Currency:** INR / integer paise  
**Primary Roles:** CUSTOMER / STAFF / ADMIN  
**Business:** TREAT HOSPITALITY SERVICES

---

# 0. Purpose

This TRD converts the approved SRS V5.0 and PRD V1.0 into a technical implementation contract.

It defines:

- application architecture;
- module boundaries;
- domain/use-case boundaries;
- repository contracts;
- SQLite responsibilities;
- Firebase responsibilities;
- Cloud Function responsibilities;
- authentication/session architecture;
- authorization enforcement;
- offline-first execution;
- sync queue and idempotency;
- concurrency/version handling;
- financial calculation boundaries;
- invoice protection;
- error handling;
- observability;
- environment separation;
- build/release controls;
- backup/recovery architecture;
- testing architecture;
- AI-agent implementation controls;
- change-control and specification-blocker procedures.

This document is an implementation contract, not a new product specification.

**SRS V5.0 remains the authoritative parent. PRD V1.0 remains the product-level contract.**

---

# 1. Authority, Precedence and Non-Invention

## 1.1 Document hierarchy

```text
SRS V5.0
   ↓
PRD V1.0
   ↓
TRD V1.0
   ↓
UI/UX Specification
   ↓
Backend Schema + Security Contract
   ↓
Test Matrix / Implementation Plan
   ↓
Code
```

The TRD may decide **how** an approved behavior is implemented.

The TRD must not decide **what the business behavior should be** when SRS/PRD already defines it.

## 1.2 Conflict precedence

If any conflict is discovered:

1. SRS V5.0 wins.
2. The conflict is recorded.
3. Affected implementation is blocked.
4. PRD is corrected if the conflict is in PRD.
5. TRD is corrected if the conflict is in TRD.
6. Downstream documents are updated.
7. Only then may affected implementation continue.

An implementation convenience is never a reason to weaken a P0 rule.

## 1.3 Specification Blocker

If a required behavior is genuinely absent from SRS + PRD + approved downstream contracts:

```text
SPECIFICATION BLOCKER
        ↓
Affected module / operation
        ↓
Missing business decision
        ↓
Owner decision
        ↓
Authoritative document update
        ↓
Schema / security / UI / tests updated
        ↓
Implementation
```

Agents must not fill the gap with:

- a guess;
- a common industry behavior;
- a mock;
- a placeholder business rule;
- an invented enum;
- an invented permission;
- an automatic fallback that changes business meaning.

## 1.4 Technical vs business decisions

Technical decisions allowed here include:

- layering;
- dependency direction;
- file/module organization;
- repository interfaces;
- adapter boundaries;
- transaction orchestration;
- queue mechanics;
- retry mechanics;
- logging architecture;
- test structure;
- build pipeline;
- environment handling.

Business decisions remain controlled by SRS/PRD, including:

- roles;
- permissions;
- statuses;
- order flows;
- cancellation rules;
- pricing meaning;
- GST meaning;
- payment acceptance;
- invoice correction;
- historical preservation;
- overpayment handling;
- customer registration;
- walk-in behavior.

---

# 2. Source Requirements Summary

The implementation must preserve these parent contracts.

## 2.1 Product

V1 is a focused laundry operating system, not an ERP.

Supported areas include:

- registered personal customers;
- registered business customers;
- walk-in customers;
- customer orders;
- Staff walk-in creation;
- business collection;
- customer drop-off;
- business delivery;
- customer self-collection;
- processing;
- received-laundry verification;
- final billing;
- GST;
- additional charges;
- payments;
- due tracking;
- invoices;
- reporting;
- offline operation;
- synchronization.

V1 excludes unrelated ERP functionality and future modules.

## 2.2 Roles

Exactly:

```text
CUSTOMER
STAFF
ADMIN
```

No additional production role may be introduced by an agent.

## 2.3 Critical Staff boundary

Staff may perform only the operational actions explicitly allowed by SRS/PRD.

In particular, Staff:

- may create WALK_IN orders;
- may perform permitted operational status actions;
- may record payments;
- may finalize an invoice only when no financial/order edit is required;
- may select/confirm GST ON/OFF at finalization where the approved rule permits.

Staff may not:

- create registered Customers;
- edit protected customer records;
- edit received quantity;
- edit final quantity;
- edit final rate;
- edit additional charges;
- edit master pricing;
- edit GST settings;
- edit business settings;
- edit/correct/reissue finalized invoices;
- edit old payments;
- delete payment history;
- deactivate Customers;
- escalate their role/business;
- access another business.

Backend authorization must enforce this independently of UI visibility.

## 2.4 Historical-data principle

```text
Current settings → future operations
Historical snapshots → historical records
```

Therefore old orders/invoices/reports must not be reconstructed from current:

- customer profile;
- price master;
- GST settings;
- business settings.

---

# 3. Technical Goals

## P0 — Never compromise

- authentication security;
- role isolation;
- business isolation;
- customer isolation;
- financial integrity;
- invoice finalization integrity;
- payment integrity;
- no overpayment;
- offline committed-data preservation;
- duplicate prevention;
- protected-field enforcement;
- historical integrity.

## P1 — Core implementation

- orders;
- collection/return flows;
- Staff operations;
- Admin management;
- Customers;
- received laundry;
- finalization;
- invoices;
- payments;
- GST;
- reports;
- sync.

## P2 — Supporting

- notifications;
- sync diagnostics;
- additional dashboard summaries;
- optional invoice presentation enhancements.

P2 must never weaken P0/P1 behavior.

---

# 4. High-Level Architecture

```text
                         ANDROID APPLICATION
                Expo + React Native + TypeScript
                              |
          +-------------------+-------------------+
          |                                       |
       Presentation                         Application
          |                                       |
     Screens / Routes                    View Models / Hooks
                                                  |
                                             Use Cases
                                                  |
                                            Domain Layer
                                                  |
                                      Repository Interfaces
                                      /                 \
                             SQLite Adapter       Firebase Adapter
                                  |                       |
                              SQLite DB             Firestore
                                  |                       |
                              Sync Queue          Cloud Functions
                                                          |
                                                Firebase Auth / FCM
```

Future:

```text
Android App --------+
                    |
                    +---- Firebase Backend
                    |
Admin Web ---------+
```

The future Admin Web uses the same backend data model. It does not justify duplicating business data.

---

# 5. Core Architectural Rules

## 5.1 Dependency direction

```text
UI
 ↓
Application / Use Cases
 ↓
Domain
 ↓
Repository Interfaces
 ↓
Infrastructure Adapters
```

Infrastructure must not leak upward.

The domain must not import:

- React;
- Expo UI;
- Expo Router;
- Firebase SDK;
- SQLite driver;
- platform-specific UI code.

## 5.2 UI rule

UI must never:

- directly write SQLite business records;
- directly perform Firebase business mutations;
- calculate authoritative financial values independently;
- decide authorization;
- decide whether a status transition is valid;
- mutate invoices;
- mutate payment history.

UI calls an approved use case.

## 5.3 Use-case rule

Every business mutation must pass through an explicit use-case boundary.

Examples:

```text
CreateCustomerOrder
CreateWalkInOrder
RecordPayment
TransitionOrderStatus
RecordReceivedLaundry
PrepareFinalization
FinalizeInvoice
CorrectInvoice
CorrectPayment
ManageStaff
ManageCustomer
ManagePrice
GenerateReport
SyncPendingOperations
```

The exact use-case inventory may be refined by downstream implementation planning, but no mutation may bypass domain/application boundaries.

## 5.4 Domain rule

Critical business calculations and rules must be centralized.

At minimum:

- order state machine;
- collection/return validity;
- cancellation cutoff validation;
- money arithmetic;
- GST calculation;
- payment reconciliation;
- due calculation;
- finalization validation;
- invoice correction eligibility;
- version/stale-write validation;
- ID validation/normalization where specified.

---

# 6. Recommended Repository Structure

The following is a technical organization contract. Exact filenames may be refined without changing behavior.

```text
src/
  app/
    routes/
    navigation/
    guards/

  presentation/
    customer/
    staff/
    admin/
    shared/
    components/
    hooks/
    view-models/

  application/
    use-cases/
    commands/
    queries/
    ports/
    dto/

  domain/
    entities/
    value-objects/
    enums/
    policies/
    state-machines/
    financial/
    validation/
    errors/

  data/
    repositories/
    sqlite/
      migrations/
      adapters/
      queries/
      transactions/
    firebase/
      adapters/
      mappers/
    sync/
      queue/
      engine/
      handlers/
      idempotency/

  services/
    auth/
    pdf/
    xlsx/
    notifications/
    diagnostics/
    connectivity/

  infrastructure/
    firebase/
    sqlite/
    logging/
    configuration/

  shared/
    constants/
    types/
    utilities/
```

The final Backend Schema document controls exact persisted field names.

Agents must not invent schema fields merely because a UI component needs one.

---

# 7. Module Ownership

## 7.1 Authentication

Responsible for:

- Firebase Auth session;
- login/logout;
- registration where permitted;
- password reset;
- email verification where required;
- session restoration;
- trusted local profile;
- role/business context retrieval.

Not responsible for deciding business permissions.

## 7.2 Authorization

Authorization is a layered system:

```text
UI capability visibility
        +
Application use-case authorization
        +
Backend Firestore Rules
        +
Cloud Function authorization
```

The backend is authoritative.

UI guards are usability controls, not security boundaries.

## 7.3 Domain

Owns business rules.

## 7.4 SQLite

Owns local operational persistence and pending offline work.

## 7.5 Firebase

Owns synchronized cloud/master business records.

## 7.6 Cloud Functions

Own privileged server-side operations and authoritative validation where client-side mutation cannot safely be trusted.

## 7.7 Sync Engine

Owns:

- pending operation discovery;
- dependency ordering;
- idempotent transmission;
- retry;
- stale-write handling;
- result reconciliation;
- diagnostics;
- restart recovery.

---

# 8. Authentication and Session Architecture

## 8.1 Customer

Customer self-registration is allowed through the approved Customer path.

## 8.2 Staff

Staff does not use public self-registration.

Staff account creation/management is Admin-controlled.

## 8.3 Admin

Admin creation/setup is controlled and is not an unrestricted public registration flow.

## 8.4 Startup sequence

```text
App launch
 ↓
Initialize secure runtime/config
 ↓
Initialize SQLite
 ↓
Restore Firebase auth state
 ↓
Load trusted local profile
 ↓
Validate role/business context
 ↓
Route to permitted area
 ↓
Check connectivity
 ↓
Run synchronization
```

The exact UI loading sequence is owned by UI/UX, but security checks must happen before privileged data/actions are exposed.

## 8.5 Logout

Logout must:

- sign out Firebase;
- clear sensitive session/auth state;
- prevent privileged screens/actions;
- preserve only local data that is explicitly safe to retain;
- never leave a usable privileged session behind.

Queued work must not be silently converted into a different user's work.

---

# 9. Role and Business Isolation

## 9.1 Authorization chain

For a protected operation:

```text
Authenticated UID
   ↓
Trusted profile
   ↓
Role
   ↓
Business ID
   ↓
Target record business ID
   ↓
Target ownership where applicable
   ↓
Allowed operation
   ↓
Current domain state
   ↓
Version/concurrency validation
   ↓
Mutation
```

Failure at any required layer rejects the operation.

## 9.2 Business scope

V1 operates one business, but businessId remains part of the architecture.

Business-owned records must retain business identity.

No client may:

- choose an arbitrary businessId;
- switch businessId;
- reassign a record to another business;
- access another business by modifying local state;
- bypass business scoping through direct document paths.

## 9.3 Customer isolation

Customer data access must be ownership-scoped.

Customer A must never obtain Customer B's:

- profile;
- orders;
- invoice;
- payment information;
- operational data.

Backend rules must enforce this.

---

# 10. Domain State Machine Architecture

The state machine is a domain service, not a UI feature.

```text
Current State
   +
Requested Transition
   +
Role
   +
Order Flow
   +
Relevant Preconditions
   ↓
Transition Validator
   ↓
Allowed / Rejected
```

## 10.1 Four canonical normal flows

The system must support:

```text
Pickup by Us + Delivery by Us
Pickup by Us + Customer Pickup
Customer Drop-Off + Delivery by Us
Customer Drop-Off + Customer Pickup
```

The exact state enum and transition graph remain governed by SRS V5.0 and must be represented once in the domain layer.

## 10.2 Transition invariants

The implementation must:

- reject invalid transitions;
- expose only valid next actions to UI;
- preserve append-only status history;
- store actor identity;
- preserve timestamps;
- prevent unauthorized reopening of protected final states;
- preserve cancellation information;
- track physical return where required.

## 10.3 No duplicated state logic

Do not create separate status logic in:

- Customer UI;
- Staff UI;
- Admin UI;
- SQLite adapter;
- Firebase adapter.

All must consume the same domain transition policy.

---

# 11. Order Data Architecture

An order must distinguish:

```text
Original Order Data
        ↓
Actual Received Data
        ↓
Final Billing Data
        ↓
Final Invoice Snapshot
```

These are not interchangeable.

## 11.1 Original data

Represents what was requested at order creation.

It must remain historically stable.

## 11.2 Received data

Represents what was actually received.

It may differ from original quantity.

Received quantity editing is Admin-controlled according to SRS/PRD.

## 11.3 Final billing data

Represents the approved billable quantity/rate/lines and additional charges used for final billing.

## 11.4 Walk-in data

Staff-created WALK_IN orders remain separate from registered Customer account creation.

Unscheduled WALK_IN pickup date/time must be stored as NULL according to the binding SRS rule.

The validator/use-case boundary must distinguish:

```text
Customer scheduled pickup
vs
Staff WALK_IN
```

Agents must not "fix" this by inserting fake dates/times.

---

# 12. ID Architecture

All business identifiers must follow the exact approved SRS rules.

## 12.1 Requirements

- durable;
- collision-resistant within required scope;
- deterministic validation;
- no process-local counters for durable Order Item IDs;
- exact approved alphabet/regex;
- business-scoped uniqueness where specified.

## 12.2 Prohibited

Never use:

- in-memory counters;
- array index as ID;
- timestamp-only IDs;
- random IDs without approved validation/uniqueness behavior;
- UI-generated identifiers that are later silently replaced.

If an identifier has already been committed locally and is referenced by pending sync operations, the identity must remain stable.

---

# 13. Financial Architecture

## 13.1 Authoritative representation

All persisted money uses:

```text
integer paise
```

Example:

```text
₹590.50 = 59050
```

JavaScript floating-point values must never be the authoritative persisted representation.

## 13.2 Line calculation

```text
lineSubtotalPaise = quantity × ratePaise
```

The result is integer paise.

## 13.3 GST

```text
taxableSubtotal =
    finalLineSubtotal
    + additionalChargesTotal

gstAmount =
    taxableSubtotal × gstRate / 100
```

GST uses half-up rounding to nearest paise.

When GST is OFF:

```text
gstApplied = false
gstRate = 0
gstAmount = 0
```

Final amount:

```text
finalAmount = taxableSubtotal + gstAmount
```

The same domain calculation must be used for:

- local calculations;
- backend validation;
- invoice rendering;
- reports;
- automated tests.

## 13.4 No financial authority in UI

UI may display calculated previews.

UI calculation is not authoritative.

Authoritative financial mutation must be validated by the domain/backend contract.

---

# 14. Payment Architecture

## 14.1 Payment ledger

Payments are append-only financial events.

Required conceptual fields include:

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
```

Exact field types/nullability/indexes are defined by the Backend Schema document.

## 14.2 Recording

A payment mutation must atomically:

```text
authorize
 ↓
validate amount
 ↓
validate business/order relationship
 ↓
validate payment method
 ↓
validate financial state
 ↓
write ledger event
 ↓
recalculate payment summary
 ↓
create sync operation
 ↓
commit
```

## 14.3 No direct edit/delete

Users must not directly mutate:

- original payment amount;
- original payment method;
- payment date;
- recorder;
- payment identity.

Admin correction follows the approved append-only adjustment/reversal model.

## 14.4 Reconciliation

After invoice finalization:

```text
paidAmount = sum(valid payment ledger entries)
dueAmount = finalAmount - paidAmount
```

If paid < final:

```text
PARTIALLY_PAID
due > 0
```

If paid = final:

```text
PAID
due = 0
```

Overpayment must never be silently accepted.

The exact Admin resolution/refund process is governed by SRS/approved correction contract; agents must not invent an alternative.

---

# 15. Invoice Architecture

## 15.1 Invoice is a protected financial snapshot

An invoice must not be reconstructed later from current master data.

The finalized invoice snapshot must contain the actual billing values used at finalization, including applicable historical identity/settings.

## 15.2 Finalization boundary

Invoice finalization is an atomic business operation.

Conceptually:

```text
Validate authorization
 ↓
Validate order state
 ↓
Validate original/received/final data
 ↓
Validate financial inputs
 ↓
Validate GST
 ↓
Calculate authoritative totals
 ↓
Reserve/validate invoice identity
 ↓
Create final invoice snapshot
 ↓
Reconcile valid payments
 ↓
Update invoice/order financial state
 ↓
Append status/history/audit information
 ↓
Create sync work
 ↓
Commit
```

Failure must not produce a partially finalized invoice.

## 15.3 Staff finalization

Staff may finalize only when no financial/order edit is required.

If quantity/rate/additional-charge/final billing modification is required, the operation moves to the Admin-controlled path.

## 15.4 Invoice visibility

A Customer must not receive final invoice access before finalization.

```text
Advance payment ≠ invoice finalized
Payment status ≠ order completion
```

## 15.5 Invoice correction

Finalized invoices are protected.

Admin correction:

- requires authorization;
- requires a reason;
- preserves original invoice;
- produces the approved revised invoice identity/relationship;
- preserves audit history;
- exposes the current valid invoice to permitted viewers;
- enforces the SRS correction eligibility and 7-calendar-day lock rules.

Staff and Customer cannot perform invoice correction.

---

# 16. Historical Integrity

Every implementation must preserve historical truth.

Examples:

```text
Current Customer Profile
        ≠
Historical Customer Snapshot
```

```text
Current Price
        ≠
Historical Order/Invoice Rate
```

```text
Current GST Setting
        ≠
Historical GST Snapshot
```

```text
Current Business Settings
        ≠
Historical Invoice Business Snapshot
```

Current master data may affect future operations only.

Historical records must remain explainable after:

- profile edits;
- price changes;
- GST changes;
- business-setting changes;
- staff deactivation;
- customer deactivation;
- invoice correction/reissue.

---

# 17. SQLite Architecture

## 17.1 Responsibility

SQLite is the local operational database for active Android workflows.

It is not the permanent backup.

## 17.2 Transaction rule

Each core mutation must use a real SQLite transaction.

Examples:

### Order creation

```text
Order
+
Order Items
+
Initial Status History
+
Sync Queue
```

### Payment

```text
Payment
+
Payment Summary/Reconciliation
+
Sync Queue
```

### Status transition

```text
Order State
+
Status History
+
Sync Queue
```

### Invoice finalization

```text
Final Invoice Snapshot
+
Invoice Number Reservation/Identity
+
Final Item Data
+
Additional Charges
+
GST Snapshot
+
Initial Payment Reconciliation
+
Invoice/Order State
+
Sync Queue
```

## 17.3 Atomicity rule

If any required part fails:

```text
ROLLBACK
```

No partially committed business operation is acceptable.

## 17.4 Foreign keys

SQLite foreign-key enforcement must be explicitly enabled and tested.

## 17.5 Migrations

Every schema change requires an explicit migration.

Migrations must preserve:

- orders;
- original quantities;
- received quantities;
- final quantities;
- historical rates;
- GST;
- invoice numbers;
- payments;
- status history;
- customer snapshots;
- pending sync work where applicable.

Migration failure must not silently destroy business data.

---

# 18. SQLite Security

Local SQLite is not treated as inherently trusted.

Implementation must:

- avoid storing unnecessary secrets;
- avoid storing authentication credentials/passwords;
- protect sensitive local session material using platform-appropriate secure storage;
- minimize sensitive diagnostic data;
- prevent accidental database replacement by test/dev data in production;
- validate migrations;
- protect local data from uncontrolled export/debug access in production builds.

The exact local encryption requirement, if needed beyond platform protection, must be explicitly approved in the technical/security contract rather than invented by an agent.

---

# 19. Firebase Architecture

## 19.1 Services

Use:

- Firebase Authentication;
- Cloud Firestore;
- Cloud Functions;
- Firebase Cloud Messaging where enabled.

## 19.2 Firestore

Firestore is the long-term cloud/master business record store.

The cloud schema must preserve:

- businessId;
- ownership;
- historical snapshots;
- immutable financial records;
- version/revision;
- actor/audit metadata;
- sync/idempotency metadata as defined by backend contract.

## 19.3 Client configuration

Public Firebase web configuration values may be present in Expo configuration.

Admin SDK credentials/private keys must never be shipped in:

- Android app;
- APK;
- React Native bundle;
- Git;
- committed .env;
- public configuration.

## 19.4 Client trust boundary

The client must never be trusted as the sole authority for:

- role;
- businessId;
- final amount;
- GST amount;
- payment validity;
- invoice identity;
- protected-field changes;
- correction eligibility;
- cross-business access.

---

# 20. Cloud Functions Architecture

Cloud Functions are required where authoritative server-side validation/privileged mutation is needed.

A function should follow:

```text
Receive request
 ↓
Authenticate caller
 ↓
Load trusted server-side identity
 ↓
Authorize role/business/ownership
 ↓
Validate schema
 ↓
Validate domain preconditions
 ↓
Validate version/idempotency
 ↓
Execute atomic server mutation
 ↓
Return authoritative result
```

## 20.1 Function design rules

Functions must be:

- idempotent where retries can occur;
- business-scoped;
- authorization-aware;
- validation-heavy for financial operations;
- safe under duplicate requests;
- safe under concurrent requests;
- observable without sensitive logging.

## 20.2 No hidden business rules

A Cloud Function may enforce the approved business rule.

It may not introduce a new business rule merely because server implementation needs a fallback.

---

# 21. Firestore Security Rules Architecture

Security Rules are mandatory and must be tested before production.

Rules must enforce at minimum:

## Customer

- own customer record only;
- own orders only;
- other customer denied;
- other business denied;
- finalized invoice mutation denied;
- payment deletion denied.

## Staff

- same business operational access only;
- other business denied;
- staff management denied;
- registered customer profile creation denied;
- received quantity edit denied;
- final rate edit denied;
- GST settings edit denied;
- price master edit denied;
- allowed payment creation permitted;
- old payment edit denied;
- unauthorized invoice correction denied.

## Admin

- same-business controlled access;
- other business denied;
- approved Admin corrections only;
- role escalation denied;
- businessId reassignment denied.

Rules are not replaced by UI checks.

---

# 22. Offline-First Architecture

## 22.1 Core sequence

```text
User Action
 ↓
Validate locally
 ↓
Domain validation
 ↓
SQLite transaction
 ↓
Immediate UI update
 ↓
Pending Sync Queue
 ↓
Connectivity available
 ↓
Firebase synchronization
```

The UI must not unnecessarily wait for Firebase before reflecting a successfully committed permitted local operation.

## 22.2 Offline authorization

Offline mode may use the user's last trusted authorization context only within the approved offline boundary.

Offline mode must never grant:

- a new role;
- a new business;
- new privileges;
- unauthorized financial editing.

## 22.3 Offline finalization

A locally finalized invoice may legitimately be:

```text
invoiceStatus = FINALIZED
syncStatus = PENDING
```

These are separate concepts.

The app must never tell the user that cloud synchronization succeeded unless cloud acknowledgement actually occurred.

---

# 23. Sync Queue Contract

Every offline mutation creates durable sync work in the same local transaction.

Conceptual fields:

```text
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

Additional fields may be defined by the Backend Schema document.

## 23.1 Queue states

The exact persisted enum must be defined once by the backend/schema contract.

The implementation must support at least the conceptual lifecycle:

```text
PENDING
 ↓
PROCESSING
 ↓
SUCCESS
```

and durable failure/retry behavior.

Agents must not invent user-visible queue semantics without the UI/UX contract.

## 23.2 Dependency ordering

Examples:

```text
Create Order
   ↓
Create Order Items
   ↓
Status Transition
   ↓
Payment
   ↓
Invoice Finalization
```

Actual dependency graphs must be defined by operation contracts.

A child operation must not be uploaded before its required parent identity exists.

## 23.3 Retry

Retries must be safe.

Repeated transmission of the same syncId must result in one logical operation.

---

# 24. Idempotency

Idempotency is mandatory for:

- order creation;
- payment recording;
- status transitions where duplicate submission is possible;
- invoice finalization;
- invoice correction/reissue;
- other mutation operations explicitly marked retryable.

## 24.1 Rule

```text
same syncId
+
same logical operation
=
same authoritative result
```

A retry must not create:

- duplicate order;
- duplicate payment;
- duplicate status-history event;
- duplicate invoice;
- duplicate correction.

## 24.2 Idempotency records

The exact Firestore idempotency collection/document structure is owned by the Backend Schema + Security Contract.

The implementation must not create ad-hoc duplicate-prevention mechanisms in individual screens.

---

# 25. Concurrency and Versioning

Mutable records must carry version/revision information where required.

## 25.1 Optimistic concurrency

Conceptually:

```text
Client reads version N
 ↓
Client submits mutation with expectedVersion N
 ↓
Server compares current version
 ↓
if current == N:
    apply mutation
    increment version
else:
    reject as stale/conflict
```

## 25.2 Financial records

Financial records must not be automatically merged when the merge could alter:

- final amount;
- GST;
- quantity;
- rate;
- payment total;
- invoice identity;
- protected financial history.

## 25.3 Status race

If two devices request the same valid transition:

- the authoritative backend must produce one logical transition;
- duplicate retry must not duplicate history.

## 25.4 Stale writes

A stale write must return a typed domain/transport error that allows the application to:

- preserve local work;
- refresh authoritative state;
- present an actionable conflict state;
- avoid silently overwriting newer data.

The UI/UX contract decides the exact presentation.

---

# 26. Conflict Handling

## 26.1 Non-financial conflicts

May use the approved version-aware conflict path.

## 26.2 Financial conflicts

Must not be silently auto-merged.

## 26.3 No destructive conflict resolution

Never:

```text
cloud wins and deletes local work
```

without preserving/reconciling the committed operation according to the approved sync contract.

Likewise never:

```text
local wins and overwrites cloud financial truth
```

without authoritative validation.

## 26.4 Conflict evidence

Failed/conflicted operations must retain enough non-sensitive diagnostic information to identify:

- syncId;
- entity;
- operation;
- attempt;
- error code;
- local/cloud version information where safe.

Do not log raw customer/financial payloads.

---

# 27. Connectivity and Sync Triggers

Sync engine should wake on:

- connectivity recovery;
- app startup;
- explicit permitted retry;
- background opportunity where platform constraints allow;
- successful completion of dependency operations.

The system must remain correct even if background execution is delayed or unavailable.

The user must never lose committed local work because a background task was not executed.

---

# 28. Error Architecture

Use typed application/domain errors.

Conceptual categories:

```text
AUTH_REQUIRED
FORBIDDEN
NOT_FOUND
VALIDATION_FAILED
INVALID_STATE_TRANSITION
STALE_VERSION
CONFLICT
DUPLICATE_OPERATION
OVERPAYMENT
INVOICE_PROTECTED
SPECIFICATION_BLOCKER
SYNC_RETRYABLE
SYNC_PERMANENT_FAILURE
NETWORK_UNAVAILABLE
STORAGE_FAILURE
MIGRATION_FAILURE
```

This is a technical error taxonomy. Exact user-facing copy belongs to UI/UX.

## 28.1 Error mapping

```text
Domain Error
 ↓
Application Error
 ↓
Presentation Error Model
 ↓
Safe User Message
```

Raw Firebase/SQLite exceptions must never be displayed directly.

## 28.2 Sensitive errors

Never expose:

- stack traces;
- service-account data;
- Security Rules internals;
- tokens;
- database internals;
- secrets;
- raw sensitive payloads.

---

# 29. Observability

Production must support:

- crash reporting;
- non-sensitive error logging;
- sync diagnostics;
- Cloud Function logs;
- performance monitoring;
- critical-operation monitoring.

Logs must not contain:

- customer personal data;
- full addresses;
- authentication credentials;
- tokens;
- secrets;
- service-account credentials;
- raw sensitive payloads.

Payment diagnostics must remain limited to approved non-sensitive information.

## 29.1 Correlation

Where technically appropriate, use non-sensitive correlation identifiers such as:

```text
syncId
operationId
requestId
function execution ID
```

Do not use customer names/mobile numbers as log correlation identifiers.

---

# 30. Performance Architecture

Priority operations:

1. dashboard load;
2. order search;
3. customer lookup;
4. order detail;
5. status update;
6. payment recording;
7. walk-in creation;
8. invoice generation;
9. reports;
10. synchronization.

Performance optimization must not:

- remove required validation;
- bypass transactions;
- weaken authorization;
- omit historical fields;
- skip audit/status history;
- use stale financial data as authoritative.

---

# 31. Search and Query Architecture

Search must be business-scoped.

Staff/Admin operational lookup may include approved:

- order ID;
- customer name;
- phone;
- customer information;
- invoice number where available.

Customer queries must be ownership-scoped.

Do not implement a global unscoped query and filter results only in the UI.

Indexes required for Firestore/SQLite will be finalized in the Backend Schema document.

---

# 32. Reporting Architecture

Reports must use authoritative finalized data.

Conceptual source:

```text
Finalized Invoice Snapshot
+
Payment Ledger
+
Finalized Operational Quantities
+
Historical Status Data
```

Not:

```text
Current Price Master
+
Current GST Setting
+
Current Customer Profile
```

Reports must distinguish:

```text
Final billed amount
Amount paid
Amount due
```

Payment aggregation must not undercount because a child dataset is partially populated or synchronization is incomplete.

XLSX output must match authoritative report calculations and must respect business/customer isolation.

---

# 33. Invoice PDF Architecture

V1 invoice PDFs are generated locally.

The PDF renderer consumes the finalized invoice snapshot, not current master data.

Required historical invoice identity/business information must come from the protected snapshot.

Rendering must be deterministic enough that the same finalized snapshot produces the same business meaning.

No PDF renderer may:

- query current price master to fill old rates;
- query current GST to replace historical GST;
- query current business identity to replace historical invoice identity;
- silently change totals.

Firebase Storage is not required for V1 invoice files.

---

# 34. XLSX Architecture

XLSX reports are generated from the report service's authoritative dataset.

Pipeline:

```text
Report Query
 ↓
Validated Report Model
 ↓
Business/Permission Filter
 ↓
XLSX Generator
 ↓
Local File
 ↓
Share/Export
```

The generator must not recalculate business totals differently from the report domain/service.

---

# 35. Notification Architecture

FCM may be enabled for V1.

Notification failure must not block a valid core business operation.

Customer notifications must not expose:

- internal audit information;
- sync errors;
- private staff information;
- unrelated customer data;
- sensitive operational diagnostics.

The notification service is secondary to the transaction that created the business event.

---

# 36. Environment Architecture

Required environments:

```text
Development
   ↓
Staging / Test
   ↓
Production
```

Each environment must use the correct Firebase project/configuration.

## 36.1 Rules

Development/test data must never be written to Production.

Production credentials must not be stored in:

- source control;
- shared developer configuration;
- test fixtures;
- debug builds;
- documentation examples.

## 36.2 Environment selection

The build must make the selected environment explicit and verifiable.

A production build must fail closed if required production configuration is missing or malformed.

---

# 37. Secrets Architecture

Mobile app may contain public Firebase web configuration.

It must never contain:

- Firebase Admin private keys;
- service-account JSON;
- backend private credentials;
- privileged server tokens;
- database administrator credentials.

Server secrets belong to the server/function environment.

Never use source-code comments as a substitute for secret management.

---

# 38. Build and Release Architecture

Production builds must:

- use locked dependency versions;
- pass TypeScript checks;
- pass lint;
- pass unit/domain tests;
- pass SQLite/repository tests;
- pass sync tests;
- pass Firebase Rules tests;
- pass Cloud Function tests;
- pass financial tests;
- pass authorization tests;
- pass invoice tests;
- pass report/XLSX tests;
- pass offline/restart tests;
- produce a valid Android build;
- pass real-device/emulator validation.

Debug tools and test credentials must be excluded from production.

---

# 39. Dependency Management

Use the current stable Expo SDK supported at implementation time, but lock exact dependency versions for the release.

No agent may upgrade a major dependency during feature implementation without:

1. identifying affected modules;
2. running regression tests;
3. checking Expo/RN compatibility;
4. updating the technical dependency record;
5. recording the change.

Dependency convenience must not modify business behavior.

---

# 40. Testing Architecture

Testing is layered.

```text
Unit
 ↓
Domain
 ↓
Repository / SQLite
 ↓
Firebase Rules
 ↓
Cloud Functions
 ↓
Sync
 ↓
Integration
 ↓
End-to-End
 ↓
Real Device
```

## 40.1 Unit/domain tests

Mandatory coverage includes:

- money arithmetic;
- GST;
- half-up rounding;
- order calculations;
- payment reconciliation;
- due calculation;
- status transitions;
- cancellation cutoffs;
- four collection/return combinations;
- customer validation;
- business validation;
- Staff permissions;
- Admin permissions;
- invoice finalization;
- invoice correction;
- seven-day correction lock;
- ID generation/validation;
- normalization;
- report mathematics.

Every business rule requires positive and negative tests where meaningful.

## 40.2 Persistence tests

Mandatory:

- migrations;
- foreign keys;
- transaction commit;
- transaction rollback;
- repository mapping;
- offline create;
- offline payment;
- offline status update;
- restart with pending queue;
- duplicate retry;
- network recovery;
- stale version;
- cross-device changes;
- incomplete payment history;
- invoice atomicity;
- invoice number uniqueness;
- correction/reissue;
- customer deactivation;
- Staff deactivation.

## 40.3 Security tests

Must cover Customer, Staff and Admin boundaries defined by SRS V5.0.

At minimum test:

- same-business allowed cases;
- cross-business denied cases;
- cross-customer denied cases;
- protected financial fields;
- payment deletion;
- payment editing;
- invoice mutation;
- Staff escalation;
- role changes;
- businessId changes.

---

# 41. Financial Integrity Test Architecture

Test combinations including:

- no payment;
- one payment;
- multiple payments;
- advance payment;
- partial payment;
- exact payment;
- overpayment;
- final amount lower than advance;
- GST ON;
- GST OFF;
- additional charges;
- multiple additional charges;
- different quantities;
- different rates;
- received quantity different from ordered;
- finalization;
- invoice correction;
- payment correction;
- cancellation before payment;
- cancellation after payment.

Invariant:

> No valid operation may silently lose, create, duplicate, or rewrite money.

---

# 42. Offline and Crash-Recovery Test Architecture

For each critical operation:

```text
Create offline
 ↓
Commit locally
 ↓
Kill app
 ↓
Restart
 ↓
Open record
 ↓
Reconnect
 ↓
Sync
 ↓
Restart
 ↓
Verify exactly one authoritative operation
```

Repeat for:

- order;
- walk-in;
- status;
- payment;
- permitted invoice finalization;
- cancellation where applicable.

Test crashes:

- before local commit;
- after local commit before sync;
- during sync;
- after cloud acknowledgement before local acknowledgement.

The implementation must reconcile these states idempotently.

---

# 43. Backup and Recovery

Production data requires:

- automated backup;
- defined retention;
- restore procedure;
- accidental-deletion recovery;
- disaster recovery process;
- restore validation.

SQLite is not the permanent backup.

Recovery must be tested before production release.

A recovery exercise must verify that restored data preserves:

- historical financial records;
- invoices;
- payments;
- status history;
- customer/business isolation;
- business identifiers;
- required audit relationships.

---

# 44. Data Integrity Invariants

The following are non-negotiable:

1. No cross-business access.
2. No customer cross-access.
3. No Staff privilege escalation.
4. No Staff unauthorized financial edits.
5. No unauthorized invoice finalization.
6. No invoice visibility before finalization.
7. No overpayment silently accepted.
8. No payment deletion.
9. No protected invoice mutation.
10. No historical price/GST/business snapshot rewrite.
11. No duplicate sync operation.
12. No duplicate invoice identity.
13. No lost committed offline operation.
14. No financial automatic conflict merge.
15. No client-clock bypass of correction rules.
16. No correction without Admin authorization and reason.
17. No destruction of original invoice during reissue.
18. No production secrets in mobile.
19. No sensitive data in logs.
20. No production/test data mixing.

---

# 45. Technical Forbidden Patterns

AI agents and developers must not implement:

```text
UI-only authorization
```

```text
Firebase mutation directly from screen components
```

```text
Financial totals based on floating point
```

```text
Current master data used to reconstruct historical invoice
```

```text
Direct UPDATE of immutable payment
```

```text
DELETE payment
```

```text
DELETE finalized invoice
```

```text
In-memory sync queue
```

```text
Process-local durable IDs
```

```text
Fake pickup date/time for WALK_IN
```

```text
Automatic financial conflict merge
```

```text
Silent overpayment offset
```

```text
Silent invoice overwrite
```

```text
Hard-coded role checks without backend enforcement
```

```text
Production Firebase project in development
```

```text
Admin SDK key in mobile app
```

```text
Raw Firebase/SQLite error displayed to user
```

```text
TODO fallback that invents business behavior
```

---

# 46. AI Coding Agent Control Protocol

This section is mandatory for every AI coding agent.

## 46.1 Agent operating mode

Agents operate as:

```text
IMPLEMENTATION EXECUTOR
```

not:

```text
PRODUCT DECISION MAKER
```

## 46.2 Before coding

Agent must identify:

1. governing SRS section;
2. governing PRD section;
3. governing TRD section;
4. relevant schema contract;
5. relevant security contract;
6. relevant UI/UX contract;
7. relevant tests;
8. affected files/modules;
9. expected inputs/outputs;
10. acceptance criteria.

If one of these is required but unavailable, the agent must determine whether the missing information is a Specification Blocker.

## 46.3 Before modifying existing code

Agent must inspect:

- imports;
- callers;
- consumers;
- repository interfaces;
- DTOs;
- domain models;
- schema mappings;
- Firebase functions;
- security rules;
- tests;
- sync handlers;
- related UI routes.

No isolated "fix" may break a connected contract.

## 46.4 Agent must not

- invent requirements;
- invent UI behavior that changes business rules;
- invent schema fields with business meaning;
- invent enum values;
- invent permissions;
- invent payment methods;
- invent refund behavior;
- invent conflict resolution;
- remove required validation;
- weaken security to make tests pass;
- use mocks as production behavior;
- silently substitute placeholder data;
- silently swallow errors;
- mark unfinished behavior as complete.

## 46.5 Dummy-code prohibition

The following are not acceptable production implementations:

```text
return mockData
```

```text
TODO: implement later
```

```text
throw new Error("not implemented")
```

hidden behind a production UI path;

```text
setTimeout(...)
```

used as fake backend behavior;

```text
Math.random()
```

used for durable business identity without approved contract;

hard-coded invoices/orders/payments used to simulate real persistence;

fake success responses;

fake sync completion;

fake payment acceptance;

fake authorization.

Temporary mocks are permitted only in explicitly marked test fixtures or isolated development scaffolding and must never satisfy a production acceptance criterion.

---

# 47. Agent Change-Safety Protocol

Every code change must answer:

```text
WHAT changed?
WHY?
WHICH REQUIREMENT authorizes it?
WHICH FILES are affected?
WHICH CONTRACTS are affected?
WHICH TESTS prove it?
```

For a mutation:

```text
UI
 ↓
Use Case
 ↓
Domain Rule
 ↓
Repository
 ↓
SQLite / Firebase
 ↓
Sync
 ↓
Security
 ↓
Tests
```

If any required layer is missing, the agent must not claim the feature is complete.

---

# 48. Traceability IDs

Downstream implementation should use stable requirement identifiers.

Recommended technical naming:

```text
TRD-ARCH-xxx
TRD-AUTH-xxx
TRD-DOM-xxx
TRD-ORDER-xxx
TRD-FIN-xxx
TRD-PAY-xxx
TRD-INVOICE-xxx
TRD-OFFLINE-xxx
TRD-SYNC-xxx
TRD-SEC-xxx
TRD-OBS-xxx
TRD-TEST-xxx
TRD-REL-xxx
TRD-AI-xxx
```

These identifiers are technical traceability labels and do not replace SRS/PRD authority.

The Test Matrix should map each ID to:

```text
SRS source
PRD source
TRD source
Implementation location
Test location
Acceptance result
```

---

# 49. Implementation Gates

No agent should jump directly from requirements to "complete app" coding.

## Gate 0 — Specification Freeze

Required:

- SRS V5.0 available;
- PRD V1.0 available;
- known conflicts resolved;
- Specification Blockers recorded.

## Gate 1 — Domain Contract

Required:

- exact enums;
- state machines;
- permission matrix;
- financial rules;
- cancellation rules;
- invoice correction rules;
- ID rules.

No UI implementation may redefine them.

## Gate 2 — Data Contract

Required:

- SQLite schema;
- Firestore schema;
- indexes;
- relationships;
- nullability;
- immutable fields;
- versions;
- audit;
- sync;
- migrations.

## Gate 3 — Security Contract

Required:

- Firestore Rules;
- Cloud Function authorization;
- business isolation;
- customer isolation;
- role boundaries;
- protected fields.

## Gate 4 — Infrastructure

Required:

- Firebase environments;
- SQLite initialization;
- migrations;
- repository adapters;
- sync engine;
- idempotency.

## Gate 5 — UI/UX

Only after domain/data/security contracts are approved.

## Gate 6 — Testing

All P0 tests must pass.

## Gate 7 — Release

Only after backup/recovery, production configuration, security, build and real-device validation pass.

---

# 50. Definition of Done for a Feature

A feature is not complete merely because a screen exists.

A feature is complete only when applicable:

```text
Requirement
+
Domain behavior
+
Persistence
+
Authorization
+
Offline behavior
+
Sync
+
Error handling
+
UI state
+
Audit/history
+
Tests
+
Real-device validation
```

have been satisfied.

For financial features also require:

```text
integer paise
+
server validation
+
idempotency
+
historical integrity
+
concurrency safety
```

---

# 51. Pull Request / Agent Output Contract

Every meaningful implementation task should produce a concise implementation report:

```text
Task:
Requirements:
Files changed:
Domain changes:
Persistence changes:
Backend changes:
Security changes:
Sync changes:
Tests added/updated:
Tests executed:
Known limitations:
Specification Blockers:
```

If no blocker exists:

```text
Specification Blockers: None
```

If a blocker exists, the agent must not hide it under "future improvement."

---

# 52. Specification Change Control

A business-rule change after SRS V5.0 freeze requires:

1. identify affected SRS section;
2. define new behavior explicitly;
3. update authoritative documentation;
4. increment SRS version;
5. update PRD;
6. update TRD;
7. update schema if needed;
8. update security rules if needed;
9. update UI/UX;
10. update tests;
11. update migration plan;
12. update reports/invoice logic if financial meaning changes.

No verbal instruction alone should silently alter production behavior.

---

# 53. Known Downstream Contract Boundaries

The following must be finalized in the next approved documents rather than guessed by implementation agents.

## 53.1 Backend Schema + Security Contract must define

- exact Firestore collections;
- exact document fields;
- exact SQLite tables;
- primary keys;
- foreign keys/references;
- field types;
- required/nullable fields;
- defaults;
- exact enums;
- indexes;
- uniqueness;
- immutable fields;
- version/revision fields;
- audit fields;
- sync fields;
- invoice revision relationship;
- payment ledger structure;
- idempotency structure;
- Security Rules;
- Cloud Function contract;
- transaction boundaries.

## 53.2 UI/UX Specification must define

- exact screen inventory;
- routes;
- navigation;
- layouts;
- component states;
- loading;
- empty;
- offline;
- sync;
- error;
- confirmation dialogs;
- form validation presentation;
- role-specific visibility;
- Staff workflow optimization;
- Customer workflow;
- Admin People workflow;
- invoice screens;
- payment screens;
- reports;
- accessibility;
- responsive behavior.

## 53.3 Test Matrix / Implementation Plan must define

- complete test IDs;
- requirement-to-test traceability;
- fixtures;
- test data;
- security test cases;
- offline scenarios;
- crash scenarios;
- concurrency scenarios;
- migration tests;
- financial edge cases;
- release evidence.

These boundaries are intentional. An agent must not fill them with undocumented business behavior.

---

# 54. Technical Specification Blocker Register

At TRD generation time, the following items are **not to be silently invented** if exact implementation detail is required downstream:

### SB-001 — Exact persistence schema
**Affected:** SQLite + Firestore  
**Missing:** final field-by-field schema contract.  
**Owner action:** approve Backend Schema + Security Contract.

### SB-002 — Exact security rules
**Affected:** Firestore  
**Missing:** complete executable Rules contract.  
**Owner action:** approve Rules document and tests.

### SB-003 — Exact Cloud Function API
**Affected:** privileged mutations  
**Missing:** final callable/HTTP function names, request/response schemas and transaction details.  
**Owner action:** approve Backend Contract.

### SB-004 — Exact sync operation catalog
**Affected:** Sync Engine  
**Missing:** complete operation list/dependency graph/result contract.  
**Owner action:** approve Sync section of Backend Contract/Test Matrix.

### SB-005 — Exact UI states/routes
**Affected:** presentation layer  
**Missing:** final screen-level route/state contract.  
**Owner action:** approve UI/UX Specification.

These are downstream documentation gates, not permission to invent product behavior.

---

# 55. Required Cross-Document Consistency Rules

The following must remain identical across documents:

```text
Roles
Permissions
Order sources
Collection modes
Return modes
Canonical states
Payment methods
Payment status
Financial formulas
GST behavior
Cancellation cutoffs
Invoice visibility
Invoice correction rules
7-day correction lock
Historical snapshot rules
Walk-in behavior
Business isolation
Customer isolation
Offline authorization
Sync/idempotency rules
```

If a downstream document changes any of these, it is a specification change, not a technical refinement.

---

# 56. Production Readiness Checklist

## Specification

- [ ] SRS V5.0 frozen
- [ ] PRD V1.0 aligned
- [ ] TRD approved
- [ ] UI/UX approved
- [ ] Backend Schema approved
- [ ] Security Rules approved
- [ ] Test Matrix approved
- [ ] No unresolved P0 Specification Blockers

## Code quality

- [ ] TypeScript passes
- [ ] Lint passes
- [ ] Dependency versions locked
- [ ] No debug credentials
- [ ] No Admin credentials
- [ ] No production mock data
- [ ] No unsafe TODOs
- [ ] No fake success paths

## Domain

- [ ] Exact enums
- [ ] State machine
- [ ] Permissions
- [ ] Financial rules
- [ ] Invoice rules
- [ ] Payment rules
- [ ] Historical snapshots
- [ ] Correction rules

## Persistence

- [ ] SQLite migrations
- [ ] Foreign keys
- [ ] Real transactions
- [ ] Rollback tests
- [ ] Repository mapping
- [ ] Firestore mapping
- [ ] Business scoping
- [ ] Versioning

## Security

- [ ] Firebase Auth
- [ ] Firestore Rules
- [ ] Cloud Function authorization
- [ ] Customer isolation
- [ ] Business isolation
- [ ] Staff boundary
- [ ] Admin boundary
- [ ] No client-only authorization

## Offline

- [ ] Order offline
- [ ] Walk-in offline
- [ ] Status offline
- [ ] Payment offline
- [ ] Permitted finalization offline
- [ ] Restart recovery
- [ ] Retry
- [ ] Duplicate prevention
- [ ] Dependency ordering
- [ ] Conflict handling

## Financial

- [ ] Integer paise
- [ ] GST half-up
- [ ] No overpayment
- [ ] Advance reconciliation
- [ ] Append-only payments
- [ ] Invoice atomicity
- [ ] Invoice uniqueness
- [ ] Correction/reissue
- [ ] 7-day lock
- [ ] Historical integrity

## Documents

- [ ] A4 invoice
- [ ] Historical snapshot rendering
- [ ] XLSX report
- [ ] Permission filtering
- [ ] No sensitive data leak

## Operations

- [ ] Backup enabled
- [ ] Restore tested
- [ ] Monitoring active
- [ ] Production Firebase verified
- [ ] Environment separation verified
- [ ] Privacy requirements verified
- [ ] Real device tested

---

# 57. Final AI Agent Contract

Every AI coding agent working on this project must operate under the following rule:

> **Implement the approved contract; do not design the business.**

The agent may:

- write code;
- refactor without behavior change;
- implement approved schemas;
- implement approved screens;
- implement approved tests;
- fix verified defects.

The agent may not decide:

- what Staff should be allowed to do;
- what Admin should be allowed to do;
- whether a payment should be accepted;
- whether an invoice should be editable;
- whether a status transition should exist;
- whether a Customer should be deactivated;
- whether a financial conflict should be auto-merged;
- whether an old invoice should be changed.

If the required behavior is missing:

```text
STOP
→ Specification Blocker
→ Owner Decision
→ Document Update
→ Contract Propagation
→ Tests Update
→ Implementation
```

---

# 58. Final Technical Principle

The application must be built as:

```text
Deterministic Domain
+
Controlled Use Cases
+
Transactional SQLite
+
Authoritative Firebase
+
Backend Authorization
+
Idempotent Offline Sync
+
Versioned Concurrency
+
Immutable Historical Financial Records
+
Auditable Corrections
+
Tested Release Gates
```

The goal is not merely to produce working screens.

The goal is to produce a system in which:

```text
Business rule
      ↓
Documented requirement
      ↓
Technical contract
      ↓
Domain implementation
      ↓
Persistence
      ↓
Authorization
      ↓
Synchronization
      ↓
UI
      ↓
Automated test
      ↓
Release evidence
```

can be traced without ambiguity.

---

# 59. Final Authority Statement

This TRD is derived from:

- `TREAT_HOSPITALITY_SERVICES_Laundry_App_Production_Master_SRS_V5.0.md`
- `TREAT_HOSPITALITY_SERVICES_Laundry_App_PRD_V1.0.md`

The SRS remains the authoritative parent.

The PRD remains the product contract.

This TRD defines technical implementation boundaries without changing product behavior.

No AI agent or developer may use this TRD to weaken:

- security;
- permission boundaries;
- financial integrity;
- historical integrity;
- offline integrity;
- customer isolation;
- business isolation.

**Code existing is not proof of requirement completion.**

Production readiness requires the approved requirement to be:

```text
Implemented
+
Persisted
+
Authorized
+
Offline-safe where required
+
Synchronized
+
Displayed
+
Tested
+
Historically preserved
+
Validated on a real Android target
```

**End of TRD V1.0**
