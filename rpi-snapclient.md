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

### Snapclient install

<https://whynot.guide/posts/howtos/multiroom-media/>

- Install 64bit raspian lite (terminal only), or debian server in a vm...
- `ssh-keygen` (if needed)
- `ssh-copy-id chris@hostname`
- ssh in
- `sudo apt update -y && sudo apt upgrade -y`
- Copy the latest snapclient release url from here. armhf for raspi, amd64 for intel (current version is trixie) <https://github.com/badaix/snapcast/releases>
- From user folder: `wget copied-url`
- `sudo apt install ./snapclient_0.26.0-1_armhf.deb`
- `sudo nano /etc/default/snapclient`

```text
START_SNAPCLIENT=true
SNAPCLIENT_OPTS="--host 192.168.x.x"
```

- `sudo systemctl enable snapclient`
- `sudo systemctl start snapclient`

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
