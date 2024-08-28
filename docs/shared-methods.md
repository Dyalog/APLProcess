Shared methods are functions that can be accessed from the `APLProcess` class itself as well as from an instance of `APLProcess`. Most of the shared methods are used for diagnostic and informational purposes. The two most-commonly-used methods, `New` and `Version`, are presented first. The remaining methods are primarily intended for diagnostic and debugging and are presented in alphbetical order.

### `New`
|--|--|
| Description | `New` creates and returns a new instance of `APLProcess`.|
| Syntax | `p←APLProcess.New args` where <ul><li>`args` are the settings for the `APLProcess` instance as described in [APLProcess Instance](./instance.md#settings)</li><li>`p` is the `APLProcess` instance</li></ul>|
| Examples | Using positional arguments:<br>`p←APLProcess.New '/myApp' 'DYALOG_NETCORE=1' 0 4502`<br><br>Using a namespace argument:<br>`ns←⎕NS ''`<br>`ns.(Ws Args RideInit)←'/myApp' 'DYALOG_NETCORE' 4502`<br>`p←APLProcess.New ns` |
| Notes | `APLProcess.New` is an alternative to using `⎕NEW`. The difference between using `⎕NEW` and `APLProcess.New` with arguments is that `⎕NEW` will automatically the child APL process whereas `APLProcess.New` will not.| 

### `Version`
|--|--|
| Description | `Version` returns version information for `APLProcess`. It returns a 3-element array of [1] `'APLProcess'` [2] The version number in the format major.minor.patch [3] The date of the version in the format YYYY-MM-DD|
| Syntax | `ver←APLProcess.Version` |
| Examples |&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;`APLProcess.Version`<br>&ensp;`APLProcess  2.3.0  2024-07-06`|

### `GetCurrentExecutable`
|--|--|
| Description | `GetCurrentExecutable` returns the full path to the current Dyalog APL executable.|
| Syntax | `ver←APLProcess.Version` |
| Examples |&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;`APLProcess.GetCurrentExecutable`<br>`C:\Program Files\Dyalog\Dyalog APL-64 19.0 Unicode\dyalog.exe`|

### `GetCurrentProcessId`
|--|--|
| Description | `GetCurrentProcessId` returns the process ID for the current process.|
| Syntax | `ver←APLProcess.GetCurrentProcessId` |
| Examples |&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;`APLProcess.GetCurrentProcessId`<br>`28288`|

### `IsAIX`
|--|--|
| Description | `IsAIX` returns `1` if the current platform is AIX, `0` otherwise. |
| Syntax | `ver←APLProcess.IsAIX` |

### `IsMac`
|--|--|
| Description | `IsMac` returns `1` if the current platform is macOS, `0` otherwise.|
| Syntax | `ver←APLProcess.IsMac` |

### `IsNetCore`
|--|--|
| Description | `IsNetCore` returns `1` if the current platform is using .NET, `0` otherwise. |
| Syntax | `ver←APLProcess.IsNetCore` |
| Notes | Given the schizophrenic naming of .NET by Microsoft, .NET Core is now known as just .NET, not to be confused .NET Framework, which sounds like it should also be .NET, but it's not. Got it?| 

### `IsRunning`
|--|--|
| Description | `IsRunning` returns `1` if the process identified by its arguments is currently running, `0` otherwise. |
| Syntax | `APLProcess.IsRunning pid [exe] [startTS]` where<ul><li>`pid` is the process ID (required)</li><li>`exe` is the executable name (optional, Windows only)</li><li>`startTS` is the process's start time in `⎕TS` format (optional, Windows only) </li></ul> |
| Examples | &ensp;&ensp;&ensp;&ensp;&ensp;&ensp;`APLProcess.IsRunning 28288 'dyalog' ⍝ Windows only`<br>`1`<br>&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;`APLProcess.(IsRunning GetCurrentProcessId) ⍝ should always return 1`<br>`1` |
| Notes | On non-Windows platforms, the `pid` is the only argument available. | 

### `IsWin`
|--|--|
| Description | `IsWin` returns `1` if the current platform is Windows, `0` otherwise. |
| Syntax | `ver←APLProcess.Version` |

### `KillChildren`
|--|--|
| Description | `KillChildren` will attempt to terminate all child processes running a specific executable process name. |
| Syntax | `r←APLProcess.KillChildren exe` where<ul><li>`exe` is the name of the process(es) to terminate.</li><li>`r` is a 2-column matrix containing information for processes that were not successfully terminated. The columns contain `[;1]` the process id `[;2]` the process name.</li></ul> |
| Examples | &ensp;&ensp;&ensp;&ensp;&ensp;&ensp;`APLProcess.KillChildren 'dyalog' ⍝ terminal all dyalog processes started by this process` |
| Notes | `KillChildren` is intended to be a utility to help clean up child processes that are still running after their `APLProcess` instance has been expunged as would be the case if [`Detach`](./instance.md#detach) is set to `1`. | 

### `ListProcesses`
|--|--|
| Description | `ListProcesses` returns process ids and names. |
| Syntax | `r←{all} APLProcess.ListProcesses procName` where <ul><li>`procName` is the name of the process(es) to return, or `''` for all processes</li><li>`all` is optional and set to `1` to return all matching processes, otherwise `ListProcesses` will return only child processes of the current process (the default).</li><li>`r` is a 2-column matrix of `[;1]` process ids, `[;2]` process names</li></ul> |
| Examples | &ensp;&ensp;&ensp;&ensp;&ensp;&ensp;`APLProcess.ListProcesses 'dyalog'`<br>`31568   dyalog`<br>&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;`≢1 APLProcess.ListProcesses '' ⍝ how many processes running?` <br>`350`|
| Notes | `ListProcesses` excludes the current process from its result. | 

### `MyDNSName`
|--|--|
| Description | `MyDNSName` returns the computer name when running on Windows or host name when running on non-Windows platforms. |
| Syntax | `r←APLProcess.MyDNSName` |
| Examples |&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;`APLProcess.MyDNSName`<br>`my-computer-name` | 

### `Platform`
|--|--|
| Description | `Platform` returns a 3-character vector indicating the current platform. It is literally `3↑⊃#.⎕WG 'APLVersion'`.  |
| Syntax | `r←APLProcess.Platform` |

### `ProcessUsingPort`
|--|--|
| Description | `ProcessUsingPort` returns the process id that is listening on the port number passed as the argument. |
| Syntax | `r←APLProcess.ProcessUsingPort port` where <ul><li>`port` is an integer port number</li><li>`r` is the process id, if any, listening on `port`</li></ul> |
| Examples | &ensp;&ensp;&ensp;&ensp;&ensp;&ensp;`APLProcess.ProcessUsingPort 8080`<br>`26702` |
| Notes | `ProcessUsingPort` can be useful for identifying a process that's already using a port you expected to be available. | 

### `Stop`
|--|--|
| Description | `Stop` attempts to terminate the process identified by the process id passed as the argument; returning `1` if the process was terminated, `0` otherwise.  |
| Syntax | `r←APLProcess.Stop pid` where<ul><li>`pid` is the process id of the process to be terminated</li><li>`r` is a Boolean where `1` indicates the process was terminated, `0` otherwise.</li></ul> |
| Examples | &ensp;&ensp;&ensp;&ensp;&ensp;&ensp;`APLProcess.Stop 12306`<br>`1`  |
| Notes | `Stop` will return `1` if there no process with an id matching `pid`. | 

### `UsingSystemDiagnostics`
|--|--|
| Description | `UsingSystemDiagnostics` returns the .NET namespace and dll that will be used under Windows. |
| Syntax | `ver←APLProcess.UsingSystemDiagnostics` |
| Examples | &ensp;&ensp;&ensp;&ensp;&ensp;&ensp;`APLProcess.UsingSystemDiagnostics ⍝ under .NET Framework`<br>`System,System.dll` <br>&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;`APLProcess.UsingSystemDiagnostics ⍝ under .NET`<br>`System,System.Diagnostics.Process`    |
| Notes | The result of `UsingSystemDiagnostics` is used to set `⎕USING` within `APLProcess` | 

### `_SH`
|--|--|
| Description | `_SH` is a cover for `⎕SH` and is used within `APLProcess` primarily for non-Windows commands. |
| Syntax | `r←APLProcess._SH cmd` where<ul><li>`cmd` is the character vector command to execute</li><li>`r` is the output from the command returned as a vector of character vectors</li></ul> |
| Examples | &ensp;&ensp;&ensp;&ensp;&ensp;&ensp;`APLProcess._SH 'whoami'`<br>` pi`|
| Notes | If a trappable error occurs when executing the command, `r` will be an empty vector.| 