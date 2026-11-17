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

Action 1: Resource Allocation

FR1.1: The system shall allow a Dispatcher to allocate a resource package that must always include exactly 1 Field Commander, selecting the resources exclusively from a predefined closed list of exactly 5 equipment types and 6 personnel roles.  

FR1.2: The system shall abort manual resource allocation transactions immediately if any requested resource is unavailable; however, for automatic allocations, the system shall perform up to 2 alternative attempts to find an available resource before 
failing the allocation process.

NFR1.1 (Transaction Atomicity): The system shall execute the resource package reservation as a single atomic SQL transaction based on the "all or nothing" principle, guaranteeing a 100% rollback if even 1 requested resource is unavailable. 

NFR1.2 (Network Latency & Real-Time Updates): The system shall push all allocation status changes to all relevant client stations immediately via Server Push, requiring exactly 0 polling requests from the client applications.  

NFR1.3 (Security & Access Control): The system shall strictly mandate that 100% of the 5 predefined user types successfully authenticate using a username and password before granting access to any system functionality.

Action 2: SLA Report Generation

FR2.1: The system shall automatically trigger the generation of the SLA report exactly at the end of each month, driven strictly by the internal system clock. 

FR2.2: The system shall calculate the SLA metric by computing the average time elapsed from the opening of an event until the closure of its final task, categorized strictly by exactly 5 predefined event types: building collapse, fire, car accident, hazmat leak, and natural disaster

NFR2.1 (Authorization Limits): The system shall restrict viewing and generating access for the SLA report exclusively to the System Administrator, explicitly blocking the other 4 roles (Logistics and Resources Officer, Dispatcher, Field Commander, and Field Crew Member). 

NFR2.2 (UI/UX Categorization): The system's graphical interface shall present the SLA report visually segmented into exactly 5 distinct categories, mapping directly to the predefined closed list of business event types.

Action 3:Task Dependencies & Lifecycle Management (DAG)

FR3.1: The system shall validate all task dependencies within the Directed Acyclic Graph (DAG) and automatically reject 100% of operations—such as adding or modifying a dependency—that would result in a circular dependency (cycle).  

FR3.2: The system shall restrict the lifecycle of every task to exactly 5 predefined operational states: Blocked, Ready, Active, Completed, and Cancelled.   

NFR3.1 (Performance & Algorithmic Efficiency): The server-side business logic shall execute the cyclic dependency validation algorithm (DAG cycle check) in a maximum execution time of 500 milliseconds for graphs containing up to 100 task nodes, ensuring real-time responsiveness.

NFR3.2 (Usability & Error Handling): The system shall display a clear, specific error notification to the Dispatcher's interface within 1 second upon rejecting any operation that creates a circular dependency.

NFR3.3 (Data Integrity & Robustness): The system's backend architecture shall strictly enforce the task state machine rules, automatically blocking 100% of invalid state transition attempts (e.g., preventing a task from transitioning directly from 'Blocked' to 'Active' without passing through 'Ready').

NFR3.4 (Auditability & Traceability): The system shall automatically record exactly 100% of all task state transitions in the centralized Audit Log, permanently capturing the precise system timestamp and the User ID of the individual who triggered the state change.

Action 4: Cascading Task Cancellation
FR4.1: The system shall automatically calculate and display a confirmation prompt to the Dispatcher prior to task cancellation, explicitly presenting the exact numerical count of all dependent tasks that have not yet reached a final state and will subsequently be cancelled.

NFR4.1 (Performance & Graph Processing): The server-side logic shall compute the cascading cancellation impact across the task dependency graph (DAG) and render the confirmation prompt on the Dispatcher's interface in no more than 500 milliseconds, ensuring efficient operation under pressure. 

NFR4.2 (Usability & Error Prevention): The system shall design the confirmation prompt to require an explicit, intentional user interaction (e.g., clicking a distinct "Confirm Cancellation" button) to proceed, preventing accidental mass-cancellations during high-stress dispatching. 

NFR4.3 (Data Integrity & Atomicity): Upon the Dispatcher's confirmation, the system shall execute the cancellation of the parent task, the cancellation of all identified dependent tasks, and the release of all associated resources as a single atomic database transaction, ensuring 100% rollback if any part of the process fails.  

Action 5:Field Commander Constraints & Concurrency

FR5.1: The system shall restrict the assignment of any Field Commander to a maximum of 1 task at any given time, strictly enforcing 0 parallel allocations by blocking any attempt to reserve them for an additional concurrent task.

