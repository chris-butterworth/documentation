# Headless Snapclient + local bluetooth receiver

## Debian install instructions

### Snapserver install

#### Snapserver
`wget https://github.com/badaix/snapcast/releases/download/v0.34.0/snapserver_0.34.0-1_amd64_trixie.deb`

`sudo apt install ~/snapserver_0.34.0-1_amd64_trixie.deb`

`sudo systemctl enable snapserver`

`sudo nano /etc/snapserver.conf`

#### Librespot (spotify connect)

Install Rust
`sudo apt install curl`

`curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh`

`rustup update` (not found for me, skipped)

`apt install git build-essential libasound2-dev libssl-dev pkg-config cargo`

`git clone https://github.com/librespot-org/librespot.git`

`cd librespot`

`cargo build --release`

`cp target/release/librespot /usr/bin/`

todo - Add rows to config


add to path
`sudo install -m 755 target/release/librespot /usr/local/bin/` 


`sudo systemctl restart snapserver`


To update librespot 
``` bash
cd ~/librespot
git fetch origin
git reset --hard origin/master
cargo build --release
sudo cp target/release/librespot /usr/local/bin/librespot
systemctl restart snapserver
```

### PulseAudio Systemd User Service

This ensures PulseAudio is running at boot for the `chris` user, so Snapclient can connect without requiring a login.

```bash
mkdir -p ~/.config/systemd/user
nano ~/.config/systemd/user/pulseaudio.service
```

``` text
[Unit]
Description=PulseAudio Sound System
After=sound.target network.target

[Service]
Type=simple
ExecStart=/usr/bin/pulseaudio --daemonize=no
Restart=always

[Install]
WantedBy=default.target
```

``` bash 
systemctl --user daemon-reexec
systemctl --user enable pulseaudio
systemctl --user start pulseaudio

# Check status
systemctl --user status pulseaudio

sudo loginctl enable-linger chris
```

### Snapclient install

<https://whynot.guide/posts/howtos/multiroom-media/>

- Install 64-bit Raspbian Lite (terminal only), or Debian server in a VM  
``` bash
ssh-keygen
ssh-copy-id chris@hostname
```

SSH in

``` bash
sudo apt update -y && sudo apt upgrade -y
```

- Copy the latest Snapclient release URL from here (armhf for Raspberry Pi, amd64 for Intel; current version is trixie) <https://github.com/badaix/snapcast/releases>  
- From user folder: `wget copied-url`  
- `sudo apt install ./snapclient.... press tab`  

**Systemd setup for Snapclient using PulseAudio:**

Create a drop-in override to run as your user:

```bash
sudo systemctl edit snapclient
```

```text
[Service]
User=chris
Environment=DISPLAY=:0
Environment=XDG_RUNTIME_DIR=/run/user/1000
ExecStart=
ExecStart=/usr/bin/snapclient --user tcp://192.168.178.163:1704 --player pulse
Restart=on-failure
```

``` bash
sudo systemctl daemon-reexec
sudo systemctl enable snapclient
sudo systemctl start snapclient
sudo journalctl -u snapclient -f
```

**Or if using ALSA instead of PulseAudio**
`sudo nano /etc/default/snapclient`

``` text
START_SNAPCLIENT=true
SNAPCLIENT_OPTS=tcp://127.0.0.1:1704

// or for 96k, will resample automatically
SNAPCLIENT_OPTS="tcp://127.0.0.1:1704 --sampleformat 96000:24:*"
```

### [incomplete] Local Bluetooth Receiver

- `sudo apt-get install pulseaudio pulseaudio-module-bluetooth`
- `sudo usermod -a -G bluetooth chris`
- `sudo reboot now`
- `sudo nano /etc/bluetooth/main.conf`

```text
Class = 0x41C  
...  
DiscoverableTimeout = 0  
```

### Viewing logs

If you can't see the logs and are getting a warning
``` bash
sudo usermod -aG adm,systemd-journal chris
```

``` bash
journalctl -u snapclient -f
journalctl -u snapserver -f
```

## minidsp cli control

https://github.com/mrene/minidsp-rs/releases/tag/v0.1.12

``` bash
cd /home/downloads
sudo wget https://github.com/mrene/minidsp-rs/releases/download/v0.1.12/minidsp_0.1.12-1_amd64.deb
sudo apt install ./minidsp......
```