# WAS-IHS-Migration

IBM HTTP Server Migration (8.5.5.15 to 9.0.011) 

 

Prerequisites: 

Take the backup of 8.5 setup. 

 
 Process: 

 Install the IHS9.0 and apply the latest fixpacks (9.0.0.11) 

Take the backup of IHS9 backup  

 

 

Migration: 

Copy the conf file from 8.5 setup to 9.0 setup 

i.e: cp /opt/IBM/HTTPServer/conf/merchantDev.conf /IHS9/IBM/HTTPServer/conf 

     2.  Go to the /IHS9/IBM/HTTPServer/conf and open the merchantDev.conf file with vi editor and do the below changes. 

Do the global replacement of /opt with /IHS9 with below command. 

i.e     :%s#/opt/IBM#/IHS9/IBM#g 

 

Replace all instances of authz_default_module module with authz_core_module 

i.e:  For example, your code might contain the following line: 

       LoadModule authz_default_module modules/mod_authz_default.so  

       After updating, the line has the following changes: 

       LoadModule authz_core_module modules/mod_authz_core.so 

 

Add the following line to load the mod_access_compat module 

LoadModule access_compat_module modules/mod_access_compat.so 

 

Add the following line to load the mod_unixd security module 

LoadModule unixd_module modules/mod_unixd.so 

 

Change the LoadModule line to update the Web Server Plug-ins from Apache 2.2 to Apache 2.4 

LoadModule was_ap24_module IHS9/IBM/WebSphere /Plugins/bin/64bits/mod_was_ap24_http.so  

 

Update third-party modules to the Apache 2.4 versions. Contact the third-party module vendor for specific instructions like DynaAgents. 

 

Search your configuration for directives, such as Include, AuthUserFile, AuthGroupFile, and KeyFile, that might point to files from the old installation root that need to be copied 

 

1 ) Change the ReportInterval time from 600 to 300 in conf file with vi editor. 

ReportInterval 300  

 

Change the Listen port number and Virtual port number from 81 to 9070 

Before: 

Listen 10.189.101.138:81 

<VirtualHost 10.189.101.138:81> 

After: 

Listen 10.189.101.138:9070 

<VirtualHost 10.189.101.138:9070> 

 

2 ) Change the User and group in .conf file 

Before: 

User nobody 

Group nobody 

 

After: 

User wasadmin 

Group was 

 

3 ) Change log path of IHS logs(error_log, ssl_access.log, pid.log) 

 

i.e /IHS9/IBM/HTTPServer/logs/sbiimeruatw18 

 

 4 ) And create the directory with hostname(sbiimeruatw18) in /IHS9/IBM/HTTPServer/logs path. 

 

5 ) Create the directory with hostname(sbiimeruatw18) in /IHS9/IBM/WebSphere/Plugins/config path. 

 

6 ) Copy the Plugin, kdb file from /opt/IBM/WebSphere/Plugins/config/sbiimeruatw18 to /IHS9/IBM/WebSphere/Plugins/config/sbiimeruatw18 

 

7 ) Make sure that copied plugin and kdb files should have wasadmin ownership and 755 permissions. 

 

8 ) Create the directory with hostname(sbiimeruatw18) in /IHS9/IBM/WebSphere/Plugins/logs path. 

 

9 ) Change the Plugin log path from /opt/IBM/WebSphere/Plugins/logs/ sbiimeruatw18 to /IHS9/IBM/WebSphere/Plugins/logs/ sbiimeruatw18 with vi editor. 

 

10 ) Stop the IHS 8.5 running conf with root user. 

 

11 ) Start the webinstance with wasadmin user and check logs of both web and plugin for start info. Or errors. 

 

12 ) Inform to Support team & respective stakeholders to check the application functionality thoroughly. 

==================================================================================================================================================================================================================

