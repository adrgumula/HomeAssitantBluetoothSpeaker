## The goal of this tutorial is to pair arbitrary bluetooth speaker with the Home Assistant (HASSO) to be able to hear notifications & Text-To-Speach (TTS)

### What was used during the installation:
Proxmox server
- BT dongle (Broadcom - BCM20702A0); A list of supported BT dongles -> https://www.home-assistant.io/integrations/bluetooth#known-working-high-performance-adapters
  You may also be able to use your integrated Bluetooth module if you are using
  something like an Intel NUC. The NUC6i5SYK is confirmed to be working.
- Home Assistant (Supervisioned version) (for this tutorial Home Assistant 2023.6.3 -> Supervisor 2023.06.4 -> Operating System 10.3 were used)
- Any Bluetooth speaker without auto-inactivity or/and auto-shutdown function. I've used **Xiaomi Mi Compact Bluetooth Speaker 2 (XMYX02YM)**
- ![Xiaomi Mi Compact Bluetooth Speaker 2](https://mi-home.pl/cdn/shop/products/2591_micompactbluetoothspeaker2-640px-hero_5b1911e4-9fdb-489b-b76e-d159d0e9ba1f.png)


### I. Connection:
1. Identify your BT speaker's **BT MAC Address** & **BT Name** 
2. Make sure that Proxmox’s HomeAssistant VM has an Audio device added (for example: ```device=intel-hda, driver=none```)
3. Pass the USB device to the VM:
   - Go to the Home Assistant VM, Hardware, Add USB Device, By Device ID.
   - Select the USB-Bluetooth dongle from the list.
3. Install **SSH** add-on into the HA and configure it
4. Logging into the HA using terminal: login@IP_Address
5. Switch off your BT speaker
6. Type: ```bluetoothctl```
7. Type: ```list``` to list all of your connected BT dongles. You should get the MAC addresses of all your BT dongles
8. Type: ```select MAC_ADRESS_DONGLE``` to select your main one dongle (for example: select 55:44:33:22:11:00)
10. Type: ```default-agent``` to make selected BT-dongle a default connector with your BT devices
11. Let's start connection & paring with your BT-speaker.
12. Type: ```scan on```
13. Turn on your BT speaker & set it to paring mode
14. Check the console to see if your BT speaker has been detected (look for its name or MAC address). NOTE: Be patient during this process. If the speaker does not appear, try putting it back into pairing mode and checking again.
15. Type: ```pair MAC_ADRESS``` (for example: pair 00:11:22:33:44:55)
16. Type: ```trust MAC_ADRESS``` (for example: trust 00:11:22:33:44:55)
17. Type: ```connect MAC_ADRESS``` (for example: connect 00:11:22:33:44:55)
    (in the case of any problems type ```help``` for more info)
18. Be sure to check the confirmations or errors from these commands.
    Simply repeating these steps if they show an error might help.
19. Optional: Check the configuration of the device in Pulseaudio:

    Note that at least with HAOS, this command only works inside the audio container,
    so you've to run `docker exec -it hassio_audio bash` to get a shell in it.
    - To check the connected devices use: ```pactl list | grep ".a2dp_sink"```.
    - Look for something similar to Name: ```bluez_sink.4C_72_74_XX_XX_XX.a2dp_sink```

    NOTE: the number represents the MAC address of your BT speaker
20. ![image](https://github.com/adrgumula/HomeAssitantBluetoothSpeaker/assets/70687019/339d12bc-6e5b-49ad-9d9a-18788a30cfa2)
21. Set the newly connected BT device as the default sound output by using following command: ```pactl set-default-sink NAME_OF_YOUR_BT_SPEAKER_FIND_IN_THE_PREV_STEP``` 
    This is not needed if you only have one audio device in your VM, so you can skip this and the next step, (it should not be muted)
22. Check whether the output-audio is not muted, nor volume set to zero, by ```Mute:``` (should be ```no```) and  ```Volume:```, should be ```front-left: 65536 / 100% / 0.00 dB,   front-right: 65536 / 100% / 0.00 dB``` by using following commend: ```pactl list sinks | grep "Mute:"``` and ```pactl list sinks | grep "Volume:"```
23. Type ```ha audio reload``` and wait for ```Command completed successfully``` message on the terminal
24. At this point your BT should be connected to your HA
25. Type: ```exit```
    
### II. Installing required add-ons & integrations
1. Goto HA and install **Settings -> Addons -> Install VLC**, start it and enable start on boot.
2. Goto **VLC -> Configuration** and select your BT Audio device from the list.
3. Goto **Settings -> Devices & Services (Integrations)** and **Add New Integration**
4. Search for **VLC** select it and enable **Local VLC Media player via Telnet**
   It will add with 1 service called "core-vlc".
   You can test it by playing some audio from your "My Media" or a TTS.
5. Go to the Media dashboard, "**My media** -> **Manage** -> **Add media**"
   Select an audio file which VLC can play from your Computer to upload it to Media.
6. Below the file area, on the bottom of the browser window is a media player
   bar with a blue selection button that reads "Web browser". Open it and select
   **VLC-TELNET**. Click the media file. It should start playing.

### III. Testing (Audio files)
1. Go to **Developers Tools** and **Services** and enter followings :
2. **Service** or **Actions** :  ```Media player: Play media```
3. **Target**: Search for ```VLC``` and select your one
4. **Content type**: ```music```
5. **Content ID**: ```/local/your.mp3``` (files your.mp3 should be located at the ```/local/www/``` folder of your HA installation)
   Note: With HAOS, you don't have /local. But you can play files in the media library.
   On the SSH shell, you can also create a `.hidden` directory and move special-purpose
   sound files that should not show up in the web browser in "My media":
   ```sh
   cd /mnt/data/supervisor/media
   mkdir .hidden
   mv 5-seconds-of-silence.mp3 .hidden/
   ```
   In HAOS, you can play back this example path using this Content ID:
   ```m
   media-source://media_source/local/.hidden/5-seconds-of-silence.mp3
   ```

6. Press **Call-Service** or **Perform the action**   
   <img width="400" alt="image" src="https://github.com/adrgumula/HomeAssitantBluetoothSpeaker/assets/70687019/583cc9ce-cf6b-41d5-b583-b23efe7d07e3">
7. To check the call_service Content-ID of other files, you can use:
   **Developers Tools** and **Events** -> **Listen to events**
   In **Listening to**, enter **call_service** and click **Start listening**
   When you play a not-hidden file from **My media**, you should see a yaml with this line:
   ```m
   media_content_id: media-source://media_source/local/5-seconds-of-silence.mp3
   ```
   On the SSH shell, run `journalctl -f` to get a live log of the HA warnings.
   In case the file is found, you get no warnings in the journal, but if there
   was an error, you'll see e.g. the errors opening the file if your Content ID is wrong.

### III. Testing (TTS - Text to speach)
1. Go to **Developers Tools** and go to **Actions** or **Services** and enter followings :
2. **Service** or **Actions**: type ```TTS``` and select one of your favourite (or default) one Text-To-Speach (for the porpouse of this tutoral I used gogole with google translate 
  <img width="1202" alt="image" src="https://github.com/user-attachments/assets/4b407020-149d-4ea7-9079-ceb20338a9cd" />

4. **Target** or **entity_id**: Search for ```VLC``` and select your one
5. **Message**: type anything you want to be converted to speach
6. Press **Call-Service** or **Perform the action**   
   <img width="1312" alt="image" src="https://github.com/user-attachments/assets/2b4bfc48-8db0-457f-b68b-344304ab2cdd" />
 
###  IV. Appendix 1 - Solution for BT inactivity – auto shutdown (Keep device busy)

At least with current Home Assistant, it no longer working to play blank-silent
"flat-line" audio at an interval to keep a BT speaker awake. The audio file must
have some audio signal or else some part of the audio pipeline detects that and
the BT speaker still disconnects.

Infrasound is a wavelength that is too low for us to hear, where 20 Hz is said
to be the limit, so anything at or below that frequency is sufficient to give
the speaker membrane of the BT speaker some signal to move, but it is so slow
that our ears (and others things at home) have no chance of picking it up.

In addition, you can set this infrasound a very low level to barely cause any
if at all movement of the speaker membrane.

With Audacity, you can generate very precisely what you want: Any infrasound
should be fine, but possibly, e.g. a too short signal could possibly cause
the BT speaker not register the audio. A very short file that should be very
hard to pick up even if you measure your speaker's amplifier would be 500ms#
of 2Hz Infrasound with an amplitude of only 0.04. With 2Hz, only a single
sine wave fits into the 0.5s time span so you could barely even speak of
a frequency, as there is really nothing at all to hear. Combine this with
a fade-out to reduce any possible click at the moment the audio stops.

To generate the tone in Audacity, using **Generate** -> **Tone...**.

1. **Upload** the audio file to **Media** -> **My media** library
   - [Download this file](Audacity-Tone-2Hz-0.04-500ms-Fadeout.opus)
     or generate a similar Infrasound audio file using Audacity to your computer.
   - On the Home Assistant website,
     click "**Media** -> **My media** -> **Manage** -> **Add media**"
     Select the audio file from your Computer. This uploads it to Media.
2. Move the file to a hidden directory. Using an SSH login, run these commands:
   ```sh
   cd /mnt/data/supervisor/media
   mkdir .hidden
   mv your-nearly-silent-audio.mp3 .hidden/
   ```
3. Create a new Automation like this:
   <img width="1312" alt="image" src="https://github.com/user-attachments/assets/e88604fb-fc1d-47cf-a5dc-d83a821f42aa" />
   For the file uploaded to media/.hidden use this a Content ID like this:
   ```m
   media-source://media_source/local/.hidden/<filename>
   ```
5. Give it a name by selecting top-right corner menu, then select ```Rename``` (for example: "Keep Xiaomi BT Speaker Awake")
6. Save it and done. Now HA will play every 5 minute a silent sound preventing BT speaker auto-shutdown

### III. Appendix 2 - Solution for BT inactivity – auto shutdown (Disable HAOS Bluetooth interactivity drop)

1. TODO
