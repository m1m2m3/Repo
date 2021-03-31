To install OpenSSH in Windows Server -

Download the OpenSSH from below link

https://github.com/PowerShell/Win32-OpenSSH/releases/tag/v8.1.0.0p1-Beta



Install “Windows for OpenSSH” using PowerShell

in the start menu, right click on power shell icon to run as administrator

Unblock the downloaded OpenSSH archive if necessary, by running following command in powershell window

Unblock-File .\Downloads\OpenSSH-Win64.zip
Extract the archive: by running following command in powershell window

.\Downloads\OpenSSH-Win64.zip -DestinationPath .
Copy the OpenSSH-Win64 folder to your desired location,

e.g c: Copy-Item -Recurse .\OpenSSH-Win64\ 'C:\'
Now run

&icacls C:\OpenSSH-Win64\libcrypto.dll /grant Everyone:RX
Now run

Run install-sshd.ps1 to create the OpenSSH Authentication Agent and OpenSSH SSH Server services
The OpenSSH SSH Server service is set to Manual startup, not automatic, Please run

&sc.exe config sshd start= auto
&sc.exe config ssh-agent start= auto
Start the OpenSSH Authentication Agent and SSH Server services, Please run

&sc.exe start sshd
&sc.exe start ssh-agent
Now Windows Defender Firewall is open for port 22, rule OpenSSH-Server-In-TCP must be enabled

New-NetFirewallRule -Name sshd -DisplayName 'OpenSSH SSH Server' -Enabled True -Direction Inbound -Protocol TCP -Action Allow -LocalPort 22 -Program "C:\OpenSSH-Win64\sshd.exe"
SSH service is started, to check that

Get-Service sshd
Get-Service ssh-agent
There will be no identities/keys will be there by default we need to add ssh keys,

Now add pem files -

PS C:\OpenSSH-Win64> .\ssh-add.exe < Path of key (pem) >
We can list added identities

PS C:\OpenSSH-Win64> .\ssh-add.exe -l
Now connect to Bastion server using OpenSSH via PowerShell, (This will run using VPN)

PS C:\OpenSSH-Win64> .\ssh.exe -A id@o.o.o.o
Here you can see/list that identities are get attached which we have added into PowerShell

$ssh-add -l
Also we can directly ssh other nodes those are accessible from Bastion, here we are using Bastion as a Jump from PowerShell. (Key need to be added)

PS C:\OpenSSH-Win64> .\ssh.exe -A -J id@o.o.o.o ec2-user@1.x.x.x
