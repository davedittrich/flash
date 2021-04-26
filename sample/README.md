# cloud-init sample config files

Here you can find a collection of sample configurations to improve your
first-boot experience using HypriotOS.

Beginning with HypriotOS 1.7.0 we have switched to [cloud-init](http://cloudinit.readthedocs.io/en/0.7.9/) which gives you much more power to customize your device automatically during the first initial boot.

You can either use our `flash` tool with option `-u` or `--userdata` to specify the YAML file. The flash tool will copy it to the SD card right after flashing.

Otherwise copy the YAML file to the boot partition of the SD card to the `/boot/user-data` file.

Quick installation:

```
$ flash -u your-cloud-init.yml https://github.com/hypriot/image-builder-rpi/releases/download/v1.8.0/hypriotos-rpi-v1.8.0.img.zip
$ ssh pirate@black-pearl.local
```

login with username "pirate", password "hypriot"


## WiFi

Setup WiFi for your Raspberry Pi Zero or Pi 3 / 3 B+.

* [wifi-user-data.yml](./wifi-user-data.yml)
  * insert WiFi SSID
  * adjust your country code

## SSH public key authentication

Setup your device with a different user account, remove default user and password.

* [ssh-pub-key.yml](./ssh-pub-key.yml)
  * adjust user name
  * insert SSH public key

## Static IP address

Setup your eth0 device with a static IP address.

* [static.yml](./static.yml)

## Hands-free Docker projects

Run a container as a service automatically.

* [rainbow.yml](./rainbow.yml)
  * insert SSH public key
  * insert WiFi SSID and preshared key
  * adjust your country code
  * attach Pimoroni Blinkt
  * flash, boot your Pi 3/0 - enjoy!

# Secure by default installation using ``psec``

You can avoid having a default password and automatically apply usable defaults for
most variables you would want to change using [``psec``](https://pypi.org/project/python-secrets/).

```
$ psec environments create --clone-from sample/hypriot.json
$ psec secrets generate --from-options
$ psec -E run -- flash sample/hypriot_psec.yml https://github.com/hypriot/image-builder-rpi/releases/download/v1.8.0/hypriotos-rpi-v1.8.0.img.zip
```

To see the current variable settings (including the generated password):

```
$ psec secrets show --no-redact
+--------------------------+------------------------+--------------------------+
| Variable                 | Value                  | Export                   |
+--------------------------+------------------------+--------------------------+
| hypriot_user             | pirate                 | hypriot_user             |
| hypriot_password         | HAMLET.usher.LEND.lazy | hypriot_password         |
| hypriot_hostname         | hypriot                | hypriot_hostname         |
| hypriot_eth0_addr        | 192.168.50.1           | hypriot_eth0_addr        |
| hypriot_client_psk       | None                   | hypriot_client_psk       |
| hypriot_client_ssid      | None                   | hypriot_client_ssid      |
| hypriot_pubkey           | None                   | hypriot_pubkey           |
| hypriot_locale           | en_US.UTF-8            | hypriot_locale           |
| hypriot_timezone         | America/Los_Angeles    | hypriot_timezone         |
| hypriot_wifi_country     | US                     | hypriot_wifi_country     |
| hypriot_keyboard_model   | pc105                  | hypriot_keyboard_model   |
| hypriot_keyboard_layout  | us                     | hypriot_keyboard_layout  |
| hypriot_keyboard_options | ctrl:swapcaps          | hypriot_keyboard_options |
+--------------------------+------------------------+--------------------------+
```

Now log in with username (``pirate``, by default) and the password generated for you.

```
$ ssh pirate@hypriot.local
```

