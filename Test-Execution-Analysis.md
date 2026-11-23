# Test Execution Analysis

## 1. Assumptions & Missing Inputs

| ID | Assumption / Missing Input | Impact |
|---|---|---|
| AM-01 | The repository descriptions are the complete test basis; no product catalogue, system specification, data model, API contract, batch schedule, or acceptance criteria were supplied. | Technical details and exact transaction rules cannot be asserted. |
| AM-02 | A regulated financial client is onboarding before account activation and must complete documentation, setup, mandate, and compliance controls. | Drives risk-based coverage of the lifecycle. |
| AM-03 | “Mandatory”, “eligible”, “resolved”, and “completed” are workflow status labels whose exact values are not defined. | Oracle gaps on field names and status codes. |
| AM-04 | All references to individual, corporate, and institutional clients are treated as personas, but sample identifiers and acceptable KYC/CDD evidence are not supplied. | Concrete test data must be reviewed with the BA/PO. |

Clarifications required:

1. Which documents are mandatory for each client type?
2. What status, field values, and audit event confirm that a checkpoint is complete?
3. Which teams own documentation, account setup, mandate, compliance, operations, and activation decisions?
4. What is the authoritative account structure, product eligibility, currency, limit, and operating-rule matrix?
5. Are systems/APIs and data stores used in onboarding? If so, provide contracts, integration endpoints, and error behaviour.
6. What are approval thresholds, exception owners, and escalation deadlines?
7. What measurable first-time-right activation criteria and service levels apply?

## 2. Requirement Inventory & Risk Assessment

| Req ID | Description | Source | Business Rules | Personas | Systems / Interfaces | Data Entities | Change Type | Risk | Rationale |
|---|---|---|---|---|---|---|---|---|---|
| REQ-01 | Clarify client entry requirements and set expectations. | `onboarding_lifecycle.md` 1. Client Entry | Documentation intake and requirements clarification occur before setup. | Individual, Corporate, Institutional | Assumed onboarding workflow; not specified | Client type, documents, requirements | Impacted | Medium | Incomplete intake can create downstream rework. |
| REQ-02 | Receive and verify mandatory documents. | `account-opening-validation-template.md` Client & Documentation | Mandatory documents must be received and verified; documentation must be consistent. | Individual, Corporate, Institutional | Assumed document/KYC systems; not specified | Names, identifiers, documents | Impacted | High | Incomplete or inconsistent documents increase regulatory and activation risk. |
| REQ-03 | Validate account structure and product eligibility. | `account-opening-validation-template.md` Account Setup | Account structure and product eligibility must be confirmed. | Individual, Corporate, Institutional | Assumed account setup system; not specified | Account structure, product | Impacted | High | Invalid setup may prevent accurate and compliant activation. |
| REQ-04 | Review currency, limits, and operating rules. | `account-opening-validation-template.md` Account Setup | Currency, limits, and operating rules must be reviewed. | Individual, Corporate, Institutional | Assumed account setup system; not specified | Currency, limits, operating rules | Impacted | High | Incorrect financial configuration can affect client transactions. |
| REQ-05 | Verify signatories and approval hierarchy. | `account-opening-validation-template.md` Mandate & Authority | Signatories and approval hierarchy must be validated. | Individual, Corporate, Institutional | Assumed mandate system; not specified | Signatories, mandates, approvers | Impacted | High | Incorrect authority can create compliance and fraud exposure. |
| REQ-06 | Document and approve exceptions. | `account-opening-validation-template.md` Mandate & Authority | Mandate exceptions must be documented and approved. | Corporate, Institutional, exception approver | Assumed workflow/approval system | Exception reason, approver, approval | Impacted | High | Unsupported exceptions can cause unauthorized account operation. |
| REQ-07 | Complete KYC/CDD checkpoints. | `account-opening-validation-template.md` Risk & Compliance | KYC/CDD checkpoints must be completed. | Individual, Corporate, Institutional | Assumed compliance system; not specified | KYC/CDD status, evidence | Impacted | High | Regulatory exposure and activation risk. |
| REQ-08 | Log and track outstanding issues. | `account-opening-validation-template.md` Risk & Compliance | Outstanding issues remain visible and tracked. | Operations, Compliance, client owner | Assumed issue/workflow system | Issue status, owner, due date | Impacted | High | Untracked blockers can reach activation. |
| REQ-09 | Prevent unresolved onboarding blockers at activation. | `account-opening-validation-template.md` Risk & Compliance | No unresolved onboarding blockers are allowed at activation. | Operations, Compliance, approver | Assumed activation control | Blocker, status, approval | Impacted | High | Directly supports compliance and first-time-right activation. |
| REQ-10 | Complete end-to-end validation, inform operations, and activate only when ready. | `account-opening-validation-template.md` Activation Readiness | End-to-end validation must be complete and operations informed before activation. | Operations, client owner, approver | Assumed activation workflow and communication system | Readiness status, notification, activation status | Impacted | High | A premature activation can require post-activation rework. |
| REQ-11 | Stabilize the new client by monitoring early-life onboarding defects. | `onboarding_lifecycle.md` 4. Stabilization | Early-life monitoring should catch onboarding defects before they affect trust. | Operations, client owner | Assumed monitoring/reporting system | Activation defects, incidents | Impacted | Medium | Defects can affect trust and transactions after activation. |
| REQ-12 | Maintain changes and periodic reviews without reintroducing risk. | `onboarding_lifecycle.md` 5. Ongoing Maintenance | Maintenance changes and periodic reviews preserve controlled onboarding risk. | All personas, operations, compliance | Assumed maintenance workflow | Change request, review status | Impacted | Medium | Poor maintenance can invalidate mandates or compliance status. |
| REQ-13 | Treat first-time-right activation as the success measure. | `onboarding_lifecycle.md` Key Principle | Success means accurate and compliant first-time-right activation. | All personas | Assumed reporting/measurement system | First-time-right result, rework | Impacted | High | Cross-cutting outcome for quality and risk. |

