# Hifi Input Service

``` bash
sudo nano /usr/local/bin/minidspInput.sh
```

old script, hit alsa/minidsp too often and cause the usb device to get overloaded overnight, and needed power cycling in the morning.
apparently pcm0p/info will probe the actual device, where as pcm0p/sub0/status is just a status file
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

attempt 2  with status change stored in tmp file to avoid polling usb device
``` bash
#!/bin/bash

state_file="/tmp/minidsp_last_state"

status=$(grep -q RUNNING /proc/asound/card0/pcm0p/sub0/status && echo "RUNNING" || echo "CLOSED")
desired=$([ "$status" = "RUNNING" ] && echo "usb" || echo "toslink")

last=$(cat "$state_file" 2>/dev/null || echo "unknown")

if [ "$desired" != "$last" ]; then
    minidsp source "$desired"
    echo "$desired" > "$state_file"
fi

sleep 1   # small debounce
```

attempt 3, with file lock to prevent rapid polling
``` bash
#!/bin/bash
(
  flock -n 200 || exit 0

  state_file="/tmp/minidsp_last_state"

  status=$(grep -q RUNNING /proc/asound/card0/pcm0p/sub0/status && echo "RUNNING" || echo "CLOSED")
  desired=$([ "$status" = "RUNNING" ] && echo "usb" || echo "toslink")

  last=$(cat "$state_file" 2>/dev/null || echo "unknown")

  if [ "$desired" != "$last" ]; then
      minidsp source "$desired"
      echo "$desired" > "$state_file"
  fi

  sleep 1   # small debounce

) 200>/tmp/minidsp.lock
```

``` bash
sudo  chmod +x /usr/local/bin/minidspInput.sh
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