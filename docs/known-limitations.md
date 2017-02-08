# Known Limitations

There are two known limitations for some Android devices of specific manufacturers.

1.  For some Huawei devices, secondary launch is disabled by default. To
    use this version of the Virtual Channel SDK, you must change the
    setting on your device. 
    
    For example, for Huawei P9, follow the steps below:
    
    In Phone Manager, choose **App auto-launch**, then choose **Secondary launch
management**, and enable the virtual driver services. 
![](media/image6.png)

1.  For some Samsung devices with Android 6.0.1, if the App containing
    the custom virtual driver service is not in foreground for long
    time, the virtual driver service cannot be started by Citrix
    Receiver for Android. The requirement for using Samsung devices with
    Android 6.0.1 is that you must launch virtual driver App manually
    every time before you launch an ICA session. Note that the
    requirement here is not to start the virtual driver
    service manually. It is to start the App that contains the service
    by clicking the icon of this App.
