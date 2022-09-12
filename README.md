# MySI

For this project, it aims to create on-premise network infrastructure to configure the machines between them.

## Step 1: configure virtualbox

if you are on VirtualBox (osx) create a file named networks.conf at **/etc/vbox/networks.conf** on your computer write :

`<* 10.242.0.0/16>`

in the file to allow your virtuelBox to use this IP adress range.

Once you're the application, go to: `file` then click on `Host Network Manager`
then click on `create` then type the following congifgurations:

```
'Configure manually'

'Disable DHCP'

'IP v4 Adress: 10.242.0.0'

'IP v4 Network Mask: 255.255.0.0'
```

Create all VM on debian 11 Bullseye
go on each VM
change networks settings at: `Settings` -> `Network` -> `adapter 1`
in the menu of `<Attached to>` choose `<Host-only Adaptater>` and below choose your networks name if it's not already set
For gate way add a second adapter and set `<Bridged Adapter>` in `<Attached to>` menu's

See `contributing.md` for ways to get started.

Please adhere to this project's `code of conduct`.

## Step 2: server congifgurations

//pharse d'arrpche

**2.1 : create your servers**

for each serveur :

set hostname:

```
sudo hostnamectl set-hostname <Serveur_Host_Name>
```

edit hostname resolution:

```
sudo nano /etc/hosts
```

`127.0.1.1 <Serveur_Host_Name>`

set fix ip:

```
sudo nano /etc/network/interfaces
```

**<interface>** = interface connect to host only

**<interface2>** = interface connect to briged

on GateWay add:

```
# The primary network interface
auto <interface2>
iface <interface2> inet dhcp

# The second network interface
auto <interface>
iface <interface> inet static
 address 10.242.0.1/24
```

On the other :

```
auto <interface>
iface <interface> inet dhcp
```

to

```
iface <interface> inet static
address <ip_of_serv>
gateway 10.242.0.1
```

on gateway active ip forwarding.

edit **sudo nano /etc/sysctl.conf**
Add the following lines to the bottom of the file
`net.ipv4.ip_forward = 1`

`sysctl -p` to active the change without restart

if you want to check ipforwarding this commande below and must be return 1 :

```
cat /proc/sys/net/ipv4/ip_forward
```

on Gateway :

```
sudo iptables -t nat -A POSTROUTING ! -d 10.242.0.0/24 -o enp0s8 -j MASQUERADE
```