FR5.2 (Task Completion Action): The system shall allow a Field Commander to manually report their assigned task as finished, which shall immediately change the task's state to 'Completed' and automatically trigger the release of 100% of its assigned resources back to the 'Available' state.

NFR5.1 (Authorization & Access Control): The system shall strictly enforce data visibility and access rights, permitting a Field Commander to view and close exactly 100% of the specific tasks they are assigned to lead, while completely denying access to view or interact with any other system tasks.

NFR5.2 (Event Dashboard Visibility): The system's GUI shall dynamically filter and display the real-time Event Status Dashboard to a Field Commander if and only if they are currently assigned to at least 1 task within that specific event, hiding all other system events from their view.

Action 6:Event Closure & External Billing

FR6.1: The system shall allow the automatic calculation of the total operational costs upon the closure of an event.

FR6.2: The system shall permanently close an event record in the database if and only if it receives exactly 1 valid Transaction Token confirming the charge from the external national clearing and billing system.

NFR6.1 (Business Rule / Data Constraint): The system shall execute the cost calculation by strictly summing exactly 3 predefined parameters: personnel operating hours, engineering and medical equipment engine hours, and consumed materials.

NFR6.2 (System Integration & Architecture): The system's architecture shall implement the communication with the external clearing and billing system utilizing the Adapter design pattern, which shall serve as a Mock/Stub component to simulate the external system's behavior.

NFR6.3 (Error Handling & Usability): The system shall strictly prevent the closure of the event and immediately push a clear visual alert directly to the Dispatcher's interface if the external billing system rejects the transaction. 

NFR6.4 (Reliability & Performance): The system's external communication component shall enforce a strict maximum timeout threshold of exactly 3000 milliseconds; if no response (approval or rejection) is received within this timeframe, the system shall safely abort the transaction and display a network connection error to the Dispatcher to prevent GUI freezing.

Action 7: System History & Audit Log

FR7.1: The system shall display the Event History interface to the System Administrator, including all historical tasks, resource allocations, and billing confirmations.

FR7.2: The system shall automatically record every system action—including task state transitions, resource updates, and event lifecycle events (open, close, billing, cancellation) into the Audit Log

FR7.3 (Data Filtering): The system shall allow the System Administrator to filter the Event History list by exactly 3 predefined criteria: date range, event type, and event status.

NFR7.1 (Data Integrity & Permissions): The system shall strictly enforce a 100% Read-Only mode for the Event History interface, technically preventing any editing, deletion, or modification of the historical records

NFR7.2 (Performance & Query Efficiency): The system shall execute the historical data retrieval and apply the requested filters to the Event History view within a maximum response time of 1000 milliseconds.

NFR7.3 (Auditability & Traceability): The system shall enforce strict data traceability by permanently attaching the exact system timestamp and the unique User ID to 100% of the recorded Audit Log entries, rejecting any log entry that lacks this metadata.

modoule 8:Real-Time Event Dashboard

FR8.1: The system shall automatically and in real-time calculate the event's progress metric, which shall be displayed as the mathematical ratio of the number of tasks in the 'Completed' state out of the total number of tasks defined in the event's dependency tree (DAG).

FR8.2: The system shall trigger a highlighted visual indication for any active task whose actual execution time has exceeded its predefined Estimated Duration.
NFR8.1 (Performance & Communication): The system's Server Push mechanism shall broadcast any task or resource status change to exactly 100% of the connected client endpoints within a maximum latency of 500 milliseconds, strictly without utilizing any client-side polling mechanism.

NFR8.2 (Usability & Display Uniformity): The system shall enforce a uniform visual design code (e.g., highlighting in red or a uniform warning icon) across 100% of client screens for tasks that have exceeded their estimated time, to ensure immediate recognition and prevent operational confusion under pressure.

Action 9: System Bootstrapping & Data Loading

FR9.1: The system shall allow the loading of initial setup data (users, personnel, and equipment) from external files, and shall support data import in exactly 3 predefined formats: JSON, SQL, and CSV.

FR9.2: The system shall restrict access to and execution of the data loading module to exactly 1 user type: the System Administrator, and shall strictly block access to this module from all other system users.

NFR9.1 (Data Integrity & Atomicity): The system shall execute the data import process as a single atomic database transaction; the system shall reject the entire file and perform a 100% database rollback if even 1 validation error (e.g., an invalid format or a missing mandatory field) is detected in any of the imported records.

