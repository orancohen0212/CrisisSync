# Requirements Architecture & System Specification

**Course:** Software Engineering Methods  
**System:** CrisisSync


### Engineering Team Details

- **Student 1:** Oran Cohen | **ID:** 324155845 | **Braude Email:** oran.cohen@e.braude.ac.il
- **Student 2:** Amit Asaf | **ID:** 207659087 | **Braude Email:** amit.asaf@e.braude.ac.il
- **Student 3:** Yasmin Askarov | **ID:** 323017012 | **Braude Email:** yasmin.askarov@e.braude.ac.il
- **Student 4:** Lihi Elias | **ID:** 319041109 | **Braude Email:** lihi.elias@e.braude.ac.il

---

## 1. System Use Case Diagram (Mermaid.js)

<!--
INSTRUCTIONS FOR STUDENTS:
1. Populate the Mermaid flowchart below based on your audited entities from Part 1, Step 4.
2. Group human actors inside Primary_Actors and hardware/external systems inside Secondary_Actors.
3. Place all core Use Cases inside the ParkingSystem boundary subgraph.
4. Use solid lines without arrows (---) for Actor associations.
5. Use dashed arrows with explicit stereotypes for dependencies:
   - Include: BaseUC -.->|"<<include>>"| IncludedUC
   - Extend:  ExtendingUC -.->|"<<extend>>"| BaseUC
-->

```mermaid
flowchart LR
    %% Primary Actors (Left)
    SysAdmin(("System Administrator"))
    LogisticsOfficer(("Logistics and\nResources Officer"))
    Dispatcher(("Dispatcher"))
    CrewMember(("Field Crew Member"))
    FieldCommander(("Field Commander"))

    %% Actor Inheritance
    FieldCommander -->|<<inherits>>| CrewMember

    %% System Boundary
    subgraph CrisisSync ["CrisisSync System"]
        direction TB
        
        UC0(["Login (Username and Password)"])

        UC1(["Manage User Accounts and Permissions"])
        UC2(["Run Data Loading Module"])
        UC3(["View Event History"])
        UC4(["Generate Management Reports"])
        UC19(["View Reports"])
        
        UC5(["Update Equipment Maintenance Status"])
        UC6(["Transfer Tools Between Stations"])
        UC7(["Update Crew Member Availability"])
        
        UC8a(["Open Event"])
        UC8b(["Cancel Event"])
        UC9(["Define Task Trees and Dependencies"])
        UC10(["Reserve and Allocate Resources"])
        UC11(["Track Event Status in Real-Time"])
        UC12(["Cancel Individual Tasks"])
        UC13(["Close Event"])
        UC13a(["Process Billing Request"])
        
        UC14(["View Task and Event Status"])
        UC15(["Report Task Completion"])
        
        UC16(["Report Shift Start and Readiness"])
        UC17(["View Assigned Task Details"])
        UC18(["Report Field Equipment Failure"])

        %% Dependencies
        UC13 -.->|<<include>>| UC13a
    end

    %% Secondary Actors / External Systems (Right)
    BillingSystem(("National Clearing\nand Billing System"))
    Timer(("System Timer"))

    %% Primary Actors Associations
    SysAdmin --- UC0
    LogisticsOfficer --- UC0
    Dispatcher --- UC0
    CrewMember --- UC0
    FieldCommander --- UC0

    SysAdmin --- UC1
    SysAdmin --- UC2
    SysAdmin --- UC3
    SysAdmin --- UC4
    SysAdmin --- UC19

    LogisticsOfficer --- UC5
    LogisticsOfficer --- UC6
    LogisticsOfficer --- UC7

    Dispatcher --- UC8a
    Dispatcher --- UC8b
    Dispatcher --- UC9
    Dispatcher --- UC10
    Dispatcher --- UC11
    Dispatcher --- UC12
    Dispatcher --- UC13

    FieldCommander --- UC14
    FieldCommander --- UC15

    CrewMember --- UC16
    CrewMember --- UC17
    CrewMember --- UC18

    %% Secondary Actors Associations
    UC13a --- BillingSystem
    UC4 --- Timer
    %% Define Dependencies (Dashed arrows with stereotypes)
    %% Note: Verify that <<extend>> arrows point toward the base use case!
```

---

## 2. Business Edge Cases & Concurrency Decisions

<!--
INSTRUCTIONS FOR STUDENTS:
Select at least two unresolved business ambiguities, concurrency edge cases, or hardware timeouts
identified during your AI auditing phase. Document your architectural decision in English below.
-->

### Edge Case 1: Concurrent Parking Spot Reservation

- **Scenario & Ambiguity:** Two registered users open the live map simultaneously, see the exact same available parking spot, and click "Reserve" at the exact same millisecond.
- **Architectural Resolution:** The system shall implement pessimistic locking on the database record for that specific parking spot. The first request to reach the database acquires the lock and successfully reserves the spot. The second concurrent request is immediately rejected with an "Already Reserved" error, prompting the user's map to refresh.
- **Impact on System State:** The target spot's database status changes from 'Available' to 'Reserved'. A 15-minute expiration timer is initialized for the successful user. The failed user's account state remains unchanged.

