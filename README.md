# Pi Print Server

Connecting a Brother HL-L2300D to a Raspberry Pi for CUPS network printing.

Instructions from [Make Your Own Wireless Printer with a Raspberry Pi](https://www.makeuseof.com/tag/make-wireless-printer-raspberry-pi/).

## Setup Raspbian lite headless

Download latest raspbian lite image from:

[Download Raspbian for Raspberry Pi](https://www.raspberrypi.org/downloads/raspbian/)

Apply image with (on MacOS):

```shell
diskutil list # note disk id <i>
diskutil unmountDisk /dev/disk<i>
sudo dd bs=1m if=path/to/raspbian.img of=/dev/rdisk<i> conv=sync
```

The image should mount.

From this source, create ssh, wifi access.

[How to set up WiFi on your Raspberry Pi without a keyboard or monitor (or ethernet)](https://howchoo.com/g/ndy1zte2yjn/how-to-set-up-wifi-on-your-raspberry-pi-without-ethernet)

```shell
touch /Volumes/boot/ssh
cat <<'EOF' >> /Volumes/boot/wpa_supplicant.conf
ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev
network={
    ssid="YOUR_NETWORK_NAME"
    psk="YOUR_PASSWORD"
    key_mgmt=WPA-PSK
}
EOF
```



## printer-driver-brlaser

This script installs `brlaser` from latest source, which includes a reference to the HL-L2300D model.

[https://github.com/pdewacht/brlaser](https://github.com/pdewacht/brlaser)

## bash script

```shell
sudo apt-get update
sudo apt-get upgrade
sudo apt-get install samba cups git libcups2-dev libcupsimage2-dev cmake
git clone https://github.com/pdewacht/brlaser.git
cd brlaser
cmake . && make && sudo make install
sudo usermod -a -G lpadmin pi
cupsctl --remote-admin --remote-any --share-printers
```

## manual steps

- run `raspi-config` and change Network Options -> Hostname to `pi-print-server`
- `passwd` to change default password of user `pi`
- reboot
- configure printer from another computer on the network via http://pi-print-server:631
