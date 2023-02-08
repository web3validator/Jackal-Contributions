# Jackal-Contributions


![image](https://user-images.githubusercontent.com/102728347/216285301-313defe4-12b0-4135-ae60-9d24618d6651.png)


* [Install node](https://github.com/web3validator/Jackal-Contributions/blob/main/README.md#state-sync)
* [State sync](https://github.com/web3validator/Jackal-Contributions/blob/main/README.md#state-sync)

<details open>
  <summary>Install node</summary>
  
  ## Install binary
  
  ```
  cd $HOME
  git clone https://github.com/JackalLabs/canine-chain.git -b v1.1.2
  cd $HOME/canine-chain
  make install
  
  ```
  ### Genesis
  ```
  wget http://jackal.web3validator.info:5000/genesis.json -P $HOME/.canine/config
  
  ```
  ### Addrbook
  ```
  wget http://jackal.web3validator.info:5000/addrbook.json -P $HOME/.canine/config
  
  ```
  ### You need to install `canined.service`
  ```
  wget http://jackal.web3validator.info:5000/canined.service -P /etc/systemd/system/
  
  ```
  If you want to quickly catch up with the network, use this [State Sync](https://github.com/MaxMavaIll/Guide_web/blob/main/README.md#state-sync)
  ```
  sudo systemctl start canined && sudo journalctl -u canined -f --no-hostname -o cat
  ```
</details>

<details open>
  <summary>State sync</summary>
  
  ## State sync
  ```
  SNAP_RPC="http://jackal.web3validator.info:26657"
  peers="b8ab84aecea3ada00110a68845bada5cbd54af12@85.10.192.146:26656"
  ```
  
  ### We get the height and txhash -> `config.toml`
  ```
  LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height); \
  BLOCK_HEIGHT=$((LATEST_HEIGHT - 2000)); \
  TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash) && \
  echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH
  sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ; \
  s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC\"| ; \
  s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ; \
  s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"| ; \
  s|^(seeds[[:space:]]+=[[:space:]]+).*$|\1\"\"|" $HOME/$Name_config_file/config/config.toml
  sed -i.bak -e  "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.canine/config/config.toml

  ```
  ### Reset the database 
  ```
  sudo systemctl stop canined && 
  canined tendermint unsafe-reset-all --home $HOME/.canine --keep-addr-book
  
  ```
  ## Restart node
  ```
  sudo systemctl start canined && sudo journalctl -u canined -f --no-hostname -o cat
  ```

  </details>