## 3. SIT Thematic Areas

| Theme ID | Level | Theme Name | Objective | Req IDs | Systems / Personas | Risk & Rationale | Test Techniques | Dependencies / Entry | Oracle Gaps | Est. Cases |
|---|---|---|---|---|---|---|---|---|---|---|
| SIT-01 | SIT | Onboarding Data Completeness | Verify that required client/document data and document consistency are controlled. | REQ-01, REQ-02 | Document intake; onboarding operations; Individual, Corporate, Institutional | High: incomplete or inconsistent identity data is a regulatory and activation risk. | BVA, EP, Error Guessing | Test data model and document rules supplied | Mandatory fields and exact validation messages/statuses | 5 |
| SIT-02 | SIT | Setup and Configuration Integrity | Verify account structure, product, currency, limits, and operating rules are stored consistently. | REQ-03, REQ-04 | Account setup; operations | High: invalid setup can cause unusable or non-compliant accounts. | EP, Decision Table, Back-to-Back | Authoritative configuration matrix and test system/data | Eligible combinations and expected stored values | 5 |
| SIT-03 | SIT | Mandate and Authority Controls | Verify signatories, hierarchy, and exception approval controls. | REQ-05, REQ-06 | Mandate workflow; approvers | High: unauthorized authority or undocumented exceptions create serious compliance risk. | Decision Table, State Transition, EP | Mandate rules and approver matrix | Valid authority combinations and exact approval states | 5 |
| SIT-04 | SIT | KYC/CDD State and Issue Tracking | Verify compliance checkpoints and outstanding issues prevent unsafe activation. | REQ-07, REQ-08, REQ-09 | Compliance and issue systems | High: unresolved blockers must not reach activation. | State Transition, EP, Error Guessing | Compliance statuses and issue lifecycle | Status names, audit fields, blocker rules | 5 |
| SIT-05 | SIT | Activation Boundary and Integration Handoff | Verify readiness is complete and operations is notified before activation. | REQ-10, REQ-13 | Activation workflow; operations notification | High: activation boundary directly prevents rework and downstream harm. | State Transition, EP, Integration | Interface contract and notification mechanism | Trigger, payload, status codes, recipient and timing | 5 |
| SIT-06 | SIT | Recovery and Maintenance Regression | Verify maintenance and stabilization data changes do not reintroduce onboarding risk. | REQ-11, REQ-12, REQ-13 | Monitoring, maintenance workflow | Medium: defects or changes can invalidate an otherwise complete setup. | State Transition, Regression, Error Guessing | Reversal/change use cases and rollback capability | Repair, reapproval, rollback and monitoring rules | 4 |

## 4. UAT Thematic Areas

| Theme ID | Level | Theme Name | Objective | Req IDs | Systems / Personas | Risk & Rationale | Test Techniques | Dependencies / Entry | Oracle Gaps | Est. Cases |
|---|---|---|---|---|---|---|---|---|---|---|
| UAT-01 | UAT | Client Intake and Document Readiness | Validate that a client receives clear requirements and submits complete, consistent documentation. | REQ-01, REQ-02 | Client, client owner, operations | High: document gaps are identified in the case study as a major delay cause. | Use-Case, EP | Approved document checklist and persona data | Mandatory documents and acceptance/rejection wording | 4 |
| UAT-02 | UAT | Account Configuration Journey | Validate the client’s account structure, product, currency, limits, and operating rules. | REQ-03, REQ-04 | Client, operations, approver | High: incorrect setup prevents accurate transacting. | Use-Case, Decision Table | Approved eligibility/configuration matrix | Exact eligible combinations and user-visible confirmation | 4 |
| UAT-03 | UAT | Mandate Approval and Exception Handling | Validate that authority is clear and exceptions are reviewed and approved. | REQ-05, REQ-06 | Signatories, corporate/institutional approvers, compliance | High: invalid authority and undocumented exceptions are compliance risks. | Use-Case, State Transition | Signed mandate examples and exception policy | Exception criteria, approver and audit record | 4 |
| UAT-04 | UAT | Compliance and Activation Readiness | Validate that KYC/CDD and outstanding issues are resolved before activation. | REQ-07, REQ-08, REQ-09 | Compliance, operations, approver | High: unresolved issues must block activation. | Use-Case, State Transition | Completed compliance examples and blocker cases | Exact readiness criteria and block behavior | 4 |
| UAT-05 | UAT | First-Time-Right Activation | Validate that end-to-end review is complete and operations/client readiness is achieved without manual intervention. | REQ-10, REQ-13 | Client, operations, client owner, approver | High: activation must be accurate and compliant at first attempt. | Use-Case, EP | Approved activation checklist and communications | Activation confirmation, transaction capability, first-time-right metric | 4 |
| UAT-06 | UAT | Stabilization and Ongoing Maintenance | Validate that early defects are detected and future changes preserve risk controls. | REQ-11, REQ-12, REQ-13 | Operations, compliance, client owner | Medium: changes or missed defects may reintroduce risk. | Use-Case, State Transition, Regression | Maintenance/change scenarios | Monitoring period, change approval, rollback and notification rules | 4 |

