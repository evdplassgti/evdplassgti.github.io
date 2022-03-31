# MSI Standards and guidelines

In the first part of this chapter the parameters that are used in the construction of MSI packages are declared. Then the versioning rules are explained, naming convention and some examples for MSI packages are given. The final part of the chapter describes the differences between new builds and upgrades concerning MSI packages.

## Deployment of MSI packages: per-Machine

MSI packages will have the following characteristics:

* MSI packages will be deployed to desktop clients using Microsoft Intune or the application portal, the installation will run in system context.
* MSI packages are deployed per-machine. To install the application at machine level the installation will take place with administrative/system credentials and by setting the ALLUSERS property to 1.
* Reboots are suppressed so users are not troubled with unwanted and/or unexpected reboots.
* MSI packages can be installed using the single MSI file or using an administrative installation point. An administrative installation is manually created by using the following command: `msiexec /a "msi-file"`. Every non-vendor MSI package supports creation of an administrative installation point.
* The MSI package will be installed with the following installation command:  
    `msiexec /i "msi-file" /qb!-`  
    Or, in case of an MSI package with a transform:  
    `msiexec /i "msi-file" TRANSFORMS="mst-file" /qb!-`

* Applications that are delivered by the vendor as an MSI based installation, follow the installation and deployment instructions from the vendor. If no instructions are given, the default properties described below will be added using a transform file.

## MSI Package Properties for deployment per-machine

To create a correct machine-based installation the following properties are set:

**ROOTDRIVE**  
If more partitions/hard disks are available on the target system, this property prevents that an installation will be done on a drive other than C:\. This is important for applications that do not use the %programFiles% folder.
Just like other public properties, The ROOTDRIVE property can be overruled in the installation command line, or in a MST: **'ROOTDRIVE = C:\'**  

**ALLUSERS**  
The ALLUSERS property is set to 1: **'ALLUSERS = 1'**

**REBOOT**  
The REBOOT property is set to suppress reboots: **'REBOOT = ReallySuppress'**

**REBOOTPROMPT**  
This property is set **'REBOOTPROMPT = S'**

For vendor MSIs these properties are set using the MST file. If they already exist in the property table, they are altered to the values above, otherwise these properties are added to the table.

## Security

All applications that are installed in the system can be used by all users. No access restrictions are set to directories. When needed for the correctly functioning of an application extra permission to the local USERS group are granted.

