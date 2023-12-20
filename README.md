# pingpong-distributed
A distributed [DevOps Model RealTime](https://www.hcl-software.com/devops-model-realtime) application consisting of two executables that send ping and pong messages back and forth. The applications communicate with each other using the library [lib-tcp-server](https://github.com/hcl-pnp-rtist/lib-tcp-server).

## Build the application
Edit the TC `dist_app.tcjs` as necessary to build it with your C++ compiler. The default TC properties assume that Visual Studio is used. If you want to use the prebuilt version of the TargetRTS set the property `tc.targetServicesLibrary` to `${RSA_RT_HOME}/C++/TargetRTS`.

Edit the property `tc.pocoLoc` to specify the location of the POCO C++ libraries (needed by [lib-tcp-server](https://github.com/hcl-pnp-rtist/lib-tcp-server)). Adjust other properties as necessary (by default a debug version of the application will be built). 

## Usage
Start each application with command-line arguments specifying a local and remote port. For example:
```
executable.EXE -URTS_DEBUG=quit -port=15000 -remotePort=16000
```

In this case the started executable will listen for incoming messages on port 15000 and send outgoing messages to port 16000. The other application should then be started with the reversed arguments, i.e. with `-port=16000 -remotePort=15000`. It is assumed that both applications run on the same machine. If you want to run them on separate machines, edit `PingPongServer::init()` to add another command-line argument for specifying the remote hostname.

Initially both applications will wait for each other to send the first event, so nothing will happen. There are several ways to make the ping pong game start:
* pass the `-injectFirstPing` argument to one of applications. Then the first message will be injected programmatically from within the application. For example:
```
executable.EXE -URTS_DEBUG=quit -port=15000 -remotePort=16000
executable.EXE -URTS_DEBUG=quit -port=16000 -remotePort=15000 -injectFirstPing
```
* inject either the "ping" or "pong" event on the "PingPongServer::player" port. You can do this from another application by using the JSON API provided by [lib-tcp-server](https://github.com/hcl-pnp-rtist/lib-tcp-server).
* attach to one of the applications using the Model RealTime Model Debugger and send the "timeout" event to the "Player::timer" port.
