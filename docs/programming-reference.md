# Programming Reference

For function summaries, see:

-   AIDL Interfaces Overview

## `canLoad`

Determines whether this driver can be loaded without error. It is the
first remote call in the Reicever.

### Calling Convention
```
boolean canLoad();
```
### Parameters

None

### Return Values

If virtual driver is ready to work, the returned value is true. If not, returns `false`.

### Remarks

When this method returns the value false, the virtual channel for this
virtual driver cannot be created. That means the virtual driver cannot
work in the ICA session.

## `getDisplayName`

Returns display name of virtual channel and it is sent to the server.

### Calling Convention
```
String getDisplayName();
```

### Parameters

None

### Return Values

Returns display name of virtual channel as String.

### Remarks

Display name is a friendly name of virtual channel, used to report
errors.

## `getMinVersionNumber`

Returns `minVersion`, the lowest supported version of this virtual channel, used to allow version
negotiation between the client-side and the server-side components.

### Calling Convention
```
int getMinVersionNumber();
```
### Parameters

None

### Return Value

The lowest supported version of this virtual channel.

### Remarks

Regarding the usage of virtual channel version, refers to Design Suggesstions.

## `getMaxVersionNumber`

Returns `maxVersion` the highest supported version of this virtual channel, used to allow version
negotiation between the client-side and the server-side components.

### Calling Convention
```
int getMaxVersionNumber();
```
### Parameters

None

### Return Value

The highest supported version of this virtual channel.

### Remarks

Regarding the usage of virtual channel version, refers to Design Suggesstions.

## getVCName

Returns the virtual channel name, send to identify
the virtual channel to the server.

### Calling Convention
```
String getVCName();
```
### Parameters

None

### Return Values

Returns the virtual channel name.

### Remarks

Each virtual channel has a unique name to identify itself. For more information, see Naming Virtual Channels.

## initializeDriver

This method is for service to do initialization work before the virtual channel starts.

### Calling Convention
```
boolean initializeDriver ();
```
### Parameters

None

### Return Values

Returns boolean value showing the status of initialization. If initialization is successful, this method returns the value true. If unsuccessful, this method returns the value false.

### Remarks

If this method returns false, a virtual channel for this method cannot be created.

## getDriverInfo

When an ICA session starts, it calls this method to retrieve module-specific information for transmission to the server. This information returns to the server side of the virtual channel by `WFVirtualChannelQuery()`.

### Calling Convention
```
byte[] getDriverInfo();
```
### Parameters

None

### Return Value

Returns `byte[]`, the output byte array to be sent to the server.

### Remarks

## driverStart

Called when the virtual driver is started by Citrix Receiver for Android.

### Calling Convention
```
void driverStart(in IVCCallback cb);
```
### Parameters

**cb**

The callback provided to the virtual driver service.

### Return Value

None

### Remarks

The instance of `IVCCallback` is passed to the virtual driver in this method. Citrix recommends that you must not call `sendData` in `driverStart`. The callbacks must be used after `driverStart` returns.

## icaDataArrival 

This method is called repeatedly when the virtual driver is running. The
implementation must read command bytes from its vStream byte array and
process them accordingly.

### Calling Convention 
```
void icaDataArrival(in byte[] vStream);
```
### Parameters

**vStream**

The byte array used to read data from the server component of the virtual channel.

### Return Value

None

### Remarks

Because Citrix Receiver for Android does not know any package detail, it only transfers all available data from ICA stream to the virtual driver by calling this method. If the virtual channel package is too large, the whole package may not arrive at the same time. That means the implementation of this method must have the ability to handle partial package. See Ping, Over
and Mix sample codes, which provide examples about how to handle partial package.

If the server-application sends several packages continuously, more than one package may come in the same byte array. Thus, the implementation of this method must have the ability to handle more than one package in the array. See Over and Mix sample codes, which provide the example about how to handle more than one packages in one byte array. For Ping sample, server application sends data synchronously. The server waits for the client to reply before sending the next packet, so Ping sample doesn’t need to handle this scenario.

## driverShutdown

Called when virtual channel is shut down by Citrix Receiver for Android.

### Calling Convention
```
void driverShutdown();
```
### Parameters

