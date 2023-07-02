# Home Assitant Bluetooth Speaker

The goal of this tutorial is to pair arbitrary bluetooth speaker and use with and the standard output for Home Assistant (HASSO)

Used installation:
- Proxmox
- Home Assitant (Supervisioned version)
- any bluetooth spearker without auto-shutdown funciton when inactive (not playing for a longer time). Iv'e used **Xiaomi Mi Compact Bluetooth Speaker 2**
- https://mi-home.pl/cdn/shop/products/2591_micompactbluetoothspeaker2-640px-hero_5b1911e4-9fdb-489b-b76e-d159d0e9ba1f.png?v=1679760040&width=1440![image](https://github.com/adrgumula/HomeAssitantBluetoothSpeaker/assets/70687019/de2e95d6-f66e-44f2-b67e-652a76e82886)


I. Connection:
    1. Identify your BT speaker's **BT MAC Address** & **BT Name**
    2. Make sure that Proxmox - HA VM as Audio device added (for example: _device=intel-hda, driver=none_)
    3. Install **SSH** addon into the HA and configure it
    4. Switch off yout BT speaker
    5. Loging into the HA using termina: login@IP_Adress
    6. Type: **bluetoothctl**
    7. Type: **scan on**
    8. Switch on your BT speaker & paring mode
    9. Check on the console if your BT speaker was detected (by name of MAC adress)
    10. Type: **pair** MAC_ADRESS (for example: pair 00:11:22:33:44:55)
    11. Type: **trust** MAC_ADRESS (for example: trust 00:11:22:33:44:55)
    12. Type: **connect** MAC_ADRESS (for example: connect 00:11:22:33:44:55)
    13. (in the case of any problems type **help** for more info)
    14. At this point your BT should be connected to your HA
    
II. Installing requied add-ons & integrations
    1. Goto HA and install Settings -> Add-in **VLC Local**
      a. Goto **VLC Local -> Configuration**
      b. Set the **Telnet Password** and **Http Password** (you can use the default **mypasswrd** as well)
      c. change the **Audio-Output** to the correspinding **BT Name**
    2. Go back ot VLC-Local **Info tab** and enable **Auto-Start**
    3. Go to HA Settings -> **Devices & Services (Integrations)** and **Add New Integration**
    4. Search for **VLC LAN** select it and pick-up **Local VLC Media player via Telnet**
    5. Enter the password **Telnet Password** and click **Submit**

III. Testing
  1. Go to **Developers Tools** and **Services** and enter followings :
     a. **Service**: Media player: Play media
     b. **Target**: Search for VLC and select your one
     c. **Content type**: music
     d. **Content ID**: _/local/your.mp3_ (files your.mp3 should be located at the local/www/ folder of your HA installation)
     e. Press **Call-Service**
     
