To quickly set up the WiFi before booting your device, you can follow this procedure in the **root/** directory of your installation (from your computer):

Copy the example configuration for a WPA connection for netctl:

    cp etc/netctl/examples/wireless-wpa etc/netctl/wireless-wpa

Edit the SSID and key for your access point:

    sed -i "s/ESSID=.*/ESSID='MySSID'/" etc/netctl/wireless-wpa
    sed -i "s/Key=.*/Key='MyKey'/" etc/netctl/wireless-wpa

Enable the netctl-auto service (the first argument starting with `/` is on purpose):

    ln -s /usr/lib/systemd/system/netctl-auto@.service etc/systemd/system/multi-user.target.wants/netctl-auto@wlan0.service

Now you can start your device and you should get automatically connected to your WiFi network.