The permissions are set using ACL commands. For setting ACL’s a tool is used which supports inheritance and all other options in the field of NTFS permissions. Sogeti uses the latest version of Setacl command line [see here](https://helgeklein.com/setacl/documentation/command-line-version-setacl-exe/){target=_blank}.

Example: a few lines out of a permission command file, using setacl, when installing an application:

**Using a global group:**

```text
Set Domain=<DomainName>
Set Globalgroup=<GroupName>
SetACL.exe -on "%ProgramFiles%\Application"   -ot file -actn ace -ace "n:%Domain%\%Globalgroup%;p:change;s:n;m:grant;w:dacl"
SetACL.exe -on "%Systemroot%\Application.ini" -ot file -actn ace -ace "n:%Domain%\%Globalgroup%;p:write;s:n;m:grant;w:dacl"
SetACL.exe -on "HKLM\software\borland"        -ot reg  -actn ace -ace "n:%Domain%\%Globalgroup%;p:read,create_link,create_subkey,set_val,notify,delete;s:n;m:grant;w:dacl"
```

**Using a the local users group:**

```text
Set SidUsers=S-1-5-32-545
SetACL.exe -on "%ProgramFiles%\Application"   -ot file -actn ace -ace "n:%SidUsers%;p:change;s:y;m:grant;w:dacl"
SetACL.exe -on "%Systemroot%\Application.ini" -ot file -actn ace -ace "n:%SidUsers%;p:write;s:y;m:grant;w:dacl"
SetACL.exe -on "HKLM\software\borland"        -ot reg  -actn ace -ace "n:%SidUsers%;p:read,create_link,create_subkey,set_val,notify,delete;s:y;m:grant;w:dacl"
```

The package engineer must describe in the release document if and exactly which permissions are set to make the application work.

## Repackaged MSI composition

Repackaged MSI packages (non-vendor MSIs) adhere to the standards described in this section. Note: these standards do not apply when vendor MSI packages are used. For vendor MSIs please see the documentation of the vendor.

### Compressed files

When possible, only one MSI per application should be built. All objects and files are compressed and placed in cabinet files. The cabinet files are placed inside the MSI (internally compressed). Installation files will not be placed in a directory structure outside the MSI, as is the case with administrative installation points.
Vendor MSIs are an exception. When a vendor MSI is not compressed, it will remain not compressed. Configuration and “finetuning” will be done by means of an MST, built on an administrative installation of the vendor MSI.

### Self-containing

The MSI package contains all the logic needed for installation or uninstallation. If this is not possible with standard actions alone – and only in this case, custom actions will be added. The MSI packages contain all system changes (files on the hard drive, system registry keys, services, etc).

### Features

**Repackaged** MSI packages contain two features:

_Usersettings  
___Complete

The feature Usersettings is the parent of the feature Complete. The feature Usersettings contains all usersettings needed for the application: the HKCU keys and files for the user profile. Files in the user’s profile always will be placed in a component having a HKCU registry key as keypath.

The feature complete contains all machine based items like:

* Files on the hard drive
* HKEY_LOCAL_MACHINE keys
* HKEY_CLASSES_ROOT keys
* Services
* Extensions
* Shortcuts
* Etc…

*In a transform, the schema of a vendor MSI database will never be changed.*

### Advertising

Desktop: Shortcuts will be advertised for desktop packages.
*To circumvent repairs on first use, user settings are copied or created using actions in the application portal.*

Registry settings will be included as-is and not in the advertising tables. This is a Microsoft best practice and prevents unwanted repairs.

### Deployment of user settings

User settings (in the context of MSI packages) involve the following objects:

* HKCU registry keys.
* files in the user profile.
* files on the home drive or other user specific folders.

(Standard) MSI packages for the desktop platform contain advertised shortcuts. When starting an application via an advertised shortcut the default MSI repair mechanism is triggered when components, such as per-user resources, are not found. This mechanism was used to deploy the user settings for desktop packages in traditional workspaces. Using Sogeti Smart Workspace, we use the application portal to publish applications to end-users. The portal has the ability to do user-based configuration of settings and this will be used instead of the self-healing capabilities of MSI packages. Self healing can be used as fallback option.

### System requirements (Launch Conditions)

The repackaged MSIs will not have Launch Conditions.

### Registration in Add/Remove Programs

When possible the MSI package should have the icon associated with the application. This can be done by means of the field Display Icon in Add/Remove Programs.

The following properties are set for Add/Remove Programs:

|Property|Value||
|--------|-----|--|
|DisplayName|[ProductName]||
|Publisher|[Manufacturer]||
|DisplayVersion|[ProductVersion]||
|Support Information Page||
||Product Updates URL|blank|
||Contact Person|blank|
||Phone Number|blank|
||Help URL|blank|
||Comments|blank|
|ARPNOMODIFY|1||
|ARPNOREMOVE|absent or 0||
|ARPNOREPAIR|absent or 0||
|ARPSYSTEMCOMPONENT|absent or 0||

### User interaction

It is not allowed that the user is prompted to fill in information during installation. The package must install completely unattended, without user interaction. All MSI-packages support completely silent/unattended installation by using the msiexec.exe command with the /q parameter like /qb-! or /qn.

In exceptional cases user interaction is inevitable. For example when a unique license code is required for every system or user.

### Merge Modules

Standard Merge Modules, supplied by Wise Package Studio or another vendors, will be used to install shared system components, like Microsoft ActiveX Controls.

### Permanent components and shared DLL’s

Applications can install dll and/or ocx files in Windows system folders (like C:\Windows\System32 or C:\Program Files\Common Files). General best practices will be used for those files:

* For files in the CommonProgramFiles folder the shared dll count is raised
* Components that install files in the SystemFolder are marked Permanent

Changing these options to other values can or will generate ICE errors. However, an exception can be made if it is obvious the dll file or ocx is specific for the application. The permanent flag or shared dll flag can be turned off but this must be documented in the package release documentation. When in doubt, follow the best practice to turn the flags on.

### DLL or OCX files in application folder

DLL or OCX files that are installed in the application’s specific installation folder (typically in the Program Files folder) and need COM registration should be examined:

* If the dll or ocx file is or may be shared with other applications, the dll or ocx should be moved to the SystemFolder. The corresponding component, including the corresponding registry values,  should be marked as permanent. If the application searches the moved file in its own installation folder and issues arise, then a copy can be added in the applications folder. In all cases, only the copy in the SystemFolder may be registered. Exceptions can be made for applications that are prerequisites for each other.
* If it is obvious the dll or ocx is application specific and no other applications use the dll or ocx file, it may remain in the application’s installation folder including the registration from this folder.

### ODBC settings

ODBC settings preferably are created using the registry. Using the ODBC tables may cause issues especially for non Microsoft ODBC drivers.

### Services

Actions (install, uninstall, start and stop) for normal Windows services are incorporated in the MSI package using the services tables. Settings that cannot be created using the services table can be created using the registry table. Services that cannot be handled using the services table, like kernel services, can be handled using custom actions. Sometimes, the application is included with a separate  executable to manage the service which can be used in custom actions.

### Validation

ICE validation for MSI packages is done using the cub file: “darice.cub”.  
For ICE *Warnings* best practices are they mostly can be ignored. ICE *Errors* should be solved in most cases. ICE errors in vendor MSI packagers, not introduced through the changes in an MST file, are not solved.

*Note: Validation always has to be done using Orca.*

## Transforming a vendor MSI

If the original installation source is a MSI, this MSI is to be used as base MSI. Adjustments, configuration, and fine-tuning will be done with a transform file. If possible, an administrative installation point is created before building the MST.

The transform file can be used to change the following:

* ALLUSERS property
* ROOTDRIVE property
* REBOOT properties
* Productname and Title
* Installation of additional files and registry keys
* Setting permissions
* Additional Custom Actions
* Add/Remove Program settings
* Etc.

Things not to be changed are:

* Compression
* ProductVersion
* ProductCode
* Manufacturer
* Schema, i.e. feature/component structure and keypaths
* Summary stream information: Title, Author, Comments
* Ice-errors
* MSI filename

Some installations extract an msi package upon installation. For these installations the extracted MSI package is to be used combined with an mst file. The MSI package can be extracted in many cases using the command line: Setup.exe /a.
This will create an administrative setup ready for use with an mst file.
In some occasions the installation using this extracted MSI directly fails and the setup.exe still must be used for deployment.

For some applications, repackaging – even the vendor MSI – can be preferred over using the vendor MSI with an MST. This can be the case if the vendor MSI is of poor quality, the vendor MSI cannot be used in an enterprise deployment scenario or when the creation of an mst file is very time consuming. The decision to repackage the MSI is for the packager using common sense, error free deployment and quality guidelines.

## Supported functionalities

All MSI packages must support the following functionalities:

|Functionality|Description|
|-------------|-----------|
|Installation|All packages have a default installation.|
|Administrative setup|It is possible to create an administrative setup for the msi package. For vendor msi’s, an admistrative setup is created just before the creation of the transform file. The administrative setup is used as source for the transform. When there are difficulties creating an administrative setup, these are mentioned in the TS form.|
|Assigned and Advertised|The msi packages can be deployed both “Assigned” and “Advertised”. Only for vendor msi’s, exceptions can be made.|
|Install-on-demand|Advertised applications will be installed during the first call to the application. This is the install on demand principle. Calls to the application can be the applications shortcut or a file-extension for example. Exceptions may be made for vendor msi’s|
|Silent install|The msi packages support silent installation|
|Self repair|The packages must support self repair. MSI packages installed per machine will install user settings at the first launch of the application. Besides that, missing parts of the application will trigger a repair of the installation at application launch. For vendor MSI packages the self repair behavior is not changed.|
|Elevated privileges|The MSI-packages can be installed by users having “Elevated privileges” for the application.|
|Deployment|The MSI-packages must support deployment using the customers deployment mechanism.|
|Uninstall|The packages can be uninstalled using the standard “Remove” action. The uninstallation process must be unattended. At uninstall, the application and, where possible, all known configuration files, temporary files and registry keys used by the application will be removed. All other files will  remain because they may contain user data, unless otherwise stated by the application manager and documented in the intake document.|

## Updates and patches

In the intake document the required procedure for updates is documented. The package can be a completely new package or an update for an existing package. When the new package version is an update for an existing application, the previous version is likely to be removed upon installation of the new version. Patched packages should always remove the previous installed package.

Using the standard upgrade tables updated packages can be installed regardless of the state of the workstation:

* When a previous version is found, it is uninstalled, and the new version is installed
* When no previous version is found, the new package installs just fine

The standard MSI procedure is the new package will be installed first and after that, the previous version is uninstalled. If both packages are using the same components (content and guid) this will result in an efficient update process where only new (or updated) components will be installed and obsolete components will be removed. Anything not changing will remain unchanged and untouched.
Since it is not always feasible both package versions use the same components (for example in case of a new application version or vendor MSI packages) this cannot be used in all circumstances. If components do not match, parts of the new package may be removed by the uninstall of the previous version resulting in conflicts and unwanted selfrepairs. In this case, the upgrade mechanism still can be used by moving the standard action RemoveExistingProducts upwards in the Install Immediate sequence, just after FindRelatedProducts.

Vendor MSI packages which do not support the MSI update mechanism will be updated using the deployment tool. An uninstall command is sent to the workstation prior to the installation of the new version. Checks must support the case where no previous version exists on the workstation.  

## Applications not suitable for MSI repackaging

Not all applications or installations are suitable for repackaging. The following applications will not be repackaged into MSI packages:

*Operating Systems*
Service patches, Service packs and hotfixes on the operating system will not be repackaged, but will be installed using the procedure described by the vendor, typically Microsoft.

*Microsoft Internet Explorer, Anti Virus software*
These applications add changes on a low level of the Operating System and will not be repackaged. They will be installed using the procedure described by the vendor.

*Unsigned Hardware Drivers*
Drivers can be packaged according to the Microsoft Driver Installation Framework principle. Therefore, the drivers for peripherals and other internal and external devices must be digitally signed (Designed for Windows 10). If a driver requires hardware to install correctly, this hardware must be supplied to the packager before any work can begin – if not, there is no guarantee that the package will work with the hardware.

*Vendor specific deployment instructions*
In some cases, the vendor’s deployment mechanism is preferred over standard repackaging and/or MSI-MST combination. If the vendor prescribes a specific deployment scenario, in most cases companioned with a deployment tool or toolkit, this scenario is preferred over repackaging. Examples for this approach are:

* Microsoft Office 365 suite
* Adobe Creative Suites
* Adobe Acrobat family products
* AutoCAD family products

## Testing an MSI Package

After the packager has finished the package it can be tested using the User test and the Administrator test. The user test typically shows the correct functionality of the application when installed with the package on a representative workplace (desktop, laptop or server). The administrator test can be used to determine if additional (ntfs-) permissions or user rights are necessary so the application can function according to the requirements.

### User test

* Start on a clean machine
* Log on with a user account
* Start a command prompt in admin context
* From the admin prompt start a new command prompt in system context (using psexec.exe)
* Install any prerequisites in system context
* Install the MSI package in system context
* Make sure user settings are set when appropriate:
    1. import the user settings (HKCU registry keys and user files) for the user account when the package is tested for deployment on a remote desktop VM (on remote desktop servers self-repair is not preferred, user settings are set with other mechanisms like the use of GPO Preferred Settings.
    2. If user settings are set using Active Setup log off and login with the test user. Check if user settings are created before the first launch of the application.
    3. In all other cases, user settings should be set through MSI self-repair.
* Run the application using the shortcut or file extension (when appropriate).
* Test the application according to the test plan in the intake document.

### Admin test

* Start on a clean machine
* Log on with a user account with local administrator privileges
* Start a command prompt in admin context
* Start from the admin prompt a new command prompt in system context
* Install any prerequisites in system context
* Install the MSI package in system context
* Make sure user settings are set when appropriate:
    1. Import the user settings (HKCU registry keys and user files) for the user account when the package is tested for deployment on a remote desktop VM (on remote desktop servers self-repair is not preferred, user settings are set with other mechanisms like the use of GPO Preferred Settings).
    2. If user settings are set using Active Setup log off and login with the test user. Check if user settings are created before the first launch of the application.
    3. In all other cases, user settings should be set through MSI self-repair.
* Run the application using the shortcut or file extension (when appropriate).
* Test the application according to the test plan in the intake document.

## MSI Naming parameters

*Note: for all parameters spaces (‘ ’) and underscores (‘_’) are not allowed.*
*If, for readability, a separation character is needed, use ‘-’.*

### Identification

Applications are identified using manufacturer, application name, version and language as described in the paragraph Application and package identification from chapter 'Introduction'.

### Buildversion

The *Buildversion* of an MSI package consists of 3 categories: major, minor and revision. The categories are separated by a dot (‘.’).

|Buildversion|major.minor.revision|
|------------|--------------------|
|major|x (0-255)|
|minor|y (0-255)|
|revision|z (0-65535)|
|Buildversion|xxx.yyy.zzzzz (no leading zero’s)|

Examples:  
1.0.0  
2.1.3  
1.0.11

## MSI Versioning

Regarding the Buildversion the following standards will be used:

* The first build, based on intake version 1.0, is version 1.0.0.
* When an MSI package is released for technical acceptation, the number will be freezed. Based on intake 1.0, the first time a package is released for technical tests, it will have number 1.0.0.
* If the MSI is not accepted after technical or functional tests due to a technical defect in the package, the build version will be raised: 1.0.0 -> 1.0.1. (bases on intake 1.0)
* If requirements or configurations are changed (this results in a new intake version 1.1), the minor version will be raised, following the intake version: 1.0.1 -> 1.1.0
* In case of a lot of changes, for example a completely different installation of the application, the intake will have version 2.0, and the major version of the build number will be raised: 1.1.0 -> 2.0.0.

*Note: In the MSI package XY always matches the intake version (the first two digits in the version for the intake are used), new releases based on the same intake are created by updating the revision-parameter Z.*

## MSI Naming convention

This paragraph describes the naming to be used in MSI packages. The maximum length of the complete package name and package filename is **128** characters (including the file extensions).

### MSI Package Properties

The following fields are properties in MSI packages:

**ProductName**
The ProductName of the MSI package is: *Manufacturer_ApplicationName_Version_Language_vmajor.minor.revision*

Examples:  
Winzip_Winzip_10_EN_v1.0.0  
Adobe_Reader_8.1_NL_v2.1.3

*Note: When building a MST file the ProductName will be set in the MST.*

**(Product)Version**
For the (product)version of a MSI package we must stick to the Windows Installer rules: major.minor.revision

Example:  
1.0.0  
2.1.3  
1.0.11  

*Note: When building a MST file the product version will NOT be changed in the MSI or MST.*

**Title**
The title is identical to the product name: *Manufacturer_ApplicationName_Version_Language_vmajor.minor.revision*

Example:  
Winzip_Winzip_10_EN_v1.0.0  
Adobe_Reader_8.1_NL_v2.1.3

*Note: When building a MST file the title will NOT be set in the MST.*

**Manufacturer**
The Manufacturer of the MSI package is: *ManufacturerName*

Example:  
Winzip  
Adobe

*Note: When building a MST file the manufacturer will NOT be changed in the MSI or MST.*

**Author**
Author = *Sogeti Nederland B.V.*

*Note: When building a MST file the author will NOT be changed in the MSI or MST.*

**Comments**
In the Comments field the date, packager, and other installed applications (prerequisites) are documented:  
Date, Name Packager, other apps

Example: 10-11-2021, P. Ackager, JRE 1.9

*Note: When building an MST file the comments will NOT be changed in the MSI or MST.*

### MSI Filenames

The following filenames are applicable when building a MSI package:

**MSI Filename**
The MSI filename must comply with the following convention. The name of the MSI package is: ManufacturerName_ApplicationName_Version_Language.msi

Example:  
Winzip_Winzip_10_EN.msi  
Adobe_Reader_8.1_NL.msi

*Note: In case of a Vendor MSI with MST the name of the Vendor MSI is not changed.*

**MST Filename**
The name of the MST files for a desktop package is: *ManufacturerName_ApplicationName_Version_Language_vmajor.minor.revision.mst

Example:  
Winzip_Winzip_10_EN_v1.0.0.mst  
Adobe_Reader_8.1_NL_v2.1.3.mst
