# sge-testnet-1

> This chain is a public-testnet.

2nd testnet for the SGE Network application.

## Hardware Requirements

- **Minimal**
  - 1 GB RAM
  - 25 GB HDD
  - 1.4 GHz CPU
- **Recommended**
  - 2 GB RAM
  - 100 GB HDD
  - 2.0 GHz x2 CPU

---

## Operating System

- Linux/Windows/MacOS(x86)
- **Recommended**
  - Linux(x86_64)
  - Ubuntu 20.04 LTS

---

## Network
- Sentry node architecture and secure firewall configurations are highly recommended [ref](https://forum.cosmos.network/t/sentry-node-architecture-overview/454)
- Port & Firewall settings (Ports Allowed)
  - rpc port: 26657
  - p2p port: 26656
  - grpc port: 9090

---

## Installation Steps

> Prerequisite: go1.18+ required [ref](https://golang.org/doc/install)
```shell
sudo snap install --classic go
```
> Prerequisite: git [ref](https://github.com/git/git)
```shell
sudo apt install -y git gcc make
```
> Prerequisite: Set environment variables
```shell
sudo nano $HOME/.profile
# Add the following two lines at the end of the file
GOPATH=$HOME/go
PATH=$GOPATH/bin:$PATH
# Save the file and exit the editor
source $HOME/.profile
# Now you should be able to see your variables like this:
echo $GOPATH
/home/[your_username]/go
echo $PATH
/home/[your_username]/go/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin
```
> Recommended requirement: Increase 'number of open files' limit
```shell
sudo nano /etc/security/limits.conf
# Before the end of the file, add:
[your_username] soft nofile 4096
# Then reboot the instance for it to take effect and check with:
ulimit -Sn
```
> Optional requirement: GNU make [ref](https://www.gnu.org/software/make/manual/html_node/index.html)
- Clone git repository and Network

```shell
git clone https://github.com/sge-network/sge
git clone https://github.com/sge-network/networks
```

- Checkout release tag

```shell
cd sge
git fetch --tags
git checkout v0.0.3
```

- Install

```shell
cd sge
go mod tidy
make install
```

### Generate keys

`sged keys add [key_name]`

or

`sged keys add [key_name] --recover` to regenerate keys with your [BIP39](https://github.com/bitcoin/bips/tree/master/bip-0039) mnemonic

---

## Node start and validator setup

### Prior to genesis creation and network launch

- [Install](#installation-steps) Sge Network application
- Initialize node

```shell
sged init {{NODE_NAME}} --chain-id sge-testnet-1
```
- Create a new key

```shell
sged keys add <keyName>
```

- Add a genesis account with `10000000000usge tokens`

```shell
sged add-genesis-account {{KEY_NAME}} 10000000000usge
```

- Make a genesis transaction to become a validator

```shell
sged gentx \
  [account_key_name] \
  5000000000usge \
  --commission-max-change-rate 0.01 \
  --commission-max-rate 0.2 \
  --commission-rate 0.05 \
  --min-self-delegation 1 \
  --details [optional-details] \
  --identity [optional-identity] \
  --security-contact "[optional-security@example.com]" \
  --website [optional.web.page.com] \
  --moniker [node_moniker] \
  --chain-id sge-testnet-1
```

- Copy the contents of `${HOME}/.sge/config/gentx/gentx-XXXXXXXX.json`
- Fork the [network repository](https://github.com/sge-network/networks)
- Create a file `gentx-{{VALIDATOR_NAME}}.json` under the `sge-testnet-1/gentxs` folder in the newly created branch, paste the copied text into the file (note: find reference file `gentx-examplexxxxxxxx.json` in the same folder)
- Run `sged tendermint show-node-id` and copy your nodeID
- Run `ifconfig` or `curl ipinfo.io/ip` and copy your publicly reachable IP address
- Create a file `peers-{{VALIDATOR_NAME}}.json` under the `sge-testnet-1/peers` folder in the new branch, paste the copied text from the last two steps into the file (note: find reference file `peers-examplexxxxxxxx.json` in the same folder)
- Create a Pull Request to the `master` branch of the [network repository](https://github.com/sge-network/networks)
  > **NOTE:** the Pull Request will be merged by the maintainers to confirm the inclusion of the validator at the genesis. The final genesis file will be published under the file `sge-testnet-1/genesis.json`.
- Once the submission process has closed and the genesis file has been created, replace the contents of your `${HOME}/.sge/config/genesis.json` with that of `sge-testnet-1/genesis.json`
- Add the required `persistent_peers` or `seeds` in `${HOME}/.sge/config/config.toml` from `sge-testnet-1/peers-nodes.txt`
- Start node
```shell
sged start
```



## Genesis Time
The genesis transactions sent before 0530HRS UTC 17th November, 2022 will be used to publish the `genesis.json` at 0730HRS UTC 17th November, 2022

<!-- > Submitting Gentx is now closed. Genesis has been published and block generation has started -->

---

## After genesis creation and network launch

### Step 1: Start a full node

- [Install](#installation-steps) Sge Network application
- Initialize node

```shell
sged init {{NODE_NAME}}
```

- Replace the contents of your `${HOME}/.sge/config/genesis.json` with that of `sge-testnet-1/genesis.json` from the `master` branch of [network repository](https://github.com/sge-network/networks)

```shell
curl https://github.com/sge-network/blob/master/networks/sge-testnet-1/genesis.json > $HOME/.sge/config/genesis.json
```

- Add `persistent_peers` or `seeds` in `${HOME}/.sge/config/config.toml` from `sge-testnet-1/peers-nodes.txt` from the `master` branch of [network repository](https://github.com/sge-network/networks)
- Start node

```shell
sged start
```

> Note: if you are only planning to run a full node, you can stop here

### Step 2: Create a validator
- Acquire SGE tokens from the [faucet]()
- Wait for your full node to catch up to the latest block (compare to the [explorer]())
- Run `sged tendermint show-validator` and copy your consensus public key
- Send a create-validator transaction

```
sged tx staking create-validator \
  --amount 500000000usge \
  --commission-max-change-rate 0.01 \
  --commission-max-rate 0.2 \
  --commission-rate 0.1 \
  --from [account_key_name] \
  --fees 400000usge \
  --min-self-delegation 1 \
  --moniker [validator_moniker] \
  --pubkey $(sged tendermint show-validator) \
  --chain-id sge-network-1 \
  -y
```

---

## Persistent Peers
The `persistent_peers` needs a comma-separated list of trusted peers on the network, you can acquire it from the [peer-nodes.txt](https://github.com/sge-network/blob/master/networks/sge-testnet-1/peer-nodes.txt) for example:
```
4980b478f91de9be0564a547779e5c6cb07eb995@3.239.15.80:26656,0e7042be1b77707aaf0597bb804da90d3a606c08@3.88.40.53:26656
```

## Version
This chain is currently running on sge [v0.0.3](https://github.com/sge-network/sge/releases/tag/v0.0.3)
Commit Hash: [c2f074f15fa895b0d8e67a9d88bfd2b9d9833b2f](https://github.com/sge-network/sge/commit/c2f074f15fa895b0d8e67a9d88bfd2b9d9833b2f)

## Binary
The binary can be downloaded from [here](https://github.com/sge-network/sge/releases/tag/v0.0.3)

## Explorer
The Block Explorer for this chain is available [here](https://blockexplorer.testnet.sgenetwork.io/)

## Faucet
Discord Faucet is available [here](https://discord.gg/2RU9FZVQ)

### Documentation
For the most up to date documentation please visit [Gitbook](https://six-sigma-sports.gitbook.io/documentation-1/)

### RPC & API 
- RPC is available [here](http://rpc.testnet.sgenetwork.io:26657/)
- API is available [here](http://api.testnet.sgenetwork.io:1317/)

