# Active Directory Fundamentals

## Project Overview
This lab introduced me to the fundamentals of **Active Directory (AD)** within a simulated Windows enterprise environment. Through hands-on exercises, I explored domain structures, authentication processes, user enumeration, and common security misconfigurations. The project mirrors real-world AD environments used in corporate networks and lays the groundwork for both blue team and red team perspectives.

---

## Scenario Background

In this lab, I was tasked with administering and securing a corporate Active Directory environment modeled after a fictional organization. As part of a junior IT support role, my responsibilities included creating users and groups, managing permissions, applying security policies, and joining new hosts to the domain. These exercises simulate realistic tasks performed by sysadmins and IT support specialists in enterprise environments, preparing me for both defensive (blue team) and administrative duties in the field.

---

## Lab Objectives
- Understand the structure of Active Directory and how domain components are organized
- Create Organizational Units (OUs)  
- Add and remove users  
- Create and manage groups  
- Assign users to groups  
- Apply Group Policy Objects (GPOs)

--- 

## Key Skills & Learning
- Developed working knowledge of AD layout and command-line interaction
- Practiced user and group enumeration to support identity auditing
- Gained foundational understanding of Windows domain authentication
- Identified risks that arise from excessive privileges or weak group policy

---

## 1. Add New Users
### ADAC Method
- Open **ADAC** 
- Navigate to the IT OU
- INLANEFREIGHT.LOCAL → Corp → Employees → HQ-NYC → IT
- Right-click the IT OU → New → User
- Fill in user details for `Andromeda Cepheus`, `Orion Starchaser`, `Artemis Castillo`
- Attributes included are full name, email, display name and that the user must change password at next logon

### PowerShell Equivalent
```powershell
New-ADUser -Name "Artemis Castillo" -AccountPassword (ConvertTo-SecureString -AsPlainText(Read-Host "enter a secure password") -Force) -enabled $true -other attributes @{'title' "Analyst" ; 'mail' = "a.castillo@inlanefreight.local"}
```

<details>
  <summary> Add New User – Screenshots</summary>

  <br>

![Add New User – ADAC](/screenshots/add-user-adac.png)  
![Add New User – ADAC](/screenshots/add-user1-adac.png)
![Add New User – ADAC](/screenshots/add-user-adac-confirm.png) 

</details>

---

## 2. Delete a User
### ADAC Method
- Locate the user by right clicking on the "search under this node" option of the Employees OU.
- Search for employee "Paul Valencia"
- Right-click → Delete → Yes

### PowerShell Equivalent
```powershell
Remove-ADUser -Identity "pvalencia"
```

<details>
  <summary> Delete user – Screenshots</summary>

  <br>

![Delete User – ADAC](/screenshots/user-search-adac.png) 
![Delete User – ADAC](/screenshots/delete-user-adac.png) 

</details>

## 3. Unlock user
### ADAC Method
- Locate the user
- Right-click → Reset Password
- Set a new temporary password
- Ensure User must change password on next logon radial is selected
- Ensure unlock users' account radial is selected

### Powershell Equivalent
```powershell
Unlock-ADAccount -Identity amasters
```
```powershell
Set-ADAccount -Identity 'amasters' -Reset -NewPassword (ConvertTo-SecureString -AsPlaintext "NewP@sswordReset!" -Force)
```
```powershell
Set-ADUser -Identity amasters -ChangePasswordatlogon $true
```
<details>
  <summary> Unlock user – Screenshots</summary>

  <br>

![Unlock User – ADAC](/screenshots/unlock-and-change-pswd-adac.png)

</details>

---
## 4. Create an OU and Add a Group
### ADAC Method
- Right-click IT OU → New → Organizational Unit
- Name the OU Security Analysts
- Right-click on the Security Analysts OU → New → Group
- Name the Group Security Analysts
- Ensure the Group Scope is set to "Domain Local" and the Group Type is set to "Security"

### Powershell Equivalent
```powershell
New-ADOrganizationUnit -Name "Security Analysts" -Path "OU=IT,OU=HQ-NYC,OU=Employees,OU=Corp,DC=inlanefreight,DC=local"
```
```powershell
New-ADGroup -Name "Security Analysts" -SamAccoutName analyst -Groupcategory Security GroupScope Global -DisplayName "Security Analysts" -Path "OU= Security Analysts,OU=IT,OU=HQ-
NYC,OU=Employees,OU=Corp,DC=INLANEFREIGHT,DC=local" -Descritption "Members of this group are Security Analysts under the IT OU."
```

<details>
  <summary> Create OU and Group – Screenshots</summary>

  <br>

