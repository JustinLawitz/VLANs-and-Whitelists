# Zero-Trust Network Segmentation & Hardware-Level Access Control

# Overview:

After deploying our new home-network with Unifi hardware, I decided the best next steps would be to separate the network into 2 VLANs to simulate an enterprise, with a guest, and private VLAN. VLANs or Virtual Local Area Network are used to create multiple, independent networks using only one piece of physical hardware. VLANs are used for many reasons, the first reason I wanted to implement them was due to security, for instance if a guest brought a device into the home infected with malware, it might be able to scan or infect other devices on the network. Separating our network into a guest and private VLANs would isolate this guest device and make it much harder for malware to laterally move through the network. The second main reason I wanted to implement this was for bandwidth management. Whenever we have family or friends spending a couple days at our home, a lot of times with an influx of devices and activities like streaming happening on the network, it can slow down all devices and make it hard to get work done on our own devices. Splitting our network into two VLANs will allow me to set QoS or Quality of Service rules which allow for setting higher priority for one VLAN than the other, capping one at a lower speed so it won’t slow down devices on the private network.

## Step 1.

The first thing I did was access the UniFi Network Application by entering the routers IP into a browser, I then navigated to the network section and created a new network. This new network would be the guest network. Part of adding this VLAN was changing the subnet mask and VLAN ID (Figure 1). A subnet mask is the first section of the IP that denotes which network the device is on, I used a 24-bit subnet mask which means the first three octets of the IP make up the subnet mask, and the last octet denotes the actual device. The subnet mask of the default network was 192.168.1.1, which I changed to 192.168.20.1. I also added a VLAN ID, which I chose on 20 to match the subnet mask to keep everything straightforward. The VLAN ID is a 4-byte tag that’s added to a standard ethernet frame, essentially, it’s a piece of data that tells the network equipment like the router exactly where it is allowed to go, without it the router wouldn’t know which packets should use the guest or private network.

<img width="533" height="1031" alt="image" src="https://github.com/user-attachments/assets/2e0c2b48-bf36-4945-99c7-327890cf66bc" />
Figure 1: Guest VLAN configuration

## Step 2.

I next had to reconfigure the current Wi-Fi SSID to use the new Guest VLAN instead of the default VLAN. I did this by managing the SSID and changing the name to “LaMurphy Guest” and changing the Network tab to the new Guest VLAN option that I had just created (Figure 2). Many enterprises leave this network open so guests can easily connect to this network, but in our case since this is our home network, I left a simple password on it so it could be easily used by guests with some security in place so people outside of the home couldn’t use it. 

<img width="625" height="713" alt="image" src="https://github.com/user-attachments/assets/16fe0735-6c51-4ee9-bb51-89ed224c28b9" />
Figure 2: Guest SSID configuration

## Step 3.

After editing the LaMurphy Guest SSID, I next made the VLAN for the Private VLAN using the same process as I did for the Guest VLAN. I changed this subnet mask to 192.168.22.1, and the VLAN ID to 22 (Figure 3).

<img width="543" height="880" alt="image" src="https://github.com/user-attachments/assets/b91f8ce3-f3ce-4e8e-b4ff-1cc2a91bfbb9" />
Figure 3: Private VLAN configuration

## Step 4.

I next created a new SSID for the Private network, created a new password, and set it to use the Private VLAN in the Network option (Figure 4).

<img width="577" height="669" alt="image" src="https://github.com/user-attachments/assets/e9e4f19a-184f-4764-a072-7fe6558fb773" />
Figure 4: Private SSID creation

## Step 5.

To add another layer of security to the Private network, I also decided on using a whitelist, or MAC address filtering. A whitelist essentially only allows certain devices to join the network, so even if somebody knew the password to the Wi-Fi, they might not necessarily be able to join if they weren’t whitelisted. This works using the MAC address, which is permanently assigned to the physical Wi-Fi or Ethernet chip inside a device the day it is manufactured. Although there are ways around a whitelist like MAC address spoofing, which is basically changing your MAC address to one that is whitelisted, it would still make breaching the network a bit more difficult. But due to MAC address spoofing, using a whitelist shouldn’t be considered as a primary security barrier as an attacker could even capture the MAC address of a whitelisted device using tools like a Wi-Fi adapter and Wireshark. 

<img width="620" height="602" alt="image" src="https://github.com/user-attachments/assets/4d19a6a1-8bef-463d-baf8-cc8f7800d821" />
Figure 5: Whitelist/ MAC address Filtering

For this reason, I also changed security protocol to WPA2/WPA3 (Figure 6). WPA2 has been the industry standard security protocol since 2004 and uses a four-way handshake to authenticate users. The issue with SPA2 is if somebody captured this handshake, they could take it and brute-force the password using a dictionary attack until they found it. WPA3 uses a much more complex exchange and would require an attacker to interact with the router for every guess instead of capturing the four-way handshake of WPA2 and cracking it offline. WPA3 would also notice a dictionary attack and block that connection attempt. The issue with WPA3 is that many older devices cannot use WPA3, so if if I used WPA3 only, many devices wouldn’t be able to connect to the Wi-Fi. For this reason I used WPA2/WPA3 which allows new devices to use WPA3 while older devices that aren’t compatible are still able to use WPA2.

