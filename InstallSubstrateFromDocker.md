# Install Substrate from source

## Requirements

1. Docker must be installed

```
docker --version
Docker version 18.06.1-ce, build e68fc7a
```

## Create a system user

```
adduser --disabled-login --gecos "" bob && usermod -a -G docker bob
```

## One-time initialization of user

```
su -l bob
wget https://raw.github.com/webmaster128/tsp-networks/chainspecs/antnet.raw.json
echo 'export NETNAME=antnet' >> ~/.profile
echo 'export NODE_PORT=8002' >> ~/.profile
echo 'export SUBSTRATE_VERSION=0.10.0-78bb4c0' >> ~/.profile
source ~/.profile
mkdir --mode=777 substrate_data
```

## Start substrate

Select substrate version from https://hub.docker.com/r/parity/substrate/tags

```
docker run \
  --user "$UID" \
  -v "$HOME":/data \
  -p "$NODE_PORT:$NODE_PORT/tcp" \
  "parity/substrate:$SUBSTRATE_VERSION" \
  --chain "/data/$NETNAME.raw.json" --base-path /data/substrate_data --port "$NODE_PORT"
```
