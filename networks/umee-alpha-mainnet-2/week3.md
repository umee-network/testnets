# Week 3 of Umee Gravity Wars Testnet
### 03.01 12:00 UTC — 10.01 12:00 UTC

## Tasks
### ⚠️ Make sure you use your second set of wallets (same wallet used in Week 1) to send the required amount of transactions.
#### Run the Most Profitable Bridge Relayer: Test our bridge implementation in a “real world setup” (Umee and ETH have an arbitrary value)
* Bridge relayers can set customized economic factors if they choose to and monitor bridge transaction requests.
* Relayers need to make the most fees from relaying transactions across blockchains while covering the network fees from the bridged blockchains.
* Umee will set up an exchange rate table for uUMEE and USDT and change the rate randomly throughout the day/week.
* Validators can choose to set a customized profit multiplier for what they would consider profitable (**batchFees >= txGasCost*profitMultiplier**).
* Each validator will need to send a minimum number of successful bridge transactions during the set time.
  * **Minimum per day: 200 successful transactions Umee-ETH and 200 ETH-Umee**
  * **Maximum per day: 1,000 successful transactions Umee-ETH and 1,000 ETH-Umee**
* P&Ls of the validators are calculated and the ones with the highest value get the highest points.
* Please note:
  * We will start counting a validator’s profit and loss from block height **6135700**
  * We will only be counting your profit and loss on your **orchestrator address (relayer transactions)**; any other transaction fees occurred outside of your orchestrator address will **NOT** be counted
  * Make sure you use your **second set of wallets (same wallet used in Week 1)** to send the required amount of transactions

## How to set the multiplier?
### Set the multiplier
1. Open your `peggod` service file:
```bash
sudo nano /etc/systemd/system/peggod.service
```
2. Add `--profit-multiplier 1` (or any other value) flag to your `ExecStart=` command
3. Do `daemon-reload`:
```bash
systemctl daemon-reload
```
4. Restart the `peggod` service
```bash
systemctl restart peggod
```

### Edit the multiplier
```bash
PEGGO_PROFIT_MULTIPLIER=1 # or any other value
sudo sed -i.bak -e "s/--profit-multiplier../--profit-multiplier=$PEGGO_PROFIT_MULTIPLIER/" /etc/systemd/system/peggod.service
```

## Sending transactions
### From Cosmos to ETH
Syntax:
```bash
umeed tx peggy send-to-eth [eth-dest-addr] [amount] [bridge-fee] [flags]
```
Example:
```bash
umeed tx peggy send-to-eth 0x05a64fE82628217900ced80Bf3747b5ef88BFa21 10000000uumee 1uumee --from validator --chain-id umee-alpha-mainnet-2
```
### From ETH to Cosmos
#### NB: `0xe54fbaecc50731afe54924c40dfd1274f718fe02` is ERC20 Umee token address
Syntax:
```bash
peggo bridge send-to-cosmos [token-address] [recipient] [amount] [flags]
```
Example:
```bash
peggo bridge send-to-cosmos 0xe54fbaecc50731afe54924c40dfd1274f718fe02 umee1qpuaz7q3qw0lnu9jng8xa5md6p3pg9zey5z875 1000000 --eth-pk=$ETH_PK_CLEAN --eth-rpc=$ETH_RPC
```
`$ETH_PK_CLEAN` - your private key from ETH wallet without `0x`

`$ETH_RPC` - your Goerli ETH endpoint (you can use `https://goerli-light.nodes.guru`)
