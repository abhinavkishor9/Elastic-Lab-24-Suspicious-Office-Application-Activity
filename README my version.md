# Elastic-Lab-24-Suspicious-Office-Application-Activity
Applications such as Microsoft Word, Excel, and PowerPoint commonly create or access files and communicate with other applications. However, an Office application launching a scripting engine or command shell may warrant further investigation, particularly when combined with an unusual command line, an unexpected executable path, or suspicious network activity.
For example, a document could trigger a process chain such as:
WINWORD.EXE
Microsoft Word opens a document
|
v
powershell.exe
A child process starts
|
v
Network connection or additional execution
Investigate only if supporting events exist

Office applications can launch other processes for legitimate reasons, so process relationships must be assessed in context.

This lab investigates Microsoft Office application activity using Elastic Security and Sysmon telemetry. The objective is to determine whether available endpoint evidence supports further investigation of potentially suspicious Office-related execution.

Office applications such as Word, Excel, and PowerPoint can be abused to initiate suspicious activity. However, the presence of an Office process alone does not establish malicious behavior. Investigators must examine available process details, execution context, and related telemetry before drawing conclusions.

## Lab Objectives

- Examine Windows endpoint telemetry to identify available evidence of Microsoft Office application execution.
- Investigate process-creation events associated with Word, Excel, and PowerPoint using ES|QL.
- Analyze process execution context, including executable paths, command-line arguments, parent processes, user accounts, and timestamps when available.
- Identify potentially suspicious relationships between Office applications and child processes without assuming malicious intent.
- Validate empty query results by checking the time range, data availability, field mappings, and event collection.
- Correlate relevant endpoint events to establish a defensible sequence of activity where sufficient evidence exists.
- Distinguish confirmed observations from investigative hypotheses and unresolved questions.
- Document evidence gaps and determine what additional telemetry would be required to support a reliable assessment.
- Produce an evidence-based conclusion without treating isolated artifacts or missing events as proof of compromise.

## Lab Scenario 

A Windows endpoint is being investigated for potentially suspicious activity involving Microsoft Office applications. The analyst must use Elastic Security to examine available Sysmon telemetry and determine whether any Office-related process execution warrants closer inspection.

The investigation focuses on:
- Searching for Word, Excel, and PowerPoint process activity.
- Examining process execution details and parent-child relationships.
- Identifying potentially suspicious behavior without relying on process names alone.
- Troubleshooting missing events and validating query results.
- Documenting findings and evidence gaps before reaching a conclusion.

During the initial search, the Office-specific query returns no matching documents. The analyst must investigate this result by validating the time range, available telemetry, and relevant field values before deciding what can be established.

The objective is to complete an evidence-based investigation that explains what the available logs show, what remains unverified, and whether further investigation is justified.

## Lab Environment

- **Platform:** Elastic Security
- **Telemetry source:** Windows Sysmon Operational logs
- **Query language:** ES|QL
- **Data view:** `logs-*`
- **Endpoint:** `DESKTOP-9MMM37V`
- **Elastic Agent policy:** `Windows-SOC-Lab`

The queries in this lab must be validated against the fields and events actually available in the environment.

## Investigation Workflow

1. Confirm that the Elastic Agent is connected and the relevant Sysmon data is available.
2. Search for process-creation events associated with Microsoft Office applications.
3. Review the returned records and note the result count.
4. If Office process events are available, examine executable paths, command lines, parent processes, users, and timestamps.
5. Investigate related child-process activity where the required telemetry exists.
6. Record any empty results and assess whether the time range, field mappings, filters, or data availability could explain them.
7. Document findings and distinguish confirmed observations from hypotheses.

## Initial ES|QL Query

Use this query to search for common Microsoft Office process names:

```esql
FROM logs-*
| WHERE process.name IN (
    "WINWORD.EXE",
    "EXCEL.EXE",
    "POWERPNT.EXE"
)
| KEEP @timestamp, host.name, user.name,
       process.name, process.executable,
       process.command_line, process.parent.name
| SORT @timestamp DESC
| LIMIT 100
```

This query searches for records matching the specified process names. An empty result means that no matching records were returned by this query under the current data, time range, and filter conditions. It does not prove that the applications never executed.

## Evidence Requirements

Record the following details whenever matching events are returned:

- Event timestamp
- Hostname and user
- Process name and executable path
- Command line, if available
- Parent process, if available
- Relevant event identifiers
- Query time range and result count
- Related events and supporting evidence

Do not infer missing process relationships or claim that suspicious activity occurred without supporting telemetry.

## Investigation Notes

Store detailed observations, query results, and evidence-based conclusions in `investigation-notes.md`.

Use `troubleshooting-notes.md` to record query failures, empty results, field-mapping issues, and telemetry limitations.

Use `timeline.md` to document verified events in chronological order.

## Expected Outcome

The lab should produce a documented assessment of whether the available telemetry contains relevant Office application activity and whether any observed behavior warrants additional investigation.

If the expected events are not present, document the limitation and the checks performed. Do not manufacture evidence to complete the scenario.

