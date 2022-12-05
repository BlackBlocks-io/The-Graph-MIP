# Linux

## Download nethermind 
```
cd ~/
sudo apt-get update && sudo apt-get install libsnappy-dev libc6-dev libc6 unzip

#wget https://github.com/NethermindEth/nethermind/releases/download/1.14.7/nethermind-linux-amd64-1.14.7-4fe81c6-20221130.zip
#unzip nethermind-linux-amd64-1.14.7-4fe81c6-20221130.zip -d nethermind
```

# Download special trace nethermind build
https://github.com/NethermindEth/nethermind/actions/runs/3591104581
```
unzip nethermind-linux-package.zip -d nethermind
```

## Increase the maximum number of open files
```
sudo bash -c 'echo "nethermind hard nofile 1000000" >> /etc/security/limits.d/nethermind.conf'
sudo bash -c 'echo "nethermind hard nofile 1000000" >> /etc/security/limits.d/nethermind.conf'

```

## Setup nethermind config
```
nano ~/nethermind/configs/gnosis_archive_rpc.cfg
```
paste:
```
{
  "Init": {
    "ChainSpecPath": "chainspec/xdai.json",
    "GenesisHash": "0x4f1dd23188aab3a76b463e4af801b52b1248ef073c648cbdc4c9333d3da79756",
    "BaseDbPath": "nethermind_db/xdai_archive",
    "LogFileName": "xdai_archive.logs.txt",
    "MemoryHint": 1024000000
  },
  "Mining": {
    "MinGasPrice": "1000000000"
  },
  "EthStats": {
    "Name": "Nethermind xDai"
  },
  "Metrics": {
    "NodeName": "xDai Archive"
  },
   "Merge": {
   "SecondsPerSlot": 5
  },
  "Bloom":
  {
    "IndexLevelBucketSizes" : [16, 16, 16]
  },
  "Pruning": {
    "Mode": "None"
  },
  "JsonRpc": {
    "Enabled": true,
    "Timeout": 20000,
    "Host": "0.0.0.0",
    "Port": 8545,
    "EnginePort": 8551,
    "EngineHost": "127.0.0.1",
    "EnabledModules": [
      "Eth",
      "AccountAbstraction",
      "Subscribe",
      "TxPool",
      "Web3",
      "Personal",
      "Proof",
      "Net",
      "Parity",
      "Health",
      "Trace"
    ]
  }
}


```

## Create a JwtSecretFile
```
openssl rand -hex 32 | tr -d "\n" > "~/nethermind/jwtsecret/jwtsecret.hex"
```

## Create a folder for your nethermind data
```
mkdir ~/nethermind_data
```


## Create systemd service for nethermind
```
sudo nano /etc/systemd/system/nethermind.service
```
paste and edit \<user> :
```
[Unit]
Description=Nethermind Node
Documentation=https://docs.nethermind.io
After=network.target

[Service]
User=<user>
WorkingDirectory=/home/<user>/
ExecStart=/home/<user>/nethermind/Nethermind.Runner --config gnosis_archive_rpc --datadir ~/nethermind_data --TraceStore.Enabled true --TraceStore.BlocksToKeep 0 --TraceStore.TraceTypes Trace,Rewards --Sync.FastSync=false --JsonRpc.JwtSecretFile ~/nethermind/jwtsecret/jwtsecret.hex
Restart=on-failure
LimitNOFILE=1000000

[Install]
WantedBy=default.target
```
## Install lighthouse
```
cd ~/
wget https://github.com/sigp/lighthouse/releases/download/v3.3.0/lighthouse-v3.3.0-x86_64-unknown-linux-gnu.tar.gz
tar -xzf lighthouse-v3.3.0-x86_64-unknown-linux-gnu.tar.gz
chmod +x ~/lighthouse
mkdir lighthouse_data
```

## Create a systemd service for lighthouse
```
sudo nano /etc/systemd/system/lighthouse.service
```
paste and edit \<user> :
```
[Unit]
Description=Lighthouse Gnosis Node
After=network.target

[Service]
User=<user>
WorkingDirectory=/home/<user>/
ExecStart=/home/<user>/lighthouse beacon_node --network gnosis --datadir ~/lighthouse_data --http --execution-endpoint http://localhost:8551 --checkpoint-sync-url https://checkpoint.gnosischain.com --execution-jwt ~/nethermind/jwtsecret/jwtsecret.hex
Restart=on-failure
LimitNOFILE=1000000

[Install]
WantedBy=default.target

```

## Start services

### If you are coming from a non TraceDB Nethermind setup you must nuke your db
```
rm -r ~/nethermind_data/*
```
```
sudo systemctl daemon-reload
sudo systemctl enable nethermind
sudo systemctl start nethermind.service
sudo systemctl enable lighthouse
sudo systemctl start lighthouse.service
```

 ## Check logs
```
journalctl -u nethermind -f
journalctl -u lighthouse -f
```

