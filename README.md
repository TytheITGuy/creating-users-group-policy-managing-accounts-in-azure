<p align="center">
<img src="https://i.imgur.com/aMMGyHQ.jpeg" height="80%" width="80%" alt="Setting Up in Azure"/>
<br />

<h1>Creating Users, Group Policy, and Managing Accounts with Active Directory in Azure</h1>


<h2>Description</h2>
This project will demonstrate how to configure Remote Desktop for non-administrative users, automate user creation with PowerShell, and manage group policies. Additionally, I’ll cover account lockouts and log monitoring to simulate a real-life IT environment. Join me as I dive deeper into deploying Active Directory and enhancing system management skills!  <br/>
<br />

This is a continuation of the [Active Directory: Deploying Active Directory In Azure](https://github.com/TytheITGuy/deploying-active-directory) project. <br />
<br />



<h2>Environments and Utilities Used</h2>

- <b>Microsoft Azure</b>
- <b>Virtual Machines</b>
- <b>Remote Desktop Connection</b>
- <b>Active Directory</b>

<h2>Operating Systems Used </h2>

- <b>Windows Server </b>
- <b>Windows 10</b>

<h2>Project Walk-through:</h2>

<p align="center">
To start, log into client-1 as the domain admin (jane_admin), using Remote Desktop Connection:

![1- Logging in as Jane Admin](https://github.com/user-attachments/assets/d47ffcd1-2b6b-4063-b0df-170c6c475691)


I want to allow Remote Desktop for non-administrative users. So, right-click on the start button > System > on the right-side click "Remote Desktop" > "Select users that can remotely access this PC": 

![2- Remote System Settings](https://github.com/user-attachments/assets/ffe0d07c-7c91-4304-bbaa-758e04df0b66)



Here, enter "Domain Users" > Check Names > OK > OK. Now all domain users can access this client via Remote Desktop:  

![3- Adding Domain Users](https://github.com/user-attachments/assets/9bce3484-6492-402e-ade4-8e6767d4fbb1)


Next, we'll create a bunch of users in Powershell. To start, log into the DC (Domain Controller) as our admin (jane_admin). Once logged in, open Powershell ISE as an admin and start a new script:  <br/>

![4- opening powershell as admin](https://github.com/user-attachments/assets/39231df2-7b93-49b7-ace2-cbe3ddd9ba71)


Now, we can copy and paste the user creation script in the top section and click the "Run" button which has a green play symbol located in the top bar:  

![5- Copying script](https://github.com/user-attachments/assets/c814d394-e7fd-4198-b72d-f17499a506a4)



Powershell will begin to run the script and create random users and place them in the OU (Organizational Unit), we created in the previous section of this project, named "_EMPLOYEES". Here we will pick a user and log into client-1 using this username and the password the script sets for all of these users which is "Password1":  

![7-Picking random user](https://github.com/user-attachments/assets/0cf7a55b-147c-4c83-bea0-f478001ca0fa)


Now, log out of Jane's account on client-1. We can log back into client-1 using our newly created user, making sure to specify the context of which we want to log in by adding "mydomain.com\" before the username:  

![8-Logging back into client as random user](https://github.com/user-attachments/assets/1dd5ce02-5b32-4c5d-8950-307447ad8ab5)


Now, let's log out of this user, because we are going to set an account lockout group policy and attempt to lock this user out. Go back to our DC and right click the start button > Run > type "gpmc.msc" to bring up the Group Policy Managment Console:  

![9-Group policy run command](https://github.com/user-attachments/assets/7adfa34b-3127-4506-a9bd-3e1113fb6ab5)


Expand Domains > mydomain.com > select Default Domain Policy. Under Computer Configuration expand Policies > Window Settings > Security Settings > select Account Lockout Policy  

![10-Navigate to GP Management Editor](https://github.com/user-attachments/assets/e6bd1690-dc27-413c-af1b-57183aa49cf4)

Under Computer Configuration expand Policies > Window Settings > Security Settings > select Account Lockout Policy  

![11- Account Lockout Policy](https://github.com/user-attachments/assets/7608f711-bf1d-4413-b3ed-cc6dee91f537)

Here we'll select "Account lockout threshhold" and choose to make the account lockout after 5 invalid attempts. Make sure to hit Apply:  

![12- Defining the Policy](https://github.com/user-attachments/assets/7e9e3d30-97ec-4510-bbfc-39417a2c771d)


You can double-check this by going to the group policy settings and scrolling down to view the lockout policy: 

![13-Confirmation of Policy](https://github.com/user-attachments/assets/47ce27a7-ae15-431d-9d79-04eb98a3baab)


The policy will update automatically, but it will take some time. We can force the update on client-1 by signing into it as our admin and running "gpupdate /force" in the command line:  

![14- GP force update](https://github.com/user-attachments/assets/972224fd-a162-4ceb-aa08-802310a70cb4)



Now, we can sign out of client-1 and attempt to sign back into it with our user with an invalid password 5 times. A lockout message will appear:  

![15-bad logins](https://github.com/user-attachments/assets/65cec089-fdc6-418a-acd5-2779b000eb8f)

![16- bad logins result](https://github.com/user-attachments/assets/e98ae7bd-96df-488e-b6ca-1a34abc6ba92)


To unlock the users account, head back to the DC > Active Directory Users and Computers > mydomain.com > _EMPLOYEES > double-click on the locked out user > Account tab > check the "Unlock account" box:  

![17- Back in DC-1 unlocking](https://github.com/user-attachments/assets/9a31fc27-8e26-4c9c-9879-e3ec12818092)


To disable users, go to the DC and in Active Directory Users and Computers > mydomain.com > _EMPLOYEES > right-click on user you want to disable > Disable: 

![18- Disable a user](https://github.com/user-attachments/assets/30f0faaf-04fb-49e6-9b43-941a63ee0864)



If we sign out of client-1 now, and try to sign back in using the disabled account, this message will appear: 

![19-Disabled Account Login Attempt](https://github.com/user-attachments/assets/bd7afb56-0fc9-4355-83b1-64a6079340fa)


To enable the user again go back to the DC and right-click again on the user and select Enable



The user can now log into client-1. So we'll go back to DC 1 and  we'll view some logs through the event viewer. To do so, search for "Event Viewer" in the start search bar > Windows Logs > Security. 
Here we can see a bunch of different information like successful and failed log on attempts, who attempted them and from what IP address, along with the date and time they happened:  

![20- User Log Viewer](https://github.com/user-attachments/assets/0dc71dd7-2ec1-4570-b8a2-01a06335ef23)


<h2>Active Directory: Creating Users, Group Policy, and Managing Accounts in Azure is Now Complete </h2>

<b>We've successfully configured Remote Desktop for non-administrative users, automated user creation with PowerShell, and managed group policies. Additionally, we covered account lockouts and log monitoring to simulate a real-life IT environment!  </b>
<br />
<br />
</p>

<!--
 ```diff
- text in red
+ text in green
! text in orange
# text in gray
@@ text in purple (and bold)@@
```
--!>
