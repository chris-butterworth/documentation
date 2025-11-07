# General Linux Setup

## Add contrib repo (Steam etc)

`sudo nano /etc/apt/sources.list`

add `contrib` to repo strings eg:

`deb http://deb.debian.org/debian/ bookworm main contrib non-free-firmware`

steam also requires the ability to install 32bit software

`sudo dpkg --add-architecture i386`

`sudo apt update`

`sudo apt install steam-installer`

## Other apps
- [sleek](https://github.com/ransome1/sleek/releases) (todo.txt)
- obs studio
- apostrophe markdown editor

## repo auto sync
``` bash
mkdir -p ~/.config/systemd/user
nano ~/.config/systemd/user/git-sync@.service
```

``` text
[Unit]
Description=Auto sync git repo %i

[Service]
Type=oneshot
WorkingDirectory=%h/repos/%i
ExecStart=/bin/bash -c "\
  set -e; \
  git fetch origin; \
  if ! git diff --quiet; then \
    git add -A; \
    git commit -m \"Auto-sync: $(date +%Y-%m-%d %H:%M) (<machine-name>)\" || true; \
  fi; \
  git pull --rebase --autostash; \
  git push"
```

`nano ~/.config/systemd/user/git-sync@.timer`

``` text
[Unit]
Description=Run git sync every 30 minutes for %i

[Timer]
OnBootSec=5min
OnUnitActiveSec=30min
Unit=git-sync@%i.service

[Install]
WantedBy=timers.target
```

``` bash
systemctl --user daemon-reload
systemctl --user enable --now git-sync@notebook.timer
systemctl --user enable --now git-sync@documentation.timer
systemctl --user list-timers
```

To see the logs `journalctl --user -u git-sync@notebook.service`

## ansible to do

- install git
- git config --global user.email "............"
- git config --global user.name "............"
- install wget
- ssh-keygen
- copy keygen to github??
- create ~/repos