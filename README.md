# Azure Lab Setup Guide

## Setup Resources in Azure

1. **Create the Domain Controller VM (Windows Server 2022) named “DC-1”**:
   - Go to the Azure Portal.
   - Navigate to the Virtual Machines section.
   - Click on "Create" to start the VM creation process.
   - Choose Windows Server 2022 as the OS.
   - Provide a name for the VM (e.g., "DC-1").
   - Configure the necessary settings such as Region, Resource Group, and Virtual Network (Vnet).
   - Set the Domain Controller’s NIC Private IP address to be static during the VM creation process.

   ![Screenshot_1](https://github.com/gabe-IT/Azure-Domain-Controller-Setup/assets/148400020/4df2b540-f458-4186-b3f4-c3d87b07cdef)

2. **Create the Client VM (Windows 10) named “Client-1”**:
   - Follow similar steps as above but choose Windows 10 as the OS.
   - Ensure to use the same Resource Group and Vnet as created for the Domain Controller.
   - Verify that both VMs are in the same Vnet for connectivity.

   ![Screenshot_5](https://github.com/gabe-IT/Azure-Domain-Controller-Setup/assets/148400020/805aadd3-4900-4963-8e79-da220d478075)

## Ensure Connectivity between the Client and Domain Controller

1. **Ping Test**:
   - Log into Client-1 with Remote Desktop.
   - Open Command Prompt and ping DC-1’s private IP address with `ping -t <ip address>` for perpetual ping.

2. **Firewall Configuration**:
   - Log into the Domain Controller.
   - Enable ICMPv4 in the local Windows Firewall to allow ping requests.

   ![Screenshot_11](https://github.com/gabe-IT/Azure-Domain-Controller-Setup/assets/148400020/8efd863e-4a09-44d5-be93-ee73ca39079b)

3. **Verification**:
   - Return to Client-1 and check if the ping to DC-1's private IP address succeeds.

   ![Screenshot_12](https://github.com/gabe-IT/Azure-Domain-Controller-Setup/assets/148400020/1df688af-0a61-4776-a877-76b0a84fa28e)

## Install Active Directory

1. **Install AD DS**:
   - Log into DC-1.
   - Install Active Directory Domain Services using Server Manager or PowerShell.

   ![Screenshot_13](https://github.com/gabe-IT/Azure-Domain-Controller-Setup/assets/148400020/8deb5fd9-3a69-421f-b442-9b57c862a2a5)

2. **Promote as a Domain Controller**:
   - Promote DC-1 as a Domain Controller and set up a new forest as mydomain.com.
   - Restart the server and log back in as user: mydomain.com\labuser.

   ![Screenshot_15](https://github.com/gabe-IT/Azure-Domain-Controller-Setup/assets/148400020/1d02817e-6ad7-4769-9d45-df49380665a5)
   ![Screenshot_16](https://github.com/gabe-IT/Azure-Domain-Controller-Setup/assets/148400020/9751d447-f6b8-45de-a383-3d0741c79e34)
   ![Screenshot_17](https://github.com/gabe-IT/Azure-Domain-Controller-Setup/assets/148400020/4bb16fe8-167f-4cff-acce-509692e37fc1)

## Create Admin and Normal User Account in AD

1. **Create Organizational Units**:
   - Open Active Directory Users and Computers (ADUC) on DC-1.
   - Right-click on the domain name and select "New" -> "Organizational Unit".![Screenshot_20](https://github.com/gabe-IT/Azure-Domain-Controller-Setup/assets/148400020/19adde29-1ef4-4da3-8bf4-16a413e7f722)

   - Name the first OU as “_EMPLOYEES”.
   - Similarly, create another OU named “_ADMINS”.
![Screenshot_21](https://github.com/gabe-IT/Azure-Domain-Controller-Setup/assets/148400020/a7ae3ae0-5b4e-450c-9efc-2c173c17018e)

2. **Create User Accounts**:
   - In ADUC, right-click on the “_EMPLOYEES” OU and select "New" -> "User".
   - Enter the user details for a new employee.![Screenshot_22](https://github.com/gabe-IT/Azure-Domain-Controller-Setup/assets/148400020/02d80dd7-6f18-47c5-8c60-2081fb759168)

   - Set a password for the user account.
   - After creating the user, navigate to the “_ADMINS” OU and add “a-gabe” to the “Domain Admins” Security Group.![Screenshot_24](https://github.com/gabe-IT/Azure-Domain-Controller-Setup/assets/148400020/1a07500b-3c98-4a35-a92e-84b20a82803d)

   - Log out or close the Remote Desktop connection to DC-1 and log back in as “mydomain.com\a-gabe” to ensure proper access permissions.

## Join Client-1 to your Domain (mydomain.com)

1. **Configure DNS Settings**:
   - Go to the Azure Portal and navigate to Client-1's settings.![Screenshot_30](https://github.com/gabe-IT/Azure-Domain-Controller-Setup/assets/148400020/96595c86-6011-4142-98d5-b7206a081d9f)

   - Set Client-1’s DNS settings to the DC’s Private IP address.![Screenshot_31](https://github.com/gabe-IT/Azure-Domain-Controller-Setup/assets/148400020/a93c5bbd-0ce8-402a-b3f8-6caa459d42ad)

   - Restart Client-1 to apply the DNS changes.

2. **Join Domain**:
   - Log into Client-1 (Remote Desktop) as the original local admin (labuser).
   - Open the Control Panel, navigate to System and Security -> System -> Change settings -> Change -> Domain and enter the domain name (mydomain.com).![Screenshot_28](https://github.com/gabe-IT/Azure-Domain-Controller-Setup/assets/148400020/ef8e44ee-3fa1-4abf-9c9b-d6afca4cb22c)

   - Follow the prompts to join the domain. The computer will restart after joining the domain.
![Screenshot_34](https://github.com/gabe-IT/Azure-Domain-Controller-Setup/assets/148400020/59cf940b-7b12-4771-ba87-0c6bf55caaf0)

3. **Verification**:
   - Log into the Domain Controller (DC-1) via Remote Desktop.
   - Open ADUC and verify if Client-1 shows up in the “Computers” container under the domain.

## Setup Remote Desktop for Non-administrative Users on Client-1

1. **Configure Remote Desktop**:
   - Log into Client-1 as mydomain.com\jane_admin.
   - Open system properties by right-clicking on This PC -> Properties.
   - Click on "Remote settings" and select the "Remote" tab.![Screenshot_35](https://github.com/gabe-IT/Azure-Domain-Controller-Setup/assets/148400020/f04821a7-7f62-4f7c-8364-714eff63786a)

   - Check the box labeled "Allow remote connections to this computer".
   - Click "Select Users" and add the “domain users” group to allow access to remote desktop (Normally, we wouild use group policies for this, but this is out of the scope of this lab).![Screenshot_36](https://github.com/gabe-IT/Azure-Domain-Controller-Setup/assets/148400020/44430daa-557d-4215-8340-335f2f8e4d40)


2. **Access as Non-administrative User**:
   - Normal, non-administrative users can now log into Client-1 using Remote Desktop by providing their domain credentials.

## Create Additional Users and Attempt to Log into Client-1

1. **Create Users**:
   - Log into DC-1 as jane_admin.
   - Open PowerShell_ise as an administrator.
   - Run the provided PowerShell script ([Generate-Names-Create-Users.ps1](https://github.com/gabe-IT/AD-powershell/blob/main/Generate-Names-Create-Users.ps1)) to create additional user accounts.![Screenshot_38](https://github.com/gabe-IT/Azure-Domain-Controller-Setup/assets/148400020/1a8045a8-7c09-439b-9d0c-517458c66659)

   - Review ADUC to confirm the creation of new user accounts in the appropriate OU.
![Screenshot_41](https://github.com/gabe-IT/Azure-Domain-Controller-Setup/assets/148400020/aa43e05b-aaf6-4004-a6ad-75c3a8409e41)

2. **Verification**:
   - Attempt to log into Client-1 with one of the newly created accounts to ensure proper user access and authentication.![Screenshot_40](https://github.com/gabe-IT/Azure-Domain-Controller-Setup/assets/148400020/d3087ddd-6e31-4be6-8d2e-c7e19f8442d9)


## Finish

Congratulations! You have successfully set up and configured your Azure lab environment.
