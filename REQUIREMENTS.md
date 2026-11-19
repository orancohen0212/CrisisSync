# Requirements Architecture & System Specification

**Course:** Software Engineering Methods\
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

FR1.1: When a Dispatcher requests manual reservation of a predefined resource package for a task in the 'Ready' state and all resources in the package are available, the system shall reserve the entire package for that task and transition the task to the 'Active' state.

FR1.2: The system shall immediately release all resources associated with a task (returning their status to 'Available') once the assigned Field Commander reports the task as 'Completed'.

FR1.3: The system shall automatically block the resource allocation process and abort the transaction if even one resource within the requested package is unavailable.

Non-Functional Requirements (NFR):

NFR1.1 : The system shall complete a manual reservation of a package of up to 10 resources and return the result to the Dispatcher within 2 seconds for at least 95% of requests, with 20 Dispatcher stations working concurrently.

NFR1.2 : The system shall guarantee that 50 concurrent reservation attempts for the same resource from different stations produce exactly one successful reservation, zero double reservations, and zero partially reserved packages.

NFR1.3 : The system shall deliver every pushed status update to all connected relevant stations within 1 second of the transaction commit for at least 95% of updates, using server push with zero periodic polling requests from the client.

NFR1.4 : The system shall allow resource reservation only to authenticated Dispatcher sessions and completion reports only to the assigned Field Commander, and shall reject 100% of unauthorized attempts and record each one in the Audit Log.

Action 2: Time Compliance and SLA Report Generation

Functional Requirements (FR):

FR2.1: The system shall use the end of each calendar month, as determined by the system clock, as the automatic trigger for generating that month's SLA report.

FR2.2: The system shall allow a System Administrator to manually generate an SLA report on-demand for any 1 selected past month.

FR2.3: The system shall calculate the SLA metric by computing the average time elapsed from the opening of an event until the closure of the last task within that event, grouped strictly by event types.

Non-Functional Requirements (NFR):

NFR2.1 : The system shall generate the SLA Compliance Report for a month containing up to 5,000 Closed events and 50,000 tasks within 30 seconds, and shall keep Dispatcher request response times at or below 2 seconds while the generation runs.

NFR2.2 : The system shall calculate average event durations with an accuracy of 1 second, matching an independently computed reference result for 100% of the events in the verification dataset.

NFR2.3 : The system shall enable at least 9 out of 10 first-time System Administrator users to generate and open the SLA report for a specified past month within 60 seconds, without assistance, starting from the signed-in home screen.

NFR2.4 : The system shall start the monthly report generation within 5 minutes after the end of the calendar month according to the system clock, and shall start any generation missed because of server downtime within 5 minutes after the server starts.

## 2. Requirements Traceability Matrix (RTM)

