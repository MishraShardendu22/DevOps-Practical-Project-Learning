# Server Virtualization using VirtualBox

## Objective

To understand and configure virtual machines using different networking modes (NAT and Host-only) and test communication between VMs, host machine, and network services. This practical demonstrates the fundamentals of server virtualization, network configuration, and service deployment in isolated environments.

## Tools Used

- VirtualBox
- Ubuntu Server 22.04
- Apache2
- OpenSSH
- Wireshark

## Procedure

### VM Setup

1. **Created two Ubuntu Server 22.04 virtual machines** in VirtualBox:
   - VM1: Server (hosting web and SSH services)
   - VM2: Client (for testing connectivity)

### Network Configuration

1. **Configured network adapters**:
   - NAT adapter for internet access
   - Host-only adapter for internal VM-to-VM and host-to-VM communication
   - Assigned static IP addresses to Host-only adapters

### Service Installation

1. **Installed and configured services on Server VM**:
   - Apache2 web server for HTTP service
   - OpenSSH server for remote access
   - Verified services are running and listening on correct ports

### Connectivity Testing

1. **Verified ping between VMs** using ICMP protocol to test basic network connectivity
2. **Verified SSH connection from host to server VM** to ensure remote access functionality
3. **Accessed Apache web page from client VM** using the server's IP address in a browser
4. **Captured network packets using Wireshark** to analyze traffic patterns and protocols

## Observations

### Network Connectivity

- VMs communicated successfully over the Host-only network
- Ping responses showed low latency between VMs (< 1ms)
- Both VMs could access the internet through NAT adapter

### Service Access

- Host machine successfully accessed Server VM using SSH over Host-only network
- Authentication worked correctly with password/key-based login using VirtualBox. The practical demonstrated:
- Effective use of multiple network adapters for different purposes
- Successful deployment of network services in virtualized environments
- Proper isolation and communication between VMs and host machine
- The ability to monitor and analyze network traffic using packet capture tools

This exercise provides hands-on experience with core DevOps concepts including infrastructure virtualization, network configuration, and service deployment that are essential for modern IT operations

- Client VM successfully accessed Apache web server and loaded the default webpage
- HTTP service responded on port 80 as expected

### Packet Analysis

- ICMP echo request/reply packets observed during ping tests
- HTTP GET requests and responses captured in Wireshark
- SSH encrypted traffic visible on port 22
- Clear distinction between NAT and Host-only network traffic patterns

## Conclusion

Server virtualization and VM networking were successfully implemented.
