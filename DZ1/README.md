# **"Lab - Configure Router-on-a-Stick Inter-VLAN Routing"**
## **Топология** 
![](https://github.com/BorisPo/BorisPo/blob/main/DZ1/topology.png?raw=true)
## **Цели:**
+ ### Part 1: Build the Network and Configure Basic Device Settings
+ ### Part 2: Create VLANs and Assign Switch Ports
+ ### Part 3: Configure an 802.1Q Trunk between the Switches
+ ### Part 4: Configure Inter-VLAN Routing on the Router
+ ### Part 5: Verify Inter-VLAN Routing is working
+ ## **Решение**
+ a.	Console into the router and enable privileged EXEC mode. // enable
+ b.	Enter configuration mode.\\ conf t
+ c.	Assign a device name to the router.\\ hostname S1
+ d.	Disable DNS lookup to prevent the router from attempting to translate incorrectly entered commands as though they were host names. \\ no ip domain-lookup
+ e.	Assign class as the privileged EXEC encrypted password. \\ enable password class
+ f.	Assign cisco as the console password and enable login.\\  username cisco
+ g.	Assign cisco as the VTY password and enable login
![](https://github.com/BorisPo/BorisPo/blob/main/DZ1/qq.png?raw=true)
+ h.	Encrypt the plaintext passwords. \\ enable secret cisco ; service password-encryption
+ i.	Create a banner that warns anyone accessing the device that unauthorized access is prohibited.\\ banner login ^C \\
+ j.	Save the running configuration to the startup configuration file. \\ write
+ k.	Set the clock on the router.
+ ## **Step 3: Configure basic settings for each switch. \\ настраивается аналогично коммутатору S1**
+ Refer to the Addressing Table for PC host address information. \\ ip 192.168.3.3 255.255.255.0 192.168.3.1
+ ## **Part 2: Create VLANs and Assign Switch Ports**
+ Step 1: Create VLANs on both switches.
+ a.	Create and name the required VLANs on each switch from the table above
+ conf t
+ vlan 3
+ name mngt
+ остальные создаются аналогично на всех коммутаторах 
+ b.	Configure the management interface and default gateway on each switch using the IP address information in the Addressing Table. 
+ conf t
+ interface Vlan3
+ ip address 192.168.3.11 255.255.255.0
+ ip default-gateway 192.168.3.1
+ на остальных коммутаторах настраивается аналогично
+ c.	Assign all unused ports on both switches to the ParkingLot VLAN, configure them for static access mode, and administratively deactivate them.
+ conf t
+ interface GigabitEthernet0/0 
+ switchport trunk allowed vlan 3,4,8 \\ настрока vlan  транком на интерфейс 
+  switchport trunk native vlan 8 \\ настройка натив влан 
+  shutdown
+   Step 2: Assign VLANs to the correct switch interfaces.
+   a.	Assign used ports to the appropriate VLAN (specified in the VLAN table above) and configure them for static access mode. Be sure to do this on both switches

conf t

interface GigabitEthernet0/1

 switchport access vlan 3 \\ настройка vlan access
 
 switchport trunk allowed vlan 3,4,8 \\ настрока vlan  транком на интерфейс
 
 switchport trunk native vlan 8 \\ настройка натив влан
 
 switchport mode access \\  настройка порта доступа 
 
 media-type rj45
 
 negotiation auto
 
остальные настраиваются аналогично 

b.	Issue the show vlan brief command and verify that the VLANs are assigned to the correct interfaces.

S1#sh vlan brief

VLAN Name                             Status    Ports

---- -------------------------------- --------- -------------------------------

1    default                          active    Gi0/0

3    mngt                             active    Gi0/1

4    Operations                       active

7    ParkingLot                       active    Gi1/0, Gi1/1, Gi1/2, Gi1/3

8    VLAN0008                         active

1002 fddi-default                     act/unsup

1003 token-ring-default               act/unsup

1004 fddinet-default                  act/unsup

1005 trnet-default                    act/unsup

S2#sh vl br

VLAN Name                             Status    Ports

---- -------------------------------- --------- -------------------------------

1    default                          active

3    mngt                             active

4    Operations                       active    Gi0/1

7    ParkingLot                       active    Gi0/0, Gi0/3, Gi1/0, Gi1/1Gi1/2, Gi1/3

8    VLAN0008                         active

1002 fddi-default                     act/unsup

1003 token-ring-default               act/unsup

1004 fddinet-default                  act/unsup

1005 trnet-default                    act/unsup

Close configuration window

Part 3: Configure an 802.1Q Trunk Between the Switches

In Part 3, you will manually configure interface F0/1 as a trunk.

Step 1: Manually configure trunk interface F0/1.

Open configuration window

a.	Change the switchport mode on interface F0/1 to force trunking. Make sure to do this on both switches.

b.	As a part of the trunk configuration, set the native VLAN to 8 on both switches. You may see error messages temporarily while the two interfaces are configured for different native VLANs.

c.	As another part of trunk configuration, specify that VLANs 3, 4, and 8 are only allowed to cross the trunk.

conf t

interface GigabitEthernet0/3

switchport trunk allowed vlan 3,4,8 \\ настройка натив влан 

switchport trunk encapsulation dot1q \\ инкапсуляция в 801.q

switchport trunk native vlan 8 \\ настройка натив влан 

switchport mode trunk

media-type rj45

negotiation auto \\ автосогласование скорости на порту


d.	Issue the show interfaces trunk command to verify trunking ports, the Native VLAN and allowed VLANs across the trunk.

S2#sh int trunk


Port        Mode             Encapsulation  Status        Native vlan

Gi0/2       on               802.1q         trunking      8

Port        Vlans allowed on trunk

Gi0/2       3-4,8


Port        Vlans allowed and active in management domain

Gi0/2       3-4,8


Port        Vlans in spanning tree forwarding state and not pruned

Gi0/2       3-4,8

S1#sh int tr

Port        Mode             Encapsulation  Status        Native vlan

Gi0/2       on               802.1q         trunking      8

Gi0/3       on               802.1q         trunking      8

Port        Vlans allowed on trunk

Gi0/2       3-4,8

Gi0/3       3-4,8

Port        Vlans allowed and active in management domain

Gi0/2       3-4,8

Gi0/3       3-4,8

Port        Vlans in spanning tree forwarding state and not pruned

Gi0/2       3-4,8

Gi0/3       3-4,8

Step 2: Manually configure S1’s trunk interface F0/5

a.	Configure the F0/5 on S1 with the same trunk parameters as F0/1. This is the trunk to the router.

b.	Save the running configuration to the startup configuration file on S1 and S2.

c.	Issue the show interfaces trunk command to verify trunking.

Question:

Why does F0/5 not appear in the list of trunks?

e.У меня все отображается 

Close configuration window

Part 4: Configure Inter-VLAN Routing on the Router

Open configuration window

a.	Activate interface G0/0/1 on the router.
 
b.	Configure sub-interfaces for each VLAN as specified in the IP addressing table. All sub-interfaces use 802.1Q encapsulation. Ensure the sub-interface for the native VLAN does not have an IP address assigned. Include a description for each sub-interface.

conf t

interface GigabitEthernet0/1.3 \\ создание sub нинтерфейса 

description mngt \\ название данного интерфейса

encapsulation dot1Q 3 \\ назначение на него нужного vlan

ip address 192.168.3.1 255.255.255.0 \\ назначение необходимой под него сети 




interface GigabitEthernet0/1.4

description Operations

encapsulation dot1Q 4

ip address 192.168.4.1 255.255.255.0

!

interface GigabitEthernet0/1.8

description Native \\ название данного интерфейса

encapsulation dot1Q 8 native \\ назначяение под него vlan  и определение его нативным ( дефолтным)

!


c.	Use the show ip interface brief command to verify the sub-interfaces are operational.

Router>sh ip interface br

Interface                  IP-Address      OK? Method Status                Protocol

GigabitEthernet0/0         unassigned      YES NVRAM  administratively down down

GigabitEthernet0/1         unassigned      YES NVRAM  up                    up  

GigabitEthernet0/1.3       192.168.3.1     YES NVRAM  up                    up  

GigabitEthernet0/1.4       192.168.4.1     YES NVRAM  up                    up  

GigabitEthernet0/1.8       unassigned      YES unset  up                    up  

GigabitEthernet0/2         unassigned      YES NVRAM  administratively down down

GigabitEthernet0/3         unassigned      YES NVRAM  administratively down down

Close configuration window

Part 5: Verify Inter-VLAN Routing is Working

Step 1: Complete the following tests from PC-A. All should be successful.

Note: You may have to disable the PC firewall for pings to be successful.

a.	Ping from PC-A to its default gateway.

Checking for duplicate address...

VPCS : 192.168.3.3 255.255.255.0 gateway 192.168.3.1

VPCS> ping 192.168.3.1

84 bytes from 192.168.3.1 icmp_seq=1 ttl=255 time=7.863 ms

84 bytes from 192.168.3.1 icmp_seq=2 ttl=255 time=4.138 ms

84 bytes from 192.168.3.1 icmp_seq=3 ttl=255 time=18.924 ms

84 bytes from 192.168.3.1 icmp_seq=4 ttl=255 time=4.691 ms

84 bytes from 192.168.3.1 icmp_seq=5 ttl=255 time=4.438

b.	Ping from PC-A to PC-B

VPCS> ping 192.168.4.3


84 bytes from 192.168.4.3 icmp_seq=1 ttl=63 time=13.974 ms

84 bytes from 192.168.4.3 icmp_seq=2 ttl=63 time=6.686 ms

84 bytes from 192.168.4.3 icmp_seq=3 ttl=63 time=5.876 ms

84 bytes from 192.168.4.3 icmp_seq=4 ttl=63 time=7.187 ms

84 bytes from 192.168.4.3 icmp_seq=5 ttl=63 time=7.430 ms

c.	Ping from PC-A to S2

PCS> ping 192.168.3.12


84 bytes from 192.168.3.12 icmp_seq=1 ttl=255 time=24.031 ms

84 bytes from 192.168.3.12 icmp_seq=2 ttl=255 time=17.018 ms

84 bytes from 192.168.3.12 icmp_seq=3 ttl=255 time=6.141 ms

84 bytes from 192.168.3.12 icmp_seq=4 ttl=255 time=9.540 ms

84 bytes from 192.168.3.12 icmp_seq=5 ttl=255 time=4.026 ms

Step 2: Complete the following test from PC-B.

From the command prompt on PC-B, issue the tracert command to the address of PC-A.

Question:

What intermediate IP addresses are shown in the results?

PCS> trace 192.168.3.3

trace to 192.168.3.3, 8 hops max, press Ctrl+C to stop

1   192.168.4.1   4.928 ms  4.112 ms  5.472 ms

2   *192.168.3.3   10.451 ms (ICMP type:3, code:3, Destination port unreachable )



