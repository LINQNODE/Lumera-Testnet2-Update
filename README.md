# Lumera Testnet-2 Update


![0G Image](https://pbs.twimg.com/profile_banners/1103325065503039489/1750799339/1500x500)

⚠️ IMPORTANT WARNING

Only apply these steps AFTER 2025-07-02T16:00:00Z! If you are not a genesis validator, do not update before this time.

---

## Community Links

- [Discord](https://discord.gg/s8YXtVRM)
- [Lumera Twitter](https://x.com/lumeraprotocol)
- [Lumera Website](https://lumera.io)
- [Lumera Github](https://github.com/LumeraProtocol/)
- [Blockchain Explorer](https://explorer.linqnode.com/lumera-testnet)

---

Step 1: Download Necessary Files (NO time limit - you can download these in advance)


### ✅ Change to the home directory:
```
cd $HOME
```

### ✅ Download the v1.6.0 binary file:
```
wget https://github.com/LumeraProtocol/lumera/releases/download/v1.6.0/lumera_v1.6.0_linux_amd64.tar.gz
```

### ✅ Extract the file:
```
tar -xvf lumera_v1.6.0_linux_amd64.tar.gz
```

### ✅ Download the new genesis.json file:
```
wget https://raw.githubusercontent.com/LumeraProtocol/lumera-networks/refs/heads/master/testnet-2/genesis.json -O genesis_testnet2.json
```

### ✅ Download the new claims.csv file:
```
wget https://raw.githubusercontent.com/LumeraProtocol/lumera-networks/refs/heads/master/testnet-2/claims.csv -O claims_testnet2.csv
```

Step 2: Stop the Node ⏹️


```
sudo systemctl stop lumerad
```

Step 3: Clear Chain Data 🗑️


CAUTION: This process will delete your testnet-1 data!

```
lumerad tendermint unsafe-reset-all rm -rf ~/.lumera/wasm
```

Step 4: Update Binary and Network Files with Cosmovisor 🔄


### ✅ Create a directory for the new binary:
```
mkdir -p ~/.lumera/cosmovisor/genesis/bin
```

### ✅ Copy the new binary:
```
cp $HOME/lumerad ~/.lumera/cosmovisor/genesis/bin/lumerad chmod +x ~/.lumera/cosmovisor/genesis/bin/lumerad
```

### ✅ Remove the existing link and create a new one:
```
unlink ~/.lumera/cosmovisor/current ln -s ~/.lumera/cosmovisor/genesis ~/.lumera/cosmovisor/current
```

### ✅ # Replace the genesis file:
```
cp $HOME/genesis_testnet2.json ~/.lumera/config/genesis.json
```

### ✅ Copy the claims file:
```
cp $HOME/claims_testnet2.csv ~/.lumera/config/claims.csv
```

Step 5: Update Chain ID and Peers 🆔


### ✅ Set the new chain ID:
```
lumerad config set client chain-id lumera-testnet-2
```


### Update Seeds and Persistent Peers:
```
URL="https://lumera-rpc.coinsspor.com/net_info" response=$(curl -s $URL) PEERS=$(echo $response | jq -r '.result.peers[] | select(.remote_ip | test("^[0-9]{1,3}(\\.[0-9]{1,3}){3}$")) | "\(.node_info.id)@\(.remote_ip):" + (.node_info.listen_addr | capture(":(?<port>[0-9]+)$").port)' | paste -sd "," -) echo "PEERS=\"$PEERS\"" sed -i 's|^persistent_peers *=.*|persistent_peers = "'$PEERS'"|' $HOME/.lumera/config/config.toml
```

Thanks Coinsspor 


Step 6: Start the Node 🚀


### ✅ Step 6: Start the Node 🚀:
```
lsudo systemctl start lumerad
```

Step 7: Check the Logs 📊


### ✅ Check the node status:
```
sudo journalctl -fu lumerad -o cat
```

### ✅ Check the sync status:
```
curl -s localhost:${LUMERA_PORT}657/status | jq .result.sync_info
```

🔍 Verification Commands

### ✅ Check the node version:
```
lumerad version
```

### ✅ Check the sync status
```
local_height=$(curl -s localhost:${LUMERA_PORT}657/status | jq -r .result.sync_info.latest_block_height) echo "Node height: $local_height"
```

### ✅ Check the chain ID:
```
lumerad status | jq -r .NodeInfo.network
```

💡 Troubleshooting

### ✅ Check the service status:
```
sudo systemctl status lumerad
```
### ✅ For detailed logs:
```
sudo journalctl -u lumerad --since "1 hour ago" -f
```

### ✅ Check config files:
```
lumerad config show-node-id lumerad config show-validator
```

### ✅ ➡️ Create Validator:
```
lumerad tx staking create-validator $HOME/validator.json --chain-id=lumera-testnet-2 --gas-prices=0.025ulume --gas-adjustment=1.5 --gas=auto --from=$LUMERA_WALLET -y
```

### ✅ ➡️ Delegate to Yourself:
```
lumerad tx staking delegate $(lumerad keys show $LUMERA_WALLET --bech val -a) 1000000ulume --from $LUMERA_WALLET --chain-id lumera-testnet-2 --gas-prices=0.025ulume --gas-adjustment=1.5 --gas=auto -y 
```

## ⚠️ Important Reminders

**Timing**: Only perform these steps after 2025-07-02T16:00:00Z.

**Data Loss**: This process will completely erase your testnet-1 data.

**Wallet**: Make sure you have backed up your wallet seed phrase.

**Validator**: If you are a validator, check your validator status once the sync is complete.


## 🎯 Post-Update

After the update is complete:

Your node will connect to testnet-2.

It will start syncing from the new genesis block.

Your old validator keys will remain valid (as long as your wallet seed phrase is the same).


