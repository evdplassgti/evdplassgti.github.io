# Introduction

## Purpose of this section

This section describes the guidelines to be used for packaging in Sogeti Smart Workspace.
All delivered packages (MSI(X)-packages or unattended setups) have to comply with the guidelines described here.
These guidelines may only be dropped after mutual consent between the packager and the SSW tech lead.

## Target readership

The packaging guidelines are intended for everyone involved in the intake and packaging of software for the Windows platform for SSW, as well as people who are in charge for the distribution and deployment of packages.

## Terminology

Intake / Discovery = The creation of an intake document.

Intake document = Document describing all (front-end) aspects of an application.

Application Dossier = Intake document, including source media, test scripts, files and documents

TS Form = Package release document

MSI file = Microsoft Installer file, a package format to install and deploy applications. Introduced by Microsoft around the year 2000.

MST file = Transform file to modify aspects in (vendor-) MSI files

MSIX = Modern packaging format to transform and deliver (win32) apps to the modern application model. This includes deployment, installation, and runtime aspects, such as sandboxing and isolation of the application.

Where “Desktop (platform)” or “(local) Client” is used, the Operating System is Windows 10, x64. This includes Windows 10 single session and Windows 10 multi-session for Azure Virtual Desktop (AVD).

Where “Server (platform)” is used, the operating system is Windows Server 2019, used in Remote Desktop platforms such as Azure Virtual Desktop (AVD), Remote Desktop Services (RDS) or Citrix XenDesktop.

## Application and package identification

Applications are identified using the applications manufacturer, name, version, and language. In some cases, multiple configurations of the same applications must coexist in the application environment. To distinguish between those configurations, the Country name is added. The Country name is an optional field.

### Manufacturer

Name of the manufacturer/vendor/supplier of the software.
The maximum length for the manufacturer is 20 characters and must be shortened when applicable.
If the manufacturers name has more words, spaces are removed, and the words are combined using ‘UpperCamelCase’ or ‘Pascal Case’ notation.

Example:

* Winzip
* Adobe
* AutoDesk

Some vendors use different names in their products, for example:

* Microsoft
* Microsoft Corporation
* Mircosoft.corp
For these vendors a unique name is to be used, preferably the shortest. In this example, that would be ‘Microsoft’.

The use of a tool to create the package structure/naming convention as well as maintaining one dedicated storage location for the packages is well advised.

### Application Name

Name of the application. The maximum length of the application name is 30 characters and must be shortened when applicable. If the application name has more words, spaces are removed, and the words are combined using ‘UpperCamelCase’ or ‘Pascal Case’ notation.

Examples:

* Winzip
* Reader
* ProjectViewer
* LiveMeeting

### Version

Version of the application. The version at least will have two digits and a maximum of three. If an application has more digits (like 1.3.4.2) the digits from 4 onwards will be ignored (in this case, 1.3.4 is used). The only exception is when multiple versions of the applications can coexist and/or where the 4th number is relevant. Examples for this case are Java 1.5.0.7 and 1.5.0.8.
Marketing versions, such as AutoCAD 2008, should not be used. AutoCAD 2021 has an internal version of 24.0.47. If necessary, the internal version should be taken from the information of the executable file or the existing installation setup. If required, the correct version mostly can be found on the manufacturer’s website.

Examples:

* 10.0
* 8.1
* 1.2.3

### Language

Language of the application. A language code is used to represent the application’s language. This is a two-letter ISO-639-1 code, i.e:

* NL = Dutch
* EN = English
* DE = German
* FR = French
* ML = Multi-language

### USER and COMPANY

The USER and COMPANY properties in the packages and/or setup screens all have the value SSW.

## References and best practice

The technology used in MSI packaging can be seen as common practice and common knowledge, since this technology is used in the market for several decades.

For MSIX, the following references can be used to start with:

* Technical documentation on Microsoft Docs: [MSIX Overview](https://docs.microsoft.com/windows/msix/overview){target=_blank}. For introduction on the MSIX technology, jump to [MSIX Resources](https://docs.microsoft.com/windows/msix/resources){target=_blank}.
* Technical reference from Advanced Installer: [MSIX Fundamentals](https://www.advancedinstaller.com/hub/msix-packaging/introduction-modern-applications.html){target=_blank}
* E-Book for MSIX, downloadable from [here](https://www.syncfusion.com/succinctly-free-ebooks/msix-succinctly){target=_blank} (requires registration), or view online [here](https://www.syncfusion.com/succinctly-free-ebooks/msix-succinctly/introduction-to-msix){target=_blank}.
