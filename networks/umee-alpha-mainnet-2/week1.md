# Week 1 of Umee Gravity Wars Testnet

## Tasks
### Important! For every task with transactions you must use second set of wallets (both umee and ETH) you submitted in the Form1.
* **Run Bridge Relayer: try our bridge implementation (zero profit multiplier)**
* **20.12 12:00 UTC - 21.12 12:00 UTC**
  * Monitor the network, make sure your validator and orchestrator is signing everything correctly.
* **21.12 12:00 UTC - 22.12 12:00 UTC**
  * Send Umee tokens through the bridge with a zero profit multiplier. Send 50 successful transactions from cosmos to Ethereum and 50 from Ethereum to cosmos. Submit the 100 tx HASHs to the Google form.
* **22.12 12:00 UTC - 23.12 12:00 UTC**
  * Send Umee tokens through the bridge with a zero profit multiplier. Send 100 successful transactions from cosmos to Ethereum and 100 transactions from Ethereum to cosmos. 
* **23.12 12:00 UTC - 23.12 23:59 UTC**
  * Send Umee tokens through the bridge with a zero profit multiplier. Send 200 successful transactions from cosmos to Ethereum and 200 from Ethereum to cosmos.

## How to set the multiplier?
1. Open your `peggod` service file:
```bash
nano /etc/systemd/system/peggod.service
```
2. Add `--profit-multiplier 0` flag to your `ExecStart=` command
3. Do `daemon-reload`:
```bash
systemctl daemon-reload
```
4. Restart the `peggod` service
```bash
systemctl restart peggod
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