### Edge Case 2: EV Unplugged but Vehicle Not Moved

- **Scenario & Ambiguity:** An EV reaches 100% charge, the 30-minute grace period expires, and the penalty begins. The user arrives and unplugs the charging cable to stop the session, but leaves the vehicle physically parked in the EV spot to attend a class.
- **Architectural Resolution:** The "Charger Idle Penalty" rule logic shall be tied to physical spot occupancy, not just the charging cable connection. Unplugging the cable ends the electrical transaction, but the 5 NIS/hour penalty continues to accrue until the LPR exit camera or spot occupancy sensor confirms the vehicle has physically vacated the space.
- **Impact on System State:** The active charging session ends in the payment gateway, but the system continues to append the 5 NIS/hour fee to the user's billing account ledger until physical departure is logged.

---

## 3. Requirements Traceability Matrix (RTM)

<!--
INSTRUCTIONS FOR STUDENTS:
1. Formulate atomic, single-responsibility requirements using RFC 2119 ("shall") and EARS syntax.
2. Maintain the starter rows below and ADD at least:
   - 2 more Functional Requirements (FR) to reach a total of at least 3 FRs.
   - 2 more Non-Functional Requirements (NFR) to reach a total of at least 3 NFRs.
3. Every NFR MUST contain an explicit, measurable numeric threshold or SLA latency limit.
-->


Action 1: Resource Allocation for a Task & Reporting Task Completion

Functional Requirements (FR):

FR1.1: The system shall allow a Dispatcher to allocate a predefined resource package to a task currently in the 'Ready' state.

FR1.2: The system shall immediately release all resources associated with a task (returning their status to 'Available') once the assigned Field Commander reports the task as 'Completed'.

FR1.3: The system shall automatically block the resource allocation process and abort the transaction if even one resource within the requested package is unavailable.

Non-Functional Requirements (NFR):

NFR1.1 : The system shall execute a resource allocation as a single SQL transaction, ensuring a 100% rollback if even 1 requested resource is unavailable, to prevent partial bookings.

NFR1.2 : The system shall push real-time status updates of the allocation to all relevant client stations immediately, requiring exactly 0 polling requests from the clients. 

NFR1.3 : The system shall mandate that 100% of users, including the Dispatcher performing the allocation, are authenticated via username and password before granting access.  

NFR1.4 : The system shall support concurrent dispatcher actions exclusively over a Local Area Network (LAN) utilizing the OCSF framework over TCP/IP, without using a Web-based interface.

Action 2: Time Compliance and SLA Report Generation

Functional Requirements (FR):

FR2.1: The system shall automatically generate the SLA report exactly at the end of each month, triggered internally by the system clock.

FR2.2: The system shall allow a System Administrator to manually generate an SLA report on-demand for any 1 selected past month.

FR2.3: The system shall calculate the SLA metric by computing the average time elapsed from the opening of an event until the closure of the last task within that event, grouped strictly by event types.

Non-Functional Requirements (NFR):

NFR2.1 : The system shall restrict access to ensure that 100% of SLA report generation and viewing actions are permitted exclusively to the "System Administrator" role, blocking the other 4 user roles.

NFR2.2 : The system's reporting module architecture shall be designed to ensure that adding any new report type in the future requires minimal development effort.

NFR2.3 : The system's graphical interface shall present the SLA report clearly segmented into exactly 5 distinct visual categories, strictly corresponding to the predefined closed list of event types.

NFR2.4 : The system shall extract the timestamps for the SLA calculations exclusively from the system's Audit Log, relying on its 100% read-only chronological records of event openings and task state changes.



