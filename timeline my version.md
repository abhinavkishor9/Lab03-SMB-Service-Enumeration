# Timeline 
| Sequence | Activity | Result |
|---|---|---|
| 1 | Started Metasploitable 2 in VMware | Target VM running |
| 2 | Executed `ifconfig` on Metasploitable | Confirmed target IP `192.168.174.129` |
| 3 | Executed `ping -c 4 192.168.174.129` from Ubuntu WSL | Four responses received; 0% packet loss |
| 4 | Launched `msfconsole` | Metasploit console started |
| 5 | Checked Metasploit database using `db_status` | PostgreSQL connection confirmed |
| 6 | Selected `auxiliary/scanner/smb/smb_version` | SMB version detection module loaded |
| 7 | Reviewed module information using `info` | Module purpose and description examined |
| 8 | Reviewed configuration using `show options` | Identified required and optional settings |
| 9 | Configured `RHOSTS` | Target set to `192.168.174.129` |
| 10 | Reviewed options again | Target configuration verified |
| 11 | Executed `run` | SMB enumeration completed |
| 12 | Reviewed scanner output | SMB version 1 and Unix host identification reported |
| 13 | Reviewed SMB signing information | Signing reported as not required |
| 14 | Recorded investigation findings | Confirmed observations and limitations documented |

