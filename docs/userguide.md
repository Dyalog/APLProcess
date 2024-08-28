`APLProcess` starts a new Dyalog APL interpreter in another process. What that process does is entirely up to you. Common uses include:

* setting up a Remote Procedure Call (RPC) service to distribute processing load between multiple APL processes.
* delegating a long-running task to another APL process
* delegating processing to another APL process to reduce the potential impact of code failure. If the code running in another process fails, it's less likely to impact the processing in the current APL session.

!!! Note
    Currently `APLProcess` can start new APL processes only on the same platform. As most processors have multiple cores, this can still help distribute processing load and increase throughput. The ability to start new APL processes on other platforms using ssh is under development.

### Terminology
Even though `APLProcess` is implemented as a Dyalog APL class, you do not need to know much about the object-oriented programming (OOP) features of Dyalog APL.

Instance
: An "instance" of `APLProcess` is created when you call `APLProcess.New` or the use the `⎕NEW` system function.  Instances are independent of one another.

Method
: In OOP terminology, APL functions within classes are called "methods". An individual method can either apply only to an instance (this is called an "instance method"), or to the class itself (this is called a "shared method").

Field or Setting
: In OOP terminology, variables are called "fields", but for this documentation, we'll refer to them as "settings".

Throughout this document we will use the following terms with the following meanings:

Parent Process
: This is the process from where `APLProcess` is used to start "child" processes.

Child Process
: This is an APL process started by a "parent" process. Each child process has a reference back to the "parent" process in the form of a "parent process ID" (PPID).

`p`
: An instance of `APLProcess` which is the result of running `APLProcess.New` or using `⎕NEW`. Of course, you can use whatever name you prefer in your code. 

### Creating and starting APLProcess
There are three ways to create and run an instance of `APLProcess`.

#### Use `⎕NEW` with constructor arguments
This will start an APL process and return an `APLProcess` instance.
```
      p←⎕NEW APLProcess ('/myApp' '' 0 'SERVE:*:4502')
```

#### Use `⎕NEW` without constructor arguments
This will create and return an instance of `APLProcess`. You can then set whatever parameters you need and start the APL process using the `Run` method.
```   
      p←⎕NEW APLProcess
      p.(Ws RideInit)←'/myApp' 'SERVE:*:4502'
      p.Run
```
#### Use `APLProcess.New`
`APLProcess.New` is a shared method that will create and return an instance of `APLProcess`, applying any arguments as parameters. You can then start the APL process using the `Run` method.
Supplying `''` as the argument to `APLProcess.New` is equivalent of using `⎕NEW APLProcess` without any arguments.
```
      p←APLProcess.New '/myApp' '' 0 'SERVE:*:4502'
      p.Run
```

### Stopping an APL Process

Use `p.Kill` to terminate the child APL process. `p.kill` will return `1` if the child process was successfully terminated, `0` otherwise.

### Checking if an APL Process is Running

Use `p.HasExited` to check if a child process is running. `p.HasExited` will return `1` is the child process is not running and `0` if the child process is running. 

### Cross-Platform Functionality
To the extent possible, `APLProcess` presents a consistent API across all platforms on which Dyalog APL runs. This way you shouldn't have to change your application code that uses `APLProcess` if you're using multiple platforms.

Once the child process has been started, the `APLProcess` instance contains an object named `Proc`. Under Windows, `Proc` is an instance of the .NET System.Diagnostics.Process class. On non-Windows platforms, `Proc` is a namespace with elements named the same as a selected subset of the features of the .NET class.