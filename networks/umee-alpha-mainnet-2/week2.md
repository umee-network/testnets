# Week 2 of Umee Gravity Wars Testnet
## Tasks

### :warning: **Make sure you use your validator wallet to vote.**

* **28.12 12:00 UTC - 29.12 12:00 UTC**
  * Participate in the network upgrade governance — cast a vote on an upgrade proposal.
* **29.12 17:00 UTC**
  * Install the specific Network Upgrade at the time specified in the proposal

## Criteria

* Scores will be issued based on the number of blocks missed after/during the update, and on the fact of casting vote in the governance.
* Miss the least blocks possible to score the most points.
* Sign first 100 blocks after upgrade to score extra points.


## How to vote?
Run the following command:
```bash
umeed tx gov vote 1 yes --from wallet --chain-id umee-alpha-mainnet-2
```

## How to upgrade?
1. Stop your `umeed` service:
```bash
systemctl stop umeed
```
2. Run the following commands:
```bash
cd $HOME/umee
git pull
git checkout tags/v0.5.0-rc3
make build
cp $HOME/umee/build/umeed /usr/local/bin
umeed version
# v0.5.0-rc3
```
3. Start your `umeed` service:
```bash
systemctl start umeed
```
