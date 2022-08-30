# The-Graph-MIP
Helpful stuff for the Graph’s multi-chain incentivized program

## Gnosis Archive Node
Disk size: x.xTB (3TB recommended TBC)

Sync time: >7d (TBC)

Ports:

- 8545 (TCP) JSON RPC (you don't want this to be accessible by everyone)
```
UFW example to allow a specific IP address to access your RPC port:
sudo ufw allow from xxx.xxx.xxx.xxx to any port 8545
```
- 30303 (TCP/UDP) ETH P2P 

If you have an eth node on the same machine, make sure you don't use the ports twice.
