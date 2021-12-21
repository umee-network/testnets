# Umee Gravity Wars Testnet (`umee-alpha-mainnet-2`)

## Hardware Requirements
* **Minimum**
  * 4 GB RAM
  * 200 GB SSD
  * 2 vCPU
* **Recommended**
  * 16 GB RAM
  * 600 GB SSD
  * 6 vCPU

## Installation Steps
### Install `go`
#### Method 1:
```bash
cd $HOME
wget -q -O go1.17.1.linux-amd64.tar.gz https://golang.org/dl/go1.17.linux-amd64.tar.gz
rm -rf /usr/local/go && tar -C /usr/local -xzf go1.17.1.linux-amd64.tar.gz && rm go1.17.1.linux-amd64.tar.gz
echo 'export GOROOT=/usr/local/go' >> $HOME/.bash_profile
echo 'export GOPATH=$HOME/go' >> $HOME/.bash_profile
echo 'export GO111MODULE=on' >> $HOME/.bash_profile
echo 'export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin' >> $HOME/.bash_profile && . $HOME/.bash_profile
go version
```

#### Method 2:
```bash
apt -y install snapd
snap install go --classic
go version
```

### Install essentials:
```bash
cd $HOME
sudo apt update
sudo apt install make build-essential git jq ncdu bsdmainutils nload -y < "/dev/null"
```
### Install `umeed`:
```bash
cd $HOME
rm -r $HOME/umee
git clone https://github.com/umee-network/umee.git
cd umee
git pull
git checkout tags/v0.5.0-rc2
make build
cp $HOME/umee/build/umeed /usr/local/bin
umeed version
```
### Install `peggo`:
```bash
cd $HOME
rm -r $HOME/peggo
git clone https://github.com/umee-network/peggo.git
cd peggo
git pull
git checkout tags/v0.1.0
make install
peggo version
```
### Configure your node:
```bash
UMEE_INTERNAL_MONIKER=$(hostname) # or what you prefer
rm $HOME/.umee/config/genesis.json
umeed init $UMEE_INTERNAL_MONIKER --chain-id umee-alpha-mainnet-2
wget -q -O $HOME/.umee/config/genesis.json https://raw.githubusercontent.com/umee-network/testnets/main/networks/umee-alpha-mainnet-2/genesis.json
peers="308fccc6b1eb545d26b1021a56d2468eaf352066@134.209.194.97:26656,542a99d76a3598c9739d54f93dc9efb6743c17f7@134.122.70.132:26656,0c81c3a9796d0edf2aefde0e31521475de81a57f@143.198.139.198:26656"
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.umee/config/config.toml
sed -i '/\[telemetry\]/{:a;n;/enabled/s/false/true/;Ta};/\[api\]/{:a;n;/enable/s/false/true/;Ta;}' $HOME/.umee/config/app.toml
umeed unsafe-reset-all
```
### Run `umeed` through `systemd`:
```bash
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
journalctl -u umeed -f
```
### Generate keys
If you dont have keys, you can generate them:
```bash
umeed keys add wallet
```

### Init variables:
```bash
CREATE_VALIDATOR_BIN=umeed
CREATE_VALIDATOR_AMOUNT=10000000
CREATE_VALIDATOR_DENOM=uumee
CREATE_VALIDATOR_CHAIN=umee-alpha-mainnet-2
CREATE_VALIDATOR_WALLET=wallet
```
### Create validator:
```bash
$CREATE_VALIDATOR_BIN tx staking create-validator \
  --amount="$CREATE_VALIDATOR_AMOUNT""$CREATE_VALIDATOR_DENOM" \
  --broadcast-mode=block \
  --pubkey=`$CREATE_VALIDATOR_BIN tendermint show-validator` \
  --moniker=$(hostname) \
  --commission-rate="0.05" \
  --commission-max-rate="0.10" \
  --commission-max-change-rate="0.01" \
  --min-self-delegation="1" \
  --from=$CREATE_VALIDATOR_WALLET \
  --chain-id=umee-alpha-mainnet-2 \
  --gas=auto \
  --gas-adjustment=1.4 -y
```

### Register ETH key:
#### Init variables:

**Be sure you have at least 0.1 ETH on your wallet in the Goerli Network**

**Faucet - https://faucet.goerli.mudit.blog/**

```bash
ETH_ADDRESS="YOUR_ETHEREUM_ADDRESS"
ETH_PK_CLEAN="YOUR_ETHEREUM_PRIVATE_KEY"
ETH_PK="0x"$ETH_PK_CLEAN
ETH_RPC="https://goerli-light.nodes.guru/" # you can use infura or your own node
ALCHEMY_API_KEY="YOUR_API_KEY" # you can get an API key for free at https://www.alchemy.com/

ORCHESTRATOR_VALIDATOR_ADDRESS=$(umeed keys show $CREATE_VALIDATOR_WALLET -a)
ORCHESTRATOR_ADDRESS=$(umeed keys show $CREATE_VALIDATOR_WALLET -a)
```
#### Send register tx:
```bash
umeed tx peggy set-orchestrator-address $ORCHESTRATOR_VALIDATOR_ADDRESS $ORCHESTRATOR_ADDRESS $ETH_ADDRESS --eth-priv-key $ETH_PK --chain-id=umee-alpha-mainnet-2 -y
```

You can found more info about `peggo` installation [here](https://github.com/umee-network/umee/wiki/Peggy-&-Peggo-Testing).

#### Run `peggo` as a service (**do not forget to replace YOUR_WALLET_PASSWORD wtih your passphrase**):
```bash
echo "[Unit]
Description=Peggo Service
After=network.target

[Service]
User=$USER
Type=simple
ExecStart=$(which peggo) orchestrator --log-level debug \
  --eth-pk=$ETH_PK_CLEAN \
  --eth-rpc=$ETH_RPC \
  --relay-batches=true \
  --relay-valsets=true \
  --cosmos-chain-id=\"umee-alpha-mainnet-2\" \
  --cosmos-keyring=\"os\" \
  --cosmos-keyring-dir=\"$HOME/.umee\" \
  --cosmos-from=\"$CREATE_VALIDATOR_WALLET\" \
  --cosmos-from-passphrase=\"YOUR_WALLET_PASSWORD\" \
  --coingecko-api=\"https://peggo-fakex-qhcqt.ondigitalocean.app/api/v3\" \
  --eth-alchemy-ws=\"wss://eth-goerli.alchemyapi.io/v2/$ALCHEMY_API_KEY\"
Restart=on-failure
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target" > $HOME/peggod.service
sudo mv $HOME/peggod.service /etc/systemd/system
sudo systemctl daemon-reload
sudo systemctl enable peggod
sudo systemctl restart peggod
journalctl -u peggod -f -o cat
```
Thats all what you need to be a validator

# Tools
## Peggo Checker
Check your `peggo` health:
```bash
wget -qO $HOME/peggo_height_check.go https://gist.githubusercontent.com/facundomedica/1c478dc3d5589fc4843278a7bedb8b5d/raw/7fb36487b9106bf6bd8ba985497894388ff44ca1/peggo_height_check.go
go run $HOME/peggo_height_check.go
```

NOTE: For it to work, API should be enabled in `app.toml` in section `[api]` with value `enable = true`. 

You already have the API enabled if you followed the instructions above.

## Explorer
The explorer is available here: https://gravity-wars.umee.nodes.guru/
