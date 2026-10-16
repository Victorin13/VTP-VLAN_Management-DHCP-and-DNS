# Technical Documentation 

## 1. Network Architecture

The network consists of one Multilayer Switch (MLS), three access switches (SW1, SW2 and SW3), multiple end hosts, and a dedicated DNS server.

The MLS provides centralized VLAN management through VTP, DHCP services, and inter-VLAN routing using Switched Virtual Interfaces (SVIs).

Inter-switch links are configured as manually established trunk links to carry traffic from multiple VLANs.

Refer to [topology.png](topology.png) for the complete network diagram.

## 2. VLAN and IP Addressing

| VLAN | Name | Network | Default Gateway |
|---|---|---|---|
| 10 | IT | 192.168.10.0/24 | 192.168.10.254 |
| 20 | SERVERS | 192.168.20.0/24 | 192.168.20.254 |
| 30 | HR | 192.168.30.0/24 | 192.168.30.254 |
| 40 | SALES | 192.168.40.0/24 | 192.168.40.254 |

Each VLAN has a dedicated IP subnet. The MLS provides the Layer 3 gateway for each subnet through its corresponding SVI.

## 3. VTP Configuration

- VTP domain: `CCNA`
- VTP version: 2
- MLS: Server mode
- SW1: Client mode
- SW2: Client mode
- SW3: Transparent mode

VTP distributes VLAN information between participating switches in the same VTP domain.

In transparent mode, SW3 maintains its own VLAN database instead of synchronizing it with the VTP server.

### Verification

```cisco
show vtp status
show vlan brief
```

## 4. Trunk Configuration

Inter-switch trunk links are manually configured using:

```cisco
switchport mode trunk
```

This explicitly configures the port as a trunk rather than relying on DTP to negotiate the trunk mode.

### Verification

```cisco
show interfaces trunk
show interfaces switchport
```

Check that the expected VLANs are allowed and active on the relevant trunk links.

## 5. Inter-VLAN Routing

The MLS routes traffic between VLANs using the following SVIs:

- VLAN 10: `192.168.10.254/24`
- VLAN 20: `192.168.20.254/24`
- VLAN 30: `192.168.30.254/24`
- VLAN 40: `192.168.40.254/24`

Each end host uses the SVI address corresponding to its VLAN as its default gateway.

### Verification

```cisco
show ip interface brief
show ip route
```

Connectivity between hosts in different VLANs is tested using ICMP echo requests.

## 6. DHCP Service

The MLS is configured as a DHCP server to assign IP addressing information to client hosts.

The DHCP pools must provide the correct subnet, default gateway, and DNS server address for each relevant VLAN.

### Verification

```cisco
show ip dhcp pool
show ip dhcp binding
```

On a Windows-based Packet Tracer host, inspect the IP configuration using the desktop IP configuration utility.

## 7. DNS Service

The dedicated DNS server is connected to VLAN 20.

- Hostname: `DNS-SVR`
- IP address: `192.168.20.2`
- DNS record: `DNS-SVR` → `192.168.20.2`

The DNS record allows clients to resolve the configured hostname to its corresponding IPv4 address.

### Verification

Ping the server by IP address and then by hostname:

```text
ping 192.168.20.2
ping DNS-SVR
```

A successful hostname-based ping confirms that name resolution works for this record in the lab environment.

## 8. Validation Results

The following tests were completed successfully:

| Test | Result |
|---|---|
| Inter-VLAN connectivity | Passed |
| DNS server reachability by IP | Passed |
| DNS hostname resolution | Passed |

DHCP address allocation should also be verified by inspecting the client configuration and the MLS DHCP bindings.

Screenshots and additional evidence are stored in the `tests/` directory.

## 9. Troubleshooting Checklist

When connectivity fails, check the following:

1. Verify VLAN creation and VTP status.
2. Check trunk status and allowed VLANs.
3. Verify access-port VLAN assignments.
4. Confirm that the SVIs are operational.
5. Inspect DHCP pool configuration and client addressing.
6. Check the default gateway and DNS server settings.
7. Verify the DNS record and repeat the hostname-based connectivity test.

## 10. Security Considerations

- Configure access ports explicitly as access ports.
- Restrict allowed VLANs on trunk links.
- Disable DTP negotiation where it is unnecessary and supported by the platform.
- Protect VTP configuration against unauthorized changes.
- Apply appropriate switch management access controls.

This documentation describes the lab's architecture, configuration principles, and validation procedure.


## Author

**C. Victorin Vodinou**

_Networking_ | _Systems Administration_ | _Network Security_

[GitHub Profile](https://github.com/Victorin13)
[LinkedIn Profile](https://www.linkedin.com/in/victorin-vodinou/)
Gmail Prifile: victorinvodinou@gmail.com

This project is part of my hands-on networking lab portfolio.