# Week 4 of Umee Gravity Wars Testnet
### 10.01 15:00 UTC — 14.01 15:00 UTC

## Tasks
### Task 1
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
  * We will start counting a validator’s profit and loss from block height **6176898**
  * We will only be counting your profit and loss on your **orchestrator address (relayer transactions)**; any other transaction fees occurred outside of your orchestrator address will **NOT** be counted
  * Make sure you use your **second set of wallets (same wallet used in Week 1)** to send the required amount of transactions

### Task 2
 * Create **3rd** umee wallet and use it to pay for your transactions fees using **feegrant** module. Best practice is to use the **2nd umee wallet** and send Umee tokens from your 2nd wallet to your **validator balance**
 * You will be asked to submit **5** umee chain tx HASHs which has fees paid by granter
 
## Feegrant
This module allows accounts to grant fee allowances and to use fees from their accounts. Grantees can execute any transaction without the need to maintain sufficient fees.
### How to use
In this case, the granter and grantee wallets will be used:
- `umee1g82z6v9lynw5fepx7rczpufls6veen4pp3hjla` - granter
- `umee1qpuaz7q3qw0lnu9jng8xa5md6p3pg9zey5z875` - grantee
1. Create a new wallet
```bash
umeed keys add granter
```
2. Set the grantee
- Syntax:
```bash
umeed tx feegrant grant [granter_key_or_address] [grantee] [flags]
```
- Example:
```bash
umeed tx feegrant grant umee1g82z6v9lynw5fepx7rczpufls6veen4pp3hjla umee1qpuaz7q3qw0lnu9jng8xa5md6p3pg9zey5z875 --spend-limit 10uumee --chain-id umee-alpha-mainnet-2
```
3. Send transaction
```bash
umeed tx bank send umee1qpuaz7q3qw0lnu9jng8xa5md6p3pg9zey5z875 umee1us0ue2707md96700fa3q0n7rcmvxtl3ndyypje 3uumee --fees=7uumee --chain-id umee-alpha-mainnet-2 --fee-account umee1g82z6v9lynw5fepx7rczpufls6veen4pp3hjla
```
Example tx hash with the `granter` field: `5DB03B1F279AC662046046FA4F92B95414138DAE41C1191D7092E703C438EFD6`

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
