# TSP test networks

This series of test networks utilize [Substrate](https://github.com/paritytech/substrate)
to evaluate if we can use this tech stack for TSP infrastructure.

Substrate is in rapid development, so unless labeled with long term support (LTS),
the networks will be shut down after a couple of weeks.

## Networks

| Name          | Started    | Substrate      | Block time | Public validators   | Supply  | Fees |
|---------------|------------|----------------|------------|---------------------|---------|----------|
| antnet 🐜      | 2019-01-20 | 0.10.0-78bb4c0 | 40s        | 5EzN8, 5FQhf, 5DDMA | 3*10^15 | existentialDeposit = 10^6; transferFee / creationFee / transactionBaseFee = 10^4; transactionByteFee = 10^2
| boarnet 🐗     |            |                |            |                     | |
| catnet 🐈      |            |                |            |                     | |
| dognet 🐕      |            |                |            |                     | |
| elephantnet 🐘 |            |                |            |                     | |
| fishnet 🐟     |            |                |            |                     | |
| goatnet 🐐     |            |                |            |                     | |
| hamsternet 🐹  |            |                |            |                     | |

## Join a network

1. Pick a compatible substrate version from [dockerhub parity/substrate](https://hub.docker.com/r/parity/substrate/tags)
2. Start a login shell for the desired user (e.g. as root `su -l bob`)
3. Download chain specification
```
wget https://raw.github.com/webmaster128/tsp-networks/chainspecs/antnet.raw.json
```
4. Set some environment variables
```
echo 'export NETNAME=antnet' >> ~/.profile
echo 'export NODE_PORT=8002' >> ~/.profile
echo 'export SUBSTRATE_VERSION=0.10.0-78bb4c0' >> ~/.profile
source ~/.profile
mkdir --mode=777 substrate_data
```
5. Start the chain
```
docker run \
  --user "$UID" \
  -v "$HOME":/data \
  -p "$NODE_PORT:$NODE_PORT/tcp" \
  "parity/substrate:$SUBSTRATE_VERSION" \
  --chain "/data/$NETNAME.raw.json" --base-path /data/substrate_data --port "$NODE_PORT"
```