<img width="627" height="452" alt="image" src="https://github.com/user-attachments/assets/81af274e-08ef-4589-954b-62c42ad5c321" />
Figure 6: WPA2/WPA3 Security Protocol

## Step 6.

Moving back to the whitelist, I next had to begin whitelisting devices so they could connect to the Private network. While the UniFi Network Application allows you to whitelist devices based on their name, I wanted to first due it manually using the MAC address to better understand the process. So, the first thing I did was run the command getmac /v /fo list in the Command Prompt of my desktop to get the MAC address (Figure 7). After obtaining the MAC address, I then entered it into the whitelist for the private network (Figure 8).

<img width="975" height="321" alt="image" src="https://github.com/user-attachments/assets/cc1ab8ac-3967-4058-bebc-2fac9088fafa" />
Figure 7: MAC address of desktop

<img width="750" height="591" alt="image" src="https://github.com/user-attachments/assets/45521656-873d-4132-8b61-e48fbe9e53f6" />
Figure 8: Adding MAC address to whitelist

## Step 7.

After using the command ipconfig in the command prompt, I noticed that the IP hadn’t changed to match the Private Wi-Fi. I noticed this because if it was on the correct network, the IP of my desktop should have been 192.168.22.172 to match the subnet mask, not 192.168.1.172 (Figure 9).

<img width="975" height="400" alt="image" src="https://github.com/user-attachments/assets/8203afb2-0778-43d0-bf1b-a66a0a47c85a" />
Figure 9: Incorrect IP

This is because the desktop doesn’t know what a VLAN ID is and just sends raw untagged data out of the ethernet port, so I had to set the ethernet port it was using to the new Private VLAN instead of the default so it would tag the data with the correct VLAN ID as it was sent out. In the UniFi dashboard I was able to confirm it was still using the Default network and see that it was connected to port 2 of the Dream Machine router (Figure 10).

<img width="975" height="289" alt="image" src="https://github.com/user-attachments/assets/962e2889-24cc-41bd-9972-fcd5e9518f84" />
Figure 10: UniFi dashboard

To fix this, I selected the router, and then the Port Manager option (Figure 11).

<img width="611" height="614" alt="image" src="https://github.com/user-attachments/assets/55b3e065-0a7e-4518-a43b-38ec71fbffcd" />
Figure 11: Port Manager

In the Port Manager for port 2, I was able to set the Native VLAN to the new Private VLAN instead of the old Default one (Figure 12).

<img width="598" height="644" alt="image" src="https://github.com/user-attachments/assets/e7de97ae-db25-4dfd-9c1a-f781803b6ea8" />
Figure 12: Native VLAN configuration

## Step 8.

After changing the Native VLAN for port 2 on the router, I  re-entered ipconfig into the Command Prompt and confirmed the IP had changed to 192.168.22.172 to match the subnet mask of the Private VLAN (Figure 13). I also confirmed this in the UniFi dashboard by checking if any IPs were being leased using the Private VLAN, where I saw one which was for my desktop (Figure 14).

<img width="798" height="439" alt="image" src="https://github.com/user-attachments/assets/319ed02e-2dbd-47c7-b705-01ed687d7502" />
Figure 13: ipconfig with correct IP

<img width="975" height="160" alt="image" src="https://github.com/user-attachments/assets/19170ff1-49ff-46ea-a2e6-0c16f82f66ce" />
Figure 14: 1 IP leased using Private VLAN

## Step 9.

I next wanted to test how the whitelist worked in a physical sense, as I knew how they worked conceptually but have never been blocked by one. I did this before adding the rest of our home devices to the Private networks whitelist by trying to use the Private Wi-Fi with my phone, where I couldn’t connect even with the password (Figure 15).

<img width="527" height="631" alt="image" src="https://github.com/user-attachments/assets/f2df8ce9-16d1-4e89-818a-dc11e0d33013" />
Figure 15: not whitelisted device

After being unable to connect without being whitelisted, I went back to the UniFi Network Application and added all the required devices to the whitelist including my phone (Figure 16).

<img width="597" height="433" alt="image" src="https://github.com/user-attachments/assets/9a201567-d6a5-48fd-a127-e898944b3e2a" />
Figure 16: Complete whitelist

When I went back to test if I could connect on my phone, I was still unable to. I learned that this is due to a security feature Apple and Google use that basically rotates your MAC address when connecting to Wi-Fi. They do this because they realized that if an attacker captures your MAC address, they would be able to track your movement across different locations due to the “Probe Request” which is when your phone isn’t connected to Wi-Fi sends a message broadcasting its MAC address looking for any known networks. If somebody where to capture these requests, they might be able to triangulate your location based on which sensors responded to your request. So, for my phone to be able to access the private Wi-Fi, I just had to set the Private Wi-Fi Address option to Off instead of Rotating so it would use its actual MAC address to connect to the Wi-Fi instead of a random one that hadn’t been whitelisted (Figure 17). After changing this field, I was able to successfully connect to the Private network on my phone (Figure 18).

<img width="761" height="431" alt="image" src="https://github.com/user-attachments/assets/3278c9ea-9968-4d70-ac2b-244edcc9f157" />
Figure 17: Private Wi-Fi Address

<img width="698" height="550" alt="image" src="https://github.com/user-attachments/assets/943fe085-5b84-4ba4-b594-565d8ff22323" />
Figure 18: Successful connection to Private Wi-Fi
