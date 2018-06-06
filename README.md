# CVE-2017-13130 BMC Patrol 'mcmnm' - Privilege escalation via a vulnerable setuid binary 

<p align="center">
  <img src="https://github.com/itm4n/bmc-patrol-mcmnm-privesc/raw/master/screenshots/00_exploit-demo.gif">
</p>

## Vulnerability analysis 

### File permissions 
The 'mcmnm' file is a setuid binary owned by 'root' and executable by everyone.

<p align="center">
  <img src="https://github.com/itm4n/bmc-patrol-mcmnm-privesc/raw/master/screenshots/01_file-permissions_obf.png">
</p>

### Vulnerable RPATH
The binary's RPATH contains the unsecure path '.'. As a reminder, RPATH is the "run-time search path". It can be used to specify the location of shared objects that will be loaded before any other library because the linker gives them a higher priority. Therefore, if a local user can place a custom shared object in one of these folders, then he/she will be able to hijack the execution flow of the program. In the present case, '.' can be regarded as a sort of wildcard, hence the obvious vulnerability.

<p align="center">
  <img src="https://github.com/itm4n/bmc-patrol-mcmnm-privesc/raw/master/screenshots/02_readelf-rpath_obf.png">
</p>

### A quick dynamic analysis using 'strace'
Thanks to 'strace' we can see which SO files the binary tries to load and from where. For example, it tries to load 'libmcmclnx.so' from the current folder (because of '.' in the RPATH). 

<p align="center">
  <img src="https://github.com/itm4n/bmc-patrol-mcmnm-privesc/raw/master/screenshots/03_strace_obf.png">
</p>

## Exploit
### Shared Object hijacking
The exploit is quite simple as a local attacker only has to forge a malicious Shared Object file and execute the vulnerable binary from the same folder. The following code will spawn a 'root' shell as soon as the Object is loaded (similar to 'DllMain' with Windows DLL).

<p align="center">
  <img src="https://github.com/itm4n/bmc-patrol-mcmnm-privesc/raw/master/screenshots/04_dll-source-code.png">
</p>

### The exploit code 
The exploit was wrapped in a single script file. The first line (BIN=/path/to/mcmnm) must be modified to match the current installation of the Patrol package. If all goes well, you should get a 'root' shell. ;)

<p align="center">
  <img src="https://github.com/itm4n/bmc-patrol-mcmnm-privesc/raw/master/screenshots/05_exploit_obf.png">
</p>

## Disclosure timeline
2017-04-21 - Vulnerability discovered  
2017-05-09 - Vendor contacted  
2017-06-05 - Still no response, reminder sent to vendor  
2017-08-22 - Still no response, vulnerability disclosed  