## 5. Theme-to-Requirement Traceability Matrix

| Requirement | SIT Themes | UAT Themes | Coverage |
|---|---|---|---|
| REQ-01 | SIT-01 | UAT-01 | Covered |
| REQ-02 | SIT-01 | UAT-01 | Covered |
| REQ-03 | SIT-02 | UAT-02 | Covered |
| REQ-04 | SIT-02 | UAT-02 | Covered |
| REQ-05 | SIT-03 | UAT-03 | Covered |
| REQ-06 | SIT-03 | UAT-03 | Covered |
| REQ-07 | SIT-04 | UAT-04 | Covered |
| REQ-08 | SIT-04 | UAT-04 | Covered |
| REQ-09 | SIT-04 | UAT-04 | Covered |
| REQ-10 | SIT-05 | UAT-05 | Covered |
| REQ-11 | SIT-06 | UAT-06 | Partial – monitoring period and expected defect action are undefined |
| REQ-12 | SIT-06 | UAT-06 | Partial – change types and approval/revalidation rules are undefined |
| REQ-13 | SIT-05, SIT-06 | UAT-05, UAT-06 | Partial – metric calculation and target are undefined |

## 6. SIT Test Cases

### SIT-01 — Onboarding Data Completeness

| TC ID | Theme ID | Level | Req ID | AC Ref | Scenario | Description | Preconditions | Test Data | Steps | Expected Result | Type | Priority | Oracle / Assumption |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| SIT-SIT01-001 | SIT-01 | SIT | REQ-01, REQ-02 | Intake, mandatory documents | Complete document intake | Verify that a complete record proceeds to the next onboarding stage. | Client type and mandatory document list are approved. | Concrete identity/document values supplied by BA; assumption AM-04 | 1. Create a client record. 2. Attach all mandatory documents. 3. Submit for verification. | TEST ORACLE GAP – mandatory record fields/status transition not sufficiently defined. BA/PO to provide exact fields and statuses; Back-to-Back: Y. | Positive | High | AM-04 |
| SIT-SIT01-002 | SIT-01 | SIT | REQ-02 | Mandatory documents | Missing mandatory document | Verify that one missing mandatory document prevents verification completion. | Mandatory checklist is available. | Complete data with one required document omitted. | 1. Create client record. 2. Attach available documents. 3. Submit. | TEST ORACLE GAP – expected missing-document state and observable message not defined. BA/PO to provide exact blocker and fields; Back-to-Back: Y. | Negative | High | AM-04 |
| SIT-SIT01-003 | SIT-01 | SIT | REQ-02 | Documentation consistency | Identifiers do not match | Verify inconsistent client names or identifiers are detected. | Client and document records exist. | Names/identifiers differing by one character; value supplied by BA. | 1. Enter client identity. 2. Attach document. 3. Run consistency validation. | TEST ORACLE GAP – mismatch rules and comparison fields are not defined. BA/Architect to define exact-name/identifier logic; Back-to-Back: Y. | Validation | High | AM-04 |
| SIT-SIT01-004 | SIT-01 | SIT | REQ-02 | Mandatory documents | Boundary number of documents | Verify behavior at the minimum approved document count. | Mandatory count is defined. | Minimum and minimum-minus-one document sets. | 1. Submit minimum-minus-one set. 2. Submit minimum set. | TEST ORACLE GAP – minimum count and pass state are not defined. BA/PO to provide; Back-to-Back: Y. | Boundary | High | AM-04 |
| SIT-SIT01-005 | SIT-01 | SIT | REQ-01, REQ-02 | Client entry | Unsupported client type | Verify unsupported/unknown client types cannot proceed. | Client type classification exists. | Unknown client type value. | 1. Create record using unknown type. 2. Submit for intake. | TEST ORACLE GAP – allowed client types and rejection state are not defined. BA/PO to confirm; Back-to-Back: Y. | Negative | Medium | AM-04 |

### SIT-02 — Setup and Configuration Integrity

| TC ID | Theme ID | Level | Req ID | AC Ref | Scenario | Description | Preconditions | Test Data | Steps | Expected Result | Type | Priority | Oracle / Assumption |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| SIT-SIT02-001 | SIT-02 | SIT | REQ-03, REQ-04 | Account Setup | Eligible configuration is persisted | Verify approved account structure and product configuration are stored consistently. | Authoritative configuration matrix exists. | One approved client/account/product/currency/limits combination. | 1. Submit setup. 2. Retrieve saved setup. 3. Compare to source request. | TEST ORACLE GAP – field names, values and stored states are not supplied. Architect to provide account data model; Back-to-Back: Y. | Integration | High | AM-03 |
| SIT-SIT02-002 | SIT-02 | SIT | REQ-03 | Account Setup | Ineligible product | Verify a product not eligible for the client type cannot be activated. | Product eligibility matrix exists. | Known ineligible client/product pair. | 1. Submit setup. 2. Attempt activation. | TEST ORACLE GAP – eligibility rule and observable blocker are not defined. BA/PO to supply; Back-to-Back: Y. | Negative | High | AM-03 |
| SIT-SIT02-003 | SIT-02 | SIT | REQ-04 | Account Setup | Currency and limit boundary | Verify approved boundary values are accepted and values outside the boundary are rejected. | Currency and limit constraints exist. | Minimum, maximum and adjacent out-of-range values. | 1. Submit each limit value. 2. Inspect saved state. | TEST ORACLE GAP – limits and rounding/units are not defined. BA/Architect to supply; Back-to-Back: Y. | Boundary | High | AM-03 |
| SIT-SIT02-004 | SIT-02 | SIT | REQ-04 | Account Setup | Unsupported currency or operating rule | Verify unsupported configuration is rejected. | Supported value lists exist. | One unsupported currency and one unsupported operating rule. | 1. Submit unsupported currency. 2. Submit unsupported rule. | TEST ORACLE GAP – valid values and rejection codes/messages are not defined. Architect to supply; Back-to-Back: Y. | Validation | High | AM-03 |
| SIT-SIT02-005 | SIT-02 | SIT | REQ-03, REQ-04 | Account Setup | Setup retry after validation failure | Verify correction and resubmission do not leave a partial configuration. | Setup supports correction and resubmission. | Initially invalid, then corrected approved values. | 1. Submit invalid setup. 2. Correct value. 3. Resubmit. 4. Inspect final state. | TEST ORACLE GAP – rollback and duplicate-state behavior are not defined. Architect to supply; Back-to-Back: Y. | Exception | High | AM-03 |

