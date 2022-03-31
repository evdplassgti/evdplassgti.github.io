# Manage End User Experience in Run

## Introduction

Nexthink is the SSW solution to monitor User Experience in Smart Workspace environments. This Standard Operation Procedure (SOP) describes recurring activities to maintain the service after going live.

## Standard OPS Activities

The following (standard) activities must be carried out by the OPS team. Activities are mentioned per customer.

### Daily Activities

**L1: Check dashboards (15 minutes)**
On a daily basis, check the Nexthink dashboards to get the status of the workspace environment:

* Digital Experience  
Check the dashboard on all categories (Overall, Device, Productivity- and Business Applications. Look at the drift/trend in the widget ‘Experience overtime’. If experience drops significantly, take action to investigate cause and raise incident(s) to solve the issues.
* Level 2, Proactive improvement  
Check the overview dashboard to notice any deviations that require action. The ‘Trend’ widget can help here.
* Office 365 Health Services  
Check the overview dashboard if all Office 365 services report ‘Green’ and the number of devices with issues is acceptable (<5 % of total).  
Click on each of the services to get detailed information.

**L1: Alerts (1 minute)**  
Check if any alerts are mentioned in the web portal or Finder. (Note: integration with Service Now needs to be established).

**L4: Engines (5 minutes)**  
Check if all engines are up and running

### Weekly Activities

**L3: Check dashboards (30 minutes)**  
On a weekly basis, check the Nexthink dashboards to get the status of the workspace environment:

* Experience Optimization Dashboard  
Check experience on all categories (Overall, Device, Productivity- and Business Applications. Look at the drift/trend in the widget ‘Experience overtime’. If experience drops significantly, take action to investigate cause and raise incident(s) to solve the issues.
* Level 2, Proactive improvement  
Check the overview dashboard to notice any deviations that require action. The ‘Trend’ widget can help here.  
Go through the dashboard for each category to be sure no issues exist which require attention.  
* Office 365 Health Services  
Check the overview dashboard if all Office 365 services report ‘Green’ and the number of devices with issues is acceptable (<5 % of total).  
Click on each of the services to get detailed information, to be sure no issues exist which require attention.
* SSW – Version and Compliance  
Check if all SSW-managed apps are up to date. If not:  
-> Update version categorization in Finder (when a new version of the app is released)  
-> Investigate issues when deviation is >5%  
Check if devices are rebooted within acceptable time frames. If not (> 5% 7 days or longer active without reboot) report to service manager.  
Check if devices are updated within acceptable time frames. If not (> 5% 7 days or longer active without update) investigate why updates are not being processed.

**L4: Nexthink Service health (15 minutes)**
On a weekly basis, check the Nexthink portal to get the status of the environment:

* Computations  
Check if the daily scheduled computations do not report errors or warnings. Investigate issues when errors or warnings are mentioned.
* License check  
Check if enough licenses are available to support current and future enrollment of devices. Get information from service manager on planned roll outs. Take the following actions:  
-> Clean up licenses for multiple instances of the same device (can happen when the device is re-enrolled) by removing inactive devices.  
-> Inform service manager if the number of consumed licenses reaches the total (after clean-up).  
* Collector deployment  
In Intune, check no issues exist for deployment of the collector to managed Windows devices.

### Monthly Activities

**L4: Check dashboards (60 minutes)**
On a monthly basis, check the Nexthink dashboards to get the status of the workspace environment:

* Experience Optimization Dashboard  
Check experience on all categories (Overall, Device, Productivity- and Business Applications. Look at the drift/trend in the widget ‘Experience overtime’. If experience drops significantly, take action to investigate cause and raise incident(s) to solve the issues.
* Level 2, Proactive improvement  
Check the overview dashboard to notice any deviations that require action. The ‘Trend’ widget can help here.  
Go through the dashboard for each category to be sure no issues exist which require attention. Initiate actions for improvement when applicable.
* Office 365 Health Services  
Check the overview dashboard if all Office 365 services report ‘Green’ and the number of devices with issues is acceptable (<5 % of total).  
Click on each of the services to get detailed information.
* SSW – Version and Compliance
Check if all SSW-managed apps are up to date. If not:  
-> Update version categorization in Finder (when a new version of the app is released)  
-> Investigate issues when deviation is >5%  
Check if devices are rebooted within acceptable time frames. If not (> 5% 7 days or longer active without reboot) report to service manager.  
Check if devices are updated within acceptable time frames. If not (> 5% 7 days or longer active without update) investigate why updates are not being processed.

### Other (unplanned) activities

**L4: Lifecycle management**  
As Nexthink is a SaaS solution, the vendor will periodically update the back end and agent. When an update has taken place, do the following checks:

* Check if engines are up and running.
* Check if computations do not show errors or warnings.
* Check if hierarchy and rule-based collector assignment are working properly.
* If rules are not applied correctly, this will sometimes self-correct when the active ruleset is deactivated and re-activated once. Make sure to wait for approx. 5 minutes after deactivating the ruleset before reactivating it.
* Check if Services, Metrics, Categories and Scores do not show errors. Correct errors and/or initiate a support call with Nexthink support.
* Check if all dashboards show correctly in the web portal.
