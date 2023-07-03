## The goal of this tutorial is to pair arbitrary bluetooth speaker with the standard output for Home Assistant (HASSO) to be able to hear notification & TTM

### What was used during the installation:
- Proxmox
- Home Assitant (Supervisioned version) (I've being using currently Home Assistant 2023.6.3 -> Supervisor 2023.06.4 -> Operating System 10.3)
- any bluetooth spearker without auto-inactivity auto-shutdown funciton. I've used **Xiaomi Mi Compact Bluetooth Speaker 2**
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
14. Check the connected devices using comment: ```list sinks``` and look for something simiar to Name: ```bluez_sink.4C_72_74_38_AA_BB.a2dp_sink```
15. Check of the output is not muted, nor volume set to zero, by ```Mute:``` (should be ```no```) and  ```Volume:```, should be ```front-left: 65536 / 100% / 0.00 dB,   front-right: 65536 / 100% / 0.00 dB```
16. At this point your BT should be connected to your HA
17. NOTE: in case when VM with HA restarts repeat steps 6 and 12. 
18. Type: ```exit```
    
### II. Installing requied add-ons & integrations
1. Goto HA and install **Settings -> Add-in VLC Local**
2. Goto **VLC Local -> Configuration**
3. Set the **Telnet Password** and **Http Password** (you can use the default **mypasswrd** as well)
4. change the **Audio-Output** to the correspinding **BT Name** (it's the same obtainied at I.1 point)
5. <img width="400" alt="image" src="https://github.com/adrgumula/HomeAssitantBluetoothSpeaker/assets/70687019/88f7a27a-105c-4fa7-ba42-61e00973ccc5">
6. Go back to VLC-Local **Info tab** and enable **Auto-Start** option
7. Get back to **Info** tab hit **Start**
8. Go to HA Settings -> **Devices & Services (Integrations)** and **Add New Integration**
9. Search for **VLC LAN** select it and pick-up **Local VLC Media player via Telnet**
10. Enter the password **Telnet Password** and click **Submit**

### III. Testing
1. Go to **Developers Tools** and **Services** and enter followings :
2. **Service**: ```Media player: Play media```
3. **Target**: Search for ```VLC``` and select your one
3. **Content type**: ```music```
4. **Content ID**: ```/local/your.mp3``` (files your.mp3 should be located at the ```/local/www/``` folder of your HA installation)
6. Press **Call-Service**   
<img width="400" alt="image" src="https://github.com/adrgumula/HomeAssitantBluetoothSpeaker/assets/70687019/583cc9ce-cf6b-41d5-b583-b23efe7d07e3">
