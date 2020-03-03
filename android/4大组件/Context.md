Context | Application | Activity | Service
:---: | :---: | :---: | :---:
Show a Dialog | No | Yes | No
Start an Activity | No | Yes | No
Inflate Layout | No | Yes | No
Show a Toast | Yes | Yes | Yes
Start a Service | Yes | Yes | Yes
Send a Broadcast | Yes | Yes | Yes
Register BroadcastReceiver | Yes | Yes | Yes
Load Resource Values | Yes | Yes | Yes

> 跟UI相关的,都应该用Activity作为Context来处理

