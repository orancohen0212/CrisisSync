# Requirements Architecture & System Specification

**Course:** Software Engineering Methods  
**System:** CrisisSync


### Engineering Team Details
- Group 1
- **Student 1:** Oran Cohen
- **Student 2:** Amit Asaf 
- **Student 3:** Yasmin Askarov 
- **Student 4:** Lihi Elias 

---
# Requirements Architecture & System Specification

**Course:** Software Engineering Methods  
**System:** CrisisSync


### Engineering Team Details
- Group 1
- **Student 1:** Oran Cohen
- **Student 2:** Amit Asaf 
- **Student 3:** Yasmin Askarov 
- **Student 4:** Lihi Elias 

---

## 1. Requirements 

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



## 2. Requirements Traceability Matrix (RTM)


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

## 3. BDD Acceptance Tests (Given-When-Then)

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



