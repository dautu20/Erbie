# Erbie
mkdir -p .erbie/erbie

cd $HOME

git clone https://github.com/erbieio/erbie

cd erbie

git checkout v0.14.5

go build -o erbie cmd/erbie/main.go

mv erbie /usr/local/bin


sudo tee /etc/systemd/system/erbied.service > /dev/null <<EOF

[Unit]

Description=erbie

After=online.target

[Service]

Type=simple

User=$USER

WorkingDirectory=$HOME

ExecStart= /usr/local/bin/erbie \
  --datadir $HOME/.erbie \
  --devnet \
  --identity chiemcan \
  --mine \
  --miner.threads 1 \
  --rpc \
  --rpccorsdomain "*" \
  --rpcvhosts "*" \
  --http \
  --rpcaddr 127.0.0.1 \
  --rpcport 8544 \
  --port 30304 \
  --maxpeers 50 \
  --syncmode full
Restart=on-failure

RestartSec=5

LimitNOFILE=4096

[Install]

WantedBy=multi-user.target

EOF

sudo systemctl daemon-reload

sudo systemctl enable erbied

sudo systemctl start erbied
