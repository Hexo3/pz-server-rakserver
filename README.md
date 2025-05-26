# pz-server-rakserver
## VM Hardware
- 50 GB Disk
- 2 Cores
- 4 GB RAM
- Ubuntu server 24.04

# Server Installation
This guide is made using https://linuxgsm.com/servers/pzserver/ 
## Manual Install using LinuxGSM
1. `adduser pzserver`
2. `sudo usermod -aG sudo pzserver`
3. `su - pzserver`
4. `curl -Lo linuxgsm.sh https://linuxgsm.sh && chmod +x linuxgsm.sh && bash linuxgsm.sh pzserver`
5. `./pzserver install`

## Configure server
1. `sudo nano /home/pzserver/lgsm/config-lgsm/pzserver/pzserver.cfg`
2. add line: `adminpassword="secret"`


## Add Workshop Mods

1. `sudo nano /home/pzserver/Zomboid/Server/pzserver.ini`
2. Add Workshop mod IDs to `WorkshopItems=` (Example: `WorkshopItems=2200148440;2711720885`)
3. Add Mod IDs to `Mods=` (Example: `Mods=BritasWeaponPack;SchizophreniaTraitMod;`)

## Usage
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

### Running on Boot
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

2. `systemctl daemon-reload`
3. `systemctl start pzserver`
4. `systemctl enable pzserver`

# LinuxGSM WebUI Installation
This guide is made using: 