### SIT-03 — Mandate and Authority Controls

| TC ID | Theme ID | Level | Req ID | AC Ref | Scenario | Description | Preconditions | Test Data | Steps | Expected Result | Type | Priority | Oracle / Assumption |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| SIT-SIT03-001 | SIT-03 | SIT | REQ-05 | Mandate & Authority | Valid signatory is accepted | Verify an authorized signatory and hierarchy combination pass mandate validation. | Approved mandate/authority matrix exists. | One valid signatory and approver combination. | 1. Submit mandate. 2. Record validation result. 3. Attempt activation. | TEST ORACLE GAP – authoritative authority rules and status values are not supplied. BA/PO to define; Back-to-Back: Y. | Positive | High | AM-03 |
| SIT-SIT03-002 | SIT-03 | SIT | REQ-05 | Mandate & Authority | Unauthorized signatory | Verify an unapproved signatory cannot authorize the account. | Mandate list exists. | Signatory absent from mandate. | 1. Submit mandate. 2. Attempt validation/activation. | TEST ORACLE GAP – rejection state and audit event are not defined. BA/Architect to define; Back-to-Back: Y. | Security | High | AM-03 |
| SIT-SIT03-003 | SIT-03 | SIT | REQ-05 | Mandate & Authority | Approval hierarchy boundary | Verify the required approval count is enforced. | Approval threshold exists. | Required-minus-one and required approvers. | 1. Submit partial approvals. 2. Submit complete approvals. | TEST ORACLE GAP – threshold and quorum rules are not supplied. BA/PO to define; Back-to-Back: Y. | Boundary | High | AM-03 |
| SIT-SIT03-004 | SIT-03 | SIT | REQ-06 | Mandate & Authority | Approved exception | Verify a documented and approved exception can proceed only within policy. | Exception policy and approver exist. | One policy-approved exception. | 1. Record exception reason. 2. Record approver. 3. Submit. | TEST ORACLE GAP – required evidence and allowed exception types are not defined. BA/Compliance to supply; Back-to-Back: Y. | Exception | High | AM-03 |
| SIT-SIT03-005 | SIT-03 | SIT | REQ-06 | Mandate & Authority | Unapproved exception | Verify an unapproved exception cannot activate. | Exception exists without approval. | Exception reason and no approver. | 1. Submit exception. 2. Attempt activation. | TEST ORACLE GAP – expected block state/audit record is not defined. BA/Compliance to supply; Back-to-Back: Y. | Negative | High | AM-03 |

### SIT-04 — KYC/CDD State and Issue Tracking

| TC ID | Theme ID | Level | Req ID | AC Ref | Scenario | Description | Preconditions | Test Data | Steps | Expected Result | Type | Priority | Oracle / Assumption |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| SIT-SIT04-001 | SIT-04 | SIT | REQ-07 | Risk & Compliance | All compliance checkpoints complete | Verify completed KYC/CDD states allow readiness evaluation. | Compliance records exist. | Completed KYC/CDD records. | 1. Complete all checkpoints. 2. Retrieve compliance status. 3. Evaluate activation readiness. | TEST ORACLE GAP – exact statuses and readiness calculation are not supplied. Compliance/Architect to define; Back-to-Back: Y. | Positive | High | AM-03 |
| SIT-SIT04-002 | SIT-04 | SIT | REQ-07 | Risk & Compliance | Missing KYC/CDD checkpoint | Verify incomplete compliance prevents readiness. | A required checkpoint is identified. | One incomplete KYC/CDD record. | 1. Mark record incomplete. 2. Evaluate readiness. | TEST ORACLE GAP – incomplete status and blocker behaviour are not defined. Compliance to supply; Back-to-Back: Y. | Negative | High | AM-03 |
| SIT-SIT04-003 | SIT-04 | SIT | REQ-08 | Risk & Compliance | Outstanding issue is tracked | Verify issue status, owner and resolution are retained. | Issue workflow exists. | One open issue with owner/due date. | 1. Log issue. 2. Update owner/status. 3. Retrieve issue history. | TEST ORACLE GAP – fields and audit history are not defined. Operations/Architect to supply; Back-to-Back: Y. | Integration | High | AM-03 |
| SIT-SIT04-004 | SIT-04 | SIT | REQ-08, REQ-09 | Risk & Compliance | Open issue blocks activation | Verify unresolved issue prevents activation. | Open issue exists. | One open blocker. | 1. Leave issue unresolved. 2. Attempt activation. | TEST ORACLE GAP – which issues block and exact activation state are not defined. Compliance/BA to supply; Back-to-Back: Y. | Negative | High | AM-03 |
| SIT-SIT04-005 | SIT-04 | SIT | REQ-08, REQ-09 | Risk & Compliance | Issue resolved and closed | Verify resolution removes the blocker and permits re-evaluation. | Issue is in trackable state. | Initially open, then resolved/closed. | 1. Resolve issue. 2. Re-evaluate readiness. 3. Inspect activation eligibility. | TEST ORACLE GAP – closure evidence and re-evaluation rule are not defined. Operations/Compliance to supply; Back-to-Back: Y. | State Transition | High | AM-03 |

