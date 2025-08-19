# SIEM-Lab---Opnsense-Firewall-Setup-Part-4
In this project I will be updating my firewall to have High Availability(HA), Common Address Redundancy Protocol (CARP), and pf sync features to create a redundant firewall cluster setup.

First we have to clone our prexisiting firewall to make a backup. This is done so we can have two firewalls just in case one firewall goes down the other will be able to take its place. The user will not know that this happens. 

<img width="425" height="437" alt="image" src="https://github.com/user-attachments/assets/5c1632c8-4d59-4cc8-b268-8ae51eab09bd" />

Next we will make a NAT network. This is done because a NAT network allows all devices that are connected to that network to be accessed. Furthemore, it has its own DHCP network and gateway, which allows for a internet connection. The reason a NAT connection was not used is because it wouldn't allow for all connections to be accessed.

<img width="676" height="240" alt="image" src="https://github.com/user-attachments/assets/ebe1e9c2-370a-4db2-a1a2-b34bd821e24f" />

To be able to communicate between both of the firewalls we have to add an adittional adapter. This will be called pfsync and this will be used to communicate between both of the firewalls. The reason this is created is because we want to know if one firewall goes down, so the other can go into effect.

<img width="771" height="487" alt="image" src="https://github.com/user-attachments/assets/e96ccf26-1a43-4fb4-8d7f-e7e5226eb5f8" />

To make sure that the firewalls work correctly we must change the LAN IP address to a different one that is configured to the original. 

<img width="727" height="496" alt="image" src="https://github.com/user-attachments/assets/378ea35f-701c-49f6-8181-86a63659f36e" />

Then we open our IP address on the kali linux machine and open the opnsense website. Then we go to interfaces and assignmnets to assign the pfsync. This must be done on both the original LAN IP address and the new one for the backup firewall.

<img width="957" height="610" alt="image" src="https://github.com/user-attachments/assets/1c6614ed-864a-40d6-b524-a2318eb947ee" />

Then we assign the IP address for both of the pfsyncs the orginial firewall pfysnc is assigned to the IP of 10.0.0.1 and the backup firewall pfsync will be assigned 10.0.0.2 

<img width="962" height="617" alt="image" src="https://github.com/user-attachments/assets/b1fc2b4f-97d4-4b90-9efb-ee5cac56c434" />

Then we have to connect both of the pfsync so data can be excahnged between them. To do this we go to firewall/rules/pfsync. Then we create a rule and apply it.

<img width="953" height="612" alt="image" src="https://github.com/user-attachments/assets/fcd78867-d5ea-438a-b771-b72af13510ae" />

We have to configure the virtual IP addresses. To do so we go to interfaces/Virtual-IPS/settings and create these IPs. I did this by setting the LAN to 192.168.1.4/24 and WAN to 10.0.2.20/24. This has to be done on both the orginial and backup firewalls.

<img width="965" height="621" alt="image" src="https://github.com/user-attachments/assets/fbbdf23d-ac4c-4054-9d82-c0f7d9c7f9e7" />

