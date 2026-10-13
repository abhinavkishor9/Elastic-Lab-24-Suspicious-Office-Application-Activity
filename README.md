# Lab 24 — Suspicious Office Application Activity

## Overview

This lab investigates Microsoft Office application activity using Elastic Security and Sysmon telemetry. The objective is to determine whether available endpoint evidence supports further investigation of potentially suspicious Office-related execution.

Office applications such as Word, Excel, and PowerPoint can be abused to initiate suspicious activity. However, the presence of an Office process alone does not establish malicious behavior. Investigators must examine available process details, execution context, and related telemetry before drawing conclusions.

## Objectives

- Search Sysmon process-creation events for Microsoft Office applications.
- Review process names, executable paths, command lines, and parent-process information when available.
- Identify potential relationships between Office applications and child processes.
- Investigate suspicious execution patterns without treating them as proof of compromise.
- Document query results, telemetry gaps, troubleshooting steps, and limitations.

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

## Conclusion

Suspicious Office activity must be assessed through evidence and context. Process names and isolated artifacts are starting points for investigation, not verdicts. A defensible conclusion should reflect what the available telemetry establishes, what remains uncertain, and what additional evidence would be required.
