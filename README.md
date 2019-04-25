# Azure Networking : Enterprise Hybrid Network
## Overview

create an Enterprise Hybrid Network

* [Create On Premises Network](#create-on-premises-network)
* [Create Hub Network](#create-the-visual-studio-2017-solution)
* [Create Spoke Network](#create-models-class-library)

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
1. Deploy a VPN Gateway (This will take 3-40 mins)
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
1. Deploy a VPN Gateway (This will take 3-40 mins)
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
1. Add a App Subnet to the SpokeA VNet
    * Click on +Subnet
    ![image](./images/3c.png)
    * Name: vn-spokea
    * Address Range: 10.5.1.0/24
    ![image](./images/3d.png)

## Create Hub to SpokeA Peering
    * Goal it to be able to get from SpokeA to OnPrem through the Hub
1. Navigate to the Hub Resource Group > vn-hub > peering > + Add

    ![image](./images/4a.png)
    * Name of peering from vn-hub to vn-spokea: pr-HubToSpokeA
    * Virtual Netowrk: vn-spokea
    * Name of peering from vn-spokea to vn-hub
    * Allow virutal network acess: Enabled
    * Allow Forwarded traffic from vn-hub to vn-spokea: Enabled
    * Allow Forwarded traffic from vn-spokea to vn-hub: Diabled
    * Allow Gateway transit: Checked
    ![image](./images/4b.png)
    ![image](./images/4c.png)

## Create SpokeA to Hub Peering
    * Goal it to be able to get from SpokeA to OnPrem through the Hub
1. Navigate to the Hub Resource Group > vn-hub > peering > + Add

    ![image](./images/4a.png)
    * Name of peering from vn-hub to vn-spokea: pr-HubToSpokeA
    * Virtual Netowrk: vn-spokea
    * Name of peering from vn-spokea to vn-hub
    * Allow virutal network acess: Enabled
    * Allow Forwarded traffic from vn-hub to vn-spokea: Enabled
    * Allow Forwarded traffic from vn-spokea to vn-hub: Diabled
    * Allow Gateway transit: Checked
    ![image](./images/4b.png)
    ![image](./images/4c.png)