# crossfi
##	Install go, if needed

<code>cd $HOME</code>

<code>VER="1.21.3"</code>

<code>wget "https://golang.org/dl/go$VER.linux-amd64.tar.gz"</code>

<code>sudo rm -rf /usr/local/go</code>

<code>sudo tar -C /usr/local -xzf "go$VER.linux-amd64.tar.gz"</code>

<code>rm "go$VER.linux-amd64.tar.gz"</code>

<code>[ ! -f ~/.bash_profile ] && touch ~/.bash_profile</code>

<code>echo "export PATH=$PATH:/usr/local/go/bin:~/go/bin" >> ~/.bash_profile</code>

<code>source $HOME/.bash_profile</code>

<code>[ ! -d ~/go/bin ] && mkdir -p ~/go/bin</code>

##	Set vars

<code>echo "export WALLET="wallet"" >> $HOME/.bash_profile</code>

<code>echo "export MONIKER="test"" >> $HOME/.bash_profile</code>

<code>echo "export CROSSFI_CHAIN_ID="crossfi-evm-testnet-1"" >> $HOME/.bash_profile</code>

<code>echo "export CROSSFI_PORT="36"" >> $HOME/.bash_profile</code>

<code>source $HOME/.bash_profile</code>

##	Download binary

<code>cd $HOME</code>

<code>wget https://github.com/crossfichain/crossfi-node/releases/download/v0.3.0-prebuild3/crossfi-node_0.3.0-prebuild3_linux_amd64.tar.gz && tar -xf crossfi-node_0.3.0-prebuild3_linux_amd64.tar.gz</code>

<code>tar -xvf crossfi-node_0.3.0-prebuild3_linux_amd64.tar.gz</code>

<code>chmod +x $HOME/bin/crossfid</code>

<code>mv $HOME/bin/crossfid $HOME/go/bin</code>

<code>rm -rf crossfi-node_0.3.0-prebuild3_linux_amd64.tar.gz $HOME/bin</code>

##	Config and init app

<code>crossfid config node tcp://localhost:${CROSSFI_PORT}657</code>

<code>crossfid config keyring-backend os</code>

<code>crossfid config chain-id crossfi-evm-testnet-1</code>

<code>rm -rf testnet ~/.mineplex-chain</code>

<code>git clone https://github.com/crossfichain/testnet.git</code>

<code>mv $HOME/testnet/ $HOME/.mineplex-chain/</code>

##	Download genesis and addrbook

<code>wget -O $HOME/.mineplex-chain/config/genesis.json https://testnet-files.itrocket.net/crossfi/genesis.json</code>

<code>wget -O $HOME/.mineplex-chain/config/addrbook.json https://testnet-files.itrocket.net/crossfi/addrbook.json</code>

##	Set seeds and peers

<code>SEEDS="dd83e3c7c4e783f8a46dbb010ec8853135d29df0@crossfi-testnet-seed.itrocket.net:36656"</code>

<code>PEERS="66bdf53ec0c2ceeefd9a4c29d7f7926e136f114a@crossfi-testnet-peer.itrocket.net:36656,b88d969ba0e158da1b4066f5c17af9da68c52c7a@65.109.53.24:44656,dd83e3c7c4e783f8a46dbb010ec8853135d29df0@65.108.231.124:36656,2ce3b5d3ce236afe1fe1f4024c888ea59febac4a@65.108.206.118:60556,5ebd3b1590d7383c0bb6696ad364934d7f1c984e@160.202.128.199:56156,dda09f9625cab3fb655c22ef85d756fc77132b9d@167.235.102.45:10956,01e0df1e6932c371640cf44e80c8f0fd28675a6b@65.109.93.58:26056,c8914e513463791d91cc9ab35035c0c1111f307f@84.247.183.225:36656,b0b01c08d7d4c6c2740cc5fe6ea74eb7fdde64f2@38.242.151.229:26656,94eac2bd4f373b31ee9897fd5a2ab4a05080390b@65.108.127.160:26656,0a2014965a3bc7aa0feeaa6936d0e34d458d21ef@65.108.13.154:52656,634780077115040a5946d8d22e98910fb68205a2@65.109.65.248:52656"</code>

