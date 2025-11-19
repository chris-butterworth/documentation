# Linux Mint Cinnamon install on primary desktop PC

## Install main OS

## Setup rsync backups with timeshift

## Godot Dev Environment

install instructions from [chickensoft](https://chickensoft.games/docs/setup) and [godot](https://docs.godotengine.org/en/stable/tutorials/scripting/c_sharp/c_sharp_basics.html)

Install the latest dotnet
```bash
cd ~/Downloads
wget https://dot.net/v1/dotnet-install.sh -O dotnet-install.sh
chmod +x ./dotnet-install.sh
./dotnet-install.sh --version latest
export DOTNET_ROOT=$HOME/.dotnet
export PATH=$PATH:$DOTNET_ROOT:$DOTNET_ROOT/tools
```

add the last two env variables to bashrc, to persist in reboot
``` bash
nano ~/bashrc
export DOTNET_ROOT=$HOME/.dotnet
export PATH=$PATH:$DOTNET_ROOT:$DOTNET_ROOT/tools
```

get latest version number of godot from [here](https://www.nuget.org/packages/GodotSharp/) to use in the command below
install godot using chickensoft's installer
``` bash
dotnet tool install --global Chickensoft.GodotEnv
godotenv godot install 4.0.1
```

Install VS Code from their website

In Godot's Editor → Editor Settings menu:
    Set Dotnet -> Editor -> External Editor to Visual Studio Code.


## Unity Dev Environment

- VS Code is a .deb from their website

`sudo apt install wget git`

### Dotnet
``` bash 
sudo apt-get update && sudo apt-get install -y dotnet-sdk-8.0
```

``` bash
dotnet --info
```

### Mono
[Guide here](https://www.mono-project.com/download/stable/)

``` bash 
sudo apt install dirmngr ca-certificates gnupg

sudo gpg --homedir /tmp --no-default-keyring --keyring gnupg-ring:/usr/share/keyrings/mono-official-archive-keyring.gpg --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 3FA7E0328081BFF6A14DA29AA6A19B38D3D831EF

sudo chmod +r /usr/share/keyrings/mono-official-archive-keyring.gpg

echo "deb [signed-by=/usr/share/keyrings/mono-official-archive-keyring.gpg] https://download.mono-project.com/repo/debian stable-buster main" | sudo tee /etc/apt/sources.list.d/mono-official-stable.list

sudo apt update
```

``` bash
sudo apt install mono-devel
```

``` bash
mozroots --import --sync
```

### Unity
``` bash
wget -qO - https://hub.unity3d.com/linux/keys/public | gpg --dearmor | sudo tee /usr/share/keyrings/Unity_Technologies_ApS.gpg > /dev/null

sudo sh -c 'echo "deb [signed-by=/usr/share/keyrings/Unity_Technologies_ApS.gpg] https://hub.unity3d.com/linux/repos/deb stable main" > /etc/apt/sources.list.d/unityhub.list'

sudo apt update

sudo apt-get install unityhub
```