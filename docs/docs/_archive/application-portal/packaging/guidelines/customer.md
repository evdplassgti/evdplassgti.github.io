# Customer environment

When creating intakes and building packages and sequences the target environment must be known. This chapter describes the environment of SSW wherein the packages will be deployed.

## Package platform

For SSW packages are built for the Windows 10 Desktop (rich client) environment, single and multi-session Windows 10 virtual desktops in AVD and Multi Session Server 2019 machines in AVD. Preferably a “clean machine” will be used for intakes and package build. A clean state can be Windows + additional packages (“gold image”) as long as it’s a good representation of the target environment.  

When applications need prerequisites (middleware or other applications), these will be installed preferably using the package created for the prerequisite, otherwise a manual installation is done based on the intake. The intake document describes the target platform or platforms for the package.

The OS default installation is used with the following settings:

* UAC will be turned on (preferably turned off during package build)
* Firewall is turned on (preferably turned off during package build)
* Windows Defender is turned on
* DEP (Data Execution Prevention) is set to only scan essential Windows programs and services.

Users have full control permissions in their user profile.
Users will not have permissions to create files and folders on the system drive and users only have read and execute permissions on files that already exist on de system.

### Regional settings

The correct regional settings are sometimes crucial for application to work correctly. The default settings are:

* Language:   (Netherlands)
* Location is:  Netherlands, The
* Date and time notation: default settings for Dutch (Netherlands)

Note that these can deviate for your customer!

## Common Layer

The common layer is the set of applications and middleware that is installed on every platform by default. This common layer can be different between platforms, a description for the generic platform is given in the appendix “Common layer for SSW”. Packages will be tested against this common layer or Gold image to make sure no conflicts occur when the new package is deployed. Changes in the common layer will be communicated and will result in an update of the Appendix “Common layer for SSW”.

## File extensions

The following extensions are part of the default OS or applications in the common layer. These extensions may only be overwritten by the package when this is explicitly stated in the intake.

|Extension|Application|
|---------|-----------|
|pdf      |Edge       |
|ica  |Citrix client   |
|txt  |Microsoft Notepad|
|ini  |Microsoft Notepad|
|mdb  |Microsoft Office Access|
|ldb  |Microsoft Office Access|
|mde  |Microsoft Office Access|
|mdw  |Microsoft Office Access|
|mda  |Microsoft Office Access|
|xls  |Microsoft Office Excel|
|xlt  |Microsoft Office Excel|
|xlk  |Microsoft Office Excel|
|xla  |Microsoft Office Excel|
|xlw  |Microsoft Office Excel|
|wbk  |Microsoft Office Word|
|doc  |Microsoft Office Word|
|dot  |Microsoft Office Word|
|wiz  |Microsoft Office Word|
|ppt  |Microsoft Office Powerpoint|
|pps  |Microsoft Office Powerpoint|
|pot  |Microsoft Office Powerpoint|
|wsh  |Microsoft Windows Based Script Host|
|wsf  |Microsoft Windows Based Script Host|
|vbs  |Microsoft Windows Based Script Host|
|vbe  |Microsoft Windows Based Script Host|
|msi  |Microsoft Windows Installer|
|msp  |Microsoft Windows Installer|
|chm  |Microsoft Windows Operating System|
|hlp  |Microsoft Windows Operating System|
|exe  |Microsoft Windows Operating System|
|bat  |Microsoft Windows Operating System|
|com  |Microsoft Windows Operating System|
|cmd  |Microsoft Windows Operating System|
|sys  |Microsoft Windows Operating System|
|dll  |Microsoft Windows Operating System|
|ocx  |Microsoft Windows Operating System|
|xml  |Microsoft Windows Operating System|
|htm  |Microsoft Windows Operating System|
|html |Microsoft Windows Operating System|
|cda  |Microsoft Windows Operating System|
|url  |Microsoft Windows Operating System|
|lnk  |Microsoft Windows Operating System|
|386  |Microsoft Windows Operating System|
|cpl  |Microsoft Windows Operating System|
|dsn  |Microsoft Windows Operating System|
|drv  |Microsoft Windows Operating System|
|fon  |Microsoft Windows Operating System|
|fft  |Microsoft Windows Operating System|
|msc  |Microsoft Windows Operating System|
|dsn  |Microsoft Windows Operating System|
|reg  |Microsoft Windows Operating System|
|js   |Microsoft Windows Operating System|
|jse  |Microsoft Windows Operating System|
|cer  |Microsoft Windows Operating System|
|crt  |Microsoft Windows Operating System|

## Infrastructure for application deployment

The **SSW desktop workspace** is a cloud managed workspace, consisting of:

* Azure Active Directory joined Windows 10 laptops and desktops
* Management with Intune (Microsoft Endpoint Manager)
* Microsoft Office 365

The desktops and laptops will be enrolled using Autopilot Pre-provisioning (aka White Glove) or Self Deploying mode during which the base applications are installed. Additionally, user specific applications are installed when the user logs in to the device (when user targeted as ‘required application’ in Intune), can be installed from the Intune Company Portal (when user targeted as ‘available application’ in Intune) or will be deployed using the application portal. The following guideline is applicable:

