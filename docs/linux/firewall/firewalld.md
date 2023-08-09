# Firewalld Basics and Usage with firewall-cmd:

Firewalld is a robust firewall management tool available for various Linux distributions. It provides a user-friendly interface for managing iptables, the packet filtering system within the Linux kernel. we will walk the fundamentals of setting up and configuring Firewalld using the firewall-cmd command-line utility. 

Detailed documentation about Firewalld can be found at the provided [link](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/security_guide/sec-using_firewalls#sec-Getting_started_with_firewalld).

## Basic Concepts in Firewalld

Firewalld introduces the concept of "zones," which dictate the behavior of the firewall based on the level of trust associated with the networks your system is connected to. Zones provide flexibility, enabling you to tailor rules depending on your environment. For instance, a laptop might need stricter rules on a public WiFi network while being more relaxed on a trusted home network. Predefined zones range from least trusted (e.g., drop, block) to most trusted (trusted, internal). The firewall-cmd utility facilitates the management of these zones.

## Zones in Firewalld
In Firewalld, zones are a crucial concept that dictates how the firewall treats network traffic based on the level of trust associated with the networks your system is connected to. Each zone defines a set of rules that determine which traffic is allowed and which is denied. Firewalld allows you to assign different interfaces to different zones, allowing for a flexible approach to network security. Zones are particularly useful for systems that might move between different networks, such as laptops, and for segmenting trust levels on servers.

## Default Zones
Firewalld comes with a set of predefined zones, each with a different level of trust. Here are the default zones provided by Firewalld, listed from least trusted to most trusted:

- **drop** : This is the lowest level of trust. All incoming connections are dropped without a reply, and only outgoing connections are possible.
- **block** : Similar to "drop," incoming connections are rejected with an ICMP message. Outgoing connections are allowed.
- **public** : Represents public, untrusted networks. You allow selected incoming connections on a case-by-case basis. This zone is useful for public Wi-Fi networks.
- **external** : Designed for external networks when your system is acting as a gateway. It configures NAT masquerading to keep your internal network private yet reachable.
- **internal** : Used for the internal side of a gateway. This zone assumes more trustworthiness for the computers and allows some additional services.
- **dmz** : Used for computers located in a DMZ (isolated computers that won't access the rest of your network). Only certain incoming connections are allowed.
- **work** : Meant for work machines. You trust most computers on the network, and a few more services might be allowed.
- **home** : Suitable for home environments. It implies a higher level of trust among computers, and more services will be accepted.
- **trusted** : The most open option, to be used sparingly. It trusts all machines in the network.

## Targets
Within each zone, there's a concept called "target." A target defines the default behavior of the firewall for incoming traffic. These targets are used to specify what happens when an incoming connection matches the rules of the zone. Here are the common targets used in Firewalld:

- **default** : This is the default target. It allows the default behavior for the zone, which can be controlled further through rule definitions.
- **accept** : Incoming traffic is accepted without restrictions.
- **drop** : Incoming traffic is dropped without any response.
- **reject** : Similar to "drop," but the system responds with an ICMP error message.
- **masquerade** : Used for source NAT (Network Address Translation) to modify the source IP address of outgoing packets.
- **redirect** : Used for destination NAT to redirect incoming packets to a different IP address and port.


## Rule Permanence

Firewalld rules can be immediate (active during the current session) or permanent (persist after reboot). The --permanent flag designates permanent rules that apply across reboots. This separation allows testing rules in the current session before making them permanent.

## Installing and Enabling Firewalld

To install Firewalld, use your package manager (e.g., `sudo apt install firewalld`). After installation, enable the service to start at boot:

```bash
sudo systemctl enable firewalld
```

It's recommended to configure your firewall rules and test them before enabling this behavior to avoid potential issues.

## Verifying Firewalld Status

- Check if Firewalld is up and running:

```bash
sudo firewall-cmd --state
```
A "running" status indicates that the firewall is operational with the default configuration.

## Exploring Current Firewall Rules

### Get the default zone:
```bash
firewall-cmd --get-default-zone
```
### List active zones:
```bash
firewall-cmd --get-active-zones
```
### View rules for a zone (e.g., "public"):
```bash
sudo firewall-cmd --zone=public --list-all
```
### Changing Zone of an Interface

### Move an interface to a different zone during the current session:

```bash
sudo firewall-cmd --zone=new-zone --change-interface=interface-name
```

## Adjusting Default Zone

- Change the default zone:

```bash
sudo firewall-cmd --set-default-zone=new-default-zone
```
## Adding Services to Zones

### List available services:
```bash
firewall-cmd --get-services
```
### Add a service to a zone (e.g., "public"):
```bash
sudo firewall-cmd --zone=public --add-service=service-name
```
### Make the change permanent:
```bash
sudo firewall-cmd --zone=public --permanent --add-service=service-name
```
## Opening Ports for Specific Applications

### Open a port for a zone (e.g., "public"):
```bash
sudo firewall-cmd --zone=public --add-port=port/protocol
```
### Open a port range:
```bash
sudo firewall-cmd --zone=public --add-port=start-end/protocol
```
## Defining Custom Services

### Copy an existing service definition (e.g., SSH) to create a custom service:
```bash
sudo cp /usr/lib/firewalld/services/ssh.xml /etc/firewalld/services/custom-service.xml
```
- Modify the custom service definition in /etc/firewalld/services/custom-service.xml.

## Reload the firewall to apply changes:

```bash
sudo firewall-cmd --reload
```
## Creating Your Own Zones

- Create a new zone (e.g., "custom-zone"):
```bash
sudo firewall-cmd --permanent --new-zone=custom-zone
```
- Reload the firewall to activate the new zone:
```bash
sudo firewall-cmd --reload
```
- Assign interfaces to the new zone (if interface is not already assigned ):
```bash
sudo firewall-cmd --zone=custom-zone --add-interface=interface-name
```

Firewalld provides an effective way to manage firewall rules using zones and services. By understanding these concepts and the usage of the firewall-cmd utility, you can create tailored firewall configurations that cater to your network environment. Additionally, the installation of Firewalld with nftables enhances your firewall capabilities, contributing to improved security and network management.