# Project Zomboid Server using LinuxGSM

* [VM Hardware](#vm-hardware)
* [Server Installation](#server-installation)
  * [Manual Install using LinuxGSM](#manual-install-using-linux-gsm)
  * [Configure server](#configure-server)
    * [Vanilla map choices](#vanilla-map-choices)
  * [Import server configurations](#import-server-configurations)
  * [Add Workshop Mods](#add-workshop-mods)
  * [First start](#first-start)
  * [Running on Boot](#running-on-boot)
    * [Usage Info](#usage-info)
    * [Running](#running)
    * [Updating](#updating)
    * [Debug](#debug)
* [LinuxGSM WebUI Installation](#linuxgsm-webui-installation)
  * [Setup user](#setup-user)
  * [Installation](#installation)
  * [Add LGSM Project Zomboid server to WebUI](#add-lgsm-project-zomboid-server-to-webui)

## VM Hardware
- 50 GB Disk
- 2 Cores
- 4 GB RAM
- Ubuntu server 24.04

# Server Installation
This guide is made using https://linuxgsm.com/servers/pzserver/ 
## Manual Install using LinuxGSM
1. `sudo adduser pzserver`
2. `sudo usermod -aG sudo pzserver`
3. `su - pzserver`
4. `curl -Lo linuxgsm.sh https://linuxgsm.sh && chmod +x linuxgsm.sh && bash linuxgsm.sh pzserver`
5. `./pzserver install`

## Configure server
1. `sudo nano /home/pzserver/lgsm/config-lgsm/pzserver/pzserver.cfg`
2. add line: `adminpassword="secret"`
3. Configure map: `sudo nano /home/pzserver/Zomboid/Server/pzserver.ini` Change `Map=Muldraugh, KY` to the wanted map.
### Vanilla map choices:
- `Muldraugh, KY`
- `Riverside, KY`
- `Rosewood, KY`
- `West Point, KY`

## Import server configurations
You can create server configurations in the Project Zomboid server host tab and add to `/home/pzserver/Zomboid/Server/` from your local computer


## Add Workshop Mods
1. `sudo nano /home/pzserver/Zomboid/Server/pzserver.ini`
2. Add Workshop mod IDs to `WorkshopItems=` (Example: `WorkshopItems=2200148440;2711720885`)
3. Add Mod IDs to `Mods=` (Example: `Mods=Britas;SchizophreniaTrait`)

## First start
The LinuxGSM Project Zomboid server doesn't ask for a administrator password, that means the first start must be with debug mode to set a administrator password.
- `./pzserver debug`

## Running on Boot
1. Create a service file in `/etc/systemd/system/`

`pzserver.service:`
```
[Unit]
Description=LinuxGSM Project Zomboid Server
After=network-online.target
Wants=network-online.target

[Service]
Type=forking
User=pzserver
WorkingDirectory=/home/pzserver
#Assume that the service is running after main process exits with code 0
RemainAfterExit=yes
ExecStart=/home/pzserver/pzserver start
ExecStop=/home/pzserver/pzserver stop
Restart=no

[Install]
WantedBy=multi-user.target
```

2. `sudo systemctl daemon-reload`
3. `sudo systemctl start pzserver`
4. `sudo systemctl enable pzserver`

### Usage Info
Must be in the pzserver user (`su - pzserver`)\

List all commands using: `./pzserver`

### Running
start: `./pzserver start`\
stop: `./pzserver stop`\
restart: `./pzserver restart`\
console: `./pzserver console`

### Updating
update: `./pzserver update`

### Debug
debug: `./pzserver debug`

# LinuxGSM WebUI Installation
This guide is made using: https://github.com/BlueSquare23/web-lgsm

## Setup user
1. `sudo adduser weblgsm`
2. `sudo usermod -aG sudo weblgsm`
3. `su - weblgsm`

## Installation
1. `git clone https://github.com/BlueSquare23/web-lgsm.git`
2. `cd web-lgsm`
3. `./install.sh`
4. `sudo cp main.conf main.conf.local`
5. Change `host` to `0.0.0.0`: `sudo nano main.conf.local` (For local usage)
6. Create a service file in `/etc/systemd/system/`

`weblgsm.service:`
```
[Unit]
Description=LinuxGSM WebUI
After=network-online.target
Wants=network-online.target

[Service]
Type=forking
User=weblgsm
WorkingDirectory=/home/weblgsm
#Assume that the service is running after main process exits with code 0
RemainAfterExit=yes
ExecStart=/home/weblgsm/web-lgsm/web-lgsm.py
ExecStop=/home/weblgsm/web-lgsm/web-lgsm.py --stop
Restart=no

[Install]
WantedBy=multi-user.target
```

7. `sudo systemctl start weblgsm.service`
8. `sudo systemctl enable weblgsm.service`
9. Access from web: ip:12357
10. Configure admin user

## Add LGSM Project Zomboid server to WebUI

1. `Add an Existing LGSM Installation`
2. Installation Title: `pzserver`
3. Installation directory path: `/home/pzserver`
4. LGSM script name: `pzserver`
5. Game server system username: `pzserver`
6. Take the public key location from the notification after adding the server. (`/home/weblgsm/.ssh/example_key.pub`)
7. `exit`
8. `su - weblgsm`
9. `cat .ssh/example_key.pub` and copy the key
10. `exit`
11. `su - pzserver`
12. `sudo mkdir .ssh`
13. `sudo nano .ssh/authorized_keys` and paste the key