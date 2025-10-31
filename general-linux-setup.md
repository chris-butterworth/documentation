# General Linux Setup

## Add contrib repo (Steam etc)

`sudo nano /etc/apt/sources.list`

add `contrib` to repo strings eg:

`deb http://deb.debian.org/debian/ bookworm main contrib non-free-firmware`

steam also requires the ability to install 32bit software

`sudo dpkg --add-architecture i386`

`sudo apt update`

`sudo apt install steam-installer`

## To be scripted in Ansible

- install git
- git config --global user.email "............"
- git config --global user.name "............"
- install wget
- ssh-keygen
- copy keygen to github??
- create ~/repos