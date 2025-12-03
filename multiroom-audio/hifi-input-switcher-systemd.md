# Hifi Input Service

``` bash
sudo nano /usr/local/bin/minidspInput.sh
```

``` bash
#!/bin/bash
# /usr/local/bin/snapclient-minidisp.sh

if cat /proc/asound/card0/pcm0p/info | grep -q "subdevices_avail: 0" && minidsp | grep "Toslink";
    then
        minidsp source usb
elif cat /proc/asound/card0/pcm0p/info | grep -q "subdevices_avail: 1" && minidsp | grep "Usb";
    then
        minidsp source toslink
fi
```

``` bash
sudo  chmod +x /usr/local/bin/snapclient-minidsp.sh
sudo nano /etc/systemd/system/minidspInput.service
```

``` bash
[Unit]
Description= Name service

[Service]
ExecStart=/usr/local/bin/minidspInput.sh

[Install]
WantedBy=multi-user.target
```

``` bash
// start the service to test and register it
sudo systemctl start minidspInput
sudo systemctl enable minidspInput.service
sudo nano /etc/systemd/system/minidspInput.timer
```

``` bash
[Unit]
Description="Run minidspInput.service every 1 second"

[Timer]
OnBootSec=2min
OnUnitActiveSec=1sec
AccuracySec=1
Unit=minidspInput.service

[Install]
WantedBy=multi-user.target
```

``` bash
systemd-analyze verify /etc/systemd/system/minidspInput.*
sudo systemctl daemon-reload
sudo systemctl start minidspInput.timer
sudo systemctl enable minidspInput.timer
sudo systemctl status minidspInput.timer
```