# vboxnet0 host-only network interface issue

### On some distributions, the host-only interface vboxnet0 will not persist between reboots. One way to have it run on reboot is to use the systemd.service.

### First create a bash script to run the vboxmanage commands:
```
sudo mkdir /opt/systemd/

sudo vi /opt/systemd/vboxhostonly
```
```
#!/bin/bash

vboxmanage hostonlyif create
vboxmanage hostonlyif ipconfig vboxnet0 --ip 192.168.56.1
vboxmanage hostonlyif remove "vboxnet1" > /dev/null 2>&1; done

```

### Make sure this file has executable permissions:

```
sudo chmod +x /opt/systemd/vboxhostonly
```

### Next, create the "vboxhostonlynic.service" file in /etc/systemd/system/:

```
sudo touch /etc/systemd/system/vboxhostonlynic.service
```

```
[Unit]
Description=Setup VirtualBox Hostonly Adapter
After=vboxdrv.service

[Service]
Type=oneshot
ExecStart=/opt/systemd/vboxhostonly

[Install]
WantedBy=multi-user.target
```

### Now install the systemd service unit and enable it so it will be executed at the boot time:

```
sudo systemctl daemon-reload
sudo systemctl enable vboxhostonlynic.service
```

#### You can check if it works properly by running:

```
sudo systemctl start vboxhostonlynic.service
```
