<p align="center">
<img src="https://i.imgur.com/NGHp7gq.png" alt="Microsoft Active Directory Logo"/>
</p>

<h1> Setting Up Azure Resources, Active Directory, and User Accounts (Azure)</h1>
This guide covers Azure resource setup, creating a domain controller VM, establishing client-DC connectivity, installing Active Directory, creating user accounts, joining the client to the domain, setting up remote desktop, and creating additional users for login.<br />

<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- Active Directory Domain Services
- PowerShell

<h2>Operating Systems Used </h2>

- Windows Server 2022
- Windows 10 (21H2)

<h2>Overview of Steps:</h2>

- Step 1: Azure Resources Setup<br>
- Step 2: Create VM1 VM<br>
- Step 3: Ensure VM1-DC Connectivity<br>
- Step 4: Install Active Directory<br>
- Step 5: Create Admin and User<br>
- Step 6: Join VM1 to Domain<br>
- Step 7: Setup Remote Desktop Access<br>
- Step 8: Create and Log into Users

<!DOCTYPE html>
<html>
  
<body>
  <h1>Azure Active Directory Setup Guide</h1>
  <ol>
    <li>
      <h2>Setup Resources in Azure</h2>
      <p>
      <img src="https://camo.githubusercontent.com/e44ee7a9857a42ee0601535958067f952bf7006154cc4b88bb9841bfcec653a5/68747470733a2f2f692e696d6775722e636f6d2f77654d753345432e706e67" height="80%" width="80%" alt="Disk Sanitization Steps"/>
      </p>
      <p>
        Create the Domain Controller VM (Windows Server 2022) named "DC"
        Take note of the Resource Group and Virtual Network (Vnet) created during this step.
        Set the Domain Controller's NIC Private IP address to be static.
      </p>
      <ol>
        <li>Navigate to DC in the Azure portal.</li>
        <li>Go to "Networking" and select "Network Interface."</li>
        <li>Click on the highlighted network interface.</li>
        <li>Go to "IP configurations" and click on the configuration.</li>
        <li>Change the IP address assignment from "Dynamic" to "Static" and save the settings.</li>
      </ol>
    </li>
    <li>
      <h2>Create the Client VM (Windows 10) named "VM1"</h2>
      <p>
      <img src="https://i.imgur.com/ZPVne2R.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
      </p>
      <p>
        Use the same Resource Group and Vnet created in Step 1.
        Ensure that both VMs are in the same Vnet by checking the topology with Network Watcher.
        Confirm that the virtual network/subnet settings are the same for both VMs.
      </p>
    </li>
    <li>
      <h2>Ensure Connectivity between the VM1 and Domain Controller</h2>
      <p>
      <img src="https://i.imgur.com/MOobBM5.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
      </p>
      <p>
        Login to VM1 using Remote Desktop and ping DC's private IP address with the command "ping -t &lt;ip address&gt;" to establish a perpetual ping.
        Login to the Domain Controller and enable ICMPv4 in the local Windows Firewall.
      </p>
      <ol>
        <li>Open "Inbound Rules" in the Windows Firewall.</li>
        <li>Sort by protocol and look for "ICMPv4."</li>
        <li>Enable the "Core Networking Echo Request" rule.</li>
      </ol>
      <p>Check VM1 to see if the ping to DC's private IP address is successful.</p>
    </li>
    <li>
      <h2>Install Active Directory</h2>
      <p>
      <img src="https://i.imgur.com/a9UU4fa.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
      </p>
      <p>
        Login to DC and install Active Directory Domain Services.
        Open the Server Manager application in Windows.
        Click on "Add Roles and Features" and proceed until "Server Roles."
        Select "Active Directory Domain Services" and install it.
        Promote DC as a Domain Controller.
      </p>
      <ol>
        <li>Launch the Server Manager application.</li>
        <li>Click on the flag in the top right corner and select "Promote this server to a domain controller."</li>
        <li>Follow the prompts to set up a new forest (e.g., "mydomain.com") and provide a password.</li>
        <li>Restart DC and log back in as the user "mydomain.com\DC" since it is now an Active Directory environment.</li>
      </ol>
    </li>
    <li>
      <h2>Create an Admin and Normal User Account in AD</h2>
      <p>
      <img src="https://i.imgur.com/IFMi9IA.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
      </p>
      <p>
        In Active Directory Users and Computers (ADUC), create an Organizational Unit (OU) called "_EMPLOYEES" within the domain.com.
        Launch Active Directory Users and Computers (ADUC) from the administrative tools.
        Create a new OU named "_ADMINS."
        Within "_ADMINS," create a new employee named "John Doe" (same password) with the username "john_admin."
        Add "john_admin" to the "Domain Admins" Security Group.
      </p>
      <ol>
        <li>Right-click on "john_admin," go to "Properties," select "Member Of," click "Add," look up the "Domain Admins," and add it. Apply the changes.</li>
        <li>Log out or close the Remote Desktop connection to DC and log back in as "mydomain.com\john_admin." Use this admin account for further actions.</li>
      </ol>
    </li>
    <li>
      <h2>Join VM1 to your domain (mydomain.com)</h2>
      <p>
      <img src="https://i.imgur.com/Eb2wpS7.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
      </p>
      <p>
        Set VM1's DNS settings to the DC's Private IP address from the Azure Portal.
      </p>
      <ol>
        <li>Go to VM1 in Azure portal, navigate to "Networking," and copy the NIC's private IP address.</li>
        <li>Go to VM2, access the "Networking" settings, select the virtual NIC, go to "DNS servers," choose "Custom," and input the private IP from VM1.</li>
        <li>Restart VM1 and log in to it using Remote Desktop as the original local admin (DC).</li>
        <li>Join VM1 to the domain, which will result in a restart.</li>
        <li>Login to the Domain Controller using Remote Desktop and verify that VM1 shows up in Active Directory Users and Computers (ADUC) within the "Computers" container at the root of the domain.</li>
        <li>Create a new OU named "_CLIENTS" and move VM1 into it.</li>
      </ol>
    </li>
    <li>
      <h2>Setup Remote Desktop for non-administrative users on VM1</h2>
      <p>
      <img src="https://i.imgur.com/Wt0UEon.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
      </p>
      <p>
        Log into VM1 as "mydomain.com\john_admin" and open system properties.
      </p>
      <ol>
        <li>Right-click the Start menu, go to "System," and open system properties.</li>
        <li>In the system properties, go to the "Remote" tab and select "Users" who can remotely access this PC.</li>
        <li>Click "Add," search for "domain users" and select it, then click "OK."</li>
      </ol>
      <p>Now, regular non-administrative users can log into VM1 using Remote Desktop.</p>
    </li>
    <li>
      <h2>Create additional users and attempt to log into VM1 with one of the users</h2>
      <p>
      <img src="https://i.imgur.com/eYKda1c.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
      </p>
      <p>
        Log in to DC as "john_admin." <br>
        Open PowerShell ISE as an administrator.<br>
        Create a new file and paste the contents of the script from this URL: <a href="https://github.com/joshmadakor1/AD_PS/blob/master/Generate-Names-Create-Users.ps1">https://github.com/joshmadakor1/AD_PS/blob/master/Generate-Names-Create-Users.ps1</a> <br>
        Run the script and observe the user accounts being created.<br>
        Once the script finishes, open ADUC and verify that the accounts are present in the appropriate OU.<br>
        Attempt to log into VM1 using one of the newly created user accounts (refer to the password mentioned in the script).
      </p>
    </li>
  </ol>
</body>
</html>
