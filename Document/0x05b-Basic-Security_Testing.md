## Basic Security Testing on Android

### Setting Up Your Testing Environment

#### Hardware

##### Rooting your device
-- TODO : Which devices can be used : Nexus / Pixel --
-- TODO : Boot Process Description --
-- TODO : Boot Loaders and ROMs--
-- TODO : Explain the restrictions in case a non-rooted device is used for testing


#### Emulator

##### Rooting an Android Virtual Device (AVD)
https://forum.xda-developers.com/showthread.php?t=2227815


#### Software
-- TODO : Existing testing tools & tool suites : proxies, fuzzers, debuggers, vulnerability scanners, ... Most common tools : Binwalk, apktool, Dex2Jar, jad, Drozer, IDA --
-- TODO: Link to testing tools section


### Attack Methodology
-- TODO : Cf testing methodologies from CEH, ... : map attack surface (Local and Remote) through Passive and Active Reconnaissance, Scanning, Gaining Access, Maintaining Access, Covering Tracks. As this is generic and common to iOS, may be part of the parent chapter --

### Static Analysis
-- TODO : Description, when it comes compared to dynamic analysis and why, what it can bring --
Static analysis is the act of looking into App components, source code and other resources without actually executing it. This test is focused on finding misconfigured or unprotected Android IPC components as well as finding programming mistakes such as misuse of cryptography routines, find libraries with known vulnerabilities and even dynamic code loading routines.

When we are performing static analysis of applications, several tools can be used. The most commonly used are Static Analyzers. The most useful ones are Androbugs, Linkedin's qark, Android Lint and the paid HP's Fortify. However there are a plethora of static code analyzers that can be used.
Some Static Analyzers rely on the availability of the source code while others take the compiled apk as input. 
I is important to keep in mind that while static analyzers can help us to focus attention on potential problems, they may not be able to find all the problems by itself, go through each finding carefully and try to understand what the app is doing to improve your chances of finding vulnerabilities.

Static Analysis can be divided into two categories, White box and Black box. The first is when the source code is available and the other is when we only have the compiled application or library. We will now go into more details on each category.

#### With Source Code ("White box")
-- TODO : Description of the methodology, pros and cons (what can be done / not done, related tools, vulnerabilities that can be found) --
White box testing an app is the act of testing an app with the source code available. To accomplish the source code testing, you will want to have a setup similar to the developer. You will need a computer with the Android SDK and an IDE installed. It is also recommended to that you have access to either a physical device or an emulator installed so you can debug the app.
Once you have the setup ready and the source code indexed by and IDE (Android Studio is recommended since it is the current IDE of choice by Google) you can start debugging and searching for interesting parts of code.
Begin by testing each [Android Component](0x05a-Platform-Overview.md#app-components). Check whether they are exported and enforcing permissions. Android Lint<sup>[5]</sup> can help in the identification of such problems.
Any Android component manipulating sensitive data (contacts, location, images, etc...) should be investigated carefully. 
Proceed on to testing the libraries the application has embedded, some libraries contain known vulnerabilities and you should check for that. What libraries are the app using? And which version of the libraries are being used? Do they have any known vulnerability?
Since you have the source code in hand, you can check for crypto mistakes in the implementation. Look for hard coded keys and implementation errors related to cryptography functions. devknox<sup>[6]</sup> can help checking most common cryptographic mistakes since it is embedded to the IDE


#### Without Source Code ("Black box")
-- TODO : Description of the methodology, pros and cons (what can be done / not done, related tools, vulnerabilities that can be found) --
Black box testing, when compared to White box testing you will not have access to the source code in its original form. Usually you will have the application package in hand (in Android .apk format<sup>[7]</sup>) which can be installed on an Android device or reverse engineered with the goal to retrieve parts of the source code.
If the application is based solely on Java and does not have any native library (code written in C/C++) the reverse engineering process is relatively easy and recovers almost the entire source code. Applications that contain a native library can still be reverse engineered by require low level knowledge and the process is not automated.
More details and tools about the Android reverse engineering topic can be found at [Tampering and Reverse Engineering on Android](0x05b-Reverse-Engineering-and-Tampering.md) section.
Besides reverse engineering, there is a handful of automated tools that perform security analysis on the APK itself searching for vulnerabilities.
They are, for example, [QARK](https://github.com/linkedin/qark/), [Androbugs](https://github.com/AndroBugs/AndroBugs_Framework) and [JAADAS](https://github.com/flankerhqd/JAADAS).


### Dynamic Analysis

Compared to static analysis, dynamic analysis is applied while executing the mobile App. The test cases can range from investigating the file system and changes made to it on the mobile device or monitoring the communication with the endpoint while using the App.

When we are talking about dynamic analysis of applications that rely on the HTTP(S) protocol, several tools can be used to support the dynamic analysis. The most important tools are so called interception proxies, like OWASP ZAP, Burp Suite Professional or Fiddler to name the most famous ones. An interception proxy allows the tester to have a Man-in-the-middle position, in order to read and/or modify all requests made from the App and responses made from the endpoint.

#### Using a hardware device

Different preparation steps need to be applied before a dynamic analysis of a mobile App can be started. Ideally the device is rooted, as otherwise some test cases cannot be tested properly. See "Rooting your device" for more information.

The available setup options for the network need to be evaluated first. The mobile device used for testing and the machine running the interception proxy need to be placed within the same WiFi network. Either an (existing) access point is used or an ad-hoc wireless network is created<sup>[3]</sup>.

Once the network is configured and connectivity is established between the testing machine and the mobile device several other steps need to be done.

* The proxy in the network settings of the WiFi connection of the Android device need to configured properly to point to the interception proxy in use<sup>[1]</sup>.
* The CA certificate of the interception proxy need to be added to the trusted certificates in the certificate storage <sup>[2]</sup> of the Android device. Due to different versions of Android and modifications of Android OEMs to the settings menu, the location of the menu to store a CA might differ.

#### Using an emulator

All of the above steps to prepare a hardware testing device do also apply if an emulator is used<sup>[4]</sup>. For dynamic testing several tools or VMs are available that can be used to test an App within an emulator environment:

* AppUse
* Drozer
* MobSF

There are several downsides when using an emulator. You might not be able to test an App properly in an emulator, if it's relying on the usage of a specific mobile network, or uses NFC or GPS. Testing within an emulator is usually also slower in nature and might lead to issues on it's own.


### References


- [1] Configuring an Android Device to Work With Burp - https://support.portswigger.net/customer/portal/articles/1841101-Mobile%20Set-up_Android%20Device.html
- [2] Installing Burp's CA Certificate in an Android Device - https://support.portswigger.net/customer/portal/articles/1841102-installing-burp-s-ca-certificate-in-an-android-device
- [3] Creating an Ad-hoc Wireless Network in OS X - https://support.portswigger.net/customer/portal/articles/1841150-Mobile%20Set-up_Ad-hoc%20network_OSX.html
- [4] Android Application Security Testing Guide: Part 2 - http://resources.infosecinstitute.com/android-app-sec-test-guide-part-2/#gref
- [5] Android Lint - https://sites.google.com/a/android.com/tools/tips/lint/
- [6] devknox - https://devknox.io/
- [7] Android application package - https://en.wikipedia.org/wiki/Android_application_package
