# Active-Directory-Creating-Users-Group-Policy-and-Managing-Accounts
<p align="center">
<img src="https://i.imgur.com/aMMGyHQ.jpeg" height="80%" width="80%" alt="Setting Up in Azure"/>
<br />

<h1>Creating Users, Group Policy, and Managing Accounts with Active Directory in Azure</h1>


<h2>Description</h2>
This project will demonstrate how to configure Remote Desktop for non-administrative users, automate user creation with PowerShell, and manage group policies. Additionally, I’ll cover account lockouts and log monitoring to simulate a real-life IT environment. Join me as I dive deeper into deploying Active Directory and enhancing system management skills!  <br/>
<br />

This is a continuation of the [Active Directory: Deploying Active Directory in Azure](https://github.com/AH1791/Active-Directory-Deploying-Active-Directory) project. <br />
<br />
You can find the Powershell script I run in this project [here.](https://github.com/joshmadakor1/AD_PS/blob/master/Generate-Names-Create-Users.ps1) 
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
To start, log into client-1 as the domain admin (jane_admin), using Remote Desktop Connection: <br/>
 
![image](https://github.com/user-attachments/assets/cf2627ae-2300-4274-af74-2fb918f9ba9c)

<br />
<br />
I want to allow Remote Desktop for non-administrative users. So, right-click on the start button > System > on the right-side click "Remote Desktop" > "Select users that can remotely access this PC": <br/>

![image](https://github.com/user-attachments/assets/ca4a02f5-d785-427a-948e-260eb594b49b)

<br />
<br />
Here, enter "Domain Users" > Check Names > OK > OK. Now all domain users can access this client via Remote Desktop:  <br/>

 ![image](https://github.com/user-attachments/assets/b648d93c-4cbe-450b-8db6-17c7c19c488e)

<br />
<br />
Next, we'll create a bunch of users in Powershell. To start, log into the DC (Domain Controller) as our admin (jane_admin): <br/>

![image](https://github.com/user-attachments/assets/e7d74ec8-8d5d-43bb-90a1-e5198a77ba4d)

<br />
<br />
Once logged in, open Powershell ISE as an admin and start a new script:  <br/>
<img 
<br />
<br />
Use Ctrl + S and save this like so:  <br/>
<img>
<br />
<br />
Now, we can copy and paste the user creation script in the top section and click the "Run" button which has a green play symbol located in the top bar:  <br/>
<img 
<br />
<br />
Powershell will begin to run the script and create random users and place them in the OU (Organizational Unit), we created in the previous section of this project, named "_EMPLOYEES". We can check by searching "Active Directory Users and Computers" > mydomain.com > _EMPLOYEES. Here we will pick a user and log into client-1 using this username and the password the script sets for all of these users which is "Password1":  <br/>
<img 
<br />
<br />
Now, log out of Jane's account on client-1:  <br/>
<img 
<br />
<br />
We can log back into client-1 using our newly created user, making sure to specify the context of which we want to log in by adding "mydomain.com\" before the username:  <br/>
<img 
<br />
<br />
When we're logged in we can see a user folder for our user if we click on file explorer > C: > Users. We can also see the folder for the other users that have signed into this client. However, we cannot access them, because we don't have those permissions as a user:  <br/>
<img 
<br />
<br />
Now, let's log out of this user, because we are going to set an account lockout group policy and attempt to lock this user out:  <br/>
<img 
<br />
<br />
Go back to our DC and right click the start button > Run > type "gpmc.msc" to bring up the Group Policy Managment Console:  <br/>
<img 
<br />
<br />
Expand Domains > mydomain.com > select Default Domain Policy:  <br/>
<img 
<br />
<br />
Under Computer Configuration expand Policies > Window Settings > Security Settings > select Account Lockout Policy:  <br/>
<img 
<br />
<br />
Here we'll select "Account lockout threshhold" and choose to make the account lockout after 5 invalid attempts. Make sure to hit Apply:  <br/>
<img
<br />
<br />
You can double-check this by going to the group policy settings and scrolling down to view the lockout policy:  <br/>
<img 
<br />
<br />
The policy will update automatically, but it will take some time. We can force the update on client-1 by signing into it as our admin and running "gpupdate /force" in the command line:  <br/>
<img 
<br />
<br />
<img
<br />
<br />
Now, we can sign out of client-1 and attempt to sign back into it with our user with an invalid password 5 times. A lockout message will appear:  <br/>
<img
<br />
<br />
<img
<br />
<br />
To unlock the users account, head back to the DC > Active Directory Users and Computers > mydomain.com > _EMPLOYEES > double-click on the locked out user > Account tab > check the "Unlock account" box:  <br/>
<img 
<br />
<br />
(Alternatively you could right-click on the user > Reset Password and there will be a "Unlock users account" option. That way you can change the password at the same time you unlock it):  <br/>
<img 
<br />
<br />
Now our user can sign into clinet-1 as normal:  <br/>
<img 
<br />
<br />
To disable users, go to the DC and in Active Directory Users and Computers > mydomain.com > _EMPLOYEES > right-click on user you want to disable > Disable:  <br/>
<img 
<br />
<br />
If we sign out of client-1 now, and try to sign back in using the disabled account, this message will appear:  <br/>
<img 
<br />
<br />
To enable the user again go back to the DC and right-click again on the user and select Enable:  <br/>
<img 
<br />
<br />
The user can now log into client-1, so we'll do that and once we are logged in we'll view some logs through the event viewer. To do so, search for "Event Viewer" in the start search bar > Windows Logs > Security. You'll notice we cannot view these:  <br/>
<img 
<br />
<br />
This is because we are not an admin. Not to worry, we don't need to sign out of this whole client and sign in as Jane (our admin). We already know her credentials. We just need to close out of this window and open it again, but this time, as an administrator. It will have a pop-up asking for admin credentials:  <br/>
<img 
<br />
<br />
Now, if we navigate back to the security log page, we can view them:  <br/>
<img 
<br />
<br />
Here we can see a bunch of different information like successful and failed log on attempts, who attempted them and from what IP address, along with the date and time they happened:  <br/>
<img 
<br />
<br />
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
