# MSIX Standards and guidelines

The naming convention and some examples for MSIX packages are given in the following paragraphs in this chapter.

## MSIX Guidelines

The following aspects of MSIX packages are important for the behavior, including the possibilities for upgrades and packages for the same application to run side by side. In this chapter, examples are given using the application Agent Ransack, with the following properties:

|Property|Example|
|--------|-------|
|Manufacturer|MythicSoft|
|Application|Agent Ransack|
|Version|2951|
|Language|EN|
|Architecture|x64|
|Buildversion|1.0.0|

### Packagename

The Package “Name” is stored in the Identity Element of the AppXManifest File. The Package Name is is a string of 3-50 characters and has limitation on the allowed character set. In general, only one package with the same name can exist on a system. Packages with the same name but with different versions are considered to be updates.

The definition of the package name is:
*Manufacturer-ApplicationName-ApplicationVersion-Language-Architecture*

For example, **MythicSoft-AgentRansack-2951-EN-x64**

### DisplayName

The displayname is the user-friendly name for the package. There is no dependency with other properties or behavior. The definition of the display name is:

    *ApplicationName ApplicationVersion*

For example, **Agent Ransack 2951**

### Version

The version specifies the build version of a package, in the form a.b.c.d in which the latest digit is reserved by Microsoft. This means, the a.b.c part of the version follows the package build version, d is always 0:

Regarding the Buildversion the following standards will be used:

* The first build, based on intake version 1.0, is version 1.0.0.
* When an MSIX package is released for technical acceptation, the number will be freezed. Based on intake 1.0, the first time a package is released for technical tests, it will have number 1.0.0.
* If the MSIX is not accepted after technical or functional tests due to a technical defect in the package, the build version will be raised: 1.0.0 -> 1.0.1. (based on intake 1.0)
* If requirements or configurations are changed (this results in a new intake version 1.1), the minor version will be raised, following the intake version: 1.0.1 -> 1.1.0
* In case of a lot of changes, for example a completely different installation of the application, the intake will have version 2.0, and the major version of the build number will be raised: 1.1.0 -> 2.0.0.

*Note: In the MSIX package a.b always matches the intake version (the first two digits in the version for the intake are used), new releases based on the same intake are created by updating the revision-parameter c. d always has a value of 0.*

### Publisher

The publisher display name is the *manufacturer* of the application.

For Example, **MythicSoft**

### Publisher Display Name

The publisher display name is the name that corresponds with issuer in the code signing certificate. For example:

    **CN=Sogeti Nederland B.V., OU=Smart Workspace, O=Sogeti Nederland B.V., STREET=Lange Dreef 17, L=Vianen, S=Utrecht, C=NL**

### Package Family Name

Together with the package name, the package family name is used to make packages unique. The package familyname is the packagename, extended with a hashcode that is based on the certificate used for signing the package. This hash is added automatically. As it is used to uniquely define the package, it is important the **same** certificate is used to sign the package in case of an update. Any previous version is detected using the package family name.

### Description

This is a free format field, describing the package. The packagename and build version are used in the description:

    **Manufacturer-ApplicationName-ApplicationVersion-Language-Architecture-vbuildversion**

For example, *MythicSoft-AgentRansack-2951-EN-x64-v1.0.0*

### Filename

The MSIX Packaging tool automatically suggests a filename for the package, which includes the package family name. Accept this default:

    **Manufacturer-ApplicationName-ApplicationVersion-Language-app_architecture-MSIX Build-architecture__hashcode.msix**

Note that the build version includes 4 tuples, including the reserved one added automatically.

For example,

    **MythicSoft-AgentRansack-2951-EN-x64_1.0.1.0_x64__wrfnxgs55ayay.msix**

### Timestamp

To make sure the package still can be installed after the code signing certificate is expired, time stamping is used. This means that the certificate is validated at the time of package creation, the certificate should be valid at that time.

For the timestamp server, we use the value: **'http://timestamp.digicert.com'**

## Installation folder

During packaging the application, in the MSIX Packaging Tool you can specify the Target Installation Folder.

## Handling plugins or add-on packages

Some applications have additional setups available that can be installed after the main installation is finished. If the addon is included in the installation procedure, just add it to the package during capture. But sometimes, plug-ins, add-ons or language packs are optional. Users cannot install those add-ons from an MSIX packaged application. If separate add-ons are required, MSIX supports the use of *modification packages*. Modification packages are similar to what transforms files are for MSI packages: these special packages only contain the differences (modification) over the original package. Modification packages require the main application but can be installed and uninstalled separately, provided the main application package is already installed. You can read more over modification packages [here](https://docs.microsoft.com/windows/msix/modification-packages){target=_blank}.

## Handling pre-requisite packages (preview)

In App-V we know about Connection Groups in which different App-V sequences can be linked together to form a new virtual environment in which resources (files, registry keys) of both packages can be shared. Without connection groups, the apps in the different sequences cannot access resources in each other environments.
By default, an MSIX package also runs in its own virtual container without the ability to access resources in another container. That can be cumbersome when applications depend on each others resources. Of course, adding both apps into a single MSIX package is an option but that can lead to additional work and complexity when many apps use the same dependency. A similar approach as with App-V Connection Groups is now available with the feature *MSIX Shared Containers*. An XML file is used to define the shared container and to specify which packages are part of it. Using PowerShell commands, the shared container is created, modified and removed. While all packages in the shared container should be installed to work, it is not required all applications in the shared container definition are installed at the time the container is defined.
More information on MSIX Shared Containers can be found [here](https://docs.microsoft.com/windows/msix/manage/shared-package-container){target=_blank}.
