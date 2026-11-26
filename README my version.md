# Lab03-SMB-Service-Enumeration
## Overview
### 1. Introduction to SMB

Server Message Block (SMB) is a network protocol used by systems to share files, directories, printers, and other resources over a network.

It is commonly associated with Windows file-sharing environments. Linux systems can also provide SMB services through implementations such as Samba.

SMB supports communication between networked systems and can provide access to shared resources subject to the configured authentication and authorization controls.

### 2. SMB Communication Ports

SMB-related communication commonly uses two TCP ports:

| Port | Purpose |
|---|---|
| TCP 139 | SMB communication over NetBIOS Session Service |
| TCP 445 | Direct SMB communication over TCP |

These ports are useful during network reconnaissance because they can indicate the presence of SMB-related services.

However, an open port does not independently establish the software implementation, version, or security configuration.

### 3. SMB Versions

SMB has evolved through multiple protocol generations.

| Protocol generation | General description |
|---|---|
| SMB 1.0 | Original SMB generation, commonly referred to as SMBv1 |
| SMB 2.x | Introduced protocol improvements and reduced overhead |
| SMB 3.x | Added further capabilities, including security and performance improvements |

SMBv1 is an older protocol generation. Its continued use may warrant review against current security requirements.

The protocol version and the server software version are different pieces of information. Identifying SMBv1 does not automatically identify a specific Samba release.

### 4. What Is SMB Enumeration?

SMB enumeration is the process of gathering information from an SMB service exposed by a target system.

Depending on the target and the enumeration method, information may include:

- SMB protocol versions.
- Server implementation.
- Operating-system identification.
- Server or domain information.
- SMB signing requirements.
- Other protocol capabilities.

The information disclosed depends on the server configuration and the capabilities of the enumeration tool.

### 5. SMB Version Detection with Metasploit

Metasploit provides an auxiliary scanner module for identifying SMB server information:

`auxiliary/scanner/smb/smb_version`

The module attempts to fingerprint an SMB server and report version-related information.

It may also report protocol information and host operating-system identification when available.

This module is used for reconnaissance and service identification. It is not an SMB exploitation module.

### 6. Understanding SMB Signing

SMB signing is a security mechanism that uses cryptographic signatures to help protect SMB message integrity and verify that messages have not been modified in transit.

Whether signing is required or optional depends on the SMB server and client configuration.

#### Signing requirements

- **Required:** SMB communication must use signing.
- **Optional:** Signing may be negotiated, but it is not mandatory.
- **Not required:** The server does not enforce signing as a requirement.

A server that does not require SMB signing may warrant further security review, particularly in environments where SMB relay attacks are a relevant threat.

However, optional or non-mandatory signing does not prove that a relay attack is possible or successful.

The complete network, authentication, and service configuration must be considered.

### 7. Understanding Enumeration Results

Enumeration results should be separated into three categories.

#### Confirmed observations

Information directly returned by the scanner, such as an identified SMB version or a signing requirement.

#### Potential security concerns

Observations that may indicate a configuration weakness or require additional investigation.

#### Unestablished claims

Conclusions that cannot be supported by the enumeration output alone, such as successful exploitation, unauthorized access, or confirmed vulnerability to a specific CVE.

This distinction prevents analysts from overstating the significance of scanner output.

### 8. Application to This Lab

In this investigation, Metasploit's SMB version detection module was executed against a Metasploitable 2 target.

The scanner reported:

- SMB version 1 detected.
- Host operating system identified as Unix.
- SMB signing not required.
- Signature status reported as optional.
- Preferred dialect field left blank.

No specific Samba software version was returned.

These results provide protocol and configuration observations for further assessment. They do not establish a particular vulnerability or successful attack path.

### 9. Conclusion

SMB enumeration is an important reconnaissance activity because it helps analysts understand the protocol information and configuration details exposed by network file-sharing services.

Metasploit's SMB version detection module provides a practical way to collect this information.

The most important analytical principle is to distinguish between what the scanner actually confirmed, what may require further security review, and what remains unestablished.

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

## Lab Objectives

### Primary Objective

To examine an SMB service exposed by a Metasploitable 2 system and collect protocol and security configuration information using Metasploit's auxiliary scanning capabilities.

### Technical Objectives

1. Identify the role of SMB in network file-sharing communication.
2. Understand the significance of TCP ports 139 and 445.
3. Verify the target's network address before initiating enumeration.
4. Validate connectivity between Ubuntu WSL and the target VM.
5. Navigate the Metasploit auxiliary module structure.
6. Inspect the SMB version detection module and understand its purpose.
7. Review module options and configure the target correctly.
8. Execute the SMB scanner and collect the returned information.
9. Identify the SMB protocol version reported by the scanner.
10. Examine the operating-system identification returned by the module.
11. Interpret SMB signing requirements and their security relevance.

### Analytical Objectives

- Distinguish SMB protocol version from server software version.
- Recognize the limitations of service fingerprinting.
- Separate confirmed observations from potential security concerns.
- Avoid treating protocol identification as proof of vulnerability.
- Understand why SMB signing requirements matter during security assessment.
- Document findings with evidence and appropriate limitations.

### Investigation Outcome

Successfully perform SMB service enumeration against the designated lab target and produce an evidence-based record of the information disclosed by the service.

The exercise must remain limited to reconnaissance and enumeration. Exploitation, authentication attacks, file access, and post-exploitation are not part of this lab.

## Lab Scenario

### Background

During a network reconnaissance exercise, a security analyst identifies a Metasploitable 2 system exposing TCP ports 139 and 445. These ports are commonly associated with Server Message Block (SMB) communication.

The presence of these ports indicates that SMB-related services may be accessible on the target. However, port identification alone does not reveal the exact protocol version, server implementation, or security configuration.

Further service enumeration is therefore required to understand what information the target discloses.

### Investigation Scenario

As part of a controlled security assessment, the analyst uses Ubuntu WSL to communicate with the Metasploitable 2 virtual machine hosted in VMware.

The analyst first verifies the target's IP address and confirms network connectivity. Metasploit is then launched, and its database connection is checked before selecting the SMB version detection auxiliary module.

The module is configured for the target and executed to collect SMB-related information.

The investigation focuses on the following questions:

- Which SMB protocol version does the target report?
- Does the scanner provide host operating-system information?
- Is SMB signing required by the target?
- What information is missing or unavailable in the scanner output?
- Which observations may require additional security assessment?

### Assessment Boundaries

The activity is restricted to service identification and protocol enumeration. The analyst will not attempt to authenticate to SMB shares, guess passwords, access files, modify target data, or exploit vulnerabilities.

All findings must be based on the actual scanner output. Potential security concerns must be clearly separated from confirmed observations.

### Expected Outcome

The analyst is expected to produce an investigation record identifying the SMB information disclosed by the target, interpreting the security relevance of the results, and documenting the limitations of the enumeration.

The final assessment should explain what was confirmed and what remains unknown without claiming that a vulnerability or successful attack has been established.

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