| Req ID     | Requirement Description (Clean English with "shall")                                                                                                                                                     | Type (FR/NFR) | Mapped Use Case      | Test ID |
| :--------- | :-----------------------------------------------------------------------------------------------------------------------------                                                                           | :-----------: | :--------------      | :-----: |
| **FR1.1** | The system shall allow a Dispatcher to allocate a predefined resource package to a task currently in the 'Ready' state.                                                                                   |      FR       | UC_AllocateResources  | TEST-01 |
| **FR1.2** | The system shall immediately release all resources associated with a task (returning their status to 'Available') once the assigned Field Commander reports the task as 'Completed'                       |      FR       | UC_ReportCompletion  | TEST-02 |
| **FR1.3** | The system shall automatically block the resource allocation process and abort the transaction if even one resource within the requested package is unavailable.                                          |      FR       | UC_AllocateResources | TEST-03 |
| **NFR1.1**| The system shall execute a resource allocation as a single SQL transaction, ensuring a 100% rollback if even 1 requested resource is unavailable, to prevent partial bookings.                            |      NFR      | UC_AllocateResources | TEST-03 |
| **NFR1.2** | The system shall push real-time status updates of the allocation to all relevant client stations immediately, requiring exactly 0 polling requests from the clients.                                     |      NFR       | UC_AllocateResources| TEST-04 |
| **NFR1.3** |The system shall mandate that 100% of users, including the Dispatcher performing the allocation, are authenticated via username and password before granting access.                                      |      NFR      | UC_AllocateResources |TEST-05 |
| **NFR1.4** | The system shall support concurrent dispatcher actions exclusively over a Local Area Network (LAN) utilizing the OCSF framework over TCP/IP, without using a Web-based interface.                        |      NFR      | UC_AllocateResources | TEST-06 |
| **FR2.1** | The system shall automatically generate the SLA report exactly at the end of each month, triggered internally by the system clock.                                                                        |      FR       |UC_AutoSLAReport      | TEST-07 |
| **FR2.2** | The system shall allow a System Administrator to manually generate an SLA report on-demand for any 1 selected past month.                                                                                 |      FR       |UC_ManualSLAReport    | TEST-08 |
| **FR2.3** | The system shall calculate the SLA metric by computing the average time elapsed from the opening of an event until the closure of the last task within that event, grouped strictly by event types.       |      FR       |UC_AutoSLAReport      | TEST-09 |
| **NFR2.1**| The system shall restrict access to ensure that 100% of SLA report generation and viewing actions are permitted exclusively to the "System Administrator" role, blocking the other 4 user roles.          |      NFR      | UC_ManualSLAReport   | TEST-10 |
| **NFR2.2**|The system's reporting module architecture shall be designed to ensure that adding any new report type in the future requires minimal development effort.                                                  |      NFR      | UC_ManualSLAReport   | TEST-11 |
| **NFR2.3**|The system's graphical interface shall present the SLA report clearly segmented into exactly 5 distinct visual categories, strictly corresponding to the predefined closed list of event types.            |      NFR      |UC_AutoSLAReport      | TEST-12 |
| **NFR2.4**|The system shall extract the timestamps for the SLA calculations exclusively from the system's Audit Log, relying on its 100% read-only chronological records of event openings and task state changes.    |      NFR      |UC_AutoSLAReport      | TEST-13 |
---

## 4. BDD Acceptance Tests (Given-When-Then)

<!--
INSTRUCTIONS FOR STUDENTS:
Author two complete BDD acceptance tests corresponding to the Test IDs in your RTM table.
- Test 1: Happy Path Operational Flow.
- Test 2: Exception, Fault, or Automated Penalty Flow.
-->

### Acceptance Test [TEST-01]: Successful Resource Allocation (Happy Path)

- **Given:** A Dispatcher is logged into the application and is viewing a task currently in the 'Ready' state, with all required resources in the predefined package marked as 'Available'.
- **When:** The Dispatcher initiates the allocation of the resource package to the task.
- **Then:** The system shall successfully allocate the resources, update their statuses to 'Allocated', and transition the task to the 'Active' state.


### Acceptance Test [TEST-03]: Resource Allocation Blocked Due to Unavailable Resource (Exception / Error Scenario)

- **Given:** A Dispatcher is logged into the application and attempts to allocate a resource package to a task in the 'Ready' state, but at least 1 required resource in the package is 'Unavailable'.
- **When:** The Dispatcher initiates the allocation process.
- **Then:** The system shall block the allocation, maintain the task in the 'Ready' state without reserving any resources, and display a clear error message to the Dispatcher.


### Acceptance Test[TEST-08]: Manual SLA Report Generation (Happy Path)

- **Given:** A user successfully authenticated with the "System Administrator" role is accessing the reporting module on the JavaFX desktop GUI.

- **When:** The System Administrator selects a specific past month and manually requests the generation of the SLA report

- **Then:** The system shall calculate the correct SLA metrics from the Audit Log timestamps, group the data by the 5 predefined event types, and successfully render the graphical report on the screen.



### Acceptance Test [TEST-10]:Unauthorized Access to SLA Report Blocked (Exception / Security Error Scenario)

- **Given:** A user logged into the system with the "Dispatcher" or "Field Commander" role (which is not a System Administrator).

- **When:** The user attempts to access the reporting module or trigger the generation of the monthly SLA report.

- **Then:** The system shall completely block the request, prevent any database queries related to the SLA data, and display an "Unauthorized Access" error, ensuring 100% restriction of the action.



---

## 5. Pre-Submission Self-Audit Checklist

Before compressing your final ZIP archive (`ex1_ID1_ID2.zip`), verify compliance:

- [ ] All sections of this document are completed exclusively in professional English.
- [ ] The Mermaid diagram renders without errors in IntelliJ's Split Preview window.
- [ ] Dependencies (`<<include>>` and `<<extend>>`) have arrows pointing in the architecturally correct directions.
- [ ] At least two resolved business edge cases are documented with state impact.
- [ ] The RTM contains at least 3 FR and 3 NFR rows, with all NFRs having explicit numeric thresholds.
- [ ] Acceptance Test IDs (`TEST-XX`) strictly match the corresponding entries in the RTM table.
