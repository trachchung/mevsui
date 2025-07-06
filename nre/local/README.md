# Run a Sui Node using Systemd

Tested using:
- Ubuntu 20.04 (linux/amd64) on bare metal
- Ubuntu 22.04 (linux/amd64) on bare metal

## Prerequisites and Setup

1. The `/opt/sui` directories

```shell
sudo mkdir -p /opt/sui/bin
sudo mkdir -p /opt/sui/config
sudo mkdir -p /opt/sui/db
sudo mkdir -p /opt/sui/key-pairs
```

2. Install the Sui Node (sui-node) binary, two options:
    
- Pre-built binary stored in Amazon S3:
        
```shell
wget https://releases.sui.io/$SUI_SHA/sui-node
chmod +x sui-node
sudo mv sui-node /opt/sui/bin
```

- Build from source:

```shell
# Optional
git clone https://github.com/MystenLabs/sui.git && cd sui
# Optional
git checkout $SUI_SHA

cargo build --release --bin sui-node
mv ./target/release/sui-node /opt/sui/bin/sui-node
```

3. 
Copy your key-pairs into `/opt/sui/key-pairs/` 

If generated during the Genesis ceremony these will be at `SuiExternal.git/sui-testnet-wave3/genesis/key-pairs/`

4. Update the node configuration file and place it in the `/opt/sui/config/` directory.

cp ./nre/config/validator.yaml /opt/sui/config/validator.yaml

```
protocol-key-pair: 
  path: /opt/sui/key-pairs/protocol.key
worker-key-pair: 
  path: /opt/sui/key-pairs/worker.key
network-key-pair: 
  path: /opt/sui/key-pairs/network.key
```



```shell
# Guide: https://moluuser.com/posts/sui-full-node/

# Install all sui CLIs 
# Go to https://github.com/MystenLabs/sui/releases 
# Download latest tar file 
tar -xvf sui-testnet-v1.51.2-ubuntu-x86_64.tgz -C ./path-to-folder
# Add it to ~/.bashrc if you want

# Downlaod snapshot free
./sui-cli/sui-tool download-formal-snapshot --latest --genesis "/opt/sui/config/genesis.blob" \
     --network mainnet \
     --path /opt/sui/db --num-parallel-downloads 50 --no-sign-request

# Make the sui-node binary executable
chmod +x /opt/sui/bin/sui-node

# Download genesis.blob and place it in `/opt/sui/config/`
https://docs.sui.io/guides/operator/sui-full-node#setting-up-a-full-node

# Download snapshot and place it in `/opt/sui/db/`
./sui-cli/sui-tool download-formal-snapshot --latest --genesis "/opt/sui/config/genesis.blob" \
     --network mainnet \
     --path /opt/sui/db --num-parallel-downloads 50 --no-sign-request

# Copy `pool_related_ids.txt` in sui-mev repo to /opt/sui/pool_related_ids.txt

# Run the node
RUST_BACKTRACE=1
RUST_LOG=info,sui_core=debug,consensus=debug,jsonrpsee=error
/opt/sui/bin/sui-node --config-path /opt/sui/config/validator.yaml
```
