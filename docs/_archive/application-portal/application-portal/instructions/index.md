# Portal Generic Recipes

## Initial configuration

After configuration of the server VMs (including SSL certificate), the database and the loadbalancer, and the installation of Liquit Workspace, the application portal must be configured for first use. Using the PowerShell module for Liquit Workspace, the initial configuration is automated. This instruction explains what is configured and how you need to run the configuration script.

The scripts first connects to Liquit Workspace using the Liquit PowerShell module. It then does the following, initial configuration:

* Create base applications for SSW (like Office, Adobe Reader, 7-Zip, etc).
* Security Settings
* Credential template for AzureAD account in the Credential Store
* Base configuration for Portal, User and Login
* Creation of standard roles (Access Policies)

### Input Parameters

The following parameters are used by the script:

* **jsonPath**, mandatory. Full path to a json file containing the configuration for initial setup
* **LiquitZone**, mandatory. Zone for the Liquit Workspace environment.

### Output

The scripts generates the following output:

* A Liquit package for each line item in the json file
* Initial security configuration
* Initial configuration for Portal, Login and User
* Credential for use in packages, based on the Azure AD UPN
* Standard roles (Level 1, ServiceDesk and Level 2-3)
* A log file in the temp folder (initLiquit.log).

### How to use

* Download and install the Liquit PowerShell module. You can download the module [here](https://www.liquit.com/support/){target=_blank}.
* Download the Liquit Workspace configuration scripts: [![zip](/assets/img/zip.png)](/assets/resource/LiquitConfig.zip). The zip file contains the following items:
    1. InitLiquit.ps1 - PowerShell script for initial configuration
    2. LiquitConfig.json - input configuration file
    3. icon folder, containing icons for the standard apps
    4. readme.md - instructions
    5. checkLiquit.ps1 - PowerShell script for checking the default configuration
* Copy the PowerShell script(s), json file and icon folder to your workstation.
* The scripts creates packages for the SSW base apps (Office, 7Zip, Adobe Reader) and assigns these to a group. Update the json file to reflect the group that is used for 'SSW Users' in your environment. In the json, look for *"APP Portal Test Users"* and change this to the display name of the proper group.
* Run the script with the command: *initLiquit.ps1 -jsonPath {path to LiquitConfig.json} -LiquitZone {Liquit zone}*. For example:  
*initLiquit.ps1 -jsonPath .\\LiquitConfig.json -LiquitZone portal.contoso.com*
* When the script runs, you must login to Liquit using an account with administrator privileges. In the login screen, for example, use **LOCAL\\username** and the password to login with a LOCAL account.
* You can run the script multiple times. If resources already exist, they will be skipped or updated.
