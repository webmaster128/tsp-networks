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
2. On a Linux machine run

````
su bob
cd ~
mkdir --mode=777 substrate_data
export NODE_PORT=8002
docker run \
  --user "$UID" \
  -v "$HOME":/data \
  -p "$NODE_PORT:$NODE_PORT/tcp" \
  parity/substrate:latest \
  --chain /data/mychain.json --base-path /data/substrate_data --port "$NODE_PORT"
```
