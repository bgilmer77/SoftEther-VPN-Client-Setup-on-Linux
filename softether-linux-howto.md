# SoftEther Linux Howto

This how-to is written to support those who wish to connect to a SoftEther VPN established by the Advanced Media Workflow Association (AMWA).
This VPN has been established to provide remote connectivity in support of AMWA workshops.
The purpose of these workshops is to further the development of technical specifications issues by the AMWA.

This how-to is written as a quick guide for those who want to install and configure the SoftEther VPN Client on a Linux system.
Specifically, these instructions were written for an Intel NUC running Kali Linux.
These instructions were based upon SoftEther documentation and an article found at https://www.cactusvpn.com/tutorials/how-to-set-up-softether-vpn-client-on-linux/.

## Install SoftEther
- Download the appropriate version of the VPN client from www.softether.org
- Run the `.install.sh` script that is provided with the client
- Start the VPN Client `vpnclient start`

## Configure a VPN account
The `vpncmd` utility is used to configure accounts and to connect to a VPN.
This utility connects to the VPN client running on your local machine.

- Run `vpncmd` and choose option 2.  Press `ENTER` to connect to the client running on your local machine.
- `check` to test your installation.
- `NicCreate vpn_se` to create a virtual VPN interface on your computer
- `AccountCreate [accountName]` to create your account.
You will need the following information:
  * User Name (You will need to contact the AMWA to obtain login credentials)
  * Account Name (this may be any name you choose)
  * VPN server URL (The current AMWA URL is `amwa-nmos-vpn.softether.net`)
  * port number for the VPN server. In the case of the AMWA VPN it is 443
  * Virtual hub to connect to (For AMWA use `NMOS-VPN`)
  * Virtual Network Adapter Name (You created this earlier - `vpn_se`)
  
  Note: during account creation, when asked, `Desination VPN Server Host Name and Port Number:`, enter the information as follows:
  `amwa-nmos-vpn.softether.net:443`

- `AccountPassword [accountName]` to enter your VPN account password.  Specify `Standard` when requested.

## Connect to SoftEther VPN
- Run `vpncmd` if you have not already done so.  Select option 2 and press `ENTER` to connect to your local VPN Client.
- `AccountConnect [accountName]` to connect to the VPN server
- `AccountList` shows connection settings.  Look for `Connected` under `Status`
- Enter `^D` to exit the `vpncmd` utility

## Modify Route Table
Now that you are connected to the VPN and have an IP address, you must modify your IP route table to send traffic through the VPN.
There are two procedures below.
The first will route ALL traffic from your computer through the VPN, including traffic destined for the Internet.
The second will route traffic from your computer throught the VPN and on to the VPN network, but leaves your default route in place so that traffic destined for the Internet still uses your local network interface.

### Route ALL traffic from your computer through the VPN
N.B. you will lose connectivity to local devices on your network such as printers.  (I am short on time - if anyone using this can submit a PR with commands to restore routing for local devices, please do so.)
- `cat /proc/sys/net/ipv4/ip_forward` to check if IP Forwarding is enabled.  If '1' is returned then skip the next step
- `echo 1 > /proc/sys/net/ipv4/ip_forward`
- `dhclient vpn_vpn_se` to obtain an IP address from the VPN DHCP server
- `ip a` to show the `vpn_se` interface and the assigned IPv4 address
- `netstat -rn` to show the route table prior to modification.

The following assumes that your local network is 192.168.0.0/24 and your default gateway is 192.168.0.1, and that the IP address of the remote VPN server is 15.48.223.55.
- `sudo ip route add 15.48.223.55/32 via 192.168.0.1`
- Delete the old default route. `ip route del default via 192.168.0.1`
Review the new route table with `netstat -rn`

Ping google's nameservers at 8.8.8.8 `ping 8.8.8.8 -c4`

Check your public IP address `wget -qO- http://ipecho.net/plain ; echo` <- note that in this line, O is "capital letter O".

### Route only VPN traffic through the VPN interface
- `cat /proc/sys/net/ipv4/ip_forward` to check if IP Forwarding is enabled.  If '1' is returned then skip the next step
- `echo 1 > /proc/sys/net/ipv4/ip_forward`
- `dhclient vpn_vpn_se` to obtain an IP address from the VPN DHCP server
- `ip a` to show the `vpn_se` interface and the assigned IPv4 address
- `netstat -rn` to show the route table prior to modification
The following assumes that your local network is 192.168.0.0/24 and your default gateway is 192.168.0.1, and that the IP address of the remote VPN server is 15.48.223.55.

- Delete the default route added by the `dhclient` command you issued earlier. `sudo ip route del default via 192.168.0.1`


Review the new route table with `netstat -rn`

Ping google's nameservers at 8.8.8.8 `ping 8.8.8.8 -c4`

Ping the remote gateway at 192.168.0.1 `ping 192.168.0.1 -c4`

Check your public IP address `wget -qO- http://ipecho.net/plain ; echo` <- note that in this line, O is "capital letter O".
The IP address returned should be your local public IP address.


## Disconnect from VPN and restore route table
- `vpnclient stop`
- `ip route del 15.48.223.55/32`
- `ip route add default via 192.168.0.1`
