# Linux

## Download nethermind 
```
cd ~/
sudo apt-get update && sudo apt-get install libsnappy-dev libc6-dev libc6 unzip
wget https://github.com/NethermindEth/nethermind/releases/download/1.14.0/nethermind-linux-amd64-1.14.0-60f920b-20220822.zip
unzip nethermind-linux-amd64-1.14.0-60f920b-20220822.zip -d nethermind
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
    "Host": "127.0.0.1",
    "Port": 8545,
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

## Create systemd service

```
sudo nano /etc/systemd/system/nethermind.service
```
paste and edit \<user> and \<datadir> (e.g. /home/\<user>/data):
```
[Unit]
Description=Nethermind Node
Documentation=https://docs.nethermind.io
After=network.target

[Service]
User=<user>
WorkingDirectory=/home/<user>/
ExecStart=/home/<user>/nethermind/Nethermind.Runner --config gnosis_archive_rpc --datadir <datadir>
Restart=on-failure
LimitNOFILE=1000000

[Install]
WantedBy=default.target
```

## Start service
```
sudo systemctl daemon-reload
sudo systemctl enable nethermind
sudo systemctl start nethermind.service
```

 ## Check logs
```
journalctl -u nethermind -f
```