### SIT-05 — Activation Boundary and Integration Handoff

| TC ID | Theme ID | Level | Req ID | AC Ref | Scenario | Description | Preconditions | Test Data | Steps | Expected Result | Type | Priority | Oracle / Assumption |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| SIT-SIT05-001 | SIT-05 | SIT | REQ-10 | Activation Readiness | All readiness checks complete | Verify the activation state is set only after all validation and operational notification steps complete. | End-to-end readiness controls are available. | Complete approved onboarding record. | 1. Complete validation. 2. Notify operations. 3. Activate. | TEST ORACLE GAP – fields, sequence, status and notification contract are not defined. Architect/BA to supply; Back-to-Back: Y. | Positive | High | AM-03 |
| SIT-SIT05-002 | SIT-05 | SIT | REQ-09, REQ-10 | Activation Readiness | Activation blocked with unresolved issue | Verify unresolved blocker prevents activation state change. | Blocker is open. | Open compliance/operational blocker. | 1. Attempt activation. 2. Retrieve activation and issue states. | TEST ORACLE GAP – expected block state and audit event are not defined. BA/Compliance to supply; Back-to-Back: Y. | Exception | High | AM-03 |
| SIT-SIT05-003 | SIT-05 | SIT | REQ-10 | Activation Readiness | Operations notification unavailable | Verify notification failure is recoverable and does not produce silent activation. | Notification integration can be simulated. | Delivery failure response. | 1. Complete validation. 2. Trigger activation notification. 3. Inspect retry/state. | TEST ORACLE GAP – timeout, retry, recipient and failure state are not defined. Architect to supply; Back-to-Back: Y. | Integration | High | AM-03 |
| SIT-SIT05-004 | SIT-05 | SIT | REQ-10, REQ-13 | Activation Readiness | Duplicate activation request | Verify a repeated activation request does not create duplicate activation or notifications. | One activation request has already been processed. | Identical activation request/key. | 1. Submit activation. 2. Repeat request. 3. Inspect activation and notification counts. | TEST ORACLE GAP – idempotency key and duplicate expected state are not defined. Architect to supply; Back-to-Back: Y. | Integration | High | AM-03 |
| SIT-SIT05-005 | SIT-05 | SIT | REQ-10 | Activation Readiness | Notification retry succeeds | Verify a failed notification can be retried and recorded without altering activation incorrectly. | Notification retry mechanism exists. | Initial failure, subsequent success. | 1. Send notification. 2. Fail delivery. 3. Retry. 4. Inspect audit state. | TEST ORACLE GAP – retry count, timing and final state are not supplied. Architect to supply; Back-to-Back: Y. | Exception | Medium | AM-03 |

### SIT-06 — Recovery and Maintenance Regression

| TC ID | Theme ID | Level | Req ID | AC Ref | Scenario | Description | Preconditions | Test Data | Steps | Expected Result | Type | Priority | Oracle / Assumption |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| SIT-SIT06-001 | SIT-06 | SIT | REQ-11 | Stabilization | Early-life defect is recorded | Verify a post-activation onboarding defect is captured and assigned. | Monitoring workflow exists. | One representative setup or mandate defect. | 1. Record defect. 2. Assign owner. 3. Retrieve defect status. | TEST ORACLE GAP – monitoring period, fields and escalation action are not defined. Operations/PO to supply; Back-to-Back: Y. | Positive | Medium | AM-03 |
| SIT-SIT06-002 | SIT-06 | SIT | REQ-12 | Ongoing Maintenance | Approved maintenance update | Verify an approved change updates the controlled record and preserves existing risk controls. | Change approval and revalidation controls exist. | One valid permitted data change. | 1. Submit change. 2. Complete approval/revalidation. 3. Inspect final record. | TEST ORACLE GAP – permitted changes and reapproval rules are not supplied. BA/Architect to supply; Back-to-Back: Y. | Regression | Medium | AM-03 |
| SIT-SIT06-003 | SIT-06 | SIT | REQ-12 | Ongoing Maintenance | Failed maintenance update rolls back | Verify a failed or invalid update does not leave partial mandate/compliance/setup state. | Update supports validation and rollback. | One invalid maintenance change. | 1. Submit change. 2. Force validation failure. 3. Inspect prior state and audit. | TEST ORACLE GAP – rollback state and audit details are not supplied. Architect to supply; Back-to-Back: Y. | Exception | High | AM-03 |
| SIT-SIT06-004 | SIT-06 | SIT | REQ-13 | Stabilization / Maintenance | First-time-right outcome | Verify the reported first-time-right outcome reflects activation and rework accurately. | Activation and rework records exist. | One successful activation and one activation requiring rework. | 1. Record both outcomes. 2. Retrieve reported measure. | TEST ORACLE GAP – calculation and target are not defined. BA/PO to supply; Back-to-Back: Y. | Regression | Medium | AM-03 |

## 7. UAT Test Cases

### UAT-01 — Client Intake and Document Readiness

