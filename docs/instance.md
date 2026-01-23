You create an instance of `APLProcess` using either `⎕NEW` or [`APLProcess.New`](shared-methods.md#new) as described in the [Creating and Starting APLProcess](./userguide.md#creating-and-starting-aplprocess). 

## `APLProcess` and Dyalog APL Configuration Parameters
Dyalog APL is customized using a set of configuration parameters. These may be defined in a number of ways, which take precedence as follows:

* Command-line settings
* Application configuration file settings
* Environment variable settings
* User configuration file settings
* Settings in the registry section defined by the IniFile parameter (Windows only)
* Built-in defaults

A child process will inherit the environment variable settings from the parent process. To demonstrate this in a somewhat contrived manner, suppose you started Dyalog APL from a Linux/MacOS command line as follows:

```APL
MAXWS=2GB dyalog MAXWS=1GB
```
The `MAXWS` to the left of dyalog is an environment variable and the `MAXWS` to the right of the dyalog is a command-line setting. The `MAXWS` in the started process will be `1GB` as command-line settings take precedence over environment variables.

However, if you use `APLProcess` to start a child process, it will inherit the `2GB` environment variable setting.

In the parent process:
```APL
      ]config MAXWS -origin
┌─────┬────────────┬───┐
│MAXWS│Command line│1GB│
└─────┴────────────┴───┘
      ]load APLProcess
      p←APLProcess.New ''
      p.Run
```
In the child process:
```APL
      ]config MAXWS -origin
┌─────┬────────────────────┬───┐
│MAXWS│Environment variable│2GB│
└─────┴────────────────────┴───┘
```
### Special Treatment of `LOAD` and `LX` Environment Variables.
In most cases, there is no issue with inheriting the parent's environment 
variables. However, the `LOAD` and `LX` configuration settings present a potential problem in that they could start a recursive chain of events that would result in a universe-eating black hole. As such, if `APLProcess` detects `LOAD` and/or `LX` as environment variables in the parent process, it will remove them from the environment of the child process(es). You can specify `LOAD` and `LX` for the child process(es) using the [`Load`](#load) and [`Lx`](#lx) settings respectively or as a part of the [`Args`](#args) setting.

## Settings
`APLProcess`'s settings can be provided as arguments to  `APLProcess.New` or `⎕NEW`. You can also specify settings in the `APLProcess` instance before launching the new process. When provided as arguments, the settings can be specified in two ways.

1. A vector of positional settings in the following order:<br>`Ws Args RunTime RideInit OutFile WorkingDir Detach`<br>If you want to supply settings with intermediate unspecified setting, you will need to supply the default value for the intermediate settings. For example, to specify the `Ws` and `RideInit` settings, you would need to specify `/myApp '' 0 'SERVE:*:4502`, using the default values for `Args` and `RunTime`.  
1. A namespace containing named variables for the settings you wish to set. 
```
      ns←⎕NS ''
      ns.(Ws RideInit)←'/myApp' 'SERVE:*:4502'
      p←APLProcess.New ns
```
!!! Note
    Remember that using `⎕NEW` with arguments, in either positional or namespace form, will start the APL process automatically. 

### `Ws`
|--|--|
| Description | `Ws` is the name of the workspace that the child process will load  |
| Default | `''`|
| Examples | `p.Ws←⎕WSID  ⍝ have the child process load the current workspace` |
| Notes | An alternative to specifying `Ws` could be to use the `Load` and `LX` command line parameters. If you specify neither `Ws` nor the `Load`/`LX` command line parameters, the child process will sit there, languishing and doing nothing.|

### `Args`
|--|--|
| Description | `Args` is a character vector which represents the Dyalog command line parameters, if any, to be passed to the interpreter in the child process. |
| Default | `''`|
| Examples | `p.Args←'Load=/myApp LX=Start' ⍝ load code from the /myApp folder and run the Start function` |

### `RunTime`
|--|--|
| Description | `RunTime` allows you to control the Dyalog interpreter that the child process will run. `RunTime` can have the following values:<br><ul><li>`0` indicating to use the same interpreter as the parent process.</li><li>`1` indicating to use the runtime version of the parent process's interpreter.</li><li>a character vector with the fully-qualified filename of the interpreter to run in the child process.</li></ul>|
| Default | `0` meaning do not use a runtime interpreter. |
| Examples | `p.RunTime←1 ⍝ use a runtime interpreter`<br>`p.RunTime←'/opt/mdyalog/19.0/64/unicode/dyalog' ⍝ use a specific interpreter` |
| Notes | `RunTime` is a somewhat overloaded setting that has evolved over time to take on additional meanings other than just whether to use a runtime interpreter. There is a pending capability to use `RunTime` to specify parameters in order to start an APL process on another maching using ssh, but that functionality is currently under development. |

### `RideInit`
|--|--|
| Description | `RideInit` specifies the `RIDE_INIT` command line parameter to use if you want to be able to RIDE into the child process's APL session. `RideInit` can be either:<ul><li>a character vector containing the setting for `RIDE_INIT` parameter.  For example: `SERVE:*:4504`</ul><li>the integer port number for RIDE to listen on in SERVE mode. `4504` would be the equivalent of specifying `'SERVE:*:4504'`|
| Default | `''` which means RIDE access is not enabled for the child process |
| Examples | `p.RideInit←4504 ⍝ run with SERVE:*:4504`<br>`p.RideInit←'HTTP:*:4504 ⍝ run with zero-footprint RIDE on port 4504` |
| Notes | `APLProcess` will always set the `RIDE_INIT` command line parameter in order to not inherit the setting from the parent APL process. |

### `Load`
|--|--|
| Description | `Load` specifies the `LOAD` parameter for the child process.  `Load` specifies the workspace, folder, or file to be loaded and run by the child process. `Load`, if specified, overrides the workspace specified by `Ws`. |
| Default | `''` |
| Examples | `p.Load←'/home/user/myapp'` |
| Notes | See [Special Treatment of LOAD and LX Environment Variables](#special-treatment-of-load-and-lx-environment-variables). |

### `Lx`
|--|--|
| Description | `Lx` specifies the `LX` parameter for the child process.  `Lx`, if specified, overrides `⎕LX` in the workspace, if a workspace is either specified by `Ws` or `Load`. |
| Default | `''` which means that the child process will inherit the `LX` parameter, if any, set for the parent process. |
| Examples | `p.Lx←'Start'` |
| Notes | See [Special Treatment of LOAD and LX Environment Variables](#special-treatment-of-load-and-lx-environment-variables).|


### `OutFile`
|--|--|
| Description | `OutFile` specifies the name of a file to which the session output of the child APL process will be written. |
| Default | `''` meaning session output will not be written to file. |
| Examples | `p.OutFile←'/home/me/Desktop/output.txt'` |

### `WorkingDir`
|--|--|
| Description | `WorkingDir` specifies the working directory for the child APL process  |
| Default | `''` meaning use the default working directory for the child process's APL interpreter |
| Examples | `p.WorkingDir←'/home/me/Desktop'` |

### `Detach`
|--|--|
| Description |`Detach` is a Boolean which indicates whether child process(es) should be terminated when the parent `APLProcess` instance is expunged.<br>`0` indicates that the child process(es) should be terminated.<br>`1` indicates that the child process(es) should not be terminated.|
| Default |`0`|
| Examples | `p.Detach←1 ⍝ do not close the child APL process when the parent's APLProcess instance is expunged.` |
| Notes | If `Detach` is set to `0`, the child APL process will be terminated if the parent APL process exits; when set to `1` the child process will continue to run even if the parent APL process exits. | 

### `Id`
|--|--|
| Description | `Id` is a read-only setting that returns the process ID of the child APL process. If the child APL process has not been started, `Id` is set to `''`.|
| Default | `''` |
| Examples | &ensp;&ensp;&ensp;&ensp;&ensp;&ensp;`(p←APLProcess.New '/myApp' '' 0 4505).Run`<br>`0`<br>&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;`p.Id`<br>`18212`|

### `Proc`
|--|--|
| Description | `Proc` is not really a setting but rather an attempt to present consistent cross-platform interface. `Proc` is created when the child APL process is started.<br><br>On Windows `Proc` is an instance of the .NET System.Diagnostic.Process class which has many more properties and methods than we expose in `APLProcess`.<br><br>On non-Windows platforms, `Proc` is a namespace which emulates a subset of the System.Diagnostic.Process class which we deem necessary to start and manage the child APL process. |

## Methods
### `Run`
|--|--|
| Description | `Run` starts the child APL process.|
| Syntax | `(rc msg)←p.Run` where `rc` and `msg` are `0` and `''` respectively if no error occurred when starting the child APL process; otherwise `rc` and `msg` are the APL error number and message.|
| Examples | &ensp;&ensp;&ensp;&ensp;&ensp;&ensp;`(ns←⎕NS '').(Ws RideInit)←'/myApp' 4505`<br>&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;`p←APLProcess.New ns ⍝ load /myApp and RIDE on port 4505`<br>&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;`p.Run`<br>`1`|

### `Kill`
|--|--|
| Description | `Kill` will attempt to terminate the child APL process.|
| Syntax | `r←p.Kill` where `r` is `1` if the child APL process was terminated, `0` if the child APLProcess is still running|
| Examples | &ensp;&ensp;&ensp;&ensp;&ensp;&ensp;`p←⎕NEW APLProcess ('myApp' '' 0 4505)`<br> &ensp;&ensp;&ensp;&ensp;&ensp;&ensp;`p.Kill`<br>`1`|

### `HasExited`
|--|--|
| Description | `HasExited` reports whether the child APL process is running |
| Syntax | `r←p.HasExited` where `r` is a Boolean where `1` indicates the child process is not running, `0` indicates the child process is running.|
| Examples | &ensp;&ensp;&ensp;&ensp;&ensp;&ensp;`p←APLProcess.New '/myApp' '' 0 4505`<br>&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;`r.Run`<br>`1`<br>&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;`p.HasExited`<br>`0`<br>&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;`p.Kill`<br>`1`<br>&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;`p.HasExited`<br>`1`|