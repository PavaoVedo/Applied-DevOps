# Applied-DevOps

# Hospital Management - DevOps Project

## 1. Project overview
## 2. Requirement analysis 
### 2.1. Functional requirements
FR-01 PATIENTS.
Register, search, view, edit. Deactivate a record while preserving past appointments.

FR-02 DOCTORS.
Register, view, update, deactivate. Track specialisation and availability.

FR-03 APPOINTMENTS.
Create with patient, doctor, date, time and reason. 
Status: scheduled, completed, cancelled.
No double-booking.

FR-04 MEDICAL RECORDS.
A doctor records visit date, diagnosis and notes. 
Records are retained and readable by authorised staff.

FR-05 AUTHENTICATION.
Users log in before reaching protected functionality. The system distinguishes three roles.

FR-06 ACCESS AND EXPORT.
Functionality restricted by role.
A doctor exports authorised data as a CSV report.

### 2.1. Non-functional requirements
Stated in the scenario.
Performance.
Requests processed within two seconds under the expected demonstration workload.

Availability.
Available during normal operation; recovers automatically from an application or 
container failure.

Security — storage.
Passwords must not be stored in plain text.

Security — access.
Authentication before protected functionality; role permissions enforced.

Implied by the project, worth adding.
Deployability.
A change on the main branch produces a published container image without manual 
steps.

Testability.
Automated unit and end-to-end tests run in CI on every pull request.

Observability.
Application and system metrics exposed and visible on a dashboard.

Maintainability.
A documented branching strategy and a versioning scheme for artefacts.

## 3. Roles and access control
Front desk.
RECEPTIONIST.
Owns patient records and the appointment 
calendar. Registers, searches, edits and 
deactivates patients; schedules, updates and 
cancels appointments. Never touches clinical 
notes.

Clinical.
DOCTOR.
Sees the patients who have appointments with 
them. Records visit date, diagnosis and notes after 
a consultation, reads previous records, exports 
authorised data to CSV.

System.
ADMINISTRATOR.
Manages the doctor register and system user 
accounts. Registers, updates and deactivates 
doctors; assigns roles. Not a clinical role.

## 4. Product backing
## 5. Process and ceremonies
