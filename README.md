# DHCP Server project

The technology used in the project:
- GNS3
- Linux/Ubuntu Server
- Cisco IOS
- Cisco L2/L3 switches
- Cisco routers
- Ubuntu docker images
- VPCS as client stations

Project description:

In this project, I am setting up a DHCP server to be used in the internal network. The DHCP server is deployed on a Ubuntu Server. As a DHCP program, I have chosen ISC DHCP, https://www.isc.org/dhcp/

The lab consists of two sites, one headquarters and branch location. The DHCP server is deployed in the HQ. The router at a branch site will point DHCP requests to the main router.

At each location, the Cisco switch is configured with DHCP snooping.

## DHCP server configuration

1. To download the DHCP server software, run the commands:

sudo apt update
sudo apt install isc-dhcp-server

2. Once this is done, the next step is to configure the DHCP server.

Set the interface, on which the DHCP daemon will serve requests.

Configuration for that setting is located in:
/etc/default/isc-dhcp-server

Look for a line that starts with: "# On what interfaces should the DHCP server (dhcpd) serve DHCP requests?". Add correct interface and save the config.

The interface details could be located by running "ip a" command.

3. Edit /etc/dhcp/dhcpd.conf file

Set required network information that will be shared to hosts, e.i. IP ranges, Default Gateway, Name Servers, etc.

4. Start and enable dhcp service:

sudo systemctl start isc-dhcp-server
sudo systemctl enable isc-dhcp-server

5. Add a rule to a firewall:

sudo ufw allow 67/udp
sudo ufw reload

Status of the firewall could be verified by running: sudo ufw status

-----------------------------------

After configuring the DHCP server, next we should apply DHCP Snooping in Cisco switches. DHCP Snooping is a technique to protect the network from unauthorized or wrongly configured DHCP servers.

The configuration of that security feature takes places in two modes.

Global configuration:

- Turning on DHCP snooping globally
SW1-main(config)#ip dhcp snooping

- Applying DHCP snooping to VLAN

SW1-main(config)#ip dhcp snooping vlan [VLAN number]

By entering the above commands, we have enabled DHCP snooping feature on a particular VLAN. All ports on the switch are assigned a role of untrusted. Ports will only now pass the DHCP's client request messages, which are Discover and Request. Any DHCP server responses will be blocked. DHCP server messages are Offer and Acknowledge

To allow DHCP server messages to pass through, interface connected to the DHCP server need to be trusted. To do this apply a command:

Interface level configuration

SW1-main(config-if)#ip dhcp snooping trust

Repeat the command on each interface where DHCP server messages are expected.

-----------------------------------

In my topology, I have a Router on a stick configuration to enable Inter-VLAN communication. By default, when DHCP snooping is configured, insertion of option 82 is enabled.  

To allow DHCP messages to pass through the router this option need to be disabled, otherwise, the router will silently drop the packets.

To disable option insertion run the command:

SW1-main(config)#no ip dhcp snooping information option

------------------------------------
Verifications

- Assigning an IP address to a host via DHCP

With DHCP snooping on
PC9> ip dhcp -r
DORA IP 10.0.100.4/24 GW 10.0.100.254

To test DHCP snooping, I have created a secondary DHCP server and I attached it to one of the subnets. Then in GNS3, I applied a filter to drop packets that are flowing to the main DHCP server. This is to create a congestion on that interface so the requests from hosts can not be processed.

In the scenario, where there are two DHCP servers on the same subnet, the host will take settings from a server that responded first. Thus I am creating network congestion on the link to main DHCP server so the Rogue DHCP server can offer the network details. 

Without DHCP snooping on
PC9> ip dhcp -r
DDORA IP 10.0.100.150/24 GW 10.0.100.200

------------------------------------
I am attaching all configuration files, should you like to replicate the lab.

Securing the DHCP server on a network is an important task. This lab serves a purpose to showcase how to configure the DHCP server and protect it on a switch level. Other various security features could be applied to the topology however, that was not the main focus. 

I am creating more labs where I will showcase various technology. Please check my GitHub for more labs.

Thank you for your attention.