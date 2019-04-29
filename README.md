# Azure Networking : Enterprise Hybrid Network
## Overview

Create an Enterprise Hybrid Network with a Hub and Spoke Topology in the Azure Portal

## Hybrid Hub and Spoke Topology

![image](./images/0a.png)

# Exercises
## [Back to Excercises](#exercises)

* [Create Virtual Networks](#create-virtual-networks)
* [Create VNET Peerings](#create-vnet-peerings)
* [Create Virtual Machines](#create-virtual-machines)
* [Create Network Security Groups](#create-network-security-groups)
* [Connect OnPrem to Hub via VPN Tunnel](#connect-onprem-to-hub-via-vpn-tunnel)
* [Check Connectivity](#check-connectivity)
* [Create Azure Firewall](#create-azure-firewall)
* [Create User Defined Routes and Network Routing Rules](#create-user-defined-routes-and-network-routing-rules)
* [Check SpokeA to Hub to SpokePCI Connectivity](#Check-SpokeA-to-Hub-to-SpokePCI-Connectivity)
* [Create Application Security Groups for Microsegmentation](#Create-Application-Security-Groups-for-Microsegmentation)

# Create Virtual Networks
## [Back to Excercises](#exercises)

* [Create On Premises Network](#create-on-premises-network)
* [Create Hub Network](#create-hub-network)
* [Create SpokeA Network](#create-spokea-network)
* [Create SpokePCI Network](#create-spokepci-network)


## Create On Premises Network

1. Create a Resource Group named OnPrem
![image](./images/1a.png)
1. Create a Virtual Network in the OnPrem Resource Group
    * Name: vn-onprem
    * Address Space: 192.168.0.0/16
    * Resource Group: OnPrem
    * Region: South Central US
    * Subnet: sn-back
    * Subnet Address Range: 192.168.0.0/24
    * Leave other defaults, they can be changed later
![image](./images/1b.png)
1. Add a Gateway Subnet to the OnPrem VNet
    * Click on +Gateway Subnet
![image](./images/1c.png)
    * Accept the Defaults
![image](./images/1d.png)
1. Deploy a VPN Gateway (This will take 30-40 mins)
    * Name: vng-onprem
    * Region: South Central US
    * Gateway Type: VPN
    * VPN Type: Route-Based
    * SKU: VpnGw1
    * Virtual Network: vn-onprem
![image](./images/1e.png)
    * Create a new Public IP Address
    * Name: pip-vng-onprem
    * Leave all other defaults
![image](./images/1f.png)
    * Click Review & Create > Create

## Create Hub Network
### [Back to Excercises](#exercises)

1. Create a Resource Group named Hub
![image](./images/2a.png)
1. Create a Virtual Network in the Hub Resource Group
    * Name: vn-hub
    * Address Space: 10.4.0.0/16
    * Resource Group: Hub
    * Region: South Central US
    * Subnet: sn-back
    * Subnet Address Range: 10.4.0.0/24
    * Leave other defaults, they can be changed later
![image](./images/2b.png)
1. Add a Gateway Subnet to the Hub VNet
    * Click on +Gateway Subnet
![image](./images/2c.png)
    * Accept the Defaults
![image](./images/2d.png)
1. Deploy a VPN Gateway (This will take 30-40 mins)
    * Name: vng-hub
    * Region: South Central US
    * Gateway Type: VPN
    * VPN Type: Route-Based
    * SKU: VpnGw1
    * Virtual Network: vn-hub
![image](./images/2e.png)
    * Create a new Public IP Address
    * Name: pip-vng-hub
    * Leave all other defaults
![image](./images/2f.png)
    * Click Review & Create > Create

## Create SpokeA Network
### [Back to Excercises](#exercises)

1. Create a Resource Group named SpokeA
![image](./images/3a.png)
1. Create a Virtual Network in the Hub Resource Group
    * Name: vn-spokea
    * Address Space: 10.5.0.0/16
    * Resource Group: SpokeA
    * Region: South Central US
    * Subnet: sn-back
    * Subnet Address Range: 10.5.0.0/24
    * Leave other defaults, they can be changed later
![image](./images/3b.png)

## Create SpokePCI Network
### [Back to Excercises](#exercises)

1. Create a Resource Group named SpokePCI
![image](./images/3aPCI.png)
1. Create a Virtual Network in the Hub Resource Group
    * Name: vn-spokepci
    * Address Space: 10.6.0.0/16
    * Resource Group: SpokePCI
    * Region: South Central US
    * Subnet: sn-back
    * Subnet Address Range: 10.6.0.0/24
    * Leave other defaults, they can be changed later
![image](./images/3bPCI.png)


# Create VNET Peerings
## [Back to Excercises](#exercises)
* [Create Hub to Spoke Peering](#create-hub-to-spokea-peering)
* [Validate Spoke to Hub Peering](#validate-spoke-to-hub-peering)


## Create Hub to Spoke Peering
    * Goal is to be able to get from SpokeA to OnPrem through the Hub
1. Navigate to the Hub Resource Group > vn-hub > peering > + Add
![image](./images/4a.png)
    * Name of peering from vn-hub to vn-spokea: pr-HubToSpokeA
    * Virtual Netowrk: vn-spokea
    * Name of peering from vn-spokea to vn-hub: pr-SpokeAToHub
    * Allow virutal network acess: Enabled
    * Allow Forwarded traffic from vn-hub to vn-spokea: Enabled
    * Allow Forwarded traffic from vn-spokea to vn-hub: Diabled
    * Allow Gateway transit: Checked
![image](./images/4b.png)
![image](./images/4c.png)
1. Navigate to the Hub Resource Group > vn-hub > peering > + Add
![image](./images/4a.png)
    * Name of peering from vn-hub to vn-spokepci: pr-HubToSpokePCI
    * Virtual Netowrk: vn-spokea
    * Name of peering from vn-spokea to vn-hub: pr-SpokePCIToHub
    * Allow virutal network acess: Enabled
    * Allow Forwarded traffic from vn-hub to vn-spokepci: Enabled
    * Allow Forwarded traffic from vn-spokepci to vn-hub: Diabled
    * Allow Gateway transit: Checked    
![image](./images/4b1.png)

## Validate Spoke to Hub Peering
### [Back to Excercises](#exercises)

* Goal it to be able to get from SpokeA to SpokePCI and vise versa through the Hub
1. Navigate to the SpokeA Resource Group > vn-spokea > peering 
    * Let's validate that the peering is enabled correctly. You should see pr-SpokeAToHub
![image](./images/4d.png)
    * Click on pr-SpokeAToHub. You should see that "Use remote gateways" is checked.
![image](./images/4e.png)
1. Navigate to the SpokeA Resource Group > vn-spokepci > peering 
    * Let's validate that the peering is enabled correctly. You should see pr-SpokePCIToHub
![image](./images/4d1.png)
    * Click on pr-SpokePCIToHub. You should see that "Use remote gateways" is checked.
![image](./images/4e1.png)

# Create Virtual Machines
## [Back to Excercises](#exercises)

* [Create OnPrem VM](#create-onprem-vm)
* [Create Hub VM](#create-hub-vm)
* [Create SpokeA VM](#create-spokea-vm)
* [Create SpokePCI VMs](#create-spokepci-vms)


## Create OnPrem VM
1. Click on + Create Resource in Portal
1. Choose Windows Server 2016 Datacenter
1. Basic Config
    * Resource Group: OnPrem
    * VM NAme: vm-onprem1
    * Region: South Central US
    * Availability Options: No infrastructure redundancy required
    * Image: Windows Server 2016 Datacenter
    * Size: Standard DS1 v2
    * UserName: <yourusername>
    * Password: <strongpassword>
    * Public inbound ports: Allowed Selected Ports (RDP)
![image](./images/5a.png)
1. Disks
    * Leave Defaults
![image](./images/5b.png)
1. Network
    * Virtual Network: vn-onprem
    * Subnet: sn-back
    * Public IP: defalut (new vm-onprem1-ip)
    * Network Security Group: None (we'll add one to the VNET/Subnet Later)
    * Leave all other defaults
![image](./images/5c.png)
1. Click on Review + create > Create

## Create Hub VM
1. Click on + Create Resource in Portal
1. Choose Windows Server 2016 Datacenter
1. Basic Config
    * Resource Group: Hub
    * VM NAme: vm-hub1
    * Region: South Central US
    * Availability Options: No infrastructure redundancy required
    * Image: Windows Server 2016 Datacenter
    * Size: Standard DS1 v2
    * UserName: <yourusername>
    * Password: <strongpassword>
    * Public inbound ports: Allowed Selected Ports (RDP)
![image](./images/5d.png)
1. Disks
    * Leave Defaults
![image](./images/5b.png)
1. Network
    * Virtual Network: vn-hub
    * Subnet: sn-back
    * Public IP: defalut (new vm-hub1-ip)
    * Network Security Group: None (we'll add one to the VNET/Subnet Later)
    * Leave all other defaults
![image](./images/5e.png)
1. Click on Review + create > Create

## Create SpokeA VM
1. Click on + Create Resource in Portal
1. Choose Windows Server 2016 Datacenter
1.  Basic Config
    * Resource Group: SpokeA
    * VM NAme: vm-spokea1
    * Region: South Central US
    * Availability Options: No infrastructure redundancy required
    * Image: Windows Server 2016 Datacenter
    * Size: Standard DS1 v2
    * UserName: <yourusername>
    * Password: <strongpassword>
    * Public inbound ports: Allowed Selected Ports (RDP)
![image](./images/5f.png)
1. Disks
    * Leave Defaults
![image](./images/5b.png)
1. Network
    * Virtual Network: vn-spokea
    * Subnet: sn-back
    * Public IP: defalut (new vm-spokea1-ip)
    * Network Security Group: None (we'll add one to the VNET/Subnet Later)
    * Leave all other defaults
![image](./images/5g.png)
1. Click on Review + create > Create

## Create SpokePCI VMs
1. Click on + Create Resource in Portal
1. Choose Windows Server 2016 Datacenter
1.  Basic Config
    * Resource Group: SpokePCI
    * VM NAme: vm-spokepci1
    * Region: South Central US
    * Availability Options: No infrastructure redundancy required
    * Image: Windows Server 2016 Datacenter
    * Size: Standard DS1 v2
    * UserName: <yourusername>
    * Password: <strongpassword>
    * Public inbound ports: Allowed Selected Ports (RDP)
![image](./images/5f1.png)
1. Disks
    * Leave Defaults
![image](./images/5b.png)
1. Network
    * Virtual Network: vn-spokepci
    * Subnet: sn-back
    * Public IP: defalut (new vm-spokepci1-ip)
    * Network Security Group: None (we'll add one to the VNET/Subnet Later)
    * Leave all other defaults
![image](./images/5g1.png)
1. Click on Review + create > Create
1. Repeat the previous 6 steps to create a 2nd VM in the vn-spokepci VNet. However, this time replace the name of the vm name with: <b>vm-spokepci2</b>

# Create Network Security Groups
## [Back to Excercises](#exercises)

* [Create OnPrem NSG](#create-onprem-nsg)
* [Create Hub NSG](#create-hub-nsg)
* [Create SpokeA NSG](#create-spokea-nsg)
* [Create SpokePCI NSG](#create-spokepci-nsg)

## Create OnPrem NSG

1. Click on + Create Resource in Portal
1. Type Network Security Group in Search Box
1. Select Network Security Group > Create
    * Name: nsg-onprem-back
    * Resource Group: OnPrem
    * Location: South Central US
![image](./images/6a.png)
    * Navigate to Hub Resource Group > nsg-onprem-back 
    * Click on Subnets > + Associate  
![image](./images/6b.png)
    * Virtual Network: vn-onprem
    * Subnet: sn-back
![image](./images/6c.png)
1. Add Inbound RDP Rule from your IP Address
    * Type whatismyipaddress in your favorite search engine, make note of your IP address
    * Click on Inbound Security Rules > + Add
![image](./images/6j.png)
    * Source: IP Address
    * Source IP Addresses/CIDR ranges: <youripaddress>/32 or range
    * Source port ranges: *
    * Destination: Any
    * Destination port ranges: 3389
    * Protocol: Any
    * Priority: 100
    * Name: RDP_3389_Rule
![image](./images/6k.png)

### Create Hub NSG

1. Click on + Create Resource in Portal
1. Type Network Security Group in Search Box
1. Select Network Security Group > Create
    * Name: nsg-hub-back
    * Resource Group: Hub
    * Location: South Central US
![image](./images/6d.png)
    * Navigate to Hub Resource Group > nsg-hub-back 
    * Click on Subnets > + Associate    
![image](./images/6e.png)
    * Virtual Network: vn-hub
    * Subnet: sn-back
![image](./images/6f.png)
1. Add Inbound RDP Rule from your IP Address
    * Type whatismyipaddress in your favorite search engine, make note of your IP address
    * Click on Inbound Security Rules > + Add
![image](./images/6j.png)    
    * Source: IP Address
    * Source IP Addresses/CIDR ranges: <youripaddress>/32 or range
    * Source port ranges: *
    * Destination: Any
    * Destination port ranges: 3389
    * Protocol: Any
    * Priority: 100
    * Name: RDP_3389_Rule
![image](./images/6k.png)

### Create SpokeA NSG

1. Click on + Create Resource in Portal
1. Type Network Security Group in Search Box
1. Select Network Security Group > Create
    * Name: nsg-spokea-back
    * Resource Group: SpokeA
    * Location: South Central US
![image](./images/6g.png)
    * Navigate to Hub Resource Group > nsg-spokea-back 
    * Click on Subnets > + Associate    
![image](./images/6h.png)
    * Virtual Network: vn-spokea
    * Subnet: sn-back
![image](./images/6i.png)    
1. Add Inbound RDP Rule from your IP Address
    * Type whatismyipaddress in your favorite search engine, make note of your IP address
    * Click on Inbound Security Rules > + Add
![image](./images/6j.png)    
    * Source: IP Address
    * Source IP Addresses/CIDR ranges: <youripaddress>/32 or range
    * Source port ranges: *
    * Destination: Any
    * Destination port ranges: 3389
    * Protocol: Any
    * Priority: 100
    * Name: RDP_3389_Rule
![image](./images/6k.png)

### Create SpokePCI NSG

1. Click on + Create Resource in Portal
1. Type Network Security Group in Search Box
1. Select Network Security Group > Create
    * Name: nsg-spokepci-back
    * Resource Group: SpokePCI
    * Location: South Central US
![image](./images/6g1.png)
    * Navigate to Hub Resource Group > nsg-spokepci-back 
    * Click on Subnets > + Associate    
![image](./images/6h1.png)
    * Virtual Network: vn-spokepci
    * Subnet: sn-back
![image](./images/6k1.png)    
1. Add Inbound RDP Rule from your IP Address
    * Type whatismyipaddress in your favorite search engine, make note of your IP address
    * Click on Inbound Security Rules > + Add
![image](./images/6j.png)    
    * Source: IP Address
    * Source IP Addresses/CIDR ranges: <youripaddress>/32 or range
    * Source port ranges: *
    * Destination: Any
    * Destination port ranges: 3389
    * Protocol: Any
    * Priority: 100
    * Name: RDP_3389_Rule
![image](./images/6k.png)

## Connect OnPrem to Hub via VPN Tunnel
## [Back to Excercises](#exercises)

### Configure OnPrem VPN Gateway
1. Navigate to OnPrem Resource Group > vng-onprem
1. Click on Connections > + Add
![image](./images/7a.png)
    * Name: connOnPremToHub
    * Connection Type: VNet-to-VNet
    * First virtual network gateway: vng-onprem
    * Second virtual network gateway: vng-hub
    * Shared key (PSK): <yourkey> 
1. Click Ok
![image](./images/7b.png)
### Configure Hub VPN Gateway
1. Navigate to Hub Resource Group > vng-hub
1. Click on Connections > + Add
    * ![image](./images/7c.png)
    * Name: connHubToOnPrem
    * Connection Type: VNet-to-VNet
    * First virtual network gateway: vng-hub
    * Second virtual network gateway: vng-onprem
    * Shared key (PSK): <yourkey> (same key as above)
1. Click Ok
    * ![image](./images/7d.png)
1. After some time you should see the status change to connected for each connection.
    * ![image](./images/7e.png)
1. Validate the Routes by navigating to the Hub Resource Group > and clicking on the Nick for the vm-hub1 virtual machine.
    * ![image](./images/7f.png)
1. Click on the Effective Routes. You should see the routes for the Onprem, Hub, SpokeA, and Internet
    * ![image](./images/7f.png)

# Check Connectivity
## [Back to Excercises](#exercises)
1. RDP into the following VMs
    * vm-onprem1 
    * vm-hub1 
    * vm-spokea1
    * vm-spokepci1
    * vm-spokepci2
1. You will need to enable icmp or disable the guest firewall on the OS.
    * To enable icmp you can run the following command from PowerShell (Run as Administrator) on each Virtual Machine
    ```powershell
    New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
    ```
1. Ping test from vm-onprem1 to vm-hub1, vm-spokea1, vm-spokepci1, vm-spokepci2
    * ![image](./images/pta.png)
1. Ping test from vm-hub1, vm-spokea1, vm-spokepci1, vm-spokepci2 to vm-onprem1
    * ![image](./images/ptb.png)
    * ![image](./images/ptc.png)
    * ![image](./images/ptd.png)
    * ![image](./images/pte.png)
1. Validate that SpokeA cannot communicate with SpokePCI and vice versa
    * Ping test from vm-spokea1 to vm-spokepci1
        * ![image](./images/ptf.png)
    * Ping test from vm-spokepci1 to vm-spokea1
        * ![image](./images/ptg.png)

# Create Azure Firewall
## [Back to Excercises](#exercises)

1. Click on + Create Resource in Portal
1. Type firewall in the search box 
1. Create an AzureFirewallSubnet in the vn-hub virtual network
    * ![image](./images/8a.png)
1. Type Firewall Group in Search Box
    * Resource group: Hub
    * Name: azfw-hub
    * Region: South Central US
    * Choose a virtual network: Use Existing
    * Virtual Network: vn-hub
    * Public IP address: Create new
    * Public IP address name: azureFirewalls-ip
1. Navigate to the created Azure Firewall and note the Private IP Address
    * ![image](./images/8b.png)


# Create User Defined Routes and Network Routing Rules
## [Back to Excercises](#exercises)
## Create User Defined Routes

### Create SpokeA User Defined Route Table
1. Click on + Create Resource in Portal
1. Type Route Table in the search box
1. Click Route Table > Create
    * Name: udr-SpokeA
    * Resource Group SpokeA
    * Location: South Central US
    * Virtual network gateway route propagation: Enabled
    * ![image](./images/9a.png)
1. Navigate to the created route table and create a new Route
    * ![image](./images/9d.png)
    * Route Name: routeSpokeAToHubFW
    * Address Prefix: 10.6.0.0/16
    * Next Hop Type: Virtual Appliance
    * Next Hop Address: 10.4.2.4
    * ![image](./images/9e.png)
1. Now associate the route table to the sn-back subnet in vn-spokea
    * ![image](./images/9f.png)
    * Virtual Network: vn-spokea
    * Subnet: sn-back
    * ![image](./images/9g.png)

### Create SpokePCI User Defined Route Table
1. Click on + Create Resource in Portal
1. Type Route Table in the search box
1. Click Route Table > Create
    * Name: udr-SpokePCI
    * Resource Group SpokePCI
    * Location: South Central US
    * Virtual network gateway route propagation: Enabled
    * ![image](./images/10a.png)
1. Navigate to the created route table and create a new Route
    * ![image](./images/10b.png)
    * Route Name: routeSpokePCIToHubFW
    * Address Prefix: 10.5.0.0/16
    * Next Hop Type: Virtual Appliance
    * Next Hop Address: 10.4.2.4
    * ![image](./images/10c.png)
1. Now associate the route table to the sn-back subnet in vn-spokea
    * ![image](./images/10d.png)
    * Virtual Network: vn-spokepci
    * Subnet: sn-back
    * ![image](./images/10e.png)

### Create Azure Firewall Network Rule
1. Navigate to the Azure Firewall and create a network rule
    * ![image](./images/9b.png)
    * Name: firewallRule
    * Priority: 100
    * Action: Allow
    * Rules 1:
        * Name: SpokeA-To-SpokePCI
        * Protocol: Any
        * Source Addresses: 10.5.0.0/16
        * Destination Addresses: 10.6.0.0/16
        * Destination Ports: * 
    * Rules 2:
        * Name: SpokePCI-To-SpokeA
        * Protocol: Any
        * Source Addresses: 10.6.0.0/16
        * Destination Addresses: 10.5.0.0/16
        * Destination Ports: *
    * ![image](./images/9c.png)

# Check SpokeA to Hub to SpokePCI Connectivity
## [Back to Excercises](#exercises)
* Let's validate that we can now route traffic from SpokeA to SpokePCI and vice vera
1. RDP into the following VMs
    * vm-spokea1
    * vm-spokepci1
    * vm-spokepci2
1. You will need to enable icmp or disable the guest firewall on the OS.
    * To enable icmp you can run the following command from PowerShell (Run as Administrator) on each Virtual Machine
    ```powershell
    New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
    ```
1. Ping test from vm-spokea1 to vm-spokepci1 and vm-spokepci2
    * ![image](./images/pth.png)
1. Ping test from vm-spokepci1, vm-spokepci2 to vm-spokea1
    * ![image](./images/pti.png)
    * ![image](./images/ptj.png)


# Create Application Security Groups for Microsegmentation
## [Back to Excercises](#exercises)

* Goal is to create Microsegmentation between vm-spokepci1 and vm-spokepci2. We do not want them to be able to communicate with each other. So we will use Application Security Groups (ASGs) to accomplish this.

## Create ASG for vm-spokepci1
1. Click on + Create Resource in Portal
1. Type Application Secruity Group in the Search Box
1. Select Application Security Group > Create
    * Resource Group: SpokePCI
    * Name: asg-vm-spokepci1
    * Region: South Central US
    * ![image](./images/11a.png)
1. Click on + Create Resource in Portal
1. Select Application Security Group > Create
    * Resource Group: SpokePCI
    * Name: asg-vm-spokepci2
    * Region: South Central US
    * ![image](./images/11b.png)
1. Validate Connectivity between vm-spokepci1 and vm-spokepcivm2
    * ![image](./images/11c.png)
    * ![image](./images/11d.png)

## Associate the VMs with the ASGs
1. Associate vm-spokepci1 to asg-spokepci1
1. Navigate to vm-spokepci1 > Networking > Application Secruity Groups >  Configure the applicaiton security groups
    * ![image](./images/11e.png)
    * Select asg-vm-spokepci1 and click Save
    * ![image](./images/11f.png)
1. Navigate to vm-spokepci2 > Networking > Application Secruity Groups >  Configure the applicaiton security groups
    * ![image](./images/11g.png)
    * Select asg-vm-spokepci2 and click Save
    * ![image](./images/11h.png)

## Create NSG Rules using ASGs for Microsegmentation
1. Navigate to nsg-spokepci-back > Inbound Security Rules > + Add
    * ![image](./images/12a.png)
    * Source: Application Security Group
    * Source ASG: asg-vm-spokepci1
    * Source port ranges: *
    * Destination: Application Security Group
    * Destination ASG: asg-vm-spokepci2
    * Destination: *
    * Protocol: Any
    * Action: Deny
    * Priority: 110
    * Name: DenyVmspokepci1ToVmspokepci2
    * ![image](./images/12b.png)
1. Validate Connectivity between vm-spokepci1 and vm-spokepcivm2 has been blocked
    * ![image](./images/12c.png)
    
