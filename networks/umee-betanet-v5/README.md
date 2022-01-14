# Umee Betanet v5

## Hardware Requirements
* **Minimal**
  * 4GB RAM
  * 100GB SSD
  * 2 vCPU
* **Recommended**
  * 8GB RAM
  * 200GB SSD
  * 4 vCPU

## Installation Steps
Install dependencies:
```shell
cd $HOME
sudo apt update
sudo apt install make clang pkg-config libssl-dev build-essential git jq ncdu bsdmainutils -y < "/dev/null"
```
Install Go:
```shell
cd $HOME
wget -O go1.17.1.linux-amd64.tar.gz https://golang.org/dl/go1.17.1.linux-amd64.tar.gz
rm -rf /usr/local/go && tar -C /usr/local -xzf go1.17.1.linux-amd64.tar.gz && rm go1.17.1.linux-amd64.tar.gz
echo 'export GOROOT=/usr/local/go' >> $HOME/.bash_profile
echo 'export GOPATH=$HOME/go' >> $HOME/.bash_profile
echo 'export GO111MODULE=on' >> $HOME/.bash_profile
echo 'export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin' >> $HOME/.bash_profile && . $HOME/.bash_profile
go version
```
Clone git repository:
```shell
git clone --depth 1 --branch v0.3.0 https://github.com/umee-network/umee.git
```
Install:
```shell
cd $HOME/umee && make install
```
Verify version:
```shell
umeed version
```
Version should be `v0.3.0`

Download genesis (replace `YOUR_NODE_NAME`):
```shell
umeed init YOUR_NODE_NAME --chain-id umee-betanet-v5
wget -O $HOME/.umee/config/genesis.json "https://raw.githubusercontent.com/umee-network/testnets/main/networks/umee-betanet-v5/genesis.json"
sha256sum $HOME/.umee/config/genesis.json
umeed unsafe-reset-all
```
Configure your node:
```shell
sed -i.bak -e "s/^minimum-gas-prices = \"\"/minimum-gas-prices = \"0.001uumee\"/" $HOME/.umee/config/app.toml
sed -i '/\[grpc\]/{:a;n;/enabled/s/false/true/;Ta};/\[api\]/{:a;n;/enable/s/false/true/;Ta;}' $HOME/.umee/config/app.toml
external_address=`curl ifconfig.me`
peers="8ca2c44d5ed4716f99c15e61099c6b085cd8b266@45.76.91.152:26656,1b18e2e71df92fb42272ceb52e6b4c85b3a25ada@185.92.222.137:26656"
sed -i.bak -e "s/^external_address = \"\"/external_address = \"$external_address:26656\"/; s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.umee/config/config.toml
```
Install service:
```shell
echo "[Unit]
Description=Umee Node
After=network.target

[Service]
User=$USER
Type=simple
ExecStart=$(which umeed) start
Restart=on-failure
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target" > $HOME/umeed.service
sudo mv $HOME/umeed.service /etc/systemd/system
sudo tee <<EOF >/dev/null /etc/systemd/journald.conf
Storage=persistent
EOF
sudo systemctl restart systemd-journald
sudo systemctl daemon-reload
sudo systemctl enable umeed
sudo systemctl restart umeed
```
Check your node logs:
```shell
journalctl -u umeed -f
```
## Generate keys
```shell
umeed keys add YOUR_WALLET_NAME
```
You can recover your keys with `--recover` flag if you have mnemonic

## Faucet
You can request funds from faucet via the following command (replace `YOUR_WALLET_ADDRESS` with your wallet address (`umeed keys show YOUR_WALLET_NAME -a`)):
```shell
curl -X POST -d "{"address": "YOUR_WALLET_ADDRESS"}" -H "Content-Type: application/json" https://faucet.umee.nodes.guru
```
This command will give you 10 UMEE, limit is 100 UMEE per address.

Or you can ask for tokens in the [#faucet](https://discord.gg/6nKmfejXSw) Discord channel.
```shell
!faucet YOUR_WALLET_ADDRESS
```
## Create validator
Use the following command (do not forget to replace `YOUR_NODE_NAME` and `YOUR_WALLET_NAME`):
```shell
$HOME/go/bin/umeed tx staking create-validator -y --amount=9500000uumee --pubkey=`$HOME/go/bin/umeed tendermint show-validator` --moniker=YOUR_NODE_NAME --commission-rate=0.10 --commission-max-rate=0.20 --commission-max-change-rate=0.01 --min-self-delegation=1 --from=YOUR_WALLET_NAME --chain-id=umee-betanet-v5 --fees 1000uumee
```

## Explorer
Explorer available [here](https://betanet-5.umee.nodes.guru/).
