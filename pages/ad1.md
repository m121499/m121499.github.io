---
---

# Users and Permissions
Firstly, I created groups and Organizational Units to simulate different departments in an organization. I created an OU and group for every department. After creating the groups I wrote a simple powershell script to automate the creation of users. I created a file of 20 names for each OU and used random passwords from the rockyou database when creating the users. I modified the script for the different OU's/name lists. 
![test](../images/Pasted image 20240808103841.png)
```powershell
#Declare username and password variables.
$usernames = Get-Content "C:\Users\Matt\Desktop\names4.txt"
$passwords = Get-Content "C:\Users\Matt\Desktop\passwords.txt"

# Ensure there are the same number of usernames and passwords
if ($usernames.Count -ne $passwords.Count) {
    Write-Error "The number of usernames and passwords does not match."
    exit
}

# Loop through usernames and assign a corresponding password
for ($i = 0; $i -lt $usernames.Count; $i++) {
    $u = $usernames[$i]
    $p = $passwords[$i]
    $pwd = ConvertTo-SecureString -String $p -AsPlainText -Force

    $first = $u.Split(" ")[0].ToLower()
    $last = $u.Split(" ")[1].ToLower()

    New-ADUser -Name "$first $last" -GivenName "$first" -Surname "$last" -SamAccountName "$first.$last" -Path "OU=IT, DC=MATT, DC=corp" -PasswordNeverExpires $true -AccountPassword $pwd -Enabled $true
}
```

After creating users and groups, I added some user access controls. I created group policy objects for every department and applied some restrictions to certain departments. For example, users in sales can not open command prompt, control panel, or powershell. 
![](../images/Pasted image 20240402184444.png)
![](../images/Pasted image 20240402185207.png)
![](../images/Pasted image 20240402185252.png)
When attempting to block PowerShell I learned that it is not as simple as disabling command prompt. I had to create a rule to the path where PowerShell is for both the x64 and x86 versions. Furthermore, I created separate rules to block PowerShell ISE. 
![](../images/Pasted image 20240402191415.png)

Below is a demonstration of the user access controls using a user named test user. Test User is a member of the Sales department.  
{% include elements/figure.html image="../images/testuser.png" caption="Test User Logged Onto Windows Client" %}
![](../images/controlpanelblockedfortestuser.png)
{% include elements/figure.html image="../images/blockedmessage.png" caption="Control Panel Blocked" %}
{% include elements/figure.html image="../images/commandpromptblockedfortestuser.png" caption="Command Prompt Blocked" %}

{% include elements/figure.html image="../images/Pasted image 20240402190207.png" caption="Powershell Blocked" %}

# SMB and more Permissions

After creating the users and groups, I made SMB shares for each department.
![](../images/shares.png)
Each department share is restricted so that only members of that department and the domain admins can access the share
![](../images/Pasted image 20240402184231.png)
As shown in the images below, Test User who is a member of the Sales department, is able to access the Sales share, but not the Legal share.
![](../images/salesshare.png)
![](../images/legalshare.png)

{% include elements/button.html link="../projects/activedirectory" text="Project Home" block=true %}
