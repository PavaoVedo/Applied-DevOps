# Applied-DevOps

# Hospital Management - DevOps Project

## 1. Project overview
## 2. Requirement analysis 
### 2.1. Functional requirements
#### FR-01 PATIENTS
Register, search, view, edit. Deactivate a record while preserving past appointments.

#### FR-02 DOCTORS
Register, view, update, deactivate. Track specialisation and availability.

#### FR-03 APPOINTMENTS
Create with patient, doctor, date, time and reason. 
Status: scheduled, completed, cancelled.
No double-booking.

#### FR-04 MEDICAL RECORDS
A doctor records visit date, diagnosis and notes. 
Records are retained and readable by authorised staff.

#### FR-05 AUTHENTICATION
Users log in before reaching protected functionality. The system distinguishes three roles.

#### FR-06 ACCESS AND EXPORT
Functionality restricted by role.
A doctor exports authorised data as a CSV report.

### 2.2. Non-functional requirements
#### Stated in the scenario
##### Performance
Requests processed within two seconds under the expected demonstration workload.

##### Availability
Available during normal operation; recovers automatically from an application or 
container failure.

##### Security — storage
Passwords must not be stored in plain text.

##### Security — access
Authentication before protected functionality; role permissions enforced.

#### Implied by the project, worth adding
##### Deployability
A change on the main branch produces a published container image without manual 
steps.

##### Testability
Automated unit and end-to-end tests run in CI on every pull request.

##### Observability
Application and system metrics exposed and visible on a dashboard.

##### Maintainability
A documented branching strategy and a versioning scheme for artefacts.

## 3. Roles and access control
#### Front desk
##### RECEPTIONIST
Owns patient records and the appointment 
calendar. Registers, searches, edits and 
deactivates patients; schedules, updates and 
cancels appointments. Never touches clinical 
notes.

#### Clinical
##### DOCTOR
Sees the patients who have appointments with 
them. Records visit date, diagnosis and notes after 
a consultation, reads previous records, exports 
authorised data to CSV.

#### System
##### ADMINISTRATOR
Manages the doctor register and system user 
accounts. Registers, updates and deactivates 
doctors; assigns roles. Not a clinical role.

| Capability | Receptionist | Doctor | Administrator |
| --- | --- | --- | --- |
| Register, search and edit a patient | Yes | No | Yes |
| Deactivate a patient record | Yes | No | Yes |
| Register, update or deactivate a doctor | No | No | Yes |
| Schedule an appointment | Yes | No | Yes |
| View appointments | All | Own only | All |
| Update or cancel an appointment | Yes | No | Yes |
| Record diagnosis and visit notes | No | Yes | No |
| Read a patient's medical history | No | Own patients | No |
| Export authorised data to CSV | No | Yes | No |
| Manage user accounts and roles | No | No | Yes |
| View the login and role-change log | No | No | Yes |

**Decisions on cells the scenario leaves open**
- **Doctor — update or cancel an appointment: No.** The calendar belongs to the front desk; the only status change a doctor causes is automatic — recording a visit (US-10) marks the appointment *completed*.
- **Administrator — read a patient's medical history: No.** The administrator is explicitly not a clinical role, so least privilege applies to clinical data.
- **Administrator — export to CSV: No.** The export contains clinical data, and the scenario grants it to doctors only.
- **"Own patients"** means patients who have at least one appointment with that doctor.

**Patients are not system users.** The scenario gives patients no login, no self-booking and no access to their own record, so a fourth role was considered and ruled out.

## 4. Product backlog

### 4.1 User stories

Story IDs are permanent and grouped by role; the order in which stories are built is set in §4.2. Stories marked *(added)* go beyond the scenario — one per role.

