# Troubleshooting Notes — Lab 24

## 1. Office Process Query Returns Zero Documents

**Symptom:** The search for `WINWORD.EXE`, `EXCEL.EXE`, and `POWERPNT.EXE` returns zero documents.

**Checks:**

1. Confirm the selected time range.
2. Verify that the expected Sysmon data stream contains recent events.
3. Check whether process-creation events are being ingested.
4. Inspect the actual values stored in `process.name`.
5. Check whether the events use a different data view or field mapping.
6. Remove the Office-specific filter temporarily to verify that process events are available.

**Important:** An empty result does not prove that the applications never executed.

## 2. General Process Search

Use this query to check whether process records are available:

```esql
FROM logs-*
| WHERE process.name IS NOT NULL
| KEEP @timestamp, host.name, process.name,
       process.executable, process.command_line
| SORT @timestamp DESC
| LIMIT 100
```

If records are returned, inspect their process names and field values before changing the Office-specific query.

If no records are returned, investigate the data view, time range, ingestion status, and field availability.

## 3. Elastic Agent or Fleet Connectivity

If recent events are missing:

1. Check the agent status in Fleet.
2. Confirm that the endpoint agent is connected and healthy.
3. Verify that the Windows Sysmon Operational log is being collected by the configured integration.
4. Check the agent's policy and integration status.
5. Confirm whether new events are arriving before repeating the investigation query.

Do not assume that a healthy agent guarantees every expected event is present. Verify the actual data.

## 4. Missing Fields

A query may return events without every requested field populated.

Check the event in Discover and verify the field names and values. If a field is absent, document that limitation rather than inferring its value.

Parent-process and command-line details should only be used when they are present in the returned records.

