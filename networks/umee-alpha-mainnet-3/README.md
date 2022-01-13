# Umee Gravity Wars Testnet Week 5(`umee-alpha-mainnet-3`)

# War Zone!

## Hardware Requirements
* **Minimum**
  * 4 GB RAM
  * 200 GB SSD
  * 2 vCPU
* **Recommended**
  * 16 GB RAM
  * 600 GB SSD
  * 6 vCPU

## 1. Umeed Installation Steps

If you already have umeed installed, you may proceed to Step 2.

### 1.1 Install `go`
#### Method 1 (recomended):
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
echo 'export GOROOT=/usr/local/go' >> $HOME/.bash_profile
echo 'export GOPATH=$HOME/go' >> $HOME/.bash_profile
echo 'export GO111MODULE=on' >> $HOME/.bash_profile
echo 'export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin' >> $HOME/.bash_profile && . $HOME/.bash_profile
go version
```

### 1.2 Install essentials:
```bash
cd $HOME
sudo apt update
sudo apt install make build-essential git jq ncdu bsdmainutils nload -y < "/dev/null"
```
### 1.3 Install `umeed`:
```bash
cd $HOME
rm -r $HOME/umee
git clone https://github.com/umee-network/umee.git
cd umee
git pull
git checkout tags/v0.5.0-rc3
make build
cp $HOME/umee/build/umeed /usr/local/bin
umeed version
```
### 1.4 Install `peggo`:
```bash
cd $HOME
rm -r $HOME/peggo
git clone https://github.com/umee-network/peggo.git
cd peggo
git pull
git checkout tags/v0.1.1
make install
peggo version
```
## 2. Gravity Wars Week 5 genesis
### 2.1 Create keys.
#### ⚠️ You can use your old keys from weeks 1-4 if you want to
```bash
umeed keys add wallet
```
### 2.2 Create genesis.json
```bash
UMEE_INTERNAL_MONIKER=$(hostname) # or what you prefer
umeed init --overwrite $UMEE_INTERNAL_MONIKER --chain-id umee-alpha-mainnet-3
```
### 2.3 Create genesis account
#### ⚠️ Do not change the initial balance, it will cost you spot in week 5 competition.
```bash
umeed add-genesis-account wallet 11000000uumee
```
### 2.4 Create gentx
#### ⚠️ Do not modify the gentx directly, it may get corrupted and will be excluded from the genesis costing you spot in week 5.
If you need to change something - create new gentx, but do not forget to remove the old one from the repo
#### ⚠️ Do not change the stake amount in genesis, it will cost you spot in week 5 competition.
```bash
umeed gentx wallet 10000000uumee --chain-id=umee-alpha-mainnet-3 --moniker="$UMEE_INTERNAL_MONIKER"
```
### 2.5 Submit gentx
- Fork [the testnets repo](https://github.com/umee-network/testnets) to your Github account

- Clone your repo using

  ```bash
  cd $HOME
  git clone https://github.com/<your-github-username>/testnets umee-testnets
  ```

- Copy the generated gentx json file to `$HOME/umee-testnets/networks/umee-alpha-mainnet-3/gentxs`

  ```bash
  cp $HOME/.umee/config/gentx/gentx-*.json $HOME/umee-testnets/networks/umee-alpha-mainnet-3/gentxs
  ```

- Commit and push to your repo
  ```bash
  cd $HOME/umee-testnets/networks/umee-alpha-mainnet-3/gentxs
  git add -A
  git commit -m "$UMEE_INTERNAL_MONIKER gentx"
  git push origin main
  ```
- Create a PR onto https://github.com/umee-network/testnets

## 3. Onboarding
### 3.1 Follow discord announcements and submit Google form, including
- umee validator operator address
- validator and orchestrator umee address
- orchestrator ETH address (DO NOT reuse old orchestrator ETH address!! create new one)
- second set of wallets for transactions: umee wallet and ETH wallet
### 3.2 Network is planned to start on 17th of January 12:01 UTC
### 3.3 Genesis.json should be available for download on Saturday
### 3.4 Upon the start participants will get delegations
## 4. Tasks will be announced on the discord, follow announcements!
