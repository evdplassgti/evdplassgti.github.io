# MSIX Package format explained

MSIX is the modern application deployment platform released by Microsoft. It can be seen as the successor of other package solutions for modern Windows devices, replacing App-V, MSI and AppX. But remember, these other technologies are still being used and work side by side with MSIX.

The ability to deploy and use MSIX packages is built into the Windows 10 operating system and no further configuration is required. Like modern apps (AppX), deployment can take place system wide or per user. By default, MSIX packages do not require privileged (admin) permissions to install, with the exception for packages that install (system) services.

The content and structure of MSIX packages is very similar to App-X packages. The package can be unzipped with a file archiver (such as 7-Zip) to view the content of the package. However, *re-zipping it does not produce a working package*. The typical content of an MSIX package is shown in the following example:
![MSIX Content](/assets/img/msix-content.png "MSIX Content"){: .center}
Packagers with experience on App-V will notice some similarities with App-V 5 packages as well, such as the VFS folder, registry.dat file and manifest.

An important aspect is that MSIX packages run in an isolated container to which other processes do not have access. Similar, but not the same as App-V sequences.

## Code Signing packages

Another important prerequisite of MSIX packages is that they need to be code signed before a package can be installed on the device. This can be done using a self-signed certificate, a public certificate or a device guard signing certificate from the Windows Store for Business. Signing packages is integrated in the package tooling but can also be done using PowerShell.

## Things that do not work (as expected)

As mentioned, with MSIX packages run in an isolated, virtualized container. That means that not all aspects of applications are supported and will work. Similar to App-V, applications that are tight to the operating system, such as drivers and anti-virus software, cannot be converted to MSIX packages. But there are some other things that need to be in mind:

* Shortcuts in MSIX do not support parameters or working folders. If your application requires one or both of these aspects, custom configuration is required.
* Environment variables, created by the application installer, are not supported.
* Applications do not have write permissions to the application folders, what is the case in App-V packages.

Some of these ‘flaws’ can be worked around, using the Package Support Framework – as explained in the following paragraph.

## Package tooling

Similar to the App-V 5 sequencer, Microsoft provides a tool to build your own MSIX packages. This tool, **MSIX Packaging Tool**, must be installed from the Windows Store. Running the tool requires administrative privileges. Besides that, the following services should be configured as below:

* Windows Update must be enabled[^1]
* Windows Search should be disabled
* Windows Defender should be disabled

[^1]: During packaging, the packaging tool will disable Windows Update after initiation.

There are also commercial tools available that can build and update MSIX packages, such as InfoPulse PACE, Advanced Installer and Flexera Admin Studio.

**Advanced Installer** provides a free packaging tool for MSIX that can be installed from the Windows Store. You need to sign up for a (free) license to use it [here](https://www.advancedinstaller.com/express-edition.html){target=_blank}. This tool can be used as an alternative when a package that is built with the MSIX Packaging Tool does not (properly) work. There are some differences with the MSIX packaging tool that are worth mentioning here:

* Applications are launched through a stub process, using the install folder as working directory by default.
* Startup parameters for Apps work out of the box.
* Some basic options for the Package Support Framework (PSF) are built in (explained later in this chapter).
* You need to add icons for your apps yourself.
* File extensions may work better when the package is captured using Advanced Installer.

*Please note that an MSIX package built with Advanced Installer can be edited later on with the MSIX Packaging Tool, just like any other MSIX package. However, the package can contain some features specifically used (and created by) Advanced installer, such as a custom implementation of the PSF. Therefore, it is better to continue working in Advanced Installer. The project files are also part of the deliverables for the package in that case.*

## Package Support Framework

The Package Support Framework is an open source project that provides solutions for a number of aspects that do not work with the standard tooling. It can help increasing the success rate by providing fixes for applications that do not follow modern best practices (remember AppCompat and shims?).

The PSF is a set of dll files providing fixes for common errors, accompanied by json files that configure the way the fix is to be applied and to what executable(s), process, files or folders in the package. The most common errors that may be fixed using PSF are:

* App Working Directory.
* App Launcher parameter(s).
* Write errors from the App.
* (some) Support for environment variables.

Applying PSF fixes requires the following steps:

* Adding the PSF dll file(s) for the fixes that need to be applied.
* Adding the PSF Launcher application, that is launched instead of the Windows app so the fix can be applied.
* Adding the fix configuration json file to the package, with application specific parameters for the applied fix or fixes.

Further information about the Package Support Framework can be found [here](https://docs.microsoft.com/windows/msix/psf/package-support-framework-overview){target=_blank}.

There is also a free tool available that helps in applying PSF fixes semi-automatically. This tool, **TMUrgent-PSFTooling**, is installed on the packaging machine from the Windows Store. During packaging - right after the installation is completed and just before finalizing the capture – the PSFTool is launched to find and suggest optional fixes that can be applied automatically. The tool is frequently updated using the latest features of the PSF project and recently is using a fork of the project.

## Handling plugins or add-on packages

Some applications have additional setups available that can be installed after the main installation is finished. If the addon is included in the installation procedure, just add it to the package during capture. But sometimes, plug-ins, add-ons or language packs are optional. Users cannot install those add-ons from an MSIX packaged application. If separate add-ons are required, MSIX supports the use of *modification packages*. Modification packages are similar to what transforms files are for MSI packages: these special packages only contain the differences (modification) over the original package. Modification packages require the main application but can be installed and uninstalled separately, provided the main application package is already installed. You can read more over modification packages [here](https://docs.microsoft.com/windows/msix/modification-packages){target=_blank}.

## Handling pre-requisite packages (preview)

In App-V we know about Connection Groups in which different App-V sequences can be linked together to form a new virtual environment in which resources (files, registry keys) of both packages can be shared. Without connection groups, the apps in the different sequences cannot access resources in each other environments.
By default, an MSIX package also runs in its own virtual container whitout the ability to access resources in another container. That can be cumbersome when applications depend on each others resources. Of course, adding both apps into a single MSIX package is an option but that can lead to additional work and complexity when many apps use the same dependency. A similar approach as with App-V Connection Groups is now available with the feature *MSIX Shared Containers*. An XML file is used to define the shared container and to specify which packages are part of it. Using PowerShell commands, the shared container is created, modified and removed. While all packages in the shared container should be installed to work, it is not required all applications in the shared container definition are installed at the time the container is defined.
More information on MSIX Shared Containers can be found [here](https://docs.microsoft.com/windows/msix/manage/shared-package-container){target=_blank}.
