# Citrix Virtual Channel SDK for Citrix Workspace app for Android

The Citrix Virtual Channel Software Development Kit (SDK) provides support for writing server-side applications and client-side drivers for additional virtual channels using the ICA protocol. The server-side virtual channel applications are on Citrix Virtual Apps and Desktops servers. This version of the SDK provides support for writing new virtual channels for Citrix Workspace app for Android. If you want to write virtual drivers for other client platforms, contact Citrix Technical support.

The Virtual Channel SDK provides:

-  The Citrix Android Virtual Driver AIDL Interfaces: `IVCService.aidl` and `IVCCallback.aidl`, used with the virtual channel functions in the Citrix Server API SDK (WFAPI SDK) to create new virtual channels.

-  A helper class `Marshall.java` designed to make writing your own virtual channels easier.

-  Working source code for three virtual channel sample programs that demonstrate programming techniques.

The Virtual Channel SDK requires the WFAPI SDK to write the server side of the virtual channel. You can find this resource by contacting Citrix Technical support.
