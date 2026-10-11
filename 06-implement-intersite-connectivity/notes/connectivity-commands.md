# Connectivity Commands

## Lab 05 – Implement Intersite Connectivity

This document contains the PowerShell commands used during the lab to configure and test connectivity between Azure virtual machines.

---

## 1. Enable Remote Desktop Firewall Rule

The following command was executed on `CoreServicesVM` using Azure VM Run Command.

```powershell
Enable-NetFirewallRule -DisplayGroup "Remote Desktop"
```

### Purpose

This enables the Windows Firewall rule that allows Remote Desktop traffic.

It is required for the TCP connectivity test on port `3389`.

---

## 2. Test TCP Connectivity

The following command was executed from `ManufacturingVM`:

```powershell
Test-NetConnection <CoreServicesVM-private-IP> -Port 3389
```

Replace `<CoreServicesVM-private-IP>` with the actual private IP address of `CoreServicesVM`.

### Example

```powershell
Test-NetConnection 10.0.0.4 -Port 3389
```

### Purpose

This command tests whether `ManufacturingVM` can establish a TCP connection to `CoreServicesVM` on port `3389`.

---

## 3. Expected Result

After VNet peering has been configured and the Windows Firewall rule has been enabled, the test should report a successful TCP connection.

Important output:

```text
TcpTestSucceeded : True
```

This confirms that private connectivity between the two virtual machines is working.

---

## 4. Connectivity Flow

```text
ManufacturingVM
      │
      │ TCP 3389
      ▼
VNet Peering
      │
      ▼
CoreServicesVM
```

---

## 5. Commands Summary

| Command                                                 | Purpose                                               |
| ------------------------------------------------------- | ----------------------------------------------------- |
| `Enable-NetFirewallRule -DisplayGroup "Remote Desktop"` | Allow Remote Desktop traffic through Windows Firewall |
| `Test-NetConnection <IP> -Port 3389`                    | Test TCP connectivity to the destination VM           |

> These commands were used as part of the Azure VM Run Command / PowerShell connectivity testing process.