* *Generic* applications are deployed using Intune, targeted to Autopilot device groups.
* Applications targeted to groups of devices, for example devices with a specific Group Tag or Device Model, are deployed using Intune. These groups preferably are based on AAD Dynamic Groups.
* Applications that are targeted to *All* users, will be deployed using Intune, targeting *All (Autopilot) devices*
* Applications that are targeted to specific users, will be deployed using the Application Portal. Installation starts when the user first clicks the application icon in the portal.
* User specific configuration for applications is configured in the Application Portal. Think of registry settings or configuration files in the user's profile.

### Guidelines for Intune and Autopilot

When using Autopilot and especially with Pre-provisioning (White Glove), mixed use of both win32 apps and LOB apps (MSI packages) must *not* be configured. Instead, all apps, including MSI packages, are wrapped to win32 apps. Therefore, best practice is to configure deployment scripts using the [PowerShell App Deployment Toolkit](https://psappdeploytoolkit.com/). This script is wrapped to an intunewin package for deployment using the Microsoft-Win32-Content-Prep-Tool. Be sure to always use the [latest version](https://github.com/Microsoft/Microsoft-Win32-Content-Prep-Tool). Usage of the PS App Deployment Toolkit provides a generic approach for application deployment and configuration in Intune. Exceptions, where win32 apps are not used are:

* The Office 365 core apps, these are configured in Intune using the 'Microsoft 365 Apps' type.
* Windows store apps.
* Modern apps, such as appX or MSIX, are installed using the 'Line-of-business app' type.

Note that Visio and Project are installed as add-on installation for Office 365, using the application portal (Install on first use). Also note, when using Autopilot any application offered from the Microsoft Store that is device targeted, must use the **Offline license**.

### Guidelines for the application portal

User specific applications are deployed upon first use from the application portal. This can be an application based on the MSIX format, MSI, unattended setup package or just a combination of copy/create actions of files and registry settings.

**Client/Server** applications preferably are deployed on a **Remote Workspace** solution, like AVD. For AVD, applications are deployed either during the build of the core image or during runtime.

### Applications in the Remote Workspace core image

Applications that will be included in the core image for an AVD host pool, will be deployed using unattended setup or MSI packages. The installation is included in the Azure Image builder script, that is used to create the image. The following apps will be included in the image:

* will be targeted to all users.
* dependencies, such as middleware applications.
* cannot be packaged to MSIX.
* are never updated, or are updated with a very low frequency.

Note that, depending on the actual AVD implementation, it is also possible to deploy applications using Intune. Consult the project lead if this will be or can be used.

### Applications for specific users in the Remote Workspace

User specific applications will be packaged to MSIX and deployed to users using MSIX App-Attach.

**The Paragraph 'Application types and order' in the 'Application Guidelines' describes when to use a specific package type.**

## User environment

A typical workspace platform for users is build up by 4 layers:

1. The hardware (desktop, laptop, or server), either physical or virtual. For SSW laptops are mostly used for rich clients, Windows 10 Virtual desktops are preferred in AVD.
2. The operating system. For SSW, this is Windows 10 x64 (rich client and AVD) or Windows Server 2019 x64 (AVD). This includes applications that are available from the OS, such as the Microsoft browser, File Explorer, Notepad, etc.
3. Common layer (part of the standard images and deployed by installing MSI packages or unattended installations) – installed using Intune or during creation of the image.
4. User / group specific applications - installed using the Application Portal on rich clients, using MSIX App Attach on AVD.

Other characteristics are:

* Workstations have a single system drive C:\, AVD Clients have a temporary D-drive which is standard in Azure hosted virtual machines. This D-drive may not be used by applications or packages.
* %ProgramFiles% is “C:\Program Files” and %ProgramFilesX86% “C:\Program Files (x86)” for 32 bits applications.
* The Desktop, Documents, Pictures, Screenshots, and Camera Roll folders are redirected to the OneDrive sync folder using the Known Folder Move configuration. The user’s OneDrive folder is added to the sync client automatically on Windows 10 operating systems.
* Microsoft Edge is set as the default browser.
* FSLogix is used as a profile management solution in AVD desktops.
* For Windows 10 rich clients, Enterprise State Roaming (ESR) is used to roam user settings using Azure Active Directory. Note that application settings for Win32 apps are not included in ESR. If this is a requirement, UE-V can be used as an alternative. The use of UE-V however is non-standard and requires Windows 10 Enterprise licenses.

## Policy settings

Policies, for SSW policies are set using Intune on rich client devices. The most important policy set is the Microsoft Security Baseline for Windows and for Edge. Active Directory GPO is used for AVD hosts but policies can also be set in Intune when the AVD hosts are enrolled in Microsoft Endpoint Manager.

There will no direct policies that restrict the behavior of modern applications. Exceptions can occur due to Windows Defender policies, such as:

* Detect and block potentially unwanted applications (PUA)
* SmartScreen settings
* Attack Surface Reduction Rules

The effect of these policies must be subject during the application intake by testing the application on a device with the configured policies activated.