NFR9.2 (Performance & Efficiency): The system shall complete the file parsing and data insertion (for files containing up to 10,000 base records) into the relational database within a maximum processing time of 5 seconds.

Action 10: Equipment Failure Handling & Replacement

FR10.1: The system shall allow exactly 2 user roles (Field Commander and Field Crew Member) to report an equipment failure directly from the field while a task is in the 'Active' state.

FR10.2 (Equipment Failure Reporting & Status Update): The system shall allow the receipt of an equipment failure report, and upon reception, shall automatically update the equipment item's status to 'Faulty/In-Treatment' and immediately release it from its currently assigned task.

FR10.3 (Alternative Equipment Allocation): The system shall allow the Dispatcher to manually allocate exactly 1 alternative equipment item to a task whose original equipment was released due to a failure, in order to enable its continued execution.

NFR10.1 (System Responsiveness): The system shall process the equipment failure report and push an alert to the Dispatcher’s screen within a maximum latency of 1000 milliseconds, ensuring minimal delay in field operations.

Action 11: Concurrent Allocation & Race Condition Prevention

FR11.1: The system shall enforce strict concurrency control, ensuring that if exactly 2 Dispatchers attempt to allocate the same specific resource at the exact same millisecond, only 1 transaction shall succeed while the other is rejected. 

NFR11.1 (Database Locking mechanism): The data persistence layer shall strictly utilize Pessimistic Locking  to lock 100% of the requested resource records during the transaction evaluation phase.

NFR11.2 (Error Feedback Delay): The system shall present a clear error notification to the Dispatcher whose transaction failed within a maximum of 2 seconds, automatically refreshing their local view to reflect the updated resource unavailability.

Action 12: Event Cancellation & Resource Release

FR12.1 (Event Cancellation Action): The system shall allow the Dispatcher to cancel an open event at any stage (e.g., due to a false alarm).

FR12.2 (Automatic Resource Release): The system shall automatically update the status of all resources allocated to the cancelled tasks back to the 'Available' state upon event cancellation.

NFR12.1 (Data Integrity & Atomicity): The system shall execute the event cancellation process and the status updates of all its associated tasks as a single atomic transaction against the database; the system shall perform a 100% rollback if even 1 task update fails, in order to prevent a state of partial data.

Action 13: Personnel Availability Management

FR13.1: The system shall restrict the capability to update abnormal availability statuses of personnel (such as sick leave or vacation) exclusively to exactly 1 user role: the System Administrator. 

FR13.2: The system shall allow a Field Crew Member to manually toggle their own daily operational readiness between exactly 2 defined states ('Available' or 'Unavailable') upon starting a shift.

NFR13.1 (Data Integrity Constraint): The system shall strictly block any attempt by a System Administrator to change a personnel member's status to 'Unavailable' if that specific member is currently allocated to exactly 1 'Active' task, guaranteeing 0 active task disruptions

Action 14 : task DAG progression

FR14.1 (Automatic Task Progression in DAG): The system shall scan the event's task dependency tree (DAG) and automatically transition any dependent task to the 'Ready' state strictly if all of its prerequisite tasks have reached the 'Completed' state, upon receiving a task completion report from the Field Commander.

NFR14.1 (Graph Processing Performance): The system shall complete the dependency tree traversal and cycle-checking algorithm (during task creation or dependency update) in less than 500 milliseconds for a Directed Acyclic Graph (DAG) containing up to 100 task nodes.

Action 15: Authentication & Access Control

FR15.1 (User Access): The system shall allow login and interface access for exactly 5 authorized user types: System Administrator, Logistics and Resources Officer, Dispatcher, Field Commander, and Operational Team Member.

NFR15.1 (Security): The system shall block 100% of unauthenticated access attempts, and shall require prior authentication via the entry of a username and password for every login attempt.

NFR15.2 (UI/UX Architecture): The system shall load a distinct Graphical User Interface (GUI) for each successfully authenticated user, tailored exactly to one of the 5 user types and their specific role.

Action 16: System Architecture & Connectivity

FR16.1 (Concurrent Operations): The system shall allow the concurrent execution of operations (such as resource allocation or event closure) from an unlimited number of different workstations connected to the network.

NFR16.1 (Environment & Platform): The system shall operate 100% as a Desktop Application (in its initial phase) and shall strictly prohibit Web-based access via a browser.

NFR16.2 (Communication Protocol): The system shall transmit all data traffic between the client endpoints and the server exclusively over a Local Area Network (LAN) utilizing the TCP/IP protocol and the OCSF framework.


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



