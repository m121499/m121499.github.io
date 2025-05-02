# Getting onto the network

For this LAB I simulated an internal attacker. I connected my kali box to the network and got an IP address via DHCP.
![](../images/kaliip.png)

After getting connected to the network, I started my reconnaissance with an nmap ping sweep of the network.
![](../images/nmap1.png)

As seen in the image above, there are 3 servers and 1 windows client.

## LLMNR/NetBios Poisoining 
I started my attack with responder. Responder is a relatively passive tool that abuses LLMNR/Netbios to listen for failed connections and get the hash for that user.
![](../images/responder.png)

I then simulated a user visiting a share on the network
![](../images/Pasted image 20240809183029.png)

Responder was able to capture this hash and I used hashcat to crack it.
![](../images/Pasted image 20240809183003.png)
![](../images/Pasted image 20240809183501.png)
![](../images/Pasted image 20240809183830.png)

# Enumerating with credentials

I used NetExec to enumerate the Domain Controller via SMB. I started by listing shares test.user has access to. The user only has access to Sales because it is a member of Sales.
![](../images/nxc1.png)

I used NetExec to list Domain users and then output the users to a file.
![](../images/nxc2.png)
![](../images/nxc3.png)

# Enumerating Service Principal Names (Kerberoasting)

I utilized the list of users and the test.user credentials to find any users with a SPN set. I was able to find the backupservice account is kerberoastable. After getting the hash I was able to crack and get the password for the backupservice user.
![](../images/spn1.png)
![](../images/spn2.png)
![](../images/spn4.png)
![](../images/spn3.png)

# Enumerating for AS-REP Roasting

I also disabled Kerberos preauthentication on an account to perform an AS-REP roasting attack. After getting the hash I was able to crack the password for paule.raune
![](../images/asrep1.png)
![](../images/asrep2.png)
![](../images/asrep3.png)
![](../images/asrep4.png)

# Escalating Privileges to Domain Admin.

I checked what shares the Backup Service user had access to.
![](../images/backupserv.png)

Since the user has access to the IT group I can assume it is a member of that group. I used this command to verify it.
![](../images/itgroup.png)

I also used the backupservice account to check the members of the domain admins group. Looking at the output I noticed j.robinson who is a member of the IT group is also a member of domain admins.
![](../images/domadmins.png)

I decided to check out the IT share to see if I could find anymore information. Looking at the share there is a file called passwords.txt with a list of passwords. I used this list to perform a password spray with the previously found users.
![](../images/smb.png)
![](../images/pass-spray.png)

Looking at the output I got a sucessful password combination `j.robinson:jd,2<n31di9`
![](../images/pwned.png)

# Getting All Users passwords
After getting Domain administrator credentials I used RDP to connect to the Certificate server.
![](../images/rdp.png)

Once connected I uploaded mimikatz and ran the following commands to get the hash of every user in the domain. I used log within mimikatz to get a file containing the output
![](../images/mimikatz.png)
![](../images/mimi2.png)
![](../images/mimi3.png)
![](../images/mimi4.png)
![](../images/mimi5.png)

After obtaining all the hashes and transferring them to my attacker machine via SMB, I used hashcat to bruteforce all of them.
![](../images/awk.png)
![](../images/hashcat.png)
![](../images/cracked.png)

Lastly, I used a python script to take each hash and password combination and match it to a username from the mimikatz log.
```python
# Load hash:password pair into a dictionary.
hash_to_password = {}
with open('cracked.txt', 'r') as hashpass:
    for line in hashpass:
        parts = line.strip().split(':', 1)
        if len(parts) == 2:
            hash_val, password = parts
            hash_to_password[hash_val.strip()] = password.strip()

# Process the user and hash from the mimikatz.log and output to a file with a user:pass pair
with open('mimikatz.log', 'r') as mimi, open('matched_output.txt', 'w') as output_file:
    for line in mimi:
        parts = line.strip().split('\t')
        if len(parts) >= 3:
            username = parts[1].strip()
            hash_val = parts[2].strip()
            password = hash_to_password.get(hash_val)
            if password:
                output_file.write(f"{username}:{password}\n")
```
Output:
![](../images/user-pass.png)

{% include elements/button.html link="../projects/activedirectory" text="Project Home" block=true %}

