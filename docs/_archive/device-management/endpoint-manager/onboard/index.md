# Onboarding Device Management - Microsoft Endpoint Manager

Being a cloud service, onboarding Microsoft Endpoint Manager still is more then just enabling the service. Activities and configuration need to be well planned and prepared, well thought through, and then properly configured. The following figure shows the steps to take in the onboarding process.
![Intune](/assets/img/genprocess.png "Onboarding process").

## Intake

Within Sogeti Smart Workspace, we *do* have a standard configuration with which we quickly can configure Microsoft Endpoint Manager. However, the customer needs to know what we provide, what's expected from them, how the service integrates within their infrastructure and IT landscape and what the user gets (experience). On our turn, we need to know what the customer expects and how we can integrate in their infrastructure, network and IT landscape.

The intake is done through a *Design Workshop* in which we explain in detail how the service looks like, which decisions need to be made and what is required to build (configure) Microsoft Endpoint Manager. During the workshop, which is lead by the SSW Tech lead for the onboarding process, the **Device Management Design Guide** is to be used. You can download the latest version here: [![ppt](/assets/img/powerpnt.png)](/assets/doc/Design Guides/SSW Design Guide – Device Management.pptx). Going through the design guide, the tech lead explains the service and gathers information, required to setup the design in the next phase.

## Design

Based on the standard design templates and the information gathered in the intake step, customer specific designs are created. You can download the latest templates from below table. Both designs include the configuration that must be set in Endpoint Manager. Note that decisions made may also impact the high-level or master design.

|Design template                |Download                                                                            |
|-------------------------------|------------------------------------------------------------------------------------|
|Detailed design Windows 10     |[![docx](/assets/img/winword.png)](/assets/doc/Designs/Detailed design Windows 10.docx)    |
|Detailed design mobile devices |[![docx](/assets/img/winword.png)](/assets/doc/Designs/Detailed design Mobile Devices.docx)|

## Build

The build step for Endpoint Manager, at high level, consists of the following activities:

1. Initial setup / configuration of Endpoint Manager.
2. Importing the Smart Workspace standard configuration and applications.
3. Fine tuning the configuration to match the customers requirements and IT landscape.
4. Adding additional, business, applications that will not be deployed through the application portal.

Steps 1 and 2 are carried out using our own standard activities and configuration. Steps 3 and 4 follow additional configuration based on the customer specific design choices, as documented in the detailed designs.

### Initial setup and import of the SSW standards

The first step, is to setup Endpoint Manager and when that is complete, the standard configuration can be applied. The checklist in below table describes all required, preferred and optional tasks. Beforehand, make the following preparations:

#### Shopping list

* Request an account with the *Global Administrator* role assigned. This is required by some tasks below. After initial configuration, the **Intune Administrator** role and **Conditional Access Administrator* role are required.
* Discuss the required Azure AD Groups:
    1. Dynamic device groups for Autopilot, policy, profile and app assignment.
    2. SSW Users group. In most cases, the customer manages this group in AD, which is then synced to AAD. Discuss which group can be used.
    3. Azure AD device Groups for assigning Software Update policies (Pilot and Test)
* Request accounts for Apple push notifications and connecting to Google Play Store.
* The PowerShell script that imports the Intune configuration creates an application registration in Azure Active Directory for use with the Graph API. Discuss this with the customer.
* When company mobile devices and/or company macOS devices need to be managed, discuss if the customer is using Apple Business Manager and/or Samsung Knox. If so, a connector needs to be configured. In many cases, the customer or their hardware vendor/reseller, manages Apple Business Manager and/or Samsung Knox. If that is the case, proper process agreements are required.

#### Implementation checklist

