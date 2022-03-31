# MSIX Compatibility reference

Not all applications can be succesfully packaged to MSIX. In order to determine if your application is suitable to be repackaged to MSIX, this page shows a list of applications for which the result is known, based on experiences from the SSW packaging team.
In this table, the packaging tool used is mentioned (MPT for the MSIX Packaging Tool and AIE for Advanced Installer Express) and wether or not it was neccessary to use fixes from the Packaging Support FrameWork (PSF). Note that AIE already applies some PSF fixes by default, such as the working directory, commandline parameters and file redirection. This may lead to different results compared to using the MPT.

|Application|Tested with|Result|Needs PSF|Remarks|
|-----------|-----------|------|---------|-------|
|Agent Ransack 2019 (2951) x64|MPT|Ok|No|
|Alterra Synbiosys 3.0.16 x64|MPT|Ok|No|After creating the package, the file 'SynBiosSysNederland.exe' needs to be removed and added back manually because the timestamp is updated during packaging. The app then starts mentioning the app is corrupt|
|Alterra Waternood 3.0.1 x64|MPT|Ok|No|After creating the package, the file 'watersnood.exe' needs to be removed and added back manually because the timestamp is updated during packaging. The app then starts mentioning the app is corrupt|
|Archi-Win 4.8.1 x64|MPT|Ok|No|When archi is launched, an error about JNI is shown. This happens because vcruntime140.dll cannot be found. Solved this by adding the dll file to the Windows\system32 folder in the package (placing this dll file in the packageroot folder works as well)|
|BizagiModeler 3.8 x64|MPT|Ok|No|
|FileZilla3.34.0 x64|MPT|Ok|No|
|FreeCommander Portable x86|AIE|Ok|Yes|The Settings folder must be redirected using the FileRedirection fixup|
|FreeCommander Portable x86|MPT|Ok|Yes|The Settings folder must be redirected using the FileRedirection fixup|
|Gimp 2.8.6 x64|AIE|Ok|No|Advanced Installer version works when dll files from app folder are copied to Windows\SysWoW64 folder or Windows\System32. When not done, app launches with errors concerning dll files not found.|
|Gimp 2.8.6 x64|MPT|Ok|No|succesfull with this approach: Install in C:\GIMP2 with PVAD to C:\GIMP2\bin and move subfolders from \VFS\AppVPackageDrive\GIMP2 to Packageroot|
|Google Earth 7.3.3 x64|MPT|Ok|No|Special attention to the approach is required as the download is a web installer. Grab the package from the temp folder, and use that for packaging Google Earth as MSIX package.|
|Irfanview 4.58 x64|MPT|Ok|No|Language packs need to be included in the package or added using modification packages|
|KeePass 2.x x64|MPT|NOk|Does not work as MSIX packaged application. See [here](https://techcommunity.microsoft.com/t5/msix-packaging-and-tools/application-does-not-launch-post-installation/m-p/353506){target=_blank}|
|Notepad++ 8.x x64|MPT|Ok|No|Plugins need to be included in the package or added later using modification packages. Updates need to be disabled using the config file. The shell extension ('Edit with Notepad++') does not work.|
|paint.net 4.3.2 x64|MPT|Ok|No|Special attention to the approach is required as the download is a web installer. Grab the package from the temp folder, which is an msi, during manual installation. Modify the MSI with the properties AUTOUPDATES=0 and BETAUPDATES=0 to disable updates in the application.|
|PaintShop Pro X2 x64|MPT|Ok|No|Use PVAD path, start app during monitoring, remove references to VC2005 components from the package (VC2005 therefor is a prereq). Remove HKCU\Software\Corel from the package, these keys are created during first launch.|
|PDFSam 4.2.4 x64|AIE|Ok|No|
|PDFSam 4.2.4 x64|MPT|Ok|No|
|Putty 0.76 x64|MPT|Ok|Yes|The logfile (putty.log) might need redirection
|VLC Mediaplayer 3.0.x x64|AIE|Ok|No|
|VLC Mediaplayer 3.0.x x64|MPT|Ok|Yes|FileRedirection Fixup is required for AppData settings|
|WinSCP 5.19.3 x64|MPT|Ok|No|
|Workrave 1.10.48 x86|MPT|Ok|No|Autostart on Windows login must be configured in the manifest.xml file, see [here](https://docs.microsoft.com/en-us/windows/apps/desktop/modernize/desktop-to-uwp-extensions#start-an-executable-file-when-users-log-into-windows){target=_blank}|






