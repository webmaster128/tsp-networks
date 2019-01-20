# Install Substrate from source

## Prepare machine

```
sudo apt update \
  && sudo apt upgrade -y \
  && sudo apt autoremove -y \
  && sudo apt install -y docker.io jq joe screen \
  && sudo reboot
```

## Install Substrate

Now run `screen` and inside

```
curl https://getsubstrate.io -sSf | time bash
```

This process takes about 1 hour.
Press `^A ^D` to detach screen and run `screen -r` to resume to installation.

## Add substrate tools to PATH

```
source ~/.cargo/env
```

## Generate validator account

Subkey is Substrate's command line tool to handle Ed25519 keypairs. To generate a new keypair, use `subkey generate`:

```
$ subkey generate
Seed 0xd26c63905a3b2f455185cff37dfbe1b70ec3c16ea33138772d430f24d6c1d7d2 is account:
  Public key (hex): 0x8d240d69a6b5006da03bfb7ee59888bd310bf7727ff193ebed73a13a8067b0db
  Address (SS58): 5FFmKSNARNV4gkJLczvWMakv8ooNRufmQtEwuwoheesqAHEc
```

Address is in the Substrate-specific SS58 format (base58 of network identifier + pubkey + checksum)

## Generate new chain

1. Create new chainspec.json

```
echo 'export NETNAME=antnet' >> ~/.profile
source ~/.profile

export PUBLIC_IP_ADDRESS=$(curl -sS 'https://api.ipify.org?format=json' | jq '.ip' --raw-output)
cat /dev/urandom | head -c 32 | xxd -p -c 256 > ~/.node_key
substrate --chain=staging --node-key "$(< ~/.node_key)" build-spec \
  | jq ".name = \"TSP $NETNAME\"" \
  | jq ".id = \"tsp_$NETNAME\"" \
  | jq '.bootNodes[0] |= gsub("127\\.0\\.0\\.1"; env.PUBLIC_IP_ADDRESS)' \
  | jq '.genesis.runtime.timestamp.period = 20' \
  | jq '.genesis.runtime.balances.existentialDeposit = 1e6' \
  | jq '.genesis.runtime.balances.transferFee = 1e4' \
  | jq '.genesis.runtime.balances.creationFee = 1e4' \
  | jq '.genesis.runtime.balances.transactionBaseFee = 1e4' \
  | jq '.genesis.runtime.balances.transactionByteFee = 1e2' \
  | jq '.genesis.runtime.balances.balances[0][1] = 1e15' \
  > ~/"$NETNAME.json"
```

2. Set your validator addresses in `authorities` of  ~/"$NETNAME.json", e.g.


```
"consensus": {
  "authorities": [
    "5EzN8QTXPj3kUqDpxpZxFmE6ZGAxaEoF3vjhVFpRLMxNn8zt",
    "5FQhfHeRdxyPsMrd8mJD33dwJ533AB3v4MdYgsXyi2yqXaME",
    "5DDMAh8wXWJiMGNNdKRcmnXvRFDFUfAmt6EWnv2bDyqdStds"
  ],
  [...]
}
```

3. Set your public keys in balances, `validators`, `invulnerables`, `intentions`, `authorities`, `sudo` of  ~/"$NETNAME.json", e.g.


```
"balances": [
  [
    "0x81654e5b550e99779579d5b5873a96ddbf102db97e48a1fd2a1753a2e58c678e",
    1000000000000000
  ],
  [
    "0x93f4f2a990d10c8a4fefd0614c9c996552657db278d16df3d96d25c4507570e6",
    1000000000000000
  ],
  [
    "0x32d3cbc03d546667ca707e9e66bb25d606228818a1128f8758caa555df8e9d67",
    1000000000000000
  ]
]
```

```
"session": {
  "validators": [
    "0x81654e5b550e99779579d5b5873a96ddbf102db97e48a1fd2a1753a2e58c678e",
    "0x93f4f2a990d10c8a4fefd0614c9c996552657db278d16df3d96d25c4507570e6",
    "0x32d3cbc03d546667ca707e9e66bb25d606228818a1128f8758caa555df8e9d67"
  ],
  [...]
}
```

```
"staking": {
  [...],
  "invulnerables": [
    "0x81654e5b550e99779579d5b5873a96ddbf102db97e48a1fd2a1753a2e58c678e",
    "0x93f4f2a990d10c8a4fefd0614c9c996552657db278d16df3d96d25c4507570e6",
    "0x32d3cbc03d546667ca707e9e66bb25d606228818a1128f8758caa555df8e9d67"
  ],
  [...],
  "intentions": [
    "0x81654e5b550e99779579d5b5873a96ddbf102db97e48a1fd2a1753a2e58c678e",
    "0x93f4f2a990d10c8a4fefd0614c9c996552657db278d16df3d96d25c4507570e6",
    "0x32d3cbc03d546667ca707e9e66bb25d606228818a1128f8758caa555df8e9d67"
  ],
  [...]
}
```

```
"grandpa": {
  "authorities": [
    [
      "5EzN8QTXPj3kUqDpxpZxFmE6ZGAxaEoF3vjhVFpRLMxNn8zt",
      1
    ],
    [
      "5FQhfHeRdxyPsMrd8mJD33dwJ533AB3v4MdYgsXyi2yqXaME",
      1
    ],
    [
      "5DDMAh8wXWJiMGNNdKRcmnXvRFDFUfAmt6EWnv2bDyqdStds",
      1
    ]
  ]
},
```

```
"sudo": {
  "key": "0x81654e5b550e99779579d5b5873a96ddbf102db97e48a1fd2a1753a2e58c678e"
}
```

4. Build raw chain definition

```
substrate --chain ~/"$NETNAME.json" build-spec --raw > ~/"$NETNAME.raw.json"
````

5. Create blockchain checksum

```
sha256sum $NETNAME*.json
```

6. Start chain as a validator

The `--key` is the password from above

```
substrate --chain ~/"$NETNAME.raw.json" --node-key "$(< ~/.node_key)" --validator --key 0x1b7f4909b5a0c605d2ddd047c4bc36e4a403c4b88739f4ac220b7a37be96df90
````

## Update substrate

```
cargo install --git https://github.com/paritytech/substrate subkey --force
cargo install --git https://github.com/paritytech/substrate substrate --force
```
