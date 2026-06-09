# KPI Lifecycle Business Process Document

## Purpose

This document describes the complete lifecycle of a KPI from organizational setup through performance reporting. It defines:

* The sequence of business activities
* The actors involved
* The outputs produced at each step
* The triggers that initiate subsequent steps
* The Measurement state machine
* Business events and notifications
* Edge cases and resolution strategies

This document intentionally avoids technical implementation details and focuses exclusively on business behavior.

---

# Section 1 — Step-by-Step Process Map

| Step | Actor                | Action                                                                                                                                                                                   | Output                                                                      | Triggers                                                                               |
| ---- | -------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------- | -------------------------------------------------------------------------------------- |
| 1    | System Administrator | Creates a department and configures its basic information, including department name and responsible manager.                                                                            | Department becomes available for operational use.                           | Department requires users to be assigned.                                              |
| 2    | System Administrator | Creates user accounts and assigns roles (Department Manager, Employee, Executive). Employees are assigned to departments.                                                                | Active users exist within the organizational structure.                     | Department Manager can begin defining KPIs.                                            |
| 3    | Department Manager   | Navigates to KPI Management and creates a KPI by specifying KPI name, description, measurement unit, target value, measurement frequency, reporting period, and assigned employee owner. | KPI is created with status **Active** and ownership assignment established. | Assigned employee must be informed of responsibility.                                  |
| 4    | System               | Generates a KPI assignment notification and delivers it to the assigned employee.                                                                                                        | Employee becomes aware of KPI responsibility.                               | Employee can begin tracking and submitting measurements.                               |
| 5    | Employee             | Reviews assigned KPI and submits a measurement for the applicable reporting period, providing actual value and optional supporting comments.                                             | Measurement record is created in **Pending** state.                         | Department Manager must review the submitted measurement.                              |
| 6    | Department Manager   | Reviews the submitted measurement and either approves or rejects it based on business evidence and KPI expectations.                                                                     | Measurement transitions to either **Approved** or **Rejected** state.       | Employee must be informed of the review outcome.                                       |
| 7    | System               | Updates dashboards, reports, scorecards, and performance summaries using approved measurement data.                                                                                      | Performance reporting reflects the latest approved KPI results.             | Executives and managers can make business decisions based on updated performance data. |

---

# Section 2 — Measurement State Machine

## States

### Pending

A measurement has been submitted by an employee and is awaiting managerial review.

### Approved

A measurement has been reviewed and accepted by the responsible Department Manager. Approved measurements contribute to reporting and dashboards.

### Rejected

A measurement has been reviewed and rejected by the Department Manager. Rejected measurements do not contribute to reporting and require employee correction and resubmission.

---

## State Transition Diagram

```text
                    +-----------+
                    |  Pending  |
                    +-----------+
                     /         \
                    /           \
             Approve             Reject
                  /               \
                 v                 v
        +------------+      +------------+
        | Approved   |      | Rejected   |
        +------------+      +------------+
                                   |
                                   |
                              Resubmit
                                   |
                                   v
                             +-----------+
                             | Pending   |
                             +-----------+
```

---

## Transition Definitions

### Transition: Submit

Actor:
Employee

Precondition:

* KPI is assigned to the employee
* KPI status is Active
* Reporting period is open
* No Pending measurement exists for the same KPI, Month, and Year
* No Approved measurement exists for the same KPI, Month, and Year

From State:
(None — new measurement)

To State:
Pending

Post-condition:

* Measurement is stored
* Manager receives notification
* Measurement awaits review

---

### Transition: Approve

Actor:
Department Manager

Precondition:

* Measurement exists
* Measurement state is Pending
* Manager belongs to the KPI's department

From State:
Pending

To State:
Approved

Post-condition:

* Measurement becomes official
* Employee receives approval notification
* Dashboard calculations become eligible for update
* Reports include measurement data

---

### Transition: Reject

Actor:
Department Manager

Precondition:

* Measurement exists
* Measurement state is Pending
* Rejection reason is provided

From State:
Pending

To State:
Rejected

Post-condition:

* Measurement excluded from reports
* Employee receives rejection notification
* Employee may correct and resubmit

---

### Transition: Resubmit

Actor:
Employee

Precondition:

* Measurement state is Rejected
* Employee owns the KPI assignment
* Employee updates submitted values or supporting information

