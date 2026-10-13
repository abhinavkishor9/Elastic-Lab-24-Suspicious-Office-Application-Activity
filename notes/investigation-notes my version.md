# Investigation Notes — Lab 24

## 1. Initial Process Search

Query:

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

### Observation

The initial Office process search returned **0 documents** in the previously observed query run.

### Interpretation

No matching records were returned for the specified process names under the query conditions used. This result does not establish that Office applications were absent from the endpoint or that no suspicious activity occurred.

Possible explanations include:

- No matching events were ingested during the selected time range.
- The relevant events are stored outside the selected data view.
- The process-name field or values differ from the query assumptions.
- The required event type was not collected or retained.
- The query filters exclude the relevant records.

These are possible explanations, not confirmed causes.

## 2. Follow-Up Checks

Perform and document the following checks before reaching a conclusion:

1. Verify that the Elastic Agent is healthy and connected.
2. Confirm that recent Sysmon Operational events are searchable.
3. Expand the time range to cover the period under investigation.
4. Inspect the available field names and actual process-name values.
5. Search process-creation events without restricting the query to Office applications.
6. Repeat the Office-specific query after confirming the data source, field values, and time range.

General process-creation search:

```esql
FROM logs-*
| WHERE process.name IS NOT NULL
| KEEP @timestamp, host.name, user.name,
       process.name, process.executable,
       process.command_line, process.parent.name
| SORT @timestamp DESC
| LIMIT 100
```

## 3. Assessment

**Confirmed:** The Office-specific query returned zero documents in the observed run.

**Not established:** Whether Office applications executed during the investigation period, whether suspicious child processes existed, or whether the absence of results was caused by telemetry collection, query scope, field mapping, or another factor.

**Next step:** Validate data availability and query assumptions before attempting to classify activity as benign or malicious.

