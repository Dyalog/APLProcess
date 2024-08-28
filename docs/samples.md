## Remote Procedure Call (RPC)

The sample RPC application is located in the `/samples/rpc` folder. The basic flow for the RPC application is:

1. Load the `rpc` workspace.
1. Run `Start` to start a child process which also loads the `rpc` workspace and starts a Conga "command mode" server.
1. After a brief pause to allow the RPC server to come up, connect to the server.
1. Use `Do` to send statements to the RPC server to be executed.
1. `Do` returns a 2-element vector.
    * The first element is the `⎕DM` for the error, if any, caused by executing the statement. If no error occurred, `''` is returned.
    * The second element is the result of executing the statement, if no error has occurred in doing so, otherwise the offending statement is returned.
1. Use `Stop` to stop the RPC server and tidy up the client environment.

These steps are seen below (`]BOX on` is used to show the structure of results):
```
      )load /git/APLProcess/samples/rpc/rpc
/git/APLProcess/samples/rpc/rpc.dws saved ...
Sample Remote Procedure Call (RPC) application demonstrating the use of APLProcess
Use Start '' to start the application with default settings.

      Start ''
┌─┬────────────────────────────────────┐
│0│Connected to RPC Server on port 8888│
└─┴────────────────────────────────────┘
      Do '⍳3 3'
┌┬─────────────┐
││┌───┬───┬───┐│
│││1 1│1 2│1 3││
││├───┼───┼───┤│
│││2 1│2 2│2 3││
││├───┼───┼───┤│
│││3 1│3 2│3 3││
││└───┴───┴───┘│
└┴─────────────┘
      Do '÷0'
┌────────────┬──┐
│DOMAIN ERROR│÷0│
│÷0          │  │
│∧           │  │
└────────────┴──┘
      Stop
RPC Client closed and expunged
RPC Server process terminated
Conga shared library unloaded and Conga was erased
```
The relevant lines in `Start` which use `APLProcess` are shown below:
```
⍝ create and configure the instance of APLProcess                                                                               
 RPC_Process←APLProcess.New''                                                                                                   
 RPC_Process.Ws←⎕WSID                              ⍝ workspace to load                                                          
 RPC_Process.Args←'RPC_Port=',⍕port                ⍝ port to use                                                                
 RPC_Process.Args,←' RPC_Threaded=',⍕threaded      ⍝ run the server in a thread in the child process?                           
 RPC_Process.RunTime←runtime                       ⍝ use runtime interpreter?                                                   
 RPC_Process.RideInit←''(port+1)rideable⊃⍨0 1⍸rideable ⍝ RIDE port                                                              
 RPC_Process.Detach←~detach                        ⍝ don't kill child process when p goes away                                  
                                                                                                                                
⍝ try to launch the child process                                                                                               
 :If 0≠⊃(rc msg)←RPC_Process.Run                                                                                                
     msg←'Failed to start APL process due to: ',∊⍕msg                                                                           
     →0                                                                                                                         
 :EndIf                                                                                                                         
                                                                                                                                
 ⎕DL 2 ⍝ pause to let child process initialize                                                                                  
 :If ~RPC_Process.HasExited ⍝ check to make sure the child process is there                                                     
     :If 0=⊃rc←RPCClt.Clt'RPCClient' 'localhost'port'Command'                                                                   
         msg←'Connected to RPC Server on port ',⍕port                                                                           
         msg,←rideable/(⎕UCS 13),'RIDE available on port ',⍕1+port                                                              
         rc←0                                                                                                                   
     :Else                                                                                                                      
         msg←'Could not connect to RPC Server on port ',(⍕port),' due to ',∊⍕(4↑rc)[1 3 4]                                      
     :EndIf                                                                                                                     
 :EndIf                                                                                                                         
```