#### Receptionist
- **US-01** — As a receptionist, I want to register a new patient with their name, date of birth and contact details, so that the patient can be booked for an appointment.
- **US-02** — As a receptionist, I want to search for an existing patient by name or date of birth, so that I can book or update a returning patient without registering them again.
- **US-03** — As a receptionist, I want to edit a patient's contact details, so that the hospital can still reach the patient about their appointments.
- **US-04** — As a receptionist, I want to deactivate a patient record, so that the patient no longer appears in everyday work while their past appointments are preserved.
- **US-05** — As a receptionist, I want to schedule an appointment with a patient, a doctor, a date, a time and a reason, so that the doctor's time is reserved for that patient.
- **US-06** — As a receptionist, I want to update or cancel an appointment, so that the calendar reflects what will actually happen and a freed slot can be offered to another patient.
- **US-07** *(added)* — As a receptionist, I want to be warned when a new patient's name and date of birth match an existing record, so that the same person is not registered twice and their history is not split across two records.

#### Doctor
- **US-08** — As a doctor, I want to view my scheduled appointments, so that I can prepare for each consultation.
- **US-09** — As a doctor, I want to open the record of a patient I am seeing, so that I can confirm I am treating the right person before I record anything.
- **US-10** — As a doctor, I want to record the visit date, diagnosis and notes after an appointment, so that the patient's clinical history is retained for future care.
- **US-11** — As a doctor, I want to read a patient's previous medical records, so that my diagnosis takes earlier visits into account.
- **US-12** — As a doctor, I want to export my authorised data as a CSV report, so that I can review my caseload in a spreadsheet or hand it on for reporting.
- **US-13** *(added)* — As a doctor, I want to add a dated amendment to a medical record I wrote, so that a mistake can be corrected without erasing what was originally recorded.

#### Administrator
- **US-14** — As an administrator, I want to register a doctor with their specialisation, so that receptionists can book appointments with them.
- **US-15** — As an administrator, I want to update a doctor's details and availability, so that receptionists cannot book a doctor outside the hours they actually work.
- **US-16** — As an administrator, I want to deactivate a doctor who is no longer available, so that no new appointments are booked with them while their past appointments and records are kept.
- **US-17** — As an administrator, I want to create a user account and assign it a role, so that each staff member reaches only the functionality their job requires.
- **US-18** — As an administrator, I want to deactivate a user account, so that someone who has left the hospital can no longer log in.
- **US-19** *(added)* — As an administrator, I want to see a log of logins and role changes, so that I can establish who had access to what when something goes wrong.

#### Every staff member
- **US-20** — As any staff member, I want to log in, so that I reach only the functionality my role permits.

### 4.2 Ordered product backlog

Ordered as Product Owner: MoSCoW buckets first, then ties broken by **dependency**, **risk** and **value**, in that order. *Must* = stated in the scenario; *Should* / *Could* = added stories.

| # | Story | Priority | Traces to | Depends on |
| --- | --- | --- | --- | --- |
| 1 | US-20 Log in and reach only my permitted functionality | Must | FR-05, FR-06 | — |
| 2 | US-14 Register a doctor with a specialisation | Must | FR-02 | US-20 |
| 3 | US-01 Register a new patient | Must | FR-01 | US-20 |
| 4 | US-05 Schedule an appointment without double-booking | Must | FR-03 | US-01, US-14 |
| 5 | US-08 View my scheduled appointments | Must | FR-03 | US-05 |
| 6 | US-10 Record diagnosis and visit notes | Must | FR-04 | US-08 |
| 7 | US-17 Create a user account and assign a role | Must | FR-05 | US-20 |
| 8 | US-15 Update doctor details and availability | Must | FR-02 | US-14, US-05 |
| 9 | US-02 Search for an existing patient | Must | FR-01 | US-01 |
| 10 | US-09 Open the record of a patient I am seeing | Must | FR-01 | US-08 |
| 11 | US-11 Read a patient's previous medical records | Must | FR-04 | US-09, US-10 |
| 12 | US-06 Update or cancel an appointment | Must | FR-03 | US-05 |
| 13 | US-04 Deactivate a patient record | Must | FR-01 | US-01, US-05 |
| 14 | US-16 Deactivate an unavailable doctor | Must | FR-02 | US-14, US-05 |
| 15 | US-18 Deactivate a user account | Must | FR-05 | US-17 |
| 16 | US-03 Edit patient contact details | Must | FR-01 | US-02 |
| 17 | US-12 Export my authorised data to CSV | Must | FR-06 | US-10 |
| 18 | US-07 Warn about a possible duplicate patient | Should | FR-01 | US-02 |
| 19 | US-13 Amend a medical record I wrote | Should | FR-04 | US-10 |
| 20 | US-19 Log of logins and role changes | Could | FR-05 | US-17 |

