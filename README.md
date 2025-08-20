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

We have to configure the virtual IP addresses. To do so we go to interfaces/Virtual-IPS/settings and create these IPs. I did this by setting the LAN to 192.168.1.4/24 and WAN to 10.0.2.20/24. This has to be done on both the orginial and backup firewall.

<img width="965" height="621" alt="image" src="https://github.com/user-attachments/assets/fbbdf23d-ac4c-4054-9d82-c0f7d9c7f9e7" />

Next we are going to configure highavaliability. To do this we will go to system/High-Avaliability/Settings and synchronize via pfsync. Then we will enter in the IP address of the other pfsync which in this case is 10.0.0.2. This is also done on the backup firewall, but using the IP address of the original firewall's pfsync(10.0.0.1). 

<img width="967" height="613" alt="image" src="https://github.com/user-attachments/assets/e3cbd422-f9a8-4612-8c45-14c23202e108" />

We need to tell both firewalls that it has to use the virtual IP address of WAN and has to have NAT translation to that particular IP address. Therefore, we have to create a rule in our outbound NAT firewall. It is done by navigating to Firewall/NAT/Outbound and we select hybrid
outbound NAT rule generation and create the rule as given by the image below. This must be done both on the orginal and backup firewalls.

<img width="965" height="617" alt="image" src="https://github.com/user-attachments/assets/f2d00ec8-a1ef-4d4d-977c-ddc86bee065e" />

The we can check that the firewalls have been configured correctly by adding widgets to display the master and backup firewalls. The image below shows this for both the original and backup firewalls.

ORIGINAL
<img width="957" height="611" alt="image" src="https://github.com/user-attachments/assets/7999b8ab-1b62-435b-94da-4b74051aef45" />

BACKUP
<img width="967" height="612" alt="image" src="https://github.com/user-attachments/assets/e401b788-b2df-4824-86f7-96a75e71cb09" />

Now we will check that everything is working correctly and this is done by pinging the firewall's.

<img width="660" height="517" alt="image" src="https://github.com/user-attachments/assets/9361b09c-8ab5-4a0a-a1f7-782bd65eece4" />

<img width="657" height="515" alt="image" src="https://github.com/user-attachments/assets/381879d5-9185-4421-b857-cb50c413ad16" />

<img width="661" height="523" alt="image" src="https://github.com/user-attachments/assets/0d11c1d3-3a11-4b83-b3ad-af145a04f5d5" />

Next we are going to simulate the master firewall failing, to do this we will go to the master firewall Virtual Machine and unplug the virtual cable connection. We will be pinging all of the firewalls and see when the firewall cuts out.

This is me cutting the connection.
<img width="973" height="601" alt="image" src="https://github.com/user-attachments/assets/ccd023c1-b05b-46a1-82bd-1513e1e9e1a7" />


This is the result of the connection being cut. We can now see that the master firewall cannot be reached anymore. Furthermore, the website is now unreachable.
<img width="885" height="520" alt="image" src="https://github.com/user-attachments/assets/208d5a84-a98b-4eef-a05b-37ff3cdfe010" />
<img width="1072" height="665" alt="image" src="https://github.com/user-attachments/assets/86657a72-0829-4c06-963b-c7a606d0ea1f" />

We can now see that the backup firewall has now become the master firewall.
<img width="1089" height="667" alt="image" src="https://github.com/user-attachments/assets/5ae47754-8529-4435-b5ec-b82f0fe04af6" />

Furthermore, we can see that the firewall did cut out momentarily because of the packet loss when the LAN was pingged. Then the second firewall did take its place correctly and became the master firewall.
<img width="889" height="545" alt="image" src="https://github.com/user-attachments/assets/20ee12fa-b2c1-44c7-9437-2b9774fb4633" />

After we have reconnected the cables in the virtual machine the master firewall is now running again and has become the master firewall once again.

It can now be pinged and the website does properly work now
<img width="666" height="538" alt="image" src="https://github.com/user-attachments/assets/078086da-70e5-44fd-8f18-caef80938698" />
<img width="982" height="667" alt="image" src="https://github.com/user-attachments/assets/6687f2e4-4691-43a8-a837-a955ccccf777" />

The backup firewall can also displays that it is back in backup mode.
<img width="967" height="657" alt="image" src="https://github.com/user-attachments/assets/17566bff-08a8-4264-943b-2239d05d2341" />

It just works! This concludes the next part of the opnsense firewall configuration.