|Item|Description|Instruction|Additional Information|Importance|
|----|-----------|-----------|----------------------|----------|
|**Setup**|
|AAD Device Groups|Configure AAD device groups for Autopilot|Create Dynamic AAD groups to be used with Autopilot. Create a group for All Autopilot devices and one per deployment profile that is based on Group Tag|Rule for All Autopilot devices is `(device.devicePhysicalIDs -any _ -contains "[ZTDId]")`, the rule for a group based on Group Tag is `(device.devicePhysicalIds -any _ -eq "[OrderID]:SSW")`. Note that the Group Tag is 'SSW' in this example|Required|
|AAD User Groups|Configure a group for SSW users|At least one group for SSW users needs to exist|This can be a cloud only group in Azure AD or a synchronized group from the on-prem AD|Required|
|Scope|Determine scope for MDM and/or MAM|By default, MDM is configured for Windows 10, MAM for iOS and Android|Follows from design decision in HLD or detailed design|Required|
|License check|Check (or configure) license assignment for SSW users|Check if the users are assigned proper licenses. If not, assign licenses to the SSW User group, in agreement with customer|[Azure Active Directory > Licenses > All Products](https://portal.azure.com/#blade/Microsoft_AAD_IAM/LicensesMenuBlade/Products){target=_blank}|Required|
|Windows MDM|Configure Windows 10 enrollment|Configure Auto-enrollment for Intune|[Devices > Enroll Devices > Windows enrollment](https://endpoint.microsoft.com/#blade/Microsoft_Intune_DeviceSettings/DevicesEnrollmentMenu/windowsEnrollment){target=_blank}|Required|
|Windows MDM|Configure AAD Device Join|Configure the ability for users to join devices to AAD|[Azure Active Directory > Devices > Device settings](https://portal.azure.com/#blade/Microsoft_AAD_Devices/DevicesMenuBlade/DeviceSettings/menuId/){target=_blank}|Required|
|iOS/iPadOS enrollment|Configure Apple Push Notification Certificate|Configure a push notification certificate for Apple Devices. An applied is required, preferable an id from the customer|[Devices > iOS/iPadOS > iOS/iPadOS enrollment](https://endpoint.microsoft.com/#blade/Microsoft_Intune_DeviceSettings/DevicesIosMenu/iosEnrollment){target=_blank}|Optional|
|Android Enrollment|Connect with Managed Google Play|Configure the connection with Managed Google Play, preferable using the customers gmail account|[Devices > Android > Android enrollment](https://endpoint.microsoft.com/#blade/Microsoft_Intune_DeviceSettings/DevicesAndroidMenu/androidEnrollment){target=_blank}|Optional|
|Enrollment Status Page (ESP)|Configure ESP|The Enrollment Status Page is required and must be enabled|[Devices > Enroll Devices > Enrollment Status Page](https://endpoint.microsoft.com/#blade/Microsoft_Intune_Enrollment/EnrollmentStatusPageProfileListBlade){target=_blank}|Required|
|Autopilot|Configure profiles for Autopilot|At least one Userdriven Enrollment Profile must be configured for laptops. Optionally, a Self Deploying profile is configured for use on shared devices / desktops. Assign the AP-profiles to the dynamic AAD device group(s)|[Devices > Enroll Devices > Deployment Profiles](https://endpoint.microsoft.com/#blade/Microsoft_Intune_Enrollment/DeploymentProfilesBlade){target=_blank}|Required|
|Enterprise State Roaming (ESR)|Configure ESR|Enterprise State Roaming helps users keeping their personal settings on all their devices. Determine if a selected group must be used or if ESR is enabled for all users. |[Azure Active Directory > Devices > Enterprise State Roaming](https://portal.azure.com/#blade/Microsoft_AAD_Devices/DevicesMenuBlade/RoamingSettings/menuId/){target=_blank}|Required|
|Enrollment restrictions|Configure Enrollment restrictions|Enrollment restrictions can limit the devices users can enroll, for example block personal devices|[Devices > Enrollment Restrictions](https://endpoint.microsoft.com/#blade/Microsoft_Intune_DeviceSettings/DevicesMenu/enrollmentRestrictions){target=_blank}|Preferred|
|**Configure**|
|Import SSW default profiles|Import the SSW Default profiles for Intune|Using the backup-restore PowerShell scripts, import the SSW default policy set. This set contains Device Compliancy policies, Device Configuration policies, Microsoft Security Baseline settings and App Protection policies.|**Download** the standard configuration here: [Generic](/assets/resource/Intune - SSW default.zip) or [macOS](/assets/resource/Intune - SSW default for macOS.zip). Read the **instructions** [here](../instructions/import-ssw.md)|Required|
|Windows Compliance Policy|Assign Windows Compliance policy|Assign the Windows Device Compliance policy to the **SSW Users Group**|[Devices > Windows > Compliance Policies](https://endpoint.microsoft.com/#blade/Microsoft_Intune_DeviceSettings/DevicesComplianceMenu/policies){target=_blank}|Required|
|Android Compliance Policy|Assign Android Compliance policy|Assign the Android Device Compliance policy or policies to the SSW Users Group. Different policy types may apply per deployment profile|[Devices > Android > Compliance policies](https://endpoint.microsoft.com/#blade/Microsoft_Intune_DeviceSettings/DevicesAndroidMenu/compliancePolicies){target=_blank}|Optional|
|iOS and iPadOS Compliance Policy|Assign Compliance policy for iOS and iPadOS|Assign the iOS/iPadOS Device Compliance policy or policies to the SSW Users Group.|[Devices > iOS/iPadOS > Compliance policies](https://endpoint.microsoft.com/#blade/Microsoft_Intune_DeviceSettings/DevicesIosMenu/compliancePolicies){target=_blank}|Optional|
|macOS Compliance Policy|Assign Compliance policy for macOS|Assign the macOS Device Compliance policy or policies to the SSW Users Group.|[Devices > macOS > Compliance policies](https://endpoint.microsoft.com/#blade/Microsoft_Intune_DeviceSettings/DevicesMacOsMenu/compliancePolicies){target=_blank}|Optional|
|Windows Configuration|Assign the Windows 10 Configuration profiles|Assign the Windows 10 Configuration profiles to the Dynamic Device Group in AAD, created in the first step|[Devices > Windows > Configuration Profiles](https://endpoint.microsoft.com/#blade/Microsoft_Intune_DeviceSettings/DevicesWindowsMenu/configProfiles){target=_blank}|Required|
|Windows Security Baseline|Assign the Windows 10 Security Baseline|Assign the Windows 10 Security Baseline to the Dynamic Device Group in AAD, created in the first step|[Endpoint Security > Security baselines](https://endpoint.microsoft.com/#blade/Microsoft_Intune_Workflows/SecurityManagementMenu/securityBaselines){target=_blank}|Required|
|Microsoft Edge Security Baseline|Assign the Microsoft Edge Baseline|Assign the Microsoft Edge Security Baseline to the Dynamic Device Group in AAD, created in the first step|[Endpoint Security > Security baselines](https://endpoint.microsoft.com/#blade/Microsoft_Intune_Workflows/SecurityManagementMenu/securityBaselines){target=_blank}|Required|
|Microsoft Defender for Endpoint Baseline|Assign the Windows Edge Baseline|Assign the Windows Edge Security Baseline to the Dynamic Device Group in AAD, created in the first step|[Endpoint Security > Security baselines](https://endpoint.microsoft.com/#blade/Microsoft_Intune_Workflows/SecurityManagementMenu/securityBaselines){target=_blank}|Optional, Required when using Defender for Endpoint|
|Windows Software Update Rings|Configure Software Update Rings for Windows|Create Software Update Rings for Windows 10 (Pilot, Test and Production). Assign Pilot and Test to specific device groups, assign Production to the Dynamic Device Group in AAD and exclude both Pilot and Test|[Devices > Windows > Update rings for Windows 10 and later](https://endpoint.microsoft.com/#blade/Microsoft_Intune_DeviceSettings/DevicesWindowsMenu/win10UpdateRings){target=_blank}|Required|
|Windows Feature Updates|Configure Feature updates for Windows|Create a Feature update profile and assign to a device group. Use the same approach (Pilot, Test and Production) as with Software Update Rings. When using this option, deferral must be set to 0 for Feature Updates in the Software Update Rings.|[Devices > Windows > Feature updates for Windows 10 and later (Preview)](https://endpoint.microsoft.com/#blade/Microsoft_Intune_DeviceSettings/DevicesWindowsMenu/featureUpdateDeployments){target=_blank}|Optional, Required to support upgrade to Windows 11|
|Windows Quality updates|Configure Quality updates for Windows|Create a Quality update profile and assign to a device group. Use the same approach (Pilot, Test and Production) as with Software Update Rings. When using this option, deferral must be set to 0 for Quality Updates in the Software Update Rings.|[Devices > Windows > Quality updates for Windows 10 and later (Preview)](https://endpoint.microsoft.com/#blade/Microsoft_Intune_DeviceSettings/DevicesMenu/windows10QualityUpdate){target=_blank}|Optional|
|SSW standard apps|Configure the deployment of SSW standard apps, such as the Microsoft 365 apps, Edge browser, 7-Zip, Chrome, Adobe Reader and SSW Agents|Create a standard App and assign as required app for the Dynamic Device Group, created in step 1. Be sure to use Win32 App type (except for Office)|[Apps > Windows Apps](https://endpoint.microsoft.com/#blade/Microsoft_Intune_DeviceSettings/AppsWindowsMenu/windowsApps){target=_blank}|Required|
|Conditional Access|Configure SSW Conditional Access Policies|Configure CA policies according the standards and assign to the specified users / groups. Consider exclusions to prevent lockout and/or service accounts that do not work with these CA rules. Additional configuration is required in SPO and EXO for Session Control (See [here](https://docs.microsoft.com/sharepoint/control-access-from-unmanaged-devices){target=_blank} and [here](https://techcommunity.microsoft.com/t5/outlook-blog/conditional-access-in-outlook-on-the-web-for-exchange-online/ba-p/267069){target=_blank}).|[Endpont security > Conditional Access > Policies](https://endpoint.microsoft.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies){target=_blank}|Required|

When setup and configuration are complete, the engineer fills in the Technical Release Document. This document is used to hand-over to the technical test and provides insight in the progress. The template for the release document can be downloaded here:

|Release document                         |Language|Download|
|-----------------------------------------|--------|:------:|
|Microsoft Endpoint Manager|English|[![xlsx](/assets/img/excel.png)](/assets/doc/Release/Technical Release Document Microsoft Endpoint Manager.xlsx)|

## Technical Test

After configuration of Endpoint Manager is complete, including standards apps, profiles and policies, an engineer enrolls a device to test the setup. Basically, the following activities are carried out to test the configuration. This test can be done on a virtual machine that is enrolled in the customer environment for smoke testing, for a complete technical test a laptop or desktop is required as Pre-provisioning or self-deploying enrollment requires physical hardware and cannot be tested on virtual machines.

* Import the hardware hash for the test device and check if the dynamic groups are populated and enrollment profile is assigned.
* Enroll the device into Intune, check whether the enrollment succeeds.
* Check if all policies, profiles and apps are successfully applied.
* Login into the device and check basic functionality.

At the end, the device is wiped and should return to the default, unenrolled state.

The engineer adds the test results and findings (if any) in the release document, that was created at the end of the build phase.

## Acceptance Test

For acceptance, the complete enrollment - including Pre-provisioning - is tested in a production like setup. If devices are prepared (pre-provisioned) by a third party hardware reseller, involve them in the test process. Let a business user perform the acceptance test and report results and any findings (when applicable). Depending on the finding, the following actions must be taken:

* Result is in line with the design but reported as a defect: Discuss with customer (intake), optionally update the design (Design) and adapt change in the configuration (Build). Test the change (Technical Test) and offer the user for retest after implementing the change (Acceptance Test).
* Result is not in line with the design which means a defect. Reconfigure the setup (Build), test the change (Technical test) and offer the user for retest after implementing the change (Acceptance Test).
* Finding cannot be reproduced. Ask tester for additional information. Update instructions and/or close finding when this is a false finding.

## Deployment

The configuration is complete, both technically and functional tested and approved. For onboarding the organization, the project plans and performs the rollout after which the environment is handed over to Run. The following deliverables must be handed over:

* Complete and up-to-date designs.
* Any customer specific instructions regarding the configuration.
* Technical Release document.
* Technical resources, such as scripts and application packages.
