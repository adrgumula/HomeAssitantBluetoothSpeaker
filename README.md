# Home Assistant Bluetooth Speaker

## The goal of this tutorial is to pair arbitrary bluetooth speaker and use with and the standard output for Home Assistant (HASSO)

### What was used during the installation:
- Proxmox
- Home Assitant (Supervisioned version) (I've being using currently Home Assistant 2023.6.3 -> Supervisor 2023.06.4 -> Operating System 10.3)
- any bluetooth spearker without auto-inactivity auto-shutdown funciton. Iv'e used **Xiaomi Mi Compact Bluetooth Speaker 2**
- ![Xiaomi Mi Compact Bluetooth Speaker 2](https://mi-home.pl/cdn/shop/products/2591_micompactbluetoothspeaker2-640px-hero_5b1911e4-9fdb-489b-b76e-d159d0e9ba1f.png)


### I. Connection:
1. Identify your BT speaker's **BT MAC Address** & **BT Name**
2. Make sure that Proxmox - HA VM as Audio device added (for example: ```device=intel-hda, driver=none```)
3. Install **SSH** addon into the HA and configure it
4. Switch off yout BT speaker
5. Loging into the HA using termina: login@IP_Adress
6. Type: ```bluetoothctl```
7. Type: ```scan on```
8. Switch on your BT speaker & enter it into paring mode
9. Check on the console if your BT speaker was detected (by name of MAC adress)
10. Type: ```pair MAC_ADRESS``` (for example: pair 00:11:22:33:44:55)
11. Type: ```trust MAC_ADRESS``` (for example: trust 00:11:22:33:44:55)
12. Type: ```connect MAC_ADRESS``` (for example: connect 00:11:22:33:44:55)
13. (in the case of any problems type ```help``` for more info)
14. At this point your BT should be connected to your HA
15. NOTE: in case when VM with HA restarts repeat steps 6 and 12. 
16. Type: ```exit```
    
### II. Installing requied add-ons & integrations
1. Goto HA and install **Settings -> Add-in VLC Local**
2. Goto **VLC Local -> Configuration**
3. Set the **Telnet Password** and **Http Password** (you can use the default **mypasswrd** as well)
4. change the **Audio-Output** to the correspinding **BT Name** (it's the same obtainied at I.1 point)
5. Go back to VLC-Local **Info tab** and enable **Auto-Start** option
6. Go to HA Settings -> **Devices & Services (Integrations)** and **Add New Integration**
7. Search for **VLC LAN** select it and pick-up **Local VLC Media player via Telnet**
8. Enter the password **Telnet Password** and click **Submit**

### III. Testing
1. Go to **Developers Tools** and **Services** and enter followings :
2. **Service**: ```Media player: Play media```
3. **Target**: Search for ```VLC``` and select your one
3. **Content type**: ```music```
4. **Content ID**: ```/local/your.mp3``` (files your.mp3 should be located at the ```/local/www/``` folder of your HA installation)
6. Press **Call-Service**   
