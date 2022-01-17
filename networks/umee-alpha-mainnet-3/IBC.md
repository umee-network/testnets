# Umee Gravity Wars Week5 IBC Task 3 guide

## IBC
IBC is an interoperability protocol for communicating arbitrary data between arbitrary state machines.
### How to use
1. Install the IBC relayer
```bash
cd $HOME
git clone https://github.com/strangelove-ventures/relayer.git
cd relayer
git checkout v1.0.0-rc2
make install
rly version
```
2. Set config path
```bash
RLY_CONFIG_PATH="$HOME/relayer/interchain/chains"
```
3. Initialize the relayer's configuration
```bash
rly config init
```
4. Add Juno (uni-1) and Umee (umee-alpha-mainnet-3) chain configurations to the relayer's configuration

**You can use your own RPC for both networks, in this example we use Nodes Guru RPC for Umee and Juno networks**
  - Umee:
  ```bash
  echo '{
    "key": "test-key",
    "chain-id": "umee-alpha-mainnet-3",
    "rpc-addr": "https://gravity-wars.rpc.umee.nodes.guru:443",
    "account-prefix": "umee",
    "gas-adjustment": 1.3,
    "gas-prices": "0.025uumee",
    "trusting-period": "336h"
  }' > $RLY_CONFIG_PATH/umee-alpha-mainnet-3.json
  ```
  - Juno:
  ```bash
  echo '{
    "key": "test-key",
    "chain-id": "uni-1",
    "rpc-addr": "https://uni.rpc.juno.nodes.guru:443",
    "account-prefix": "juno",
    "gas-adjustment": 1.3,
    "gas-prices": "0.025ujunox",
    "trusting-period": "336h"
  }' > $RLY_CONFIG_PATH/uni-1.json
  ```
  ```bash
  rly chains add -f $RLY_CONFIG_PATH/umee-alpha-mainnet-3.json
  rly chains add -f $RLY_CONFIG_PATH/uni-1.json
  ```
5. Either import or create new keys for the relayer to use when signing and relaying transactions
```bash
rly keys add umee-alpha-mainnet-3 test-key
rly keys add uni-1 test-key
```
**or**
```bash
rly keys restore umee-alpha-mainnet-3 test-key "YOUR_MNEMONIC"
rly keys restore uni-1 test-key "YOUR_MNEMONIC"
```
6. Both relayer accounts need to funded with tokens in order to successfully sign and relay transactions between the IBC-connected networks
  - Juno faucet - [link](https://discord.gg/6krUB8QtCC)
7. Ensure both relayer accounts are funded by querying each
```bash
rly q balance umee-alpha-mainnet-3
rly q balance uni-1
```
8. Next, we generate a new path representing a client, connection, channel and a specific port between the two networks
```bash
rly paths generate uni-1 umee-alpha-mainnet-3 transfer --port=transfer
```
9. **(Optional)** Open a channel for relaying
```bash
rly tx link transfer --debug
```
10. Check paths is ok
```bash
rly paths list -d
```
Output should be:
> 0: transfer             -> chns(✔) clnts(✔) conn(✔) chan(✔) (uni-1:transfer<>umee-alpha-mainnet-3:transfer)
11. Create IBC transactions
```bash
rly tx transfer umee-alpha-mainnet-3 uni-1 1500uumee $(rly keys show uni-1 test-key) --path transfer
rly tx transfer uni-1 umee-alpha-mainnet-3 1000ujunox $(rly keys show umee-alpha-mainnet-3 test-key) --path transfer
```
12. Start the relayer
```bash
rly start transfer
```
Output should be like:

**⚠️ Save these hashes, you will need them to fill out the form**

> ✔ [uni-1]@{62165} - msg(0:/ibc.core.client.v1.MsgUpdateClient,1:/ibc.core.channel.v1.MsgRecvPacket) hash(**D413E1DEC6646947C73609685B2672ED3731D2BFC34388D21E22ECEC4CD34EB6**) 

> ✔ [umee-alpha-mainnet-3]@{386173} - msg(0:/ibc.core.client.v1.MsgUpdateClient,1:/ibc.core.channel.v1.MsgRecvPacket) hash(**B881212DCD5736E3CC14E422BBC6C5CA9066E81A002FE447EADD600E11D2BD26**)

> ★ Relayed 1 packets: [uni-1]port{transfer}->[umee-alpha-mainnet-3]port{transfer}

> ★ Relayed 1 packets: [umee-alpha-mainnet-3]port{transfer}->[uni-1]port{transfer}
13. Verify your balance
```bash
rly q balance umee-alpha-mainnet-3
rly q balance uni-1
```
Output should be like:
 - Umee:
 > 1000transfer/channel-3/ujunox,889929uumee
 - Juno:
 > 1500transfer/channel-2/uumee,123706260ujunox

