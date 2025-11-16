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


This script will auto resolve any conflicts by discarding the local. 

It will always favour the remote. This is okay as long as:

1. the repo is only used by one person
2. that person doesn't edit the repo on 2 machines at once
``` text
[Unit]
Description=Auto sync git repo %i

[Service]
Type=oneshot
WorkingDirectory=%h/repos/%i
ExecStart=/bin/bash -c "\
  set -e; \
  branch=$(git rev-parse --abbrev-ref HEAD); \
  git fetch origin; \
  if ! git diff --quiet; then \
    git add -A; \
    git commit -m \"Auto-sync: $(date -Iseconds)\" || true; \
  fi; \
  if ! git pull --rebase --autostash; then \
    echo 'Conflict detected, auto-resolving...'; \
    git checkout --theirs . || git checkout --ours .; \
    git add .; \
    git rebase --continue 2>/dev/null || git commit -m \"Auto-resolved sync conflict\"; \
  fi; \
  git checkout $branch 2>/dev/null || true; \
  git push origin HEAD --progress"

```

`nano ~/.config/systemd/user/git-sync@.timer`

``` text
[Unit]
Description=Run git sync every 1 minutes for %i

[Timer]
OnUnitActiveSec=1min
Persistent=true
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