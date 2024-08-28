`APLProcess` is a cross-platform utility that enables you to easily start additional processes running Dyalog APL. This can be useful in a number of scenarios including:

* to distribute the processing load across multiple processors.  This is what the isolate workspace does.
* to delegate processing to another process so that if there is some sort of failure, the primary process is not impacted.

`APLProcess` runs on Windows, Linux and MacOS. ssh support, which will enable you to start an APL process on another platform using the ssh protocol, is forthcoming.

## Terminology
Parent Process
: This is the process from where `APLProcess` is used to start "child" processes.

Child Process
: This is a process started by a "parent" process. Each child process has a reference back to the "parent" process in the form of a "parent process ID" (PPID).

!!! note
    While `APLProcess` itself is `⎕IO` and `⎕ML` insensitive, the examples in this documentation assume an environment of `(⎕IO ⎕ML)←1`.

## Obtaining `APLProcess`
 `APLProcess` is included with your Dyalog APL installation. To bring it into your workspace:

### Dyalog APL Version 19.0 and later (using `Link`)
```APL
      ]import [ns] APLProcess
```
or, under program control, do:
```APL
      ⎕SE.Link.Import [ns] 'APLProcess' 
```
where `ns` is an optional namespace name or reference in which to load `APLProcess`. If `ns` is not specified, `APLProcess` will be loaded into the current namespace.

### Dyalog APL versions before 19.0 (using `SALT`)  
```APL
      ]load APLProcess [-target=ns]
```
or, under program control, do:
```APL
      ⎕SE.SALT.Load 'APLProcess [-target=ns]' 
```
`-target=ns` optionally specifies the namespace in which to load `APLProcess` where `ns` is the namespace name. If not specified, `APLProcess` will be loaded into the current namespace. 

## Upgrading to the Latest `APLProcess`
The documentation describes the latest released version of `APLProcess` which may be more recent than the version that came pre-installed with your Dyalog APL. Use `APLProcess.Version` to check the `APLProcess` version number. 

The latest released version of `APLProcess` can be downloaded from the Assets section of [the latest release page](https://github.com/dyalog/aplprocess/releases/latest) on GitHub.

## `APLProcess` is Available as a Tatin Package 
[Tatin](https://tatin.dev) is a package manager for APL-based packages. `APLProcess` is available as a Tatin package. If you have the Tatin client installed, you can load `APLProcess` using:
```APL
      ]TATIN.LoadPackages APLProcess
```
The Tatin client will be included in your Dyalog installation beginning with Dyalog version 19.0. For earlier versions of Dyalog, refer to the [Tatin website](https://tatin.dev) for instructions on installing the Tatin client.