IM (Installation Manager installation:
========================================

[wasadmin@WSINBOAOSTAGINGDMZ01UAT IM]$ ./userinstc -c
Preprocessing the input.
Loading repositories...
Preparing and resolving the selected packages...


=====> IBM Installation Manager> Install

Select packages to install:
     1. [X] IBM® Installation Manager 1.8.9.4

     O. Check for Other Versions, Fixes, and Extensions

     N. Next,      C. Cancel
-----> [N] N
Validating package prerequisites...


=====> IBM Installation Manager> Install> Licenses

Read the following license agreements carefully.
View a license agreement by entering the number:
     1. IBM Installation Manager - License Agreement

Options:
     A. [ ] I accept the terms in the license agreement
     D. [ ] I do not accept the terms in the license agreement

     B. Back,      C. Cancel
-----> [C] A


=====> IBM Installation Manager> Install> Licenses

Read the following license agreements carefully.
View a license agreement by entering the number:
     1. IBM Installation Manager - License Agreement

Options:
     A. [X] I accept the terms in the license agreement
     D. [ ] I do not accept the terms in the license agreement

     B. Back,      N. Next,      C. Cancel
-----> [N] N


=====> IBM Installation Manager> Install> Licenses> Location

Installation Manager installation location:
        /home/wasadmin/IBM/InstallationManager/eclipse

Options:
     L. Change Installation Manager Installation Location

     B. Back,      N. Next,      C. Cancel
-----> [N] L


=====> IBM Installation Manager> Install> Licenses> Location> Summary

Target Location:
  Package Group Name         :  IBM Installation Manager
  Installation Directory     :  /IHS9/IBM/InstallationManager/eclipse

Packages to be installed:
        IBM® Installation Manager 1.8.9.4

Options:
     G. Generate an Installation Response File

     B. Back,      I. Install,      C. Cancel
-----> [I] I
                 25%                50%                75%                100%
------------------|------------------|------------------|------------------|
............................................................................

=====> IBM Installation Manager> Install> Licenses> Location> Summary>
  Completion

The install completed successfully.

Options:
     R. Restart Installation Manager
-----> [R] R
Preprocessing the input.

=====> IBM Installation Manager

Select:
     1. Install - Install software packages
     2. Update - Find and install updates and fixes to installed software packages
     3. Modify - Change installed software packages
     4. Roll Back - Revert to an earlier version of installed software packages
     5. Uninstall - Remove installed software packages

Other Options:
     L. View Logs
     S. View Installation History
     V. View Installed Packages
        ------------------------
     P. Preferences
        ------------------------













==================================================================================================================================================================================================================

WAS9.0 installation:
===================

TO find the package:
====================


./imcl listAvailablePackages -repositories /WAS9/software9/FixpackWAS9

./imcl listAvailablePackages -repositories /WAS9/software9/java9




Package names:
==============
com.ibm.websphere.ND.v90_9.0.0.20160526_1854


com.ibm.java.jdk.v8_8.0.3000.20160623_1418


To Installation:
===============

./imcl install  com.ibm.websphere.ND.v90_9.0.0.20160526_1854 com.ibm.java.jdk.v8_8.0.3000.20160623_1418 -repositories "/WAS9/softwares/FixpackWAS9","/WAS9/softwares/java9" -installationDirectory "/WAS9/IBM/WebSphere/AppServer" -sharedResourcesDirectory "/WAS9/IBM/IMShared" -acceptLicense -sP



To find the fixpack package name:
================================

./imcl listAvailablePackages -repositories /WAS9/software9/FixpackWAS9/9.0.0.11



To Install the fixpack:
=======================

./imcl install com.ibm.websphere.ND.v90_9.0.11.20190312_2048 -repositories "/WAS9/software9/FixpackWAS9/9.0.0.11" -installationDirectory "/WAS9/IBM/WebSphere/AppServer" -sharedResourcesDirectory "/WAS9/IBM/IMShared" -acceptLicense -sP





IHS9.0 installation:
===================


To find the package name:
========================

./imcl listAvailablePackages -repositories /doc-root/Softwares/WAS_9/IHS

Package names:
==============

com.ibm.websphere.IHS.v90_9.0.0.20160526_1854
com.ibm.java.jdk.v8_8.0.3000.20160623_1418 


T0 Install the WebServer with non-root user:
===========================================

./imcl install com.ibm.websphere.IHS.v90_9.0.0.20160526_1854 com.ibm.java.jdk.v8_8.0.3000.20160623_1418 -repositories "/doc-root/Softwares/WAS_9/IHS","/doc-root/Softwares/WAS_9/java9" -installationDirectory /IHS9/IBM/HTTPServer -sharedResourcesDirectory "/IHS9/IBM/IMShared" -properties  user.ihs.allowNonRootSilentInstall=true,user.ihs.httpPort=80 -acceptLicense -sP


To find the package name:
========================

./imcl listAvailablePackages -repositories /doc-root/Softwares/WAS_9/PLUGIN


Package names:
==============

com.ibm.websphere.PLG.v90_9.0.0.20160526_1854 
com.ibm.java.jdk.v8_8.0.3000.20160623_1418 

To Install Plugin:
==================

./imcl install com.ibm.websphere.PLG.v90_9.0.0.20160526_1854 com.ibm.java.jdk.v8_8.0.3000.20160623_1418 -repositories "/doc-root/Softwares/WAS_9/PLUGINS","/doc-root/Softwares/WAS_9/java9" -installationDirectory /IHS9/IBM/WebSphere/Plugins -sharedResourcesDirectory "/IHS9/IBM/IMShared" -acceptLicense -sP



To find the fixpack package name of IHS and Plugin:
==================================================

./imcl listAvailablePackages -repositories /doc-root/Softwares/WAS_9/FP_IHS

com.ibm.websphere.IHS.v90_9.0.11.20190312_2048
com.ibm.websphere.PLG.v90_9.0.11.20190312_2048


TO Install the fixpack on IHS:
=============================

./imcl install com.ibm.websphere.IHS.v90_9.0.11.20190312_2048 -repositories "/doc-root/Softwares/WAS_9/FP_IHS" -installationDirectory "/IHS9/IBM/HTTPServer"  -sharedResourcesDirectory "/IHS9/IBM/IMShared" -acceptLicense -sP


TO Install the fixpack on Plugin:
================================

./imcl install com.ibm.websphere.PLG.v90_9.0.11.20190312_2048 -repositories "/doc-root/Softwares/WAS_9/FP_IHS" -installationDirectory "/IHS9/IBM/WebSphere/Plugins" -sharedResourcesDirectory "/IHS9/IBM/IMShared" -acceptLicense -sP


=============================================================================================================================================================================================================================
                               ********************************************************* WAS Migration *****************************************************************************
============================================================================================================================================================================================================================

IBM WAS Migration (8.5.5.15 to 9.0.011) 

Prerequisites: 

Perform the housekeeping of server(profiles) like wstemp_bkp, temp_bkp, translog_bkp, javacore.*,Snip.*,heap.*coredump.*, from path= /opt/IBM/WebSphere/AppServer/profiles/Dmgr01  

Remove è/opt/IBM/WebSphere/AppServer/profiles/Dmgr01/bin/” WebSphereConfig_2019-05-11.zip” 

Remove è/opt/IBM/WebSphere/AppServer/profiles/AppSrv01/bin/” WebSphereConfig_2019-05-11.zip” 

Capture the Screen Shots of JVM’s Status. 

Capture the Screen Shots of Applications (war files) Status. 

Capture the Screen Shots of DataSource Test Connection. 

Ensure all the JVM’s are pointing to JDKv1.8 

Ensure all the Dmgr & NodeAgent are pointing to JDKv1.8 

Ensure that Protocol is TLSv1.2 and Security Level is “HIGH” 

 

Process: 

Install the WAS9.0 and apply the latest fixpacks (9.0.0.11) 

Create the Profiles like Dmgr01, AppSrv01 as per WAS8.5 setup in WAS9.0 

Take the backup of WAS9.0 manually. 

Take the backup of 8.5.5 manually( .tar) 

Take the backup config in each profile by the command ./backupConfig.sh -nostop 

 

Migration: 

Start the migration of profiles with Deployment Manager Node (DMGR). 

 

Pre-Migration for Dmgr: 

Go to the WAS9.0 <WAS_HOME>/bin( i.e. /opt/IBMWAS9/WebSphere/AppServer/bin) 

Then execute the below command. 

Syntax: - ./WASPreUpgrade.sh <path of WAS8.5 backup> <path of 8.5 was-home> -traceString *=all=enabled -summaryLogName <path of WAS8.5 backup>/logs/WASPreMigrationSummary.log -oldProfile <Profile Name> 

 

Example command for DMGR profile: 

 

./WASPreUpgrade.sh /WAS9/WAS8.5_backup /opt/IBM/WebSphere/AppServer -traceString *=all=enabled -summaryLogName /WAS9/WAS8.5_backup/logs/WASPreMigrationSummary.log -oldProfile Dmgr01 -username wasadmin -password wasadmin 

 

Tail the below log file while executing the above command to check for status, errors or any warnings. 

tail –f  /WAS9/WAS8.5_backup/logs/WASPreMigrationSummary.log 

 

Refer the below screenshot for successful Pre-Migration. 

 

Post-Migration:  

 Go to the WAS9.0 <WAS_Home/bin>( i.e. /opt/IBMWAS9/WebSphere/AppServer/bin) 

Then execute the below command. 

Syntax:-  ./WASPostUpgrade.sh <Path of WAS8.5 backup> -profileName <profile Name> -traceString *=all=enabled -summaryLogName <Path of WAS8.5 backup>/logs/WASPostMigrationSummary.log -oldProfile <profile Name> -setPorts useOld -backupConfig TRUE -includeApps TRUE  -keepDmgrEnabled TRUE –username <username> –password <pasword> 

 

Example command for DMGR profile: 

 

./WASPostUpgrade.sh /WAS9/WAS8.5_backup -profileName Dmgr01 -traceString *=all=enabled -summaryLogName /WAS9/WAS8.5_backup/logs/WASPostMigrationSummary.log -oldProfile Dmgr01 -setPorts useOld -backupConfig TRUE -includeApps TRUE -keepDmgrEnabled TRUE -username wasadmin -password wasadmin 

Tail the below log while executing the above command to check the errors. 

tail –f  /WAS9/WAS8.5_backup/logs/WASPostMigrationSummary.log 

 

Refer the below screenshot for successful Post-Migration. 

 

 

 

Now start the Migration for Federated profiles like AppSrv01, AppSrv02, etc.(residing on same node machine or different node machine). 

 

Note: Ensure your DMGR Server is up and Running to run the pre-migration & post migration command. 

 

    Pre-migration for Federated profiles 

Go to the WAS9.0 <WAS_Home>/bin ( i.e. /opt/IBMWAS9/WebSphere/AppServer/bin) 

Then execute the below command. 

Syntax: - ./WASPreUpgrade.sh <path of WAS8.5 backup> <path of 8.5 was-home> -traceString *=all=enabled -summaryLogName <path of WAS8.5 backup>/logs/WASPreMigrationSummary.log -oldProfile <Profile Name> 

 

Example command for AppServer profile: 

 

./WASPreUpgrade.sh /WAS9/WAS8.5_backup /opt/IBM/WebSphere/AppServer -traceString *=all=enabled -summaryLogName /WAS9/WAS8.5_backup/logs/WASPreMigrationSummary.log -oldProfile Appsrv01 -username wasadmin -password wasadmin 

 

 

Tail the below log while executing the above command to check the errors. 

tail –f  /WAS9/WAS8.5_backup/logs/WASPreMigrationSummary.log 

 

Refer the below screenshot for successful Pre-Migration. 

 

 

 

Post-Migration:  

 Go to the WAS9.0 <WAS_HOME>/bin( i.e. /opt/IBMWAS9/WebSphere/AppServer/bin) 

Then execute the below command. 

Syntax:-  ./WASPostUpgrade.sh <Path of WAS8.5 backup> -profileName <profile Name> -traceString *=all=enabled -summaryLogName <Path of WAS8.5 backup>/logs/WASPostMigrationSummary.log -oldProfile <profile Name> -setPorts useOld -backupConfig TRUE -includeApps TRUE  -keepDmgrEnabled TRUE –username <username> –password <pasword> 

Example command for AppServer profile: 

 

./WASPostUpgrade.sh /WAS9/WAS8.5_backup -profileName Appsrv01 -traceString *=all=enabled -summaryLogName /WAS9/WAS8.5_backup/logs/WASPostMigrationSummary.log -oldProfile Appsrv01 -setPorts useOld -backupConfig TRUE -includeApps TRUE -keepDmgrEnabled TRUE -username wasadmin -password wasadmin 

 

 

Tail the below log while executing the above command to check the errors. 

tail –f  /WAS9/WAS8.5_backup/logs/WASPostMigrationSummary.log 

 

Refer the below screenshot for successful Post-Migration. 

 

 

 

Preform the below checks after Migrating all the Profiles. 

Copy the commonconfig and appjars to /WAS9/IBM/WebSphere/AppServer/profiles 

Check the JVM’s status as per the previously captured screen shots. 

Check the applications status as per the previously captured screen shots. 

Check the status of DataSource Test Connections as per the previously captured screen shots. 

Inform to Support team & respective stakeholders to check the application functionality thoroughly. 

Update the WAS path in warbuild.sh script  to take the backup of existing war file with help of warbuild.sh(/home/wasadmin) 

Change WAS9 path of for loop while start JVM’s with help of for loop. 

   
