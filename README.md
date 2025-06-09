# OG-Storage


<div align="left">

#   **Introduction📔**

</div>


**Storage Nodes:**
Unlike Validator Nodes that focus on securing the blockchain itself, Storage Nodes focus on managing and serving data. They are the backbone of the network's data storage capabilities, ensuring persistence and availability for long-term data storage (e.g., training datasets, large AI models). By running a storage node, you'll contribute to the decentralized storage of 0G data, making it accessible and resilient.

<div align="center">

#   **0G Storage Node Guide** 

</div>


# Device/System Requirements 💻

| Components |  Minimum   |  Recommended  |
|------|-------|-----------|
| RAM | 32 GB | 32 GB |
| CPU | 8 CORE | 8 CORE |
| DISK | 500GB/ 1 TB NVME SSD | Size matches the KV streams it maintains |
| BANDWIDTH | 100 MBPS (Download/Upload) |         -                  |


# Pre-Requirements 🛠

* Add 0G-Galileo-Testnet chain from here: https://docs.0g.ai/run-a-node/testnet-information

* Take faucet: https://faucet.0g.ai/


# Install All Require Dependecies

```
sudo apt-get update && sudo apt-get upgrade -y
```

```
sudo apt install curl iptables build-essential git wget lz4 jq make cmake gcc nano automake autoconf tmux htop nvme-cli libgbm1 pkg-config libssl-dev libleveldb-dev tar clang bsdmainutils ncdu unzip libleveldb-dev screen ufw -y
```

* Install rustup

```
curl https://sh.rustup.rs -sSf | sh
```

```
source $HOME/.cargo/env
```

Check version

```
rustc --version
```


* Install go

```
wget https://go.dev/dl/go1.24.3.linux-amd64.tar.gz && \
sudo rm -rf /usr/local/go && \
sudo tar -C /usr/local -xzf go1.24.3.linux-amd64.tar.gz && \
rm go1.24.3.linux-amd64.tar.gz && \
echo 'export PATH=$PATH:/usr/local/go/bin' >> ~/.bashrc && \
source ~/.bashrc
```

check version

```
go version
```


# Clone the Repository

```
git clone https://github.com/0glabs/0g-storage-node.git
```

```
cd 0g-storage-node && git checkout v1.0.0 && git submodule update --init
```

* Build in release mode 

```
cargo build --release
```

# Set Configrations

```
rm -rf $HOME/0g-storage-node/run/config.toml
```

```
curl -o $HOME/0g-storage-node/run/config.toml https://raw.githubusercontent.com/Mayankgg01/0G-Storage-Node-Guide/main/config.toml
```


* Add Your Wallet's Private KEY in `config.toml`, ❗❗Dont Add **0X** before the key:

-open and go to `miner_key` and add your pvt key:

```
nano $HOME/0g-storage-node/run/config.toml
```

![image](https://github.com/user-attachments/assets/a513812f-177e-4a74-83a9-1548c98f4556)


# If u want to change RPC then follow this:

1. get rpc from here - https://www.astrostake.xyz/0g-status

2. Chooose any rpc and edit in the `config.toml` file

![image](https://github.com/user-attachments/assets/44b682a5-45ce-4fc8-8c3a-7f2355f3b9ac)


# Create a Systemd Service File

```
sudo tee /etc/systemd/system/zgs.service > /dev/null <<EOF
[Unit]
Description=ZGS Node
After=network.target

[Service]
User=$USER
WorkingDirectory=$HOME/0g-storage-node/run
ExecStart=$HOME/0g-storage-node/target/release/zgs_node --config $HOME/0g-storage-node/run/config.toml
Restart=on-failure
RestartSec=10
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
```

* Reload

```
sudo systemctl daemon-reload
```

* Enable

```
sudo systemctl enable zgs
```

* Start service

```
sudo systemctl start zgs
```


# Managing Logs

```
sudo systemctl status zgs
```

![Screenshot 2025-05-27 190436](https://github.com/user-attachments/assets/3b01ab3f-8d43-43b3-9bf1-b2a8e870e1fe)


* check Full Logs

```
tail -f ~/0g-storage-node/run/log/zgs.log.$(TZ=UTC date +%Y-%m-%d)
```

* Check block & Sync process - Match to the latest block on explorer

```
 while true; do     response=$(curl -s -X POST http://localhost:5678 -H "Content-Type: application/json" -d '{"jsonrpc":"2.0","method":"zgs_getStatus","params":[],"id":1}');     logSyncHeight=$(echo $response | jq '.result.logSyncHeight');     connectedPeers=$(echo $response | jq '.result.connectedPeers');     echo -e "logSyncHeight: \033[32m$logSyncHeight\033[0m, connectedPeers: \033[34m$connectedPeers\033[0m";     sleep 5; done
```

![Screenshot 2025-05-28 155703](https://github.com/user-attachments/assets/ab97078b-2c2a-4328-aace-bc94982ab802)


# Stop & Delete the service

```
sudo systemctl stop zgs
```

```
sudo systemctl disable zgs
sudo rm /etc/systemd/system/zgs.service
rm -rf $HOME/0g-storage-node
```



# Explorer & Useful Webs

* Explorer- (View your txs - Paste Your Address): https://chainscan-galileo.bangcode.id/ OR https://chainscan-galileo.0g.ai/

* View Miner Details- (Add your wallet address at the end of the link): https://storagescan-galileo.0g.ai/miner/
