# Umee Testnet canon-1

* This testnet will start initially as a 1.x.x testnet and will later be upgraded to 3.x.x once a release candidate is available.
* All listed validators are running badgerdb


| Attribute | Value |
|----------:|-------|
| Chain ID         | `canon-1` |
| Gravity Address  | [0xa22344153e826580e679712648e487Dc6032fB4d](https://goerli.etherscan.io/address/0xa22344153e826580e679712648e487Dc6032fB4d) |

## Software Component Versions

| Component | Version |
|----------:|---------|
| Umee | `1.1.2` |
| Peggo | `0.4.0` |
| Price Feeder | `0.3.0` |

## Upgrades

### v1.1.2

With [canon-1 prop 1](https://explorer.network.umee.cc/canon-1/gov/1), canon-1 was upgraded to [v1.1.2](https://github.com/umee-network/umee/releases/tag/v1.1.2) to be up-to-date with the current version running on mainnet. Mainnet was upgraded as a part of [umee-1 prop 20](https://www.mintscan.io/umee/proposals/20).

## Tools

* [Umee Explorer](https://explorer.network.umee.cc)

## Validator Nodes


### Validator "blossom"

| Attribute | Value |
|----------:|-------|
| ID                       | `dc1b1b89a83873f20b613cdb1361f932afb84a97` |
| Orchestrator Eth Address | `0xfacf66789DD2fA6d80A36353f900922cb6D990F1` |

##### Endpoints

| Kind | Value |
|-----:|-------|
| Peer Address | `dc1b1b89a83873f20b613cdb1361f932afb84a97@35.215.72.45:26656` |
| RPC          | https://rpc.blossom.canon-1.network.umee.cc |
| API          | https://api.blossom.canon-1.network.umee.cc |
| GRPC         | `grpc.blossom.canon-1.network.umee.cc:443` |

### Validator "bubbles"

| Attribute | Value |
|----------:|-------|
| ID                       | `5e01b69ead6e0781af0361d3ec4e436d96dba932` |
| Orchestrator Eth Address | `0xfac87ECc6009Be4a856CB30846F82ea0B94d8C01` |

##### Endpoints

| Kind | Value |
|-----:|-------|
| Peer Address | `5e01b69ead6e0781af0361d3ec4e436d96dba932@35.215.98.106:26656` |
| RPC          | https://rpc.bubbles.canon-1.network.umee.cc |
| API          | https://api.bubbles.canon-1.network.umee.cc |
| GRPC         | `grpc.bubbles.canon-1.network.umee.cc:443` |

### Validator "buttercup"

| Attribute | Value |
|----------:|-------|
| ID                       | `94ac8328b4b9f45b6f7b8e9569ae0253dc53c7eb` |
| Orchestrator Eth Address | `0xfacEFb63F7CDFf0ca7a7A064639b3956Ad9f3acC` |

##### Endpoints

| Kind | Value |
|-----:|-------|
| Peer Address | `94ac8328b4b9f45b6f7b8e9569ae0253dc53c7eb@35.212.143.125:26656` |
| RPC          | https://rpc.buttercup.canon-1.network.umee.cc |
| API          | https://api.buttercup.canon-1.network.umee.cc |
| GRPC         | `grpc.buttercup.canon-1.network.umee.cc:443` |

#### Swap binaries

* We had an consensus failure using [v3.0.0-rc1](https://github.com/umee-network/umee/releases/tag/v3.0.0-rc1) so we made another release with improvements [v3.0.0-rc2](https://github.com/umee-network/umee/releases/tag/v3.0.0-rc2)
* The last block created before swapping the binary was [416813](https://explorer.network.umee.cc/canon-1/blocks/416813)
  * Exported genesis from block [416813](/networks/canon-1/canon-1-export-block-416813.json)
* We wait one hour to restart the chain to give time to others validators to swap their binary also
