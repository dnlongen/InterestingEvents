# InterestingEvents
Windows events of interest for logging and detection of compromise. Work in progress.

| Event ID | Log | Source | Task Category | Notes |
| -------- | --- | ------ | ------------- | ----- |
| 1102 | SECURITY | | | Windows event log was cleared. |
| 4720 | SECURITY | Microsoft-Windows-Security-Auditing |	User Account Management | A user account was created. High confidence on workstations and member servers. Noisy on domain controllers and needs to correlate against expected changes. |
