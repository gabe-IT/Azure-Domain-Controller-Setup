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
   - In Active Directory Users and Computers (ADUC), create an Organizational Unit (OU) called “_EMPLOYEES”.
   - Create another OU named “_ADMINS”.

2. **Create User Accounts**:
   - Create a new employee named “Jane Doe” with the username of “jane_admin”.
   - Add jane_admin to the “Domain Admins” Security Group.
   - Log out/close the Remote Desktop connection to DC-1 and log back in as “mydomain.com\jane_admin”.

## Join Client-1 to your Domain (mydomain.com)

1. **Configure DNS Settings**:
   - From the Azure Portal, set Client-1’s DNS settings to the DC’s Private IP address.
   - Restart Client-1.

2. **Join Domain**:
   - Login to Client-1 (Remote Desktop) as the original local admin (labuser) and join it to the domain.
   - The computer will restart.

3. **Verification**:
   - Login to the Domain Controller (Remote Desktop) and verify if Client-1 shows up in Active Directory Users and Computers (ADUC).

## Setup Remote Desktop for Non-administrative Users on Client-1

1. **Configure Remote Desktop**:
   - Log into Client-1 as mydomain.com\jane_admin and open system properties.
   - Click “Remote Desktop” and allow “domain users” access to remote desktop.

2. **Access as Non-administrative User**:
   - Now, normal, non-administrative users can log into Client-1 using Remote Desktop.

## Create Additional Users and Attempt to Log into Client-1

1. **Create Users**:
   - Log into DC-1 as jane_admin.
   - Open PowerShell_ise as an administrator.
   - Create additional user accounts using a PowerShell script.

   [Generate-Names-Create-Users.ps1](https://github.com/gabe-IT/AD-powershell/blob/main/Generate-Names-Create-Users.ps1)

2. **Verification**:
   - After creating users, observe the accounts in the appropriate OU.
   - Attempt to log into Client-1 with one of the newly created accounts.

## Finish

Congratulations! You have successfully set up and configured your Azure lab environment.
