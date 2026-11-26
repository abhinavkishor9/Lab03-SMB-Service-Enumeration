# Lab 03 – SMB Service Enumeration

## Overview

This lab focuses on identifying and examining an SMB service exposed by a Metasploitable 2 virtual machine using the Metasploit Framework.

During the previous network reconnaissance lab, TCP ports 139 and 445 were identified as open on the target. These ports are commonly associated with SMB communication. Based on that observation, this lab uses Metasploit's SMB Version Detection auxiliary scanner to collect additional information about the exposed service.

The investigation focuses on SMB protocol identification, host operating-system information, and SMB signing requirements. All activities were limited to service enumeration and information gathering.

## Lab Environment

| Component | Details |
|---|---|
| Attacker environment | Ubuntu 24.04 under WSL |
| Target | Metasploitable 2 |
| Target IP | 192.168.174.129 |
| Target network interface | eth0 |
| Framework | Metasploit Framework |
| Metasploit version | 6.5.12-dev- |
| Database | PostgreSQL |
| Auxiliary module | auxiliary/scanner/smb/smb_version |
| Target service port contacted | TCP 445 |

## Objectives

- Understand the purpose of SMB and its common TCP ports.
- Verify network connectivity between Ubuntu WSL and Metasploitable 2.
- Select and inspect the Metasploit SMB version detection module.
- Configure the target and execute SMB enumeration.
- Identify protocol and host information disclosed by the service.
- Examine the SMB signing information reported by the scanner.
- Document observations without assuming vulnerability or exploitability.

## Activities Performed

1. Verified the target's network configuration using `ifconfig`.
2. Confirmed the Metasploitable 2 IP address as `192.168.174.129`.
3. Tested connectivity from Ubuntu WSL using `ping`.
4. Launched Metasploit Framework.
5. Verified the PostgreSQL database connection using `db_status`.
6. Selected `auxiliary/scanner/smb/smb_version`.
7. Reviewed module information and available options.
8. Configured the target using `RHOSTS`.
9. Executed the SMB version detection scanner.
10. Reviewed and recorded the returned information.

## Key Findings

The scanner returned the following information:

| Finding | Result |
|---|---|
| SMB service | Detected |
| Target endpoint | 192.168.174.129:445 |
| SMB version | 1 |
| Preferred dialect | Not displayed |
| Host operating system | Unix |
| SMB signing | Not required |
| Signature status | Optional |

The scanner successfully identified SMB version 1 and reported that the host was running Unix.

The output also indicated that SMB signing was not required. This is a security configuration observation that may warrant further assessment depending on the environment and its security requirements.

The scanner did not disclose a specific Samba software version.

## Analysis

The results confirm that the target exposes an SMB service over TCP port 445 and supports SMB version 1.

The SMB signing result indicates that signing was not mandatory according to the scanner's negotiation. This does not, by itself, demonstrate that an attacker can successfully perform a relay attack or compromise the target.

Similarly, SMB version identification does not establish that a particular vulnerability is present. Additional validation would be required to assess the security impact.

## Scope and Limitations

This lab was restricted to SMB service enumeration.

The following activities were not performed:

- Exploitation of SMB vulnerabilities.
- Password guessing or brute-force attacks.
- SMB share enumeration or file access.
- File download or modification.
- Payload execution.
- Session creation.
- Post-exploitation.

The results represent information returned by the SMB version detection module during this scan. They do not constitute a complete security assessment of the target.

## Conclusion

The SMB version detection module successfully identified SMB version 1 on the Metasploitable 2 target and reported Unix as the host operating-system identification.

The scanner also reported that SMB signing was not required. These observations provide useful information for a subsequent security configuration review.

The investigation remained within the scope of reconnaissance and enumeration, with no exploitation or access to target data.

## Tools Used

- Metasploit Framework
- Ubuntu 24.04 under WSL
- Metasploitable 2
- VMware Workstation
- PostgreSQL database

## Key Learning

SMB enumeration helps identify protocol information and security-related configuration details exposed by a network service. Findings must be interpreted carefully: service detection and configuration observations are not equivalent to confirmed vulnerabilities or successful exploitation.
