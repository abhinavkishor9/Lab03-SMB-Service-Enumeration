
# Troubleshooting Notes – Lab 03: SMB Service Enumeration

## 1. Overview

This document records the environment checks, warnings, and operational observations encountered while performing SMB service enumeration against Metasploitable 2.

The lab was completed using Ubuntu WSL, VMware Workstation, Metasploitable 2, and Metasploit Framework.

## 2. Target Network Verification

### Observation

The Metasploitable 2 network configuration was checked using:

`ifconfig`

The `eth0` interface reported:

- IPv4 address: `192.168.174.129`
- Broadcast address: `192.168.174.255`
- Subnet mask: `255.255.255.0`
- Interface state: UP

### Validation

From Ubuntu WSL:

`ping -c 4 192.168.174.129`

### Result

- Four packets transmitted.
- Four packets received.
- Zero percent packet loss.
- Average response time: 1.313 ms.

### Conclusion

Network connectivity was working. No network troubleshooting or VMware adapter changes were required during this exercise.

## 3. Metasploit Startup Warnings

### Observation

When `msfconsole` was launched, Ruby displayed warnings about unresolved or ambiguous gem specifications.

The affected gems were:

- `base64`
- `logger`

Multiple installed versions were listed.

### Result

Despite the warnings:

- Metasploit started successfully.
- The console displayed version `6.5.12-dev-`.
- The database initialization check completed.
- The SMB scanner module loaded.
- The scanner executed successfully.

### Conclusion

The warnings were non-blocking in this lab.

No gem cleanup, package modification, or Ruby environment changes were required.

**Troubleshooting lesson:** Do not modify installed gems merely because a warning appears. First determine whether it causes an actual operational problem.

## 4. Database Initialization and Connectivity

### Observation

During startup, Metasploit displayed:

`Existing database found, attempting to start it`

### Validation

The database connection was checked using:

`db_status`

### Result

`Connected to msf. Connection type: postgresql.`

### Conclusion

The PostgreSQL database was available and connected to Metasploit.

No database reinitialization or repair was required.

## 5. SMB Module Configuration

### Observation

After selecting:

`auxiliary/scanner/smb/smb_version`

The `show options` output showed that:

- `RHOSTS` was required but initially empty.
- `RPORT` was optional and displayed no explicit value.
- `THREADS` was set to 1.

### Action

The target was configured using:

`set RHOSTS 192.168.174.129`

The options were reviewed again before executing the scanner.

### Result

The scanner contacted:

`192.168.174.129:445`

### Conclusion

The module executed successfully without manually setting `RPORT`.

The actual contacted port was confirmed by the scanner output.

## 6. Preferred Dialect Field

### Observation

The scanner returned:

`preferred dialect:`

No value appeared after the field.

### Interpretation

The preferred dialect was not displayed in the output.

### Troubleshooting lesson

Do not assume a specific SMB dialect when the scanner does not provide one. Record the field as unavailable or blank in the investigation notes.

## 7. SMB Signing Output

### Observation

The scanner returned:

`signatures: optional`

It also reported:

`SMB signing is not required`

### Interpretation

The scanner indicated that SMB signing was not mandatory during the negotiation.

This should be recorded as a configuration observation rather than proof of a successful attack path.

### Troubleshooting lesson

Do not interpret optional signing as proof that SMB signing is completely disabled or that an SMB relay attack is guaranteed to succeed.

Further assessment would be necessary to establish the actual security impact.

## 8. Issues That Did Not Occur

The following problems were not encountered during the completed scan:

- Target connectivity failure.
- Metasploit console startup failure.
- PostgreSQL connection failure.
- Module loading failure.
- Missing required target configuration.
- Scanner execution failure.

## 9. Final Status

| Component | Status |
|---|---|
| Metasploitable VM | Running |
| Target IP verification | Successful |
| WSL-to-target connectivity | Successful |
| Metasploit startup | Successful |
| PostgreSQL connection | Successful |
| SMB module selection | Successful |
| SMB scanner execution | Successful |
| Result collection | Successful |

## 10. Key Takeaway

Troubleshooting should be based on actual failures rather than warnings alone. In this lab, the Ruby warnings were non-blocking, database connectivity was healthy, and the SMB scanner completed successfully.

The blank preferred dialect field and optional signing result were recorded as observations rather than treated as errors.