From State:
Rejected

To State:
Pending

Post-condition:

* Previous rejection remains in audit history
* Measurement re-enters approval workflow
* Manager receives new review notification

---

## Invalid Transitions

| From State | Invalid Action | Reason                                    |
| ---------- | -------------- | ----------------------------------------- |
| Approved   | Approve        | Already approved                          |
| Approved   | Reject         | Historical records must remain stable     |
| Approved   | Resubmit       | Approved results are considered finalized |
| Rejected   | Approve        | Must first be resubmitted for review      |
| Pending    | Resubmit       | Submission already awaiting review        |

---

# Section 3 — Domain Events Catalogue

| Event Name              | Triggered By         | Triggered When                              | Notifies                            | Channel       |
| ----------------------- | -------------------- | ------------------------------------------- | ----------------------------------- | ------------- |
| DepartmentCreated       | System Administrator | New department is successfully created      | System Administrator                | In-App        |
| UserCreated             | System Administrator | New user account is created                 | New User                            | In-App, Email |
| KpiCreated              | Department Manager   | KPI is successfully created                 | Department Manager                  | In-App        |
| KpiAssigned             | Department Manager   | KPI is assigned to an employee              | Assigned Employee                   | In-App, Email |
| MeasurementSubmitted    | Employee             | Measurement enters Pending state            | Department Manager                  | In-App        |
| MeasurementApproved     | Department Manager   | Measurement enters Approved state           | Employee                            | In-App, Email |
| MeasurementRejected     | Department Manager   | Measurement enters Rejected state           | Employee                            | In-App, Email |
| MeasurementResubmitted  | Employee             | Rejected measurement is resubmitted         | Department Manager                  | In-App        |
| ReportingPeriodOpened   | System Scheduler     | New reporting period begins                 | Assigned Employees                  | In-App        |
| MeasurementReminderSent | System Scheduler     | Submission deadline approaches              | Employees with missing measurements | In-App, Email |
| DashboardUpdated        | System               | Approved measurement affects reporting data | Executives, Managers                | In-App        |

---

# Section 4 — Edge Cases and Resolution Strategies

## Edge Case 1 — Duplicate Measurement Submission

### Scenario

An employee attempts to submit multiple measurements for the same KPI, Month, and Year.

### Risk

Duplicate records create inconsistent reporting results.

### Resolution

The system prevents creation when a Pending or Approved measurement already exists for the same KPI-period combination.

---

## Edge Case 2 — Manager Reviews After Employee Resubmission

### Scenario

A manager opens a Pending measurement review screen while the employee submits an updated version before approval occurs.

### Risk

Manager approves outdated information.

### Resolution

The system always reviews the latest submitted version and maintains version history for audit purposes.

---

## Edge Case 3 — Employee Changes Department After KPI Assignment

### Scenario

An employee is transferred to another department while active KPI assignments still exist.

### Risk

Ownership ambiguity and reporting gaps.

### Resolution

Existing KPI assignments remain valid until explicitly reassigned by a Department Manager. Historical ownership remains preserved.

---

## Edge Case 4 — KPI Deactivated During Active Reporting Period

### Scenario

A manager deactivates a KPI after measurements have already been submitted.

### Risk

Loss of reporting consistency.

### Resolution

Previously approved measurements remain in historical reports. No new measurements may be submitted after KPI deactivation.

---

## Edge Case 5 — Measurement Rejected Multiple Times

### Scenario

An employee repeatedly submits incorrect measurements.

### Risk

Infinite approval loops with no accountability.

### Resolution

The system permits unlimited resubmissions but preserves every rejection reason and review decision within the audit history.

---

## Edge Case 6 — Dashboard Calculation Before Approval

### Scenario

A Pending measurement is accidentally included in performance calculations.

### Risk

Executives make decisions based on unverified data.

### Resolution

Only Approved measurements are eligible for dashboard aggregation, KPI achievement calculations, and executive reporting.

---

# Business Outcome

The process ensures that:

1. KPIs are formally assigned and owned.
2. Employees are accountable for reporting performance.
3. Managers validate performance data before publication.
4. Executives view only approved and trustworthy results.
5. Every significant business action generates appropriate notifications.
6. Historical decisions remain auditable and traceable.
7. Dashboard metrics accurately reflect approved organizational performance.

```
```
