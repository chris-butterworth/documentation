# Raspberry Pi Headless Snapclient install

## Snapclient + local bluetooth receiver

1. Install 64bit raspian list (terminal only)
2. Copy the latest snapclient armhf release url from here (current version is trixie) <https://github.com/badaix/snapcast/releases>
3. From user folder: wget copied-url
4. apt install snapclient_0.26.0-1_armhf.deb
5. nano /etc/default/snapclient
6. START_SNAPCLIENT=true
SNAPCLIENT_OPTS="--host 192.168.x.x"
7. systemctl enable snapclient
8. systemctl start snapclient
