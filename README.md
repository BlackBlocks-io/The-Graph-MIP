# The-Graph-MIP
Helpful stuff for the Graph’s multi-chain incentivized program

## Links

### Explorer
Official:   https://testnet.thegraph.com/

Graphscan:  https://goerli.graphscan.io/#indexers

### Docs
https://thegraph.com/docs/en/

### Github repos

Indexer(Agent,Service,CLI): https://github.com/graphprotocol/indexer

Graph-Node:                 https://github.com/graphprotocol/indexer

### Graph infra stack setup guides

Hypervisor/Bare metal:      https://docs.thegraph.academy/official-docs/indexer/testnet/graph-protocol-testnet-baremetal/1_architectureconsiderations
Docker:                     https://github.com/StakeSquid/graphprotocol-testnet-docker

## Gnosis Archive Node

[Install as systemd service](Gnosis-Node/Install%20as%20systemd%20service.md)

CPUs: 8 cores

RAM: 16GB

Disk size: 1.85TB @ Block 24051411 05.09.22 (3TB allocation recommended)

Sync time: ~7d

Comments: Used ZFS RaidZ2 with compression=on. Compression ratio = 1.0. So compression won't give you any advantage here. Others were able to to sync within 2 days using ext4 raid0 with high IOPS drives.

Ports:

- 8545 (TCP) JSON RPC (you don't want this to be accessible by everyone)
```
UFW example to allow a specific IP address to access your RPC port:
sudo ufw allow from xxx.xxx.xxx.xxx to any port 8545
```
- 30303 (TCP/UDP) ETH P2P 

If you have an eth node on the same machine, make sure you don't use the ports twice.
