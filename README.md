<p align="center">
<img src="https://i.imgur.com/pU5A58S.png" alt="Microsoft Active Directory Logo"/>
</p>

<h1>Group Policy Management and Inspecting Event Viewer logs (Azure)</h1>
This tutorial outlines the how to do group policy management in Active Directory and briefly touching on Event Viewer within Azure Virtual Machines.<br />


<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- Active Directory Domain Services
- Event Viewer

<h2>Operating Systems Used </h2>

- Windows Server 2022, at least 2vCPUs, 8GB RAM
- Windows 10 Pro (21H2), at least 2vCPUs, 8GB RAM

<h2>High-Level Deployment and Configuration Steps</h2>

- Create some users within the domain
- Configure group policy account lockout settings
- Enabling and disabling accounts
- Observing logs

<h2>Deployment and Configuration Steps</h2>

<p>
Create users in Active Directory Users and Computers (or run dsa.msc). Right-click _EMPLOYEES > New > User and fill in the user details.  Then, open the Group Policy Management Console (gpmc.msc).
</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/6bdfa1f6-b9a4-498c-b968-1a05a5d77bc5" height="80%" width="80%" alt="gpmc"/>
  <img src="https://github.com/user-attachments/assets/929bf199-4d39-4ebb-b2ff-1e7acd2910a3" height="80%" width="80%" alt="gpmc landing page"/>
</p>
<p>
Edit the "Default Domain Policy" GPO:  Navigate to 
Computer Configuration > Policies > Windows Settings > Security Settings > Account Policies > Account Lockout Policy.
</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/ffa19f3f-7511-4e0b-8eef-0d1d152b6652" height="80%" width="80%" alt="account lockout policies"/>
</p>
<p>
Some defintions:
<p><b>Account lockout duration</b>: determines how long a locked account remains inaccessible.</p>
<p><b>Account lockout threshold</b>: defines the number of incorrect login attempts before an account is locked. </p>
<p><b>Allow administrator account lockout</b>: setting determines whether the built-in Administrator account is subject to the account lockout policy, preventing potential brute-force attacks.</p>
<p><b>Reset account lockout counter after</b>: determines the time it takes for the failed login attempt counter to reset after an incorrect password entry</p>
<p>In this case we can set the lockout duration to 30min, threshold to 5 attempts, leave the administrator account lockout as not defined and the reset duration to 10min. 
<p>Before testing the account lockout policy, we need to ensure Client-1 has the latest Group Policy settings:

    Log in to Client-1 as an administrator.
    Open either the command prompt or PowerShell as an administrator.
    Run the command gpupdate /force to update the Group Policy settings.</p>

<img src="https://github.com/user-attachments/assets/375c8ce7-40b8-4c39-9ae0-9f2f9caedb37" height="80%" width="80%" alt="gpudpate force"/>

 <p> Update Group Policy on Client-1: log in as an administrator and run 
gpupdate /force.  Then, attempt to log in with incorrect credentials for any user more than 5 times to trigger the account lockout.</p>
</p>
<br />

<p>
  <img src="https://github.com/user-attachments/assets/2a82e323-69d5-45ca-b389-c93936e1a4a6" height="80%" width="80%" alt="account lockout banner"/>
</p>
<p>
Let's unlock the account:

    Log in to the domain controller (DC-1) as an administrator.
    Open Active Directory Users and Computers.
    If you can't easily find the locked user, right-click on mydomain.com and select "Find..." to search for the user.
    Once you've located the user, right-click on their account and select "Unlock Account."
</p>
<br />
<p>
  <img src="https://github.com/user-attachments/assets/395190cc-0168-4744-b1af-a6a2dca191f5" height="80%" width="80%" alt="find locked out user"/>
</p>
<p>
Unlock the user's account by checking the "Unlock account" box in their account properties. You can also reset their password (and unlock the account) by right-clicking the user and selecting "Reset password."
</p>
<br />
<p>
  <img src="https://github.com/user-attachments/assets/57f2848d-c80d-421b-9302-39126d83d7f0" height="50%" width="50%" alt="unlock account"/>
  <img src="https://github.com/user-attachments/assets/6ce6b46a-6923-47b6-8d63-35fd80f65d06" height="50%" width="50%" alt="reset password"/>

</p>
<p>
Disable or re-enable a user account through the right-click menu. A disabled account is indicated by a black down arrow on its icon.  Accounts are typically disabled when an employee leaves or if an account is compromised.
</p>
<br />

<p>
  <img src="https://github.com/user-attachments/assets/f21cd9f9-94a2-4d0a-9321-d7ca629693d1" height="80%" width="80%" alt="disabled account"/>
</p>
<p>
To view security logs in Event Viewer (
eventvwr.msc), log in to Client-1 as an administrator. Navigate to Windows Logs > Security and filter the current log for Event IDs 4624 (successful logon) and 4625 (failed logon).
  
</p>
<br />
<p>
  <img src="https://github.com/user-attachments/assets/4530b589-126c-4c4a-af82-6fb925199f38" height="80%" width="80%" alt="filter current log"/>
  <img src="https://github.com/user-attachments/assets/c29ac1ac-86ee-425c-b89e-e57088b6dd88" height="80%" width="80%" alt="observing logs in event viewer"/>
</p>
<p>
Observe the 5 failed logon attempts in the security log. Unlock the account in Active Directory Users and Computers, then log in as the user to confirm access is restored.
</p>
