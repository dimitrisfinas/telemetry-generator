# TELEMETRY GENERATOR VM INSTALLATION

## Prerequisites

- Create a debian 12 VM
- if necessary, create firewall rule to enable SSH the VM (see https://cloud.google.com/iap/docs/using-tcp-forwarding#firewall)
  - firewall setting to use:
    - name: ssh-to-cs-dim-telemetry-generator
    - description: Allow SSH connection from GCloud to my VM
    - Source filter: IP Range: 35.235.240.0/20
    - Destination filter: IP ranges: (put internal IP of your VM here)
    - Protocol and ports: TCP: 22


## Pre-Setup

- SSH login to the VM
- install git
```shell
sudo apt update
sudo apt install git
git --version
```

- install go (follow instruction here https://www.digitalocean.com/community/tutorials/how-to-install-go-on-debian-10 with newer version)
```shell
curl -O https://dl.google.com/go/go1.21.6.linux-amd64.tar.gz
tar xvf go1.21.6.linux-amd64.tar.gz
sudo chown -R root:root ./go
sudo mv go /usr/local
```
- edit your profile to add go path
```shell
nano ~/.profile
```
- Add lines below and Ctrl+X to save and leave
```shell
export GOPATH=$HOME/work
export PATH=$PATH:/usr/local/go/bin:$GOPATH/bin
```
- reload your profile
```shell
source ~/.profile
```
- check your installation
```shell
go version
```

Now, you can follow instructions from [README.md](README.md)

Don't forget to build the project the first time without
```shell
opentelemetry-collector-builder --config config/builder-config.yml
```
(this will take several minutes to run)

# Running your command


# Running your command in background
```shell
nohup command >/dev/null 2>&1 &
```

example:
```shell
nohup ./git/telemetry-generator/build/telemetry-generator --config $CONFIG_FILE >/dev/null 2>&1 &
```
