# BMC Patrol 'mcmnm' - Privilege escalation via a vulnerable setuid binary 

## File permissions 
The 'mcmnm' file is a setuid binary owned by 'root' and executable by everyone.
![File permissions](https://github.com/itm4n/bmc-patrol-mcmnm-privesc/raw/master/screenshots/01_file-permissions_obf.png)

## Vulnerable RPATH
The binary's RPATH contains several unsecure paths such as '.'.
![Binary RPATH](https://github.com/itm4n/bmc-patrol-mcmnm-privesc/raw/master/screenshots/02_readelf-rpath_obf.png)

## A quick dynamic analysis using 'strace'
Thanks to 'strace' we can see which SO files the binary tries to load and from where. For example, it tries to load 'libmcmclnx.so' from the current folder (because of '.' in the RPATH). 
![strace analysis](https://github.com/itm4n/bmc-patrol-mcmnm-privesc/raw/master/screenshots/03_strace_obf.png)

## Shared Object hijacking
The exploit is quite simple as a local attacker only has to forge an evil Shared Object file and execute the vulnerable binary from the same folder. The following code will spawn a 'root' shell as soon as the Object is loaded (similar to 'DllMain' with Windows DLL).
![Evil SO source code](https://github.com/itm4n/bmc-patrol-mcmnm-privesc/raw/master/screenshots/04_dll-source-code.png)

## The exploit 
The exploit was wrapped in a single script file. The first line (BIN=/path/to/mcmnm) must be modified to match the current installation of the Patrol package. If all goes well, you should get a 'root' shell. ;)
![Exploit](https://github.com/itm4n/bmc-patrol-mcmnm-privesc/raw/master/screenshots/05_exploit_obf.png)


