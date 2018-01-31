# InterestingEvents
Windows events of interest for logging and detection of compromise. Work in progress.

| Event ID | Log | Source | Task Category | Notes |
| -------- | --- | ------ | ------------- | ----- |
| 1102 | SECURITY | Eventlog | Log clear | The audit log (aka SYSTEM log) was cleared. This may indicate an intruder covering their tracks; the event message will specify the domain name and account name. Note that clearing logs other than the audit log will generate an Event ID 104 in the SYSTEM log instead. |
| 4720 | SECURITY | Security-Auditing |	User Account Management | A user account was created. High confidence on workstations and member servers. Noisy on domain controllers and needs to correlate against expected changes. SubjectDomainName and SubjectAccountName are the privileged user that made the change; TargetDomainName and TargetUserName are the new user |
| 4722 | SECURITY | Security-Auditing |	User Account Management | A user account was enabled. High confidence on workstations and member servers. Noisy on domain controllers and needs to correlate against expected changes. If terminated employee accounts are disabled rather than deleted, re-enablement outside of an established process is a high confidence indicator. |
| 4726 | SECURITY | Security-Auditing |	User Account Management | A user account was deleted. Could indicate an intruder covering their tracks. |
| 4697 | SECURITY | Security-Auditing | Security System Extension | A service was installed in the system. Potentially noisy on workstations and member servers, but a service unexpectedly installed could also indicate a persistence mechanism. Filtering events where the SubjectUserName ends in $ will filter out services installed at boot time |
| 7045 | SYSTEM | Service Control Manager | | A service was installed in the system. Installing a service generates a 7045 event in the System log, and a 4697 event in the Security log. The difference is that the 4697 event also identifies the account that installed the service.
| 4698 | SECURITY | Security-Auditing | Other Object Access Events | A scheduled task was created. Potentially noisy on workstations and member servers, but an unexpected scheduled task could also indicate a persistence mechanism. 4698 events contain details about the newly-created scheduled task in the Task Information field. **4698 events are only created if Object Access --> Other Object Access Events is enabled. Without that audit item, you are left with less informative Event ID 106 (Task registered) in the Microsoft-Windows-TaskScheduler/Operational log** |
| 4702 | SECURITY | Security-Auditing | Other Object Access Events | A scheduled task was updated. 4702 events contain details about the newly-updated scheduled task in the Task Information field. **4702 events are only created if Object Access --> Other Object Access Events is enabled. Without that audit item, you are left with less informative Event ID 140 (Task registration updated) in the Microsoft-Windows-TaskScheduler/Operational log** |
| 4672 | SECURITY | Security-Auditing | Special Logon | Special privileges assigned to new logon. Indicates an administrator account logged on. Best practice is to restrict admin privileges to admin accounts that are separate from daily-use accounts; 4672 events where TargetDomainName and TargetAccountName are other than an expected administrator account can indicate inappropriate access. |
| 4624 | SECURITY | Security-Auditing | Logon | An account was successfully logged on. The Logon Type field indicates whether it was an interactive local logon (2), a remote network logon (3), or remote interactive (such as remote desktop or remoe assistance; type 10). Noisy, but very useful in specific scenarios. <br><br>In a domain environment, the local Administrator account should never be used except to initially build a system or to troubleshoot domain membership issues. For local accounts, TargetDomainName will be the machine account and will end in $. After tuning any expected use of local accounts, any 4624 event where TargetDomainName ends in $ is a high-confidence indicator. <br><br>The Workstation Name field may identify where the user was when they logged on; monitoring for a TargetAccountName (that is not a machine, service or sysadmin account) that logs onto many WorkstationNames in a short period of time may indicate an attempt at lateral movement. |
| 4624 | SECURITY | Security-Auditing | Logon | An account failed to logon. The Failure Reason and Status / Sub Status fields identify why the logon failed (bad username or password, locked out, account is not permitted to logon in the way requested) |
| 4648 | SECURITY | Security-Auditing | Logon | A logon was attempted using explicit credentials. This differs from 4624 in that a user is already logged on, and is launching a process (via RUNAS or a scheduled task) using different credentials. Not a high confidence indicator, but can indicate attempts to elevate privileges. | 
| 4627 | SECURITY | Security-Auditing | Group Membership | Group membership information. This event is generated along with every 4624 logon event, and documents the groups to which the logging on user is a member. This includes local and domain groups, including groups a user is a member of by virtue of nested membership. |
| 4688 | SECURITY | Security-Auditing | Process Creation | A new process has been created. This is a very noisy indicator. Interesting scenarios to look for include 4688 events where ParentProcessName is WinWord.EXE, Excel.EXE, PowerPnt.EXE, AcroRd32.exe, or other applications one would not expect to execute additional programs. See https://www.securityforrealpeople.com/2017/10/exploiting-office-native-functionality.html for an example of this technique detecting malice. **4688 events are only created if Detailed Tracking --> Process Creation auditing is enabled.**|
| 4778 | SECURITY | Security-Auditing | Other Logon/Logoff Events | A session was reconnected to a Window Station. In other words, a remote desktop session was connected. The Client Name field indicates the remote source. For systems not expected to be used as remote terminals, a remote session should be investigated. |
| 4689 | SECURITY | Security-Auditing | Process Termination | A process has exited. Generally noisy, but can be useful to monitor for specific processes that should not exit while the system is operating normally (such as endpoint protection processes). |
| 104 | SYSTEM | Eventlog | Log clear | A Windows event log was cleared. The log message will identify the specific log. |
| 300 | Microsoft Office Alerts | Microsoft Office {Version} Alerts | None | Generic message that captures any popup message Microsoft Office presents to a user. This is a noisy indicator but there are a few interesting scenarios to watch for.<br><br>A message with the text "Do you want to start the application" indicates an Office application attempting to execute some additional program - possibly an enterprise macro but possibly a macro-enabled malware downloader. |
