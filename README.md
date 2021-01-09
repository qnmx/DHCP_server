#DHCP Server project

Technology used in the project:
- GNS3
- Linux/Ubuntu Server/Kali
- Cisco IOS
- Cisco L2/L3 switches
- Cisco routers
- Ubuntu docker images
- VPCS as a client stations

Project description:

In this project I am setting up a DHCP server to be used in the internal network. The DHCP server is deployed on a Ubuntu Server. As a DHCP program I have choosen ISC DHCP, https://www.isc.org/dhcp/ 

The lab consist two sites, one headquarter and branch location. The DHCP server is deployed in the HQ. Router at a branch site will point DHCP requests to the main router.

At each location Cisco switches are configured with DHCP snooping and Dynamic ARP inspection for additional security. 

I will introduce a rougue DHCP at each location and will verify DHCP snooping state.

++ more details to be added