| **Req ID** | **Requirement Description (Clean English with "shall")**                                                                                                                                                                                                               | **Type (FR/NFR)** | **Mapped Use Case**                                   | **Test ID** |
| ---------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------- | ----------------------------------------------------- | ----------- |
| FR1.1      | When a Dispatcher requests manual reservation of a predefined resource package for a task in the 'Ready' state and all resources in the package are available, the system shall reserve the entire package for that task and transition the task to the 'Active' state.                                                                                                                                                | FR                | Manual Resource Reservation                           | TEST-01 |
| FR1.2      | The system shall immediately release all resources associated with a task (returning their status to 'Available') once the assigned Field Commander reports the task as 'Completed'.                                                                                   | FR                | Report Task Completion                                | TEST-02 |
| FR1.3      | The system shall automatically block the resource allocation process and abort the transaction if even one resource within the requested package is unavailable.                                                                                                       | FR                | Manual Resource Reservation                           | TEST-03 |
| NFR1.1     | The system shall complete a manual reservation of a package of up to 10 resources and return the result to the Dispatcher within 2 seconds for at least 95% of requests, with 20 Dispatcher stations working concurrently.                                             | NFR               | Manual Resource Reservation                           | TEST-04 |
| NFR1.2     | The system shall guarantee that 50 concurrent reservation attempts for the same resource from different stations produce exactly one successful reservation, zero double reservations, and zero partially reserved packages.                                           | NFR               | Manual Resource Reservation                           | TEST-05 |
| NFR1.3     | The system shall deliver every pushed status update to all connected relevant stations within 1 second of the transaction commit for at least 95% of updates, using server push with zero periodic polling requests from the client.                                   | NFR               | Manual Resource Reservation<br>Report Task Completion | TEST-06 |
| NFR1.4     | The system shall allow resource reservation only to authenticated Dispatcher sessions and completion reports only to the assigned Field Commander, and shall reject 100% of unauthorized attempts and record each one in the Audit Log.                                | NFR               | Manual Resource Reservation<br>Report Task Completion | TEST-07 |
| FR2.1      | The system shall use the end of each calendar month, as determined by the system clock, as the automatic trigger for generating that month's SLA report.                                                                                                                                     | FR                | Generate Management Reports                           | TEST-09 |
| FR2.2      | The system shall allow a System Administrator to manually generate an SLA report on-demand for any 1 selected past month.                                                                                                                                              | FR                | Generate Management Reports                           | TEST-08, TEST-10 |
| FR2.3      | The system shall calculate the SLA metric by computing the average time elapsed from the opening of an event until the closure of the last task within that event, grouped strictly by event types.                                                                    | FR                | Generate Management Reports                           | TEST-08 |
| NFR2.1     | The system shall generate the SLA Compliance Report for a month containing up to 5,000 Closed events and 50,000 tasks within 30 seconds, and shall keep Dispatcher request response times at or below 2 seconds while the generation runs.                             | NFR               | Generate Management Reports                           | TEST-11 |
| NFR2.2     | The system shall calculate average event durations with an accuracy of 1 second, matching an independently computed reference result for 100% of the events in the verification dataset.                                                                               | NFR               | Generate Management Reports                           | TEST-08, TEST-12 |
| NFR2.3     | The system shall enable at least 9 out of 10 first-time System Administrator users to generate and open the SLA report for a specified past month within 60 seconds, without assistance, starting from the signed-in home screen. | NFR               | Generate Management Reports<br>View Reports           | TEST-13 |
| NFR2.4     | The system shall start the monthly report generation within 5 minutes after the end of the calendar month according to the system clock, and shall start any generation missed because of server downtime within 5 minutes after the server starts.                    | NFR               | Generate Management Reports                           | TEST-09, TEST-14 |

## 3. BDD Acceptance Tests (Given–When–Then)

### TEST-01 — Reserve a complete package

**Requirements:** FR1.1

- **Given** A Dispatcher is signed in. Task A is Ready and each resource in its required package is Available.
- **When** The Dispatcher reserves the package for A.
- **Then** Every resource in the package is reserved for A and A becomes Active.

### TEST-02 — Release resources on completion

**Requirements:** FR1.2

- **Given** Task A is Active. Its assigned Field Commander and two equipment items are reserved for A. Task B has A as its only prerequisite. Task C also depends on another unfinished task.
- **When** The assigned Field Commander reports A complete.
- **Then** At the end of the completion transaction, A is Completed, all three resources are Available, B is Ready and C remains Blocked.

### TEST-03 — Recover from an unavailable resource

**Requirements:** FR1.3

- **Given** Task A is Ready. One resource in the requested package is reserved for another task. All remaining resources are Available.
- **When** The Dispatcher requests manual reservation of the whole package.
- **Then** The transaction rolls back. A remains Ready, no resource changes assignment, and the station displays an error and the current resource statuses. After the conflicting resource is released, retrying the request reserves the entire package and makes A Active.

### TEST-04 — Return manual reservation results under load

**Requirements:** NFR1.1

- **Given** Twenty Dispatcher stations are signed in. In each of five rounds per package size (1 through 10), each station has a distinct Ready task and a separate available package.
- **When** All 20 stations submit one reservation simultaneously in each round. Elapsed time is measured from submission to receipt of the result at the station.
- **Then** For each package size, all 100 reservations succeed and at least 95 results arrive within 2 seconds.

### TEST-05 — Resolve 50 competing reservations

**Requirements:** NFR1.2

- **Given** Fifty Dispatcher stations each have a distinct Ready task. All 50 packages request the same available bulldozer and otherwise contain distinct available resources.
- **When** All stations request manual reservation simultaneously.
- **Then** Exactly one task becomes Active and holds its complete package. The other 49 remain Ready with zero resources reserved for them. The bulldozer has exactly one assignment.

### TEST-06 — Push status changes to relevant stations

**Requirements:** NFR1.3

