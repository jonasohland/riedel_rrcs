# Riedel RRCS Nodejs Client & Server

Express Server for interacting with the Riedel RRCS XML-RPC application

## Installation

`npm install riedel_rrcs`

## Getting Started

The RRCS Nodejs Server provides both a client XLM-RPC interface for making requests to RRCS and a server interface for receiving notifications from RRCS.

```js

  import { RRCS_Server, RRCS_Client} from 'riedel_rrcs'

  //Make a request of RRCS for information
  let rrcs = new RRCS_Client('192.168.42.10', 8913) //IP Address and Port of RRCS
  rrcs.getAllPorts().then((ports) => {
    console.log('getAllPorts')
    console.log(ports)
  }).catch((err) => {
    console.error('getAllPorts Error')
    console.error(err)
  })


  //Setup a server to receive notifications from RRCS
  // local: {ip: '10.1.1.1', port: 3000}
  // remote: {ip: '192.168.42.10', port: 8193}
  // callback object
  let rrcs_server = new RRCS_Server(local, remote, {
    initial: function(msg) {
      console.log(msg)
    },
    error: function (msg, err) {
      console.error(msg)
      console.error(err)
    },
    registerForAllEvents: function (msg) {
      console.log(msg)
    },
    crosspointChange: function(params) {
      console.log('crosspointChange')
      console.log(params)
    },
    sendString: function(string) {
      console.log('sendString')
      console.log(string)
    }
  })

```

Complete API documentation here: http://jlommori.github.io/riedel_rrcs

### RRCS_Client

`new RRCS_Client(ip, port)` Ip Address and Port of RRCS.

Overview Available RRCS Client Commands
* getVersion() - Get RRCS Version info
* getState() - Get RRCS Operational State (working or standby)
* isConnectedToArtist() - Get RRCS Artist connectivity state
* getObjects() - Get all objects from Artist system
* getAllPorts() - Get All Ports from the Artist System, including detailed port data
* getObjectProperties(id) - Get properties for a specific object
* getAllActiveXps() - Get all currently active XPs
* getXpStatus(src, dest) - Get XP status between src {net: int, node: int, port: int} & dest {net: int, node: int, port: int}
* getXpVolume(src, dest) - Get XP volume between src {net: int, node: int, port: int} & dest {net: int, node: int, port: int}
* getInputGain(src) - Get input gain for a port: src {net: int, node: int, port: int}
* getOutputGain(src) - Get output gain for a port: src {net: int, node: int, port: int}
* getGPInputStatus(src) - Get gpio input status src {net: int, node: int, port: int, gpio: int}
* getGPOutputStatus(src) - Get gpio output status src {net: int, node: int, port: int, gpio: int}
* getLogicSources() - Get all system logic sources
* getAllIFBs() - Get all system IFBs
* getAllActivePortClones() - Get all active port clones
* getAllTrunkPorts() - Get all ports enabled or used for trunking
* getTrunklineSetup() - Get trunkline setup as it relates to this system
* getTrunklineActivity() - Get current trunkline activity


### RRCS_Server

`new RRCS_Server(local, remote, callbacks)` IP and Port for local server and RRCS
For each RRCS event, a callback needs to be created, as shown in the example above. The name of the key in the callback object needs to match the name of the RRCS event.
It is recommended to also implement a `callback = { error: function() {} }` to handle any errors thrown by the events.

Overview of available RRCS events:
* getAlive - Ping from RRCS to verify server is still active
* crosspointChange - XP Change in the system
* sendString - Send String command activated
* sendStringOff - Send String command de-activiated
* gpInputChange - GPIO Input changed
* logicSourceChange - Logic Source changed
* configurationChange - Alarm of a config change
* upstreamFailed - Alarm of Upstream Fiber Failure
* upstreamFaieldCleared - Alarm of Upstream Fiber Failure cleared
* downstreamFailed - Alarm of Downstream Fiber Failure
* downstreamFailedCleared - Alarm of Downstream Fiber Failure cleared
* nodeControllerFailed - Alarm of Node Controller Failure
* nodeControllerReboot - Alarm of Node Controller Reboot
* clientFailed - Alarm of Client Card Failure
* clientFailedCleared - Alarm of Client Card Failure cleared
* portInactive - Alarm of Port becoming Inactive
* portActive - Alarm of Port becoming Active
* connectArtistRestored - Alarm of Artist connectivity restored
* connectArtistFailed - Alarm of Artist connectivity failure
* gatewayShutdown - Alarm of RRCS Shutdown
* notFound - Catch all for an RRCS Event not otherwise listed

## License

This project is licensed under the MIT License
