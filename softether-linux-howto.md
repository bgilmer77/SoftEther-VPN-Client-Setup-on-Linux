# SoftEther Linux Howto

This how-to is written to support those who wish to connect to a SoftEther VPN established by the Advanced Media Workflow Association (AMWA).
This VPN has been established to provide remote connectivity in support of AMWA workshops.
The purpose of these workshops is to further the development of technical specifications issues by the AMWA.

This how-to is written as a quick guide for those who want to install and configure the SoftEther VPN Client on a Linux system.
Specifically, these instructions were written for an Intel NUC running Kali Linux.
These instructions were based upon SoftEther documentation and an article found at https://www.cactusvpn.com.

## Install SoftEther
- Download the appropriate version of the VPN client from www.softether.com
- Run the `.install.sh` script that is provided with the client
- Start the VPN Client `vpnclient start`

## Configure a VPN account
The `vpncmd` utility is used to configure accounts and to connect to a VPN.
This utility connects to the VPN client running on your local machine.

- Run `vpncmd` and choose option 2.  Press `ENTER` to connect to the client running on your local machine.
- `check` to test your installation.
- `NicCreate vpn_se` to create a virtual VPN interface on your computer
- `AccountCreate [accountName]` to create your account. The VPN server URL is `nmos-api-test.softether.net`.  You will need to contact the AMWA to obtain login credentials.
- `AccountPassword [accountName]` to enter your VPN account password.  Specify `Standard` when requested.

## Connect to SoftEther VPN
- Run `vpncmd` if you have not already done so.  Select option 2 and press `ENTER` to connect to your local VPN Client.
- `AccountConnect [accountName]` to connect to the VPN server
- `AccountList` shows connection settings.  Look for `Connected` under `Status`
- Enter `^D` to exit the `vpncmd` utility
- At the Linux command prompt, enter `dhclient vpn_vpn_se` to obtain an IP address from the VPN DHCP server
- `ip a` to show the `vpn_se` interface and the assigned IPv4 address

## Disconnect from SoftEther VPN
- Run `vpncmd` if you have not already done so.  Select option 2 and press `ENTER` to connect to your local VPN Client.
- `AccountDisconnect [accountName]` to disconnect from the VPN server
- Enter `^D` to exit the `vpncmd ` utility
- At the Linux command prompt, enter `vpnclient stop`