<code>sed -i -e "s/^seeds *=.*/seeds = \"$SEEDS\"/; s/^persistent_peers *=.*/persistent_peers = \"$PEERS\"/" $HOME/.mineplex-chain/config/config.toml</code>

##	Set custom ports in app.toml

<code>sed -i.bak -e "s%:1317%:${CROSSFI_PORT}317%g;</code>

<code>s%:8080%:${CROSSFI_PORT}080%g;</code>

<code>s%:9090%:${CROSSFI_PORT}090%g;</code>

<code>s%:9091%:${CROSSFI_PORT}091%g;</code>

<code>s%:8545%:${CROSSFI_PORT}545%g;</code>

<code>s%:8546%:${CROSSFI_PORT}546%g;</code>

<code>s%:6065%:${CROSSFI_PORT}065%g" $HOME/.mineplex-chain/config/app.toml</code>

##	Set custom ports in config.toml file

<code>sed -i.bak -e "s%:26658%:${CROSSFI_PORT}658%g;</code>

<code>s%:26657%:${CROSSFI_PORT}657%g;</code>

<code>s%:6060%:${CROSSFI_PORT}060%g;</code>

<code>s%:26656%:${CROSSFI_PORT}656%g;</code>

<code>s%^external_address = \"\"%external_address = \"$(wget -qO- eth0.me):${CROSSFI_PORT}656\"%;</code>

<code>s%:26660%:${CROSSFI_PORT}660%g" $HOME/.mineplex-chain/config/config.toml</code>

##	Config pruning

<code>sed -i -e "s/^pruning *=.*/pruning = \"custom\"/" $HOME/.mineplex-chain/config/app.toml</code>

<code>sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"100\"/" $HOME/.mineplex-chain/config/app.toml</code>

<code>sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"50\"/" $HOME/.mineplex-chain/config/app.toml</code>

##	Set minimum gas price, enable prometheus and disable indexing

<code>sed -i 's|minimum-gas-prices =.*|minimum-gas-prices = "10000000000000mpx"|g' $HOME/.mineplex-chain/config/app.toml</code>

<code>sed -i -e "s/prometheus = false/prometheus = true/" $HOME/.mineplex-chain/config/config.toml</code>

<code>sed -i -e "s/^indexer *=.*/indexer = \"null\"/" $HOME/.mineplex-chain/config/config.toml</code>

##	Create service file

<code>sudo tee /etc/systemd/system/crossfid.service > /dev/null <<EOF
[Unit]
Description=Crossfi node
After=network-online.target
[Service]
User=$USER
WorkingDirectory=$HOME/.mineplex-chain
ExecStart=$(which crossfid) start --home $HOME/.mineplex-chain
Restart=on-failure
RestartSec=5
LimitNOFILE=65535
[Install]
WantedBy=multi-user.target
EOF</code>

##	Reset and download snapshot

<code>crossfid tendermint unsafe-reset-all --home $HOME/.mineplex-chain</code>

<code>if curl -s --head curl https://testnet-files.itrocket.net/crossfi/snap_crossfi.tar.lz4 | head -n 1 | grep "200" > /dev/null; then</code>

  <code>curl https://testnet-files.itrocket.net/crossfi/snap_crossfi.tar.lz4 | lz4 -dc - | tar -xf - -C $HOME/.mineplex-chain</code>
  
    <code>else</code>
    
  <code>echo no have snap</code>
  
<code>fi</code>

##	Enable and start service

<code>sudo systemctl daemon-reload</code>

<code>sudo systemctl enable crossfid</code>

<code>sudo systemctl restart crossfid && sudo journalctl -u crossfid -f</code>