| TC ID | Theme ID | Level | Req ID | AC Ref | Scenario | Description | Preconditions | Test Data | Steps | Expected Result | Type | Priority | Oracle / Assumption |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| UAT-UAT01-001 | UAT-01 | UAT | REQ-01, REQ-02 | Client Entry | Individual client submits complete documents | An individual client provides the mandatory documentation and proceeds to validation. | Individual document requirements are available. | Approved individual identity/document set. | 1. Select Individual. 2. Review requirements. 3. Upload required documents. 4. Submit. | TEST ORACLE GAP – mandatory documents and completion status are not defined. BA/PO to supply; Back-to-Back: Y. | Positive | High | AM-04 |
| UAT-UAT01-002 | UAT-01 | UAT | REQ-02 | Client & Documentation | Corporate documents are incomplete | A corporate applicant sees which mandatory documents are missing before setup. | Corporate checklist is available. | Corporate set without one mandatory document. | 1. Select Corporate. 2. Submit documents. 3. Review validation outcome. | TEST ORACLE GAP – checklist and missing-document message are not defined. BA/PO to supply; Back-to-Back: Y. | Negative | High | AM-04 |
| UAT-UAT01-003 | UAT-01 | UAT | REQ-02 | Documentation consistency | Names and identifiers conflict | An applicant is not advanced when names or identifiers conflict across documents. | Consistency rules are approved. | Mismatched identity values. | 1. Submit documents with mismatched details. 2. Review validation outcome. | TEST ORACLE GAP – comparison tolerance and resolution instruction are not defined. BA/Operations to supply; Back-to-Back: Y. | Validation | High | AM-04 |
| UAT-UAT01-004 | UAT-01 | UAT | REQ-01 | Client Entry | Institutional requirements are explained | An institutional client receives and confirms the required onboarding information. | Institutional entry journey is configured. | Approved institutional information set. | 1. Select Institutional. 2. Review requirements. 3. Confirm understanding. | TEST ORACLE GAP – institutional-specific requirements and confirmation mechanism are not defined. BA/PO to supply; Back-to-Back: Y. | Positive | Medium | AM-04 |

### UAT-02 — Account Configuration Journey

| TC ID | Theme ID | Level | Req ID | AC Ref | Scenario | Description | Preconditions | Test Data | Steps | Expected Result | Type | Priority | Oracle / Assumption |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| UAT-UAT02-001 | UAT-02 | UAT | REQ-03, REQ-04 | Account Setup | Eligible account configuration is confirmed | Operations confirms an eligible account structure and configuration. | Eligibility matrix is approved. | One valid client/account/product/currency combination. | 1. Select client. 2. Choose approved structure and product. 3. Review currency, limits and operating rules. 4. Confirm setup. | TEST ORACLE GAP – exact eligible combinations and confirmation output are not supplied. BA/PO to supply; Back-to-Back: Y. | Positive | High | AM-03 |
| UAT-UAT02-002 | UAT-02 | UAT | REQ-03 | Account Setup | Ineligible product is stopped | Operations cannot continue with a product ineligible for the client type. | Eligibility matrix exists. | Ineligible client/product pair. | 1. Select ineligible product. 2. Attempt setup confirmation. | TEST ORACLE GAP – rejection message and allowed alternatives are not defined. BA/PO to supply; Back-to-Back: Y. | Negative | High | AM-03 |
| UAT-UAT02-003 | UAT-02 | UAT | REQ-04 | Account Setup | Unsupported currency or limit | Operations receives a clear validation result for unsupported financial configuration. | Approved currency and limit policy exists. | Unsupported currency and out-of-range limit. | 1. Enter unsupported values. 2. Submit for review. | TEST ORACLE GAP – valid values and messages are not defined. BA/Architect to supply; Back-to-Back: Y. | Validation | High | AM-03 |
| UAT-UAT02-004 | UAT-02 | UAT | REQ-04 | Account Setup | Approved limits are reviewed | Operations reviews and confirms the approved limits and operating rules. | Approved limit and operating-rule matrix exists. | Boundary/approved values. | 1. Enter approved boundary values. 2. Review resulting setup. 3. Confirm. | TEST ORACLE GAP – values and user confirmation are not defined. BA/PO to supply; Back-to-Back: Y. | Boundary | Medium | AM-03 |

### UAT-03 — Mandate Approval and Exception Handling

| TC ID | Theme ID | Level | Req ID | AC Ref | Scenario | Description | Preconditions | Test Data | Steps | Expected Result | Type | Priority | Oracle / Assumption |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| UAT-UAT03-001 | UAT-03 | UAT | REQ-05 | Mandate & Authority | Valid mandate is approved | Operations confirms a valid signatory and approval hierarchy. | Mandate and approver records exist. | One valid mandate set. | 1. Review signatories. 2. Review approval hierarchy. 3. Approve mandate. | TEST ORACLE GAP – required approvers and approval evidence are not defined. BA/Compliance to supply; Back-to-Back: Y. | Positive | High | AM-03 |
| UAT-UAT03-002 | UAT-03 | UAT | REQ-05 | Mandate & Authority | Unauthorized signatory is rejected | The process does not accept an signatory outside the approved mandate. | Mandate list exists. | Unauthorized signatory. | 1. Submit unauthorized signatory. 2. Review outcome. | TEST ORACLE GAP – rejection state and user message are not defined. BA/Compliance to supply; Back-to-Back: Y. | Security | High | AM-03 |
| UAT-UAT03-003 | UAT-03 | UAT | REQ-06 | Mandate & Authority | Approved mandate exception | An exception is documented and approved before activation. | Exception policy exists. | Policy-approved exception and approver. | 1. Document exception. 2. Obtain approval. 3. Continue onboarding. | TEST ORACLE GAP – exception types and required approval evidence are not defined. BA/Compliance to supply; Back-to-Back: Y. | Exception | High | AM-03 |
| UAT-UAT03-004 | UAT-03 | UAT | REQ-06 | Mandate & Authority | Exception is not approved | An undocumented or unapproved exception cannot proceed. | Exception exists without approval. | Exception reason and missing approver. | 1. Submit exception without approval. 2. Attempt onboarding continuation. | TEST ORACLE GAP – block outcome and escalation path are not defined. BA/Compliance to supply; Back-to-Back: Y. | Negative | High | AM-03 |

