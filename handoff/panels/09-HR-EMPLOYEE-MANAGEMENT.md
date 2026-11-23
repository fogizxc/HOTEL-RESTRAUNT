# Panel 09 — HR & Employee Management

## 1. Ownership
HR owns employee lifecycle, employment records, contracts, attendance, leave, schedules, compensation and payroll calculation records. Authentication owns credentials/sessions; RBAC owns application permissions; Finance owns accounting postings and disbursement reconciliation. HR must not grant application access merely because an employee is active.

## 2. Pages and behaviour

### 2.1 HR dashboard and organisation chart
- Show headcount, active/on-leave/terminated counts, upcoming shifts, attendance exceptions, leave requests, onboarding tasks and payroll state.
- Filter by legal entity, property, outlet, department and employment type.
- Org chart reflects effective-dated reporting structure and must not imply permission inheritance.

### 2.2 Employee directory and profile
- Search by employee number, name, department, designation, status and assigned location.
- Profile sections: personal/contact (restricted), employment, documents, assignments, emergency contact, compensation (highly restricted), attendance/leave summary, training/performance and access reference.
- Employee number unique within tenant or configured legal entity; never expose government ID in list views.
- Create, update, transfer, suspend employment, terminate, reinstate with reason and effective date.

### 2.3 Contracts and documents
- Document type, owner, validity, verification status, private storage reference, retention policy and access scope.
- Upload validation, malware scan and least-privilege download.
- Expiry alerts; never use public permanent links for personnel documents.

### 2.4 Departments, designations and transfers
- Manage department/designation hierarchy and effective date.
- Transfer changes HR placement; RBAC assignment is separately reviewed and must be explicitly updated or confirmed.
- Preserve historical position and reporting line.

### 2.5 Recruitment
- Vacancy approval, job details, candidate, interview stages, interviewer feedback, offer and hired transition.
- Candidate PII consent/retention policy required; restrict notes and avoid collecting protected/sensitive traits unless legally necessary.
- Convert successful candidate to employee with controlled data mapping; do not create duplicate identity on retry.

### 2.6 Onboarding and offboarding
- Checklist templates, assigned tasks, due dates, completion evidence and owner.
- Onboarding can request a platform identity/invitation and application role assignment but never automatically grants broad privileges.
- Offboarding triggers access-revocation workflow, device/asset return, final payroll and document retention. Access revocation must be prompt and independent of payroll completion.

### 2.7 Attendance and time-clock integrations
- Attendance records: employee, timestamp, source, device, property, shift, location and exception state.
- Integrate supported biometric/time-clock systems through adapter; do not store raw biometric templates by default.
- Device mapping, import validation, duplicate punch detection, correction request and approval.
- Manual correction records original value, proposed value, reason, approver and timestamp.
- Attendance correction does not silently recalculate a closed payroll period; correction after close uses adjustment process.

### 2.8 Shifts, rosters and swaps
- Create schedules by role, department, property/outlet, skill and expected coverage.
- Validate overlap, rest rules and employee availability under configured labour policy.
- Employee shift swap requests require eligible replacement and manager approval.
- Publish schedule notifications; changes retain history.

### 2.9 Leave and holiday management
- Leave types, policy, entitlement accrual, balance, blackout rules, holidays and carry-forward.
- Request → manager review → approved/rejected/cancelled; balance reservation/consumption is transactional.
- Reversal restores balance according to policy; never delete leave history.

### 2.10 Compensation and payroll
- Salary structure, earning/deduction components, effective dates, statutory configuration and employee eligibility.
- Payroll period draft → calculation → validation → review → approval → finalised; lock the period after posting.
- Payslip generated per employee and privately delivered; employee cannot read another employee's payslip.
- Recalculate draft before approval; after finalisation, corrections are adjustment payroll runs.
- Payroll engine must be jurisdiction-specific and legally reviewed; no statutory rates or filings should be assumed by this baseline.
- Finance receives approved payroll posting batch; HR does not post directly into GL.

### 2.11 Performance, training and employee relations
- Goals/reviews, feedback, training courses, attendance/completion, certification expiry and recognition.
- Disciplinary/grievance cases restricted to designated HR roles; audit access and redact sensitive attachments.

### 2.12 Employee self-service
- Employee sees own profile, roster, attendance, leave, payslip, training, documents and requests permitted for self-service.
- Self-service changes to bank/pay details require verification and approval, not direct update.

### 2.13 HR reporting, audit and settings
- Reports: headcount, turnover, attendance, overtime, leave, vacancy pipeline, training, payroll cost.
- Show data scope, as-of timestamp, formula and privacy threshold/suppression for small groups.
- Audit all sensitive profile/document/payroll access, export, corrections and approvals.

## 3. Acceptance tests
- Employee A cannot read Employee B's payslip or private HR documents.
- Termination/revocation disables relevant app sessions and roles independent of final payroll.
- Department transfer does not silently change RBAC permissions.
- Raw biometric templates are not stored by default.
- Attendance correction preserves original and approval evidence.
- Finalised payroll is immutable; adjustments are separate records.
- Payroll GL posting is created only from approved payroll batch by Finance integration.