**Why the top three.** Login comes first because every other story sits behind authentication and role checks, so nothing can be demonstrated — and no authorisation test can pass — without it. A doctor and a patient come next because the appointment, the centre of the hospital's workflow, needs both to exist, and together with login they form the thin slice that proves a request can travel from the browser to the database inside a container.

**Other placements worth defending**
- **US-08 before US-10** — a doctor reaches the appointment they record against through their own appointment list (dependency).
- **US-15 at 8** — "availability" is the least defined requirement in the scenario and it changes the booking rule from US-05, so it is tackled while there is still time to be wrong about it (risk).
- **US-12 late despite being Must** — it only reads data that earlier stories create and carries little technical risk, so it waits until that data exists (dependency, risk).

### 4.3 Acceptance criteria

Written for the top six backlog items in Given / When / Then form, so that each criterion converts directly into an automated test.

**US-20 — Log in**
- *Given* an active account and the correct password, *when* the user logs in, *then* they land on the start page for their role.
- *Given* a wrong password, *when* the user logs in, *then* access is refused with a message that does not reveal whether the username exists.
- *Given* no session, *when* any protected route is requested, *then* the system returns 401 or redirects to the login page.
- *Given* a logged-in receptionist, *when* they request a clinical endpoint such as recording a diagnosis, *then* the system returns 403.
- *Given* any stored account, *when* the users table is inspected, *then* the password column holds a bcrypt hash with a cost factor of at least 10, never the plain password.

**US-14 — Register a doctor**
- *Given* an administrator, *when* they submit a first name, last name and specialisation, *then* the doctor is saved as active and appears in the list receptionists book from.
- *Given* a missing specialisation, *when* the form is submitted, *then* nothing is saved and the missing field is named.
- *Given* a receptionist or a doctor, *when* they try to register a doctor, *then* the system returns 403.

**US-01 — Register a new patient**
- *Given* a receptionist, *when* they submit a name, date of birth and contact details, *then* the patient is saved as active and can be found by name.
- *Given* a date of birth in the future, *when* the form is submitted, *then* nothing is saved and the reason is shown.
- *Given* a doctor, *when* they try to register a patient, *then* the system returns 403.

**US-05 — Schedule an appointment**
- *Given* a doctor already has an appointment at 10:00 on 14 October, *when* a receptionist tries to book the same doctor at 10:00 on 14 October, *then* the system refuses and explains why.
- *Given* a free slot, *when* a receptionist books it with a patient, doctor, date, time and reason, *then* the appointment is saved with status *scheduled*.
- *Given* a date and time in the past, *when* a receptionist tries to book it, *then* the system refuses.

**US-08 — View my scheduled appointments**
- *Given* a logged-in doctor, *when* they open their appointment list, *then* they see only their own appointments, ordered by date and time.
- *Given* an appointment belonging to another doctor, *when* a doctor requests it directly by its ID, *then* the system returns 403.

**US-10 — Record diagnosis and visit notes**
- *Given* one of my scheduled appointments, *when* I save a diagnosis and notes for it, *then* a medical record is stored with the visit date and the appointment status becomes *completed*.
- *Given* an empty diagnosis, *when* I save, *then* nothing is stored and the missing field is named.
- *Given* a cancelled appointment, or an appointment with another doctor, *when* I try to record a visit against it, *then* the system refuses.
- *Given* a receptionist or an administrator, *when* they call the endpoint that records a visit, *then* the system returns 403.

### 4.4 Definition of Done

One list, applied to every task and story. Nothing is *done* until all of the following hold:

- All acceptance criteria of the story pass.
- Merged into `main` through a pull request — never pushed directly.
- Unit tests written and passing in CI.
- The end-to-end test for the affected flow is green.
- Every RBAC refusal the change touches is covered by an automated test.
- The container image builds and is published to GHCR.
- README updated if a requirement or decision changed.
- The linked GitHub Issue is closed by the pull request.

