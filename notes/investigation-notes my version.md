# Investigation Notes 
## 1. Investigation Summary

This investigation examined the SMB service exposed by a Metasploitable 2 virtual machine using the Metasploit Framework.

The previous reconnaissance lab identified TCP ports 139 and 445 as open. This lab focused on identifying SMB protocol information and reviewing the signing requirements reported by the target.

The investigation was performed from Ubuntu WSL against the Metasploitable 2 VM hosted in VMware.

## 2. Target Information

| Attribute | Observed Value |
|---|---|
| Target system | Metasploitable 2 |
| Target IP | 192.168.174.129 |
| Network interface | eth0 |
| IPv4 address | 192.168.174.129 |
| Subnet mask | 255.255.255.0 |
| Broadcast address | 192.168.174.255 |
| Attacker environment | Ubuntu WSL |

The target IP was verified directly from the Metasploitable console using `ifconfig`.

## 3. Network Connectivity Validation

From Ubuntu WSL, the following command was executed:

`ping -c 4 192.168.174.129`

### Result

| Metric | Result |
|---|---|
| Packets transmitted | 4 |
| Packets received | 4 |
| Packet loss | 0% |
| Minimum RTT | 1.153 ms |
| Average RTT | 1.313 ms |
| Maximum RTT | 1.573 ms |

### Observation

All four ICMP echo requests received responses from the target.

This confirmed network-level reachability between Ubuntu WSL and Metasploitable 2 at the time of testing.

## 4. Metasploit Environment Validation

Metasploit was launched using:

`msfconsole`

The console reported:

- Metasploit version: `6.5.12-dev-`
- PostgreSQL database initialization check completed.
- Existing database found and startup attempted.

The database connection was verified using:

`db_status`

### Result

`Connected to msf. Connection type: postgresql.`

### Observation

The Metasploit console started successfully and its PostgreSQL database connection was active.

Ruby gem warnings involving `base64` and `logger` appeared during startup. They did not prevent Metasploit from launching or executing the scanner.

## 5. Module Selection

The following auxiliary module was selected:

`use auxiliary/scanner/smb/smb_version`

Module information was reviewed using:

`info`

### Module Details

| Attribute | Value |
|---|---|
| Module name | SMB Version Detection |
| Module path | auxiliary/scanner/smb/smb_version |
| Module category | Auxiliary scanner |
| Purpose | Fingerprint SMB servers and display version information |
| Module rank | Normal |

The module description states that it can report protocol information and host operating-system information when available.

The module information also notes that host operating-system detection requires the remote server to support SMB version 1.

## 6. Module Configuration

The available options were reviewed using:

`show options`

The target was configured using:

`set RHOSTS 192.168.174.129`

The configuration was checked again using:

`show options`

### Relevant Options

| Option | Configured Value | Purpose |
|---|---|---|
| RHOSTS | 192.168.174.129 | Target address |
| RPORT | Not explicitly configured | Module-selected/default behavior |
| THREADS | 1 | Number of concurrent threads |

The module output showed that the scanner contacted TCP port 445.

No explicit `RPORT` value was set during this exercise.

## 7. Scanner Execution

The scanner was executed using:

`run`

### Actual Output

```text
[*] 192.168.174.129:445   - SMB Detected (versions: 1) (preferred dialect: ) (signatures: optional)
[+] 192.168.174.129:445   -   Host is running Unix
[*] 192.168.174.129:445   -   SMB signing is not required
[*] 192.168.174.129       - Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
```

## 8. Findings

### Finding 1 – SMB Service Detection

**Status:** Confirmed

The scanner detected an SMB service on:

`192.168.174.129:445`

This confirms that the target responded to the SMB version detection module on TCP port 445.

### Finding 2 – SMB Version 1

**Status:** Confirmed

The scanner reported:

`SMB Detected (versions: 1)`

This indicates that SMB version 1 was detected during the scan.

The output did not identify a specific Samba software version.

### Finding 3 – Host Operating-System Identification

**Status:** Confirmed scanner output

The module reported:

`Host is running Unix`

This is the operating-system identification returned by the scanner. It is not a detailed operating-system version or distribution identification.

### Finding 4 – SMB Signing Not Required

**Status:** Confirmed scanner output

The scanner reported:

`SMB signing is not required`

The output also included:

`signatures: optional`

This indicates that SMB signing was not mandatory according to the scanner's negotiation.

This is a security configuration observation. It does not prove that an SMB relay attack or another attack would succeed.

### Finding 5 – Preferred Dialect Not Displayed

**Status:** Observed limitation

The scanner output contained an empty preferred dialect field:

`preferred dialect:`

No preferred dialect value was displayed. A specific dialect should therefore not be inferred from this result.

## 9. Evidence-Based Assessment

| Observation | Interpretation |
|---|---|
| SMB detected on TCP 445 | SMB service responded to the scanner |
| SMB version 1 detected | Target supports or exposed SMBv1 during negotiation |
| Host identified as Unix | Scanner returned Unix as the OS identification |
| Signing not required | SMB signing was not mandatory according to the scanner |
| Preferred dialect blank | No preferred dialect value was displayed |

### Potential Security Considerations

- SMB version 1 is an older SMB protocol generation and may warrant review against current security requirements.
- SMB signing not being required may increase exposure to certain SMB relay-related risks in applicable environments.
- The actual risk depends on the complete service configuration, network architecture, authentication behavior, and available attack paths.

### What Was Not Established

The investigation did not establish:

- A specific Samba software version.
- A specific CVE affecting the target.
- Successful SMB authentication.
- Accessible SMB shares.
- Unauthorized file access.
- Successful SMB relay.
- Remote code execution.
- Successful exploitation.