None

### Return Value

None

### Remarks

This method is to inform the virtual driver that the virtual channel
closes. Once virtual channel closes, data cannot be sent to server. And
Citrix Receiver for Android waits for confirmation after calling this
method. If virtual driver doesn’t call `confirmShutdown()` to confirm in
time, virtual channel keeps close.

## sendData 

To send a package of channel protocol to the server.

### Calling Convention

```
void sendData(in byte[] data, in int off, in int length);```

### Parameters

**data**

The data as byte array is to be sent to the server side.

**off**

The start offset in the data array.

**length**

Length the number of bytes to send.

### Return Value

None.

### Remarks

This method must be used after driverStart returns and before `driverShutdown` is called.

## confirmShutdown

Called to confirm that the virtual channel can be shut down. It must be
called after `driverShutdown()` is called. Once confirmed, data sent to
the server component are ignored.

### Calling Convention
```
void confirmShutdown();
```
### Parameters

None

### Return Value

None

### Remarks

To make sure that all data sent from the Client is done, and it is safe
to terminate the channel. This method must be called after
driverShutdown is called. When `driverShutdown` is called, Citrix Receiver
for Android waits for confirmation. Virtual driver can call
confirmShutdown to confirm that the virtual channel can be closed. And
if this method is not called in time, the virtual channel is still
closed.

## writeInt2

Writes an int to a buffer in the form of two bytes, little endian order.

### Calling Convention

```
int writeInt2(byte[] buffer, int offset, int i)
```

### Parameters

**buffer**

The buffer to write the integer to.

**offset**

Offset from the start of buffer, where this method starts writing the
int.

**i**

The int to write.

### Return Value

Returns offset + 2.

### Remarks

## writeInt4


Writes an int to a buffer in the form of four bytes, little endian
order.

### Calling Convention

```
int writeInt4(byte[] buffer, int offset, int i)
```

### Parameters

**buffer**

The buffer to write the integer to.

**offset**

Offset from the start of buffer, where this method will start writing
the int.

**i**

The int to write.

### Return Value

Returns offset + 4.

### Remarks

## writeInt2


Writes an int to an output stream in the form of two bytes, little
endian order.

### Calling Convention

```
void writeInt2(ByteArrayOutputStream stream, int i)```

### Parameters

**stream**

The stream to write the integer to.

**i**

The int to write.

### Return Value

### Remarks

## writeInt4

Writes an int to an output stream in the form of four bytes, little endian order.

### Calling Convention

```
void writeInt4(ByteArrayOutputStream stream, int i)```

### Parameters

**stream**

The stream to write the integer to.

**i**

The int to write.

### Return Value

### Remarks

## readInt2

Reads two bytes from a byte array in little endian order and creates a 2
byte short.

### Calling Convention

```
short readInt2(byte[] buffer, int index)```

### Parameters

**buffer**

The buffer to read the short integer from.

**offset**

Offset from the start of buffer, where this method starts reading the
short integer.

### Return Value

Returns the short integer.

### Remarks

## readInt4

Reads four bytes from a byte array in little endian order and creates a
4 byte integer.

### Calling Convention

```
int readInt4(byte[] buffer, int index)```

### Parameters

**buffer**

The buffer to read the integer from.

**offset**

Offset from the start of buffer, where this method starts reading the
integer.

### Return Value

Returns the integer.

### Remarks

## readUInt2


Reads two bytes from a byte array in little endian order and creates an
unsigned value stored in an integer.

### Calling Convention

```
int readUInt2(byte[] buffer, int index)```

### Parameters

**buffer**

The buffer to read the unsigned integer from.

**offset**

Offset from the start of buffer, where this method starts reading the
unsigned integer.

### Return Value

Returns the unsigned integer.

### Remarks

## readUInt1

Reads one byte from a byte array in little endian order and creates an
unsigned value stored in an integer.

### Calling Convention

```
int readUInt1(byte[] buffer, int index)```

### Parameters

**buffer**

The buffer to read the unsigned integer from.

**offset**

Offset from the start of buffer, where this method starts reading the
unsigned integer.

### Return Value

Returns the unsigned integer.

### Remarks