- **Given** All relevant stations are connected, their clocks are synchronized with the server, and transaction, display and network traces are enabled.
- **When** Reservations and completions produce 100 committed task or resource status changes. For each change, record the commit time and receipt time at every relevant station.
- **Then** Every change reaches every relevant station by server push. For at least 95 of the 100 changes, the last relevant station receives the update within 1 second of commit. The network trace contains zero periodic client polling requests.

### TEST-07 — Enforce permissions and audit rejected requests

**Requirements:** NFR1.4

- **Given** Separate valid tasks are available for each attempt. Test identities include a Dispatcher, the assigned and an unassigned Field Commander, a System Administrator, a logistics officer, a Field Crew Member and a signed-out user.
- **When** Each identity attempts both manual reservation and completion. Each completion targets an Active task and each reservation targets a Ready task with an available package.
- **Then** Only the signed-in Dispatcher succeeds at reservation. Only the assigned Field Commander succeeds at completion. The system rejects 100% of the other attempts, records each rejection in the Audit Log and leaves the affected task and resources unchanged for each rejected attempt.

### TEST-08 — Generate an accurate report for the selected month

**Requirements:** FR2.2, FR2.3, NFR2.2

- **Given** October contains two fires and one road accident. Each opens at 10:00:00 on its respective day. The last task closes at 10:10:01 and 10:20:03 for the fires and at 10:04:09 for the accident. Each event has an earlier task closure and is administratively closed later. September contains one fire lasting 60 minutes.
- **When** In November, a signed-in System Administrator selects October and requests its SLA report.
- **Then** The October report shows an average of 902 seconds for fires and 249 seconds for road accidents, within 1 second of each reference value. The September fire contributes to neither result.

### TEST-09 — Trigger the monthly report at the calendar boundary

**Requirements:** FR2.1, NFR2.4

- **Given** The server is running, the system clock is just before the end of October, and no October SLA report has been generated.
- **When** The system clock advances from October 31 at 23:59:59 to November 1 at 00:00:00 without a user request.
- **Then** The month-end transition automatically triggers the October report at 00:00:00. Generation starts within 5 minutes of that boundary and produces the October SLA report.

### TEST-10 — Switch between past months on demand

**Requirements:** FR2.2

- **Given** It is November. September contains one fire lasting 600 seconds and October contains one fire lasting 1,200 seconds, measured from event opening to last task closure. A System Administrator is signed in.
- **When** The administrator generates the October SLA report, then selects September and requests its report in the same session.
- **Then** The first report identifies October and shows a fire average of 1,200 seconds. The second identifies September and shows a fire average of 600 seconds.

### TEST-11 — Generate a full monthly report during Dispatcher activity

**Requirements:** NFR2.1

- **Given** The target month contains 5,000 Closed events and 50,000 tasks. Dispatcher stations are submitting ordinary requests and recording end-to-end response times.
- **When** An administrator requests the monthly SLA report while the stations continue submitting requests throughout generation.
- **Then** The report is ready within 30 seconds of the request, and every Dispatcher request submitted during generation receives its response within 2 seconds.

### TEST-12 — Verify timing accuracy across the reference dataset

**Requirements:** NFR2.2

- **Given** A fixed audit dataset contains all five event types, several multi-task events and durations with nonzero seconds. An independent calculation records the opening-to-last-task duration for every event and the mean for each type.
- **When** Generate the report for the full dataset. Then repeat with each event as the only event in its test month so its individual duration can be checked through the report.
- **Then** All five group means differ from their reference means by at most 1 second. In every single-event run, the reported mean differs from that event’s reference duration by at most 1 second (100% of events).

### TEST-13 — Generate and open a report without assistance

**Requirements:** NFR2.3

- **Given** Ten participants with the System Administrator role have never used the reporting module. Each starts independently on the signed-in home screen. The specified past month contains reportable events.
- **When** Each participant is asked to generate and open the SLA report for that month, the observer starts a timer and provides no instructions or hints.
- **Then** At least 9 of the 10 participants display the SLA report for the specified month within 60 seconds of receiving the task and without assistance. Opening a different month does not count as success.

### TEST-14 — Recover a monthly generation missed during downtime

**Requirements:** NFR2.4

- **Given** The server is offline from October 31 at 23:58 until November 1 at 00:12. October data is available and the month-end report was missed.
- **When** The server starts at 00:12 without an administrator requesting a report.
- **Then** The system starts generation of the missed October report by 00:17 and makes the completed report available to the System Administrator.