### UAT-04 — Compliance and Activation Readiness

| TC ID | Theme ID | Level | Req ID | AC Ref | Scenario | Description | Preconditions | Test Data | Steps | Expected Result | Type | Priority | Oracle / Assumption |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| UAT-UAT04-001 | UAT-04 | UAT | REQ-07 | Risk & Compliance | KYC/CDD complete | Operations sees that all required compliance checkpoints are complete. | Compliance records exist. | Completed KYC/CDD set. | 1. Open compliance checklist. 2. Confirm all checkpoints. 3. Proceed to readiness review. | TEST ORACLE GAP – required checkpoints and completion labels are not defined. Compliance/BA to supply; Back-to-Back: Y. | Positive | High | AM-03 |
| UAT-UAT04-002 | UAT-04 | UAT | REQ-07, REQ-09 | Risk & Compliance | Incomplete compliance blocks activation | An incomplete KYC/CDD checkpoint prevents activation. | One checkpoint is incomplete. | Incomplete compliance record. | 1. Review readiness. 2. Attempt activation. | TEST ORACLE GAP – required block behavior and status are not defined. Compliance/PO to supply; Back-to-Back: Y. | Negative | High | AM-03 |
| UAT-UAT04-003 | UAT-04 | UAT | REQ-08 | Risk & Compliance | Outstanding issue is visible and owned | Operations sees an outstanding issue and responsible owner. | Issue tracker/workflow exists. | Open issue with owner and due date. | 1. Create or view issue. 2. Assign owner. 3. Review issue status. | TEST ORACLE GAP – issue fields and workflow are not defined. Operations/BA to supply; Back-to-Back: Y. | Exception | High | AM-03 |
| UAT-UAT04-004 | UAT-04 | UAT | REQ-08, REQ-09 | Risk & Compliance | Issue resolved before activation | Operations closes an issue and then completes readiness. | Open issue exists and can be resolved. | Open issue followed by resolution evidence. | 1. Record resolution. 2. Re-evaluate readiness. 3. Continue only if clear. | TEST ORACLE GAP – acceptable resolution evidence and reapproval are not defined. Compliance/Operations to supply; Back-to-Back: Y. | State Transition | High | AM-03 |

### UAT-05 — First-Time-Right Activation

| TC ID | Theme ID | Level | Req ID | AC Ref | Scenario | Description | Preconditions | Test Data | Steps | Expected Result | Type | Priority | Oracle / Assumption |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| UAT-UAT05-001 | UAT-05 | UAT | REQ-10, REQ-13 | Activation Readiness | Complete onboarding activates first time | A fully validated client can transact after activation without manual post-activation correction. | All controls and approvals are complete. | Complete approved onboarding record. | 1. Complete readiness review. 2. Inform operations. 3. Activate. 4. Confirm ready-to-transact state. | TEST ORACLE GAP – user confirmation and transaction-ready evidence are not defined. BA/PO to supply; Back-to-Back: Y. | Positive | High | AM-03 |
| UAT-UAT05-002 | UAT-05 | UAT | REQ-09, REQ-10 | Activation Readiness | Blocker prevents activation | A client with an unresolved blocker cannot be activated. | At least one unresolved blocker exists. | One unresolved compliance or setup blocker. | 1. Attempt activation. 2. Review displayed status. | TEST ORACLE GAP – blocker presentation and activation state are not defined. BA/PO to supply; Back-to-Back: Y. | Negative | High | AM-03 |
| UAT-UAT05-003 | UAT-05 | UAT | REQ-10 | Activation Readiness | Operations receives readiness notification | Operations is informed that the client is ready to activate/has been activated. | Operations is an identified recipient. | Approved client record and operations recipient. | 1. Complete readiness review. 2. Trigger operational notification. 3. Have operations acknowledge receipt. | TEST ORACLE GAP – channel, content, timing and acknowledgement are not defined. Operations/Architect to supply; Back-to-Back: Y. | Integration | High | AM-03 |
| UAT-UAT05-004 | UAT-05 | UAT | REQ-13 | Activation Readiness | Rework is not hidden after activation | Post-activation manual correction is reflected as not-first-time-right. | Reporting supports activation outcome and rework. | One activation without rework and one with rework. | 1. Complete a clean activation. 2. Complete an activation requiring correction. 3. Review outcome reporting. | TEST ORACLE GAP – metric calculation and reporting fields are not defined. BA/PO to supply; Back-to-Back: Y. | Regression | Medium | AM-03 |

### UAT-06 — Stabilization and Ongoing Maintenance

