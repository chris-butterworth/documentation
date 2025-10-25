# Headless Snapclient + local bluetooth receiver

## Debian install instructions

<https://whynot.guide/posts/howtos/multiroom-media/>

- Install 64bit raspian lite (terminal only), or debian server in a vm...
- ssh-keygen (if needed)
- ssh-copy-id chris@hostname
- ssh in
- sudo apt update -y && sudo apt upgrade -y
- Copy the latest snapclient release url from here. armhf for raspi, amd64 for intel (current version is trixie) <https://github.com/badaix/snapcast/releases>
- From user folder: wget copied-url
- sudo apt install ./snapclient_0.26.0-1_armhf.deb
- sudo nano /etc/default/snapclient
- START_SNAPCLIENT=true
SNAPCLIENT_OPTS="--host 192.168.x.x"
- sudo systemctl enable snapclient
- sudo systemctl start snapclient