![Create OU – ADAC](/screenshots/adding-ou-adac.png)
![Create OU – ADAC](/screenshots/create-ou-adac.png) 
![Create OU – ADAC](/screenshots/adding-new-group-adac.png)
![Create Group – ADAC](/screenshots/create-group-adac.png)

</details>

---
## 5. Add Users to the Group
### ADAC Method
- Locate users to add to the group
- Right-click user → Add to Group → Specify Group

### Powershell Equivalent
```powershell
Add-ADGroupMember -Identity analysts -Members ACastillo, ACepheus, OStarchaser
```

<details>
  <summary> Add Users to Group – Screenshots</summary>

  <br>

![Add to Group – ADAC](/screenshots/add-user-to-group1.png)  
![Add to Group – ADAC](/screenshots/add-user-to-group2.png) 
![Add to Group – ADAC](/screenshots/add-to-group-adac-confirm.png)

</details>

--- 

## 6. Apply Group Policy to the OU using Powershell and Modify User Configurations via GPMC
### PowerShell
```powershell
Copy-GPO -SourceName "Logon Banner" -TargetName "Security Analysts Control"
```
```powershell
New-GPLink -Name "Security Analysts Control" -Target "OU= Security Analysts,OU=IT,OU=HQ-NYC,OU=Employees,OU=Corp,DC=INLANEFREIGHT,DC=local" -LinkEnabled yes
````

### ADAC / GPMC Method
- Open Group Policy Management Console
- Navigate to Securty Analysts Contol
- Navigate to Removable storage access → Right-click All Removable Storage classes → Deny all access → Edit → Enable this configurations
- Navigate to System → Right-click Prevent Access to Command prompt → Edit → Disable
- Navigate to Interactive logon message text and interactive logon Message title → Right-click → Edit → Select Define Policy radial→ Define Policies
- Navigate to Password Policy → Right-click Minimum Password length, Minimum Password Age, etc. → Properties → Select Define policy radial → Define policy

<details>
  <summary> GPO Configuration – Screenshots</summary>

  <br>
 
![Copy GPO – PowerShell](/screenshots/gpo-copy-link-PS.png)   
![GPO Settings – GPMC](/screenshots/gpmc-settings1-adac.png)
![GPO Settings – GPMC](/screenshots/gpmc-settings1-enable.png)
![GPO Settings – GPMC](/screenshots/gpmc-settings2-adac.png)
![GPO Settings – GPMC](/screenshots/gpmc-settings2-enable.png)
![GPO Settings – GPMC](/screenshots/gpmc-interactive-settings.png)
![GPO Settings – GPMC](/screenshots/gpmc-interactive-settings1.png)
![GPO Settings – GPMC](/screenshots/gpmc-interactive-settings2.png)
![GPO Settings – GPMC](/screenshots/gpmc-password-policy.png)
![GPO Settings – GPMC](/screenshots/gpmc-passwordlen.png)
![GPO Settings – GPMC](/screenshots/gpmc-password-history.png)
![GPO Settings – GPMC](/screenshots/gpmc-password-age.png)
![GPO Settings – GPMC](/screenshots/gpmc-password-complex.png)
![GPO Settings – GPMC](/screenshots/gpmc-password-policy-confirm.png)

</details>

--- 

## 7. Adding a Remote computer to a domain & Moving to a New OU
### ADAC Method
- Navigate to the Control Panel → System and Security → System → Change settings
- Select the "Change" radial to rename the computer or change its domain. 
- Enter the name of the Domain → Select ok → Enter credentials of an account that has permission join the computer to the domain
- Open ADAC → Navigate to the Computers OU → Right-click the newly added computer → Select move
- Move the computer to the Security Analysts OU we created.

### Powershell Equivalent
```powershell
Add-Computer -ComputerName ACADEMY-IAD-W10 -LocalCredentials ACADEMY-IAD-W10/image -DomainName INLANEFREIGHT.LOCAL -Credential INLANEFREIGHT\htb-student_adm -Restart
```
```powershell
Get-ADComputer -Identity "ACADEMY-IAD-W10" -Properties * | select CN,CanonicalName, IPv4Address
```

<details>
  <summary> Join Domain & Move Computer – Screenshots</summary>

  <br>

 ![Join Domain – ADAC](/screenshots/join-domain-adac.png)  
 ![Join Domain – ADAC](/screenshots/join-domain-adac-confirm.png)
 ![Move Computer to OU – ADAC](/screenshots/move-computer-to-ou.png)

 </details>

---

## Result
Completed the lab with a full understanding of Active Directory fundamentals and its importance in networked environments. The experience strengthened my ability to work in AD-supported enterprise systems, a critical skill for IT Support and SOC roles.

---

*[Back to Main Project](../README.md)*