| TC ID | Theme ID | Level | Req ID | AC Ref | Scenario | Description | Preconditions | Test Data | Steps | Expected Result | Type | Priority | Oracle / Assumption |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| UAT-UAT06-001 | UAT-06 | UAT | REQ-11, REQ-13 | Stabilization | Early-life defect is escalated | Operations identifies an early onboarding defect and escalates it before it affects trust. | Stabilization monitoring process exists. | Representative activation defect. | 1. Record defect. 2. Assign owner. 3. Escalate according to process. | TEST ORACLE GAP – monitoring period and escalation timing are not defined. Operations/BA to supply; Back-to-Back: Y. | Exception | Medium | AM-03 |
| UAT-UAT06-002 | UAT-06 | UAT | REQ-12 | Ongoing Maintenance | Approved client change preserves controls | A permitted client or account change is approved and revalidated. | Maintenance process exists. | One valid maintenance change. | 1. Request change. 2. Complete required approval. 3. Review resulting mandate/compliance/setup state. | TEST ORACLE GAP – permitted changes and revalidation requirements are not defined. BA/Compliance to supply; Back-to-Back: Y. | Positive | Medium | AM-03 |
| UAT-UAT06-003 | UAT-06 | UAT | REQ-12 | Ongoing Maintenance | Rejected maintenance change | An unsupported or unapproved change does not take effect. | Change approval rules exist. | One unsupported change. | 1. Submit change. 2. Reject or withhold approval. 3. Review account state. | TEST ORACLE GAP – reject/approval states and downstream impact are not defined. BA/Compliance to supply; Back-to-Back: Y. | Negative | Medium | AM-03 |
| UAT-UAT06-004 | UAT-06 | UAT | REQ-11, REQ-12 | Stabilization / Maintenance | Periodic review detects stale controls | A periodic review identifies records that need maintenance or revalidation. | Review schedule and stale-control criteria exist. | Record with expired or due control. | 1. Run/review periodic process. 2. Identify stale control. 3. Complete revalidation. | TEST ORACLE GAP – review frequency and stale criteria are not defined. Compliance/Operations to supply; Back-to-Back: Y. | Regression | Medium | AM-03 |

## 8. Test Oracle Gap Register

| Gap ID | TC ID | Missing Info | Owner / Source | Back-to-Back |
|---|---|---|---|---|
| OG-01 | SIT-SIT01-001 to SIT-SIT01-005; UAT-UAT01-001 to UAT-UAT01-004 | Mandatory documents by client type, required fields, validation rules, status values and messages. | BA / PO / Operations | Y |
| OG-02 | SIT-SIT02-001 to SIT-SIT02-005; UAT-UAT02-001 to UAT-UAT02-004 | Account structure, product eligibility, currencies, limits, operating rules, units and boundaries. | BA / PO / Architect | Y |
| OG-03 | SIT-SIT03-001 to SIT-SIT03-005; UAT-UAT03-001 to UAT-UAT03-004 | Authority, mandate, signatory and exception approval rules. | BA / Compliance | Y |
| OG-04 | SIT-SIT04-001 to SIT-SIT04-005; UAT-UAT04-001 to UAT-UAT04-004 | KYC/CDD checkpoint list, status labels, audit fields and blocker calculation. | Compliance / Operations / Architect | Y |
| OG-05 | SIT-SIT05-001 to SIT-SIT05-005; UAT-UAT05-001 to UAT-UAT05-004 | Activation sequence, readiness criteria, notification contract, acknowledgement and idempotency. | Architect / Operations / BA | Y |
| OG-06 | SIT-SIT06-001 to SIT-SIT06-004; UAT-UAT06-001 to UAT-UAT06-004 | Stabilization period, defect escalation, permitted maintenance changes, reapproval and rollback. | Operations / Compliance / PO | Y |
| OG-07 | SIT-SIT06-004; UAT-UAT05-004 | First-time-right calculation, target and rework definition. | PO / Operations | Y |

## 9. Coverage Summary

| Metric | Count |
|---|---:|
| Total requirements | 13 |
| Covered requirements | 10 |
| Partial requirements | 3 |
| Uncovered requirements | 0 |
| SIT themes | 6 |
| UAT themes | 6 |
| SIT test cases | 29 |
| UAT test cases | 24 |
| Total test cases | 53 |

### Counts by Level

| Level | Count |
|---|---:|
| SIT | 29 |
| UAT | 24 |

### Counts by Priority

| Priority | Count |
|---|---:|
| High | 44 |
| Medium | 9 |
| Low | 0 |

### Counts by Type

| Type | Count |
|---|---:|
| Positive | 12 |
| Negative | 10 |
| Boundary | 5 |
| Validation | 5 |
| Exception | 7 |
| Security | 2 |
| Integration | 5 |
| State Transition | 4 |
| Regression | 3 |

### Oracle Gaps per Theme

| Theme | Oracle gaps |
|---|---:|
| SIT-01 / UAT-01 | 9 |
| SIT-02 / UAT-02 | 9 |
| SIT-03 / UAT-03 | 9 |
| SIT-04 / UAT-04 | 9 |
| SIT-05 / UAT-05 | 9 |
| SIT-06 / UAT-06 | 8 |

### Residual Risks and Out-of-Scope Recommendations

- The supplied basis does not specify APIs, database schemas, batch jobs, asynchronous messaging, service levels, volumes or downtime behaviour; SIT integration coverage is therefore process-oriented and must be expanded after technical artifacts are supplied.
- No security test basis for authentication, authorization, encryption or masking was provided. Interface security testing is out of scope until an interface and credential model are supplied.
- No financial calculation basis was supplied. Fees, taxes and rounding are out of scope.
- Run the cases marked with Back-to-Back testing against the approved baseline or current process once an implementation is available.
- Close oracle gaps before execution or obtain explicit stakeholder-approved assumptions, because one scenario currently cannot be considered fully executable without an exact expected result.