## 5. Process and ceremonies

The project is run with Scrum by a single person, who changes hats between **Product Owner**, **Developer** and **Scrum Master**. This section describes the process as it is actually followed; the GitHub Projects board and commit history are the evidence.

### 5.1 Sprint length

**Two-week sprints, four sprints in total.** Two weeks is long enough to finish a vertical slice alongside other courses, and short enough to inspect and adapt four times before the defence; one-week sprints would spend too much time on overhead, and one-month sprints would leave only two chances to correct course.

### 5.2 Sprint outline

Each sprint pulls the next items from the ordered backlog in §4.2, plus the pipeline work the course requires at that stage.

| Sprint | Goal | Backlog items | Pipeline and repository work |
| --- | --- | --- | --- |
| 1 — Walking skeleton | A logged-in user can create a doctor and a patient, served from a container | US-20, US-14, US-01 | Repository conventions, branching strategy chosen and documented, CI builds and runs unit tests on every pull request |
| 2 — Core clinical flow | A patient can be booked, seen and have the visit recorded, end to end | US-05, US-08, US-10, US-17, US-15 | Container image published to GHCR on every change to `main` |
| 3 — Complete records | Every scenario requirement is implemented, including deactivation and CSV export | US-02, US-09, US-11, US-06, US-04, US-16, US-18, US-03, US-12 | Delivery pipeline, versioning scheme for artefacts |
| 4 — Harden and observe | The application is tested, monitored and ready to defend | US-07, US-13, US-19 | End-to-end tests in the pipeline, monitoring dashboard, README completed for the defence |

The sprints follow the backlog order rather than one module per sprint: the full booking-to-diagnosis flow is working by the end of sprint 2, so the riskiest rule (no double-booking) is proven early and the remaining sprints add breadth around a flow that already works.

### 5.3 Ceremonies

| Ceremony | When | What I do | What it produces |
| --- | --- | --- | --- |
| **Sprint planning** | Monday evening at the start of the sprint, 30–60 min | Product Owner hat: check the top of the backlog is ready, choose what fits in two weeks, commit to a goal | A sprint goal and a sprint backlog — the chosen issues moved into the sprint on the Projects board |
| **Sprint review** | Before the Friday lab at the end of the sprint, ~30 min | Run the working software and demonstrate it to a classmate or the lab instructor | Feedback, recorded in the sprint notes, that re-orders or adds to the product backlog |
| **Sprint retrospective** | Straight after the review, 20 min | Write down what went well, what did not, and why | One concrete change to try in the next sprint, recorded in the sprint notes and checked at the next retro |
| **Daily scrum — replaced by a work log** | Each day I work on the project, 2 min | With nobody to synchronise with, a stand-up would be fiction; instead I add a dated entry: what I did, what is next, what is blocking me | A dated work log in `docs/worklog.md` |

Sprint notes (goal, review feedback, retrospective outcome) are kept in `docs/sprints/sprint-N.md`, one file per sprint.

### 5.4 Backlog refinement

Refinement is continuous rather than a fixed meeting. Around a tenth of each sprint — roughly one hour mid-sprint — is reserved for it, so the top of the backlog is always ready to be pulled at the next planning.

**A story is ready** when it follows the template in §4.1, has acceptance criteria, fits inside one sprint, and its dependencies are done or planned in the same sprint.

**Refinement is triggered by:**
1. **A new or changed requirement** — a stakeholder asks for something the backlog does not cover, including a change handed over at the defence. It is written as a story, given acceptance criteria and placed in the order.
2. **A story that is too large** — it cannot be finished inside one sprint, so it is treated as an epic and split before planning.
3. **Missing acceptance criteria** — nobody can say when the story would be done, so it is not pulled into a sprint until it has them.
4. **Spillover** — an item did not finish in its sprint. It is re-estimated and re-ordered rather than silently rolled forward.
5. **Discovery during implementation** — a technical constraint or hidden dependency changes what is realistic, so affected stories are re-ordered or split.
6. **Feedback from the sprint review** — the demonstration changes what the stakeholder wants next, and the backlog order is updated to match.
