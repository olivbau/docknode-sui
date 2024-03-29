# Docknode SUI

## Metrics

- `https://yourdomain.com:9101/metrics`
- `https://yourdomain.com:9102/metrics`

## Install

0. VPS config (optional)

```bash
apt update
apt upgrade -y
apt install -y git screen
# apt update && apt upgrade -y && apt install -y git screen
# install docker https://docs.docker.com/engine/install/ubuntu/#install-using-the-repository
```

1. Clone the repository and

```bash
git clone https://github.com/olivbau/docknode-sui.git
cd docknode-sui
```

2. Configure environement variables

```bash
cp .env.example .env

# Generate users passwords for basic auth
docker run --rm caddy:2-alpine caddy hash-password --plaintext 'password'

# Set users and passwords for basic auth
# Set the host
nano .env
```

3. Setup UFW

```bash
ufw allow ssh && ufw deny 9000 && ufw deny 9100 && ufw deny 9184
ufw enable
```

4. Download snapshot (optional)

```bash
# docker run --rm --volume ./sui/genesis.blob:/genesis.blob:ro --volume ./sui/db:/tmp:rw mysten/sui-tools:mainnet /usr/local/bin/sui-tool download-db-snapshot --formal --epoch 249 --snapshot-bucket mysten-mainnet-formal --snapshot-bucket-type gcs --num-parallel-downloads 50 --genesis /genesis.blob --network mainnet --path /tmp --no-sign-request

apt update
apt install -y awscli

screen -R snapshot
# Replace XXX by the current epoch - 1
# https://suiexplorer.com/
aws s3 cp s3://mysten-mainnet-snapshots/epoch_XXX ./sui/db/live --recursive --no-sign-request
```

5. Run

```bash
wget -O ./sui/genesis.blob https://github.com/MystenLabs/sui-genesis/raw/main/mainnet/genesis.blob

docker compose pull
docker compose up -d
docker logs -f docknode-sui-fullnode-1 --since 5m
docker compose down
```
