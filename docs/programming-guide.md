# Programming Guide

This version of Virtual Channel SDK illustrates how to implement the client virtual driver for the Android client. You can find resources on the Citrix website or by contacting us.

The client virtual driver for the Android client must be implemented as an Android service. The APIs in `IVCService.aidl` are to be implemented by the virtual driver.

This SDK provides a helper class to make the implementation more convenient.

## Design Suggestions


Follow these suggestions to make your virtual channels easier to design
and enhance:

* When you design your own virtual channel protocol, allow for the flexibility to add features. Virtual channels have version numbers that are exchanged during initialization so that both the client and the server detect the maximum level of functionality that can be used. For example, if the client is at Version 3 and the server is at Version 5, the server does not send any packets with functionality beyond Version 3 because the client does not know how to interpret the newer packets.

* Because you can implement the server side of a virtual channel protocol as a separate process, it is easier to write code that interfaces with the Citrix-provided virtual channel support on the server than on the client (where the code must fit into an existing code structure). The server side of a virtual channel simply opensthe channel, reads from and writes to it, and closes it when done.

* Writing code for the server-side is similar to writing an application that uses services exported by the system. It is easier to write an application to handle the virtual channel communication because it can then be run once for each ICA connection supporting the virtual channel.

* If you are designing new hardware for use with new virtual channels (for example, an improved compressed video format), make sure the hardware can be detected so that the client can determine whether or not it is installed. Then the client can communicate to the server if the hardware is available before the server uses the new data format. Optionally, you could have the virtual driver translate the new data format for use with older hardware.

* There might be limitations preventing your new virtual channel from performing at an optimum level. If the client is might not be sufficient bandwidth to properly support audio or video data. You can make your protocol adaptive, so that as
    bandwidth decreases, performance degrades gracefully, possibly by
    sending sound normally but reducing the frame rate of the video to
    fit the available bandwidth.

* To identify where problems are occurring (connection,
    implementation, or protocol), first get the connection and
    communication working. Then, after the virtual channel is complete
    and debugged, do some time trials and record the results. These
    results establish a baseline for measuring further optimizations
    such as compression and other enhancements so that the channel
    requires less bandwidth.

* Citrix Workspace app for Android binds the virtual driver service using
    the Android API bindService with flag `BIND\_AUTO\_CREATE` set. That
    means that if the service has not been started before the ICA
    session launches,Citrix Workspace app for Android starts this
    service automatically. So the virtual driver you write must never
    block or perform time-consuming task in the `onCreate()` and `onBind()`
    methods. Citrix Workspace app for Android waits only two seconds
    for binding.

## Naming Virtual Channels

Virtual channels are referred to by a seven-character (or shorter) ASCII
name. In several previous versions of the ICA protocol, virtual channels
were numbered. The numbers are now assigned dynamically based on the
ASCII name, making implementation easier.

When developing virtual channel code for internal use only, you can use
any seven-character name that does not conflict with existing virtual
channels. Use only upper and lowercase ASCII letters and numbers. Follow
the existing naming convention when adding your own virtual channels.

The predefined channels, which begin with the OEM identifier CTX, are
for use only by Citrix.

## Declare Intent Filter in Manifest File


In the manifest file of your package, you must declare the following
intent action in the section for virtual driver service (Refer to the
sample code) so that Citrix Workspace app for Android can detect and bind
your virtual driver service.

```
<intent-filter>

	<action android:name="com.citrix.action.vcsdk.BIND">

</intent-filter>
```
## AIDL Interfaces Overview

When the virtual driver and Citrix Workspace app
for Android bind successfully, they communicate with each other through
the AIDL interfaces.

In this version of the Virtual Channel SDK, Citrix provides two AIDL
interfaces: `IVCService.aidl` and `IVCCallback.aidl`. You must implement the
APIs in `IVCService.aidl`. The APIs in `IVCCallback.aidl` have been
implemented by Citrix for your usage.

You must include the two AIDL interfaces in your custom virtual driver
package. And the contents of the two interfaces must not be modified.

### User-implemented Methods

All user-defined methods are in IVCService.aidl. You must implement:

| Method               | Description                                                                                                                                                                                                                                   |
|----------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `canLoad`             | Checks whether the driver is ready to load. It is the first remote call in Citrix Workspace app for Android.                                                                                                                                       |
| `getDisplayName`       | Returns the display name of the virtual channel. It is sent to the server and used to report errors from the virtual channel.                                                                                                                 |
| `getMinVersionNumber`  | Returns `minVersion`, the lowest supported version of this virtual channel. It is used to allow version negotiation between the client-side and the server-side components.                                                                     |
|  `getMaxVersionNumber` | Returns `maxVersion`, the highest supported version of this virtual channel. It is used to allow version negotiation between the client-side and the server-side components.                                                                    |
| `getVCName`            | Returns `streamName` used to identify the virtual channel to the server. For more information, see Naming Virtual Channels. Note that strings starting with, "CTX" are reserved for use only by Citrix.                                         |
| `initializeDriver`     | This method is for the service to do initialization work before the virtual channel starts. Returning Boolean values shows the status of initialization.                                                                                      |
| `getDriverInfo`        | When an ICA session starts, Citrix Workspace app for Android calls this method to retrieve module-specific information for transmission to the host. Information is returned to the server side of the virtual channel by `WFVirtualChannelQuery()`. |
| `driverStart`          | Called when Citrix Workspace app for Android starts the virtual driver. The instance of `IVCCallback` is provided to virtual driver by this API.                                                                                                     |
| `icaDataArrival`       | This method is called repeatedly when the virtual driver is running. The implementation must read command bytes from its vStream byte array and process them accordingly.                                                                     |
| `driverShutdown`       | Called when the virtual channel is shut down. Once the virtual channel closes, data cannot be sent to the server. This method can free all resources held by the virtual channel.                                                             |
### Callback Methods

The virtual driver uses the callback method to send data to the server and confirm a driver shutdown event. When driverStart starts the virtual driver, an instance of `IVCCallback` is passed to the virtual driver.

| Function        | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
|-----------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `sendData`        | Sends a package of virtual channel data to the server.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| `confirmShutdown` | To make sure that all data sent from the Client is done, and it is safe to terminate the channel. Called to confirm that the virtual channel can be shutdown. It must be called after `driverShutdown` is called. When `driverShutdown` is called, Citrix Workspace app for Android waits for confirmation. The virtual driver can call `confirmShutdown` to confirm that the virtual channel can be closed. And if this method is not called in time, the virtual channel will still be closed. Once the virtual channel closes, the data sent to the server will be ignored. |

## Helper Class

### Marshall.java

To make the implementation of the virtual driver more convenient, Citrix provides a help class `Marshall.java` in this version of SDK. Data coming from the server and sending to the server are all encapsulated as byte array. `Marshall.java` provides some helper methods to read data from byte array and write data to byte array or `ByteArraryOutputStream`. Citrix encourages you to include this class in your package and use the methods in `Marshall.java` to avoid making mistakes. For usage of these methods, refer to the three samples.
