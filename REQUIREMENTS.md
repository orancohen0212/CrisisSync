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

NFR1.1 : The system shall complete a manual reservation of a package of up to 10 resources and return the result to the Dispatcher within 2 seconds for at least 95% of requests, with 20 Dispatcher stations working concurrently.

NFR1.2 : The system shall guarantee that 50 concurrent reservation attempts for the same resource from different stations produce exactly one successful reservation, zero double reservations, and zero partially reserved packages. 

NFR1.3 : The system shall deliver every pushed status update to all connected relevant stations within 1 second of the transaction commit for at least 95% of updates, using server push with zero periodic polling requests from the client.

NFR1.4 : The system shall allow resource reservation only to authenticated Dispatcher sessions and completion reports only to the assigned Field Commander, and shall reject 100% of unauthorized attempts and record each one in the Audit Log.



Action 2: Time Compliance and SLA Report Generation

Functional Requirements (FR):

FR2.1: The system shall automatically generate the SLA report exactly at the end of each month, triggered internally by the system clock.

FR2.2: The system shall allow a System Administrator to manually generate an SLA report on-demand for any 1 selected past month.

FR2.3: The system shall calculate the SLA metric by computing the average time elapsed from the opening of an event until the closure of the last task within that event, grouped strictly by event types.

Non-Functional Requirements (NFR):

NFR2.1 : The system shall generate the SLA Compliance Report for a month containing up to 5,000 Closed events and 50,000 tasks within 30 seconds, and shall keep Dispatcher request response times at or below 2 seconds while the generation runs.

NFR2.2 : The system shall calculate average event durations with an accuracy of 1 second, matching an independently computed reference result for 100% of the events in the verification dataset.

NFR2.3 : The system shall allow a new report type to be added by adding one new class that implements the report-generator interface and one registration entry, with zero modifications to the scheduler, to the existing report generators, and to the report viewing screen.

NFR2.4 : The system shall start the monthly report generation within 5 minutes after the end of the calendar month according to the system clock, and shall start any generation missed because of server downtime within 5 minutes after the server starts.



## 2. Requirements Traceability Matrix (RTM)

| Req ID | Requirement Description (Clean English with "shall") | Type (FR/NFR) | Mapped Use Case | Test ID |
|--------|------------------------------------------------------|---------------|-----------------|---------|
| FR1.1 | The system shall allow a Dispatcher to allocate a predefined resource package to a task currently in the 'Ready' state. | FR | UC – Manual Resource Reservation | AT-FR1.1-1<br>AT-FR1.1-2 |
| FR1.2 | The system shall immediately release all resources associated with a task (returning their status to 'Available') once the assigned Field Commander reports the task as 'Completed'. | FR | UC – Report Task Completion | AT-FR1.2-1<br>AT-FR1.2-2 |
| FR1.3 | The system shall automatically block the resource allocation process and abort the transaction if even one resource within the requested package is unavailable. | FR | UC – Manual Resource Reservation | AT-FR1.3-1<br>AT-FR1.3-2 |
| NFR1.1 | The system shall complete a manual reservation of a package of up to 10 resources and return the result to the Dispatcher within 2 seconds for at least 95% of requests, with 20 Dispatcher stations working concurrently. | NFR | UC – Manual Resource Reservation | AT-NFR1.1-1 |
| NFR1.2 | The system shall guarantee that 50 concurrent reservation attempts for the same resource from different stations produce exactly one successful reservation, zero double reservations, and zero partially reserved packages. | NFR | UC – Manual Resource Reservation | AT-NFR1.2-1<br>AT-NFR1.2-2 |
| NFR1.3 | The system shall deliver every pushed status update to all connected relevant stations within 1 second of the transaction commit for at least 95% of updates, using server push with zero periodic polling requests from the client. | NFR | UC – Manual Resource Reservation<br>UC – Report Task Completion | AT-NFR1.3-1 |
| NFR1.4 | The system shall allow resource reservation only to authenticated Dispatcher sessions and completion reports only to the assigned Field Commander, and shall reject 100% of unauthorized attempts and record each one in the Audit Log. | NFR | UC – Manual Resource Reservation<br>UC – Report Task Completion | AT-NFR1.4-1<br>AT-NFR1.4-2<br>AT-NFR1.4-3 |
| FR2.1 | The system shall automatically generate the SLA report exactly at the end of each month, triggered internally by the system clock. | FR | UC – Generate Management Reports | AT-FR2.1-1 |
| FR2.2 | The system shall allow a System Administrator to manually generate an SLA report on-demand for any 1 selected past month. | FR | UC – Generate Management Reports | AT-FR2.2-1<br>AT-FR2.2-2 |
| FR2.3 | The system shall calculate the SLA metric by computing the average time elapsed from the opening of an event until the closure of the last task within that event, grouped strictly by event types. | FR | UC – Generate Management Reports | AT-FR2.3-1<br>AT-FR2.3-2 |
| NFR2.1 | The system shall generate the SLA Compliance Report for a month containing up to 5,000 Closed events and 50,000 tasks within 30 seconds, and shall keep Dispatcher request response times at or below 2 seconds while the generation runs. | NFR | UC – Generate Management Reports | AT-NFR2.1-1 |
| NFR2.2 | The system shall calculate average event durations with an accuracy of 1 second, matching an independently computed reference result for 100% of the events in the verification dataset. | NFR | UC – Generate Management Reports | AT-NFR2.2-1 |
| NFR2.3 | The system shall allow a new report type to be added by adding one new class that implements the report-generator interface and one registration entry, with zero modifications to the scheduler, to the existing report generators, and to the report viewing screen. | NFR | UC – Generate Management Reports<br>UC – View Reports | AT-NFR2.3-1 |
| NFR2.4 | The system shall start the monthly report generation within 5 minutes after the end of the calendar month according to the system clock, and shall start any generation missed because of server downtime within 5 minutes after the server starts. | NFR | UC – Generate Management Reports | AT-NFR2.4-1<br>AT-NFR2.4-2 |


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



