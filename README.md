
# docker-bitcoind

[![Docker Stars](https://img.shields.io/docker/stars/bcawthra/bitcoind.svg)](https://hub.docker.com/r/bcawthra/bitcoind/)
[![Docker Pulls](https://img.shields.io/docker/pulls/bcawthra/bitcoind.svg)](https://hub.docker.com/r/bcawthra/bitcoind/)
[![CircleCI](https://circleci.com/gh/bonovoxly/docker-bitcoind/tree/master.svg?style=svg)](https://circleci.com/gh/bonovoxly/docker-bitcoind/tree/master)

A Docker configuration with sane defaults for running a full validating Bitcoin node. For more info, see:

- Deploying to AWS using Terraform and ansible-pull - <https://blog.billyc.io/2017/11/20/deploying-a-bitcoin-node-using-terraform-and-ansible-pull/>
- Deploying to Google Cloud using Terraform and ansible-pull - <https://blog.billyc.io/2017/11/23/deploying-a-bitcoin-node-to-google-cloud-using-terraform-and-ansible-pull/>
- Github repo for using Terraform and ansible-pull (AWS and Google Cloud) - <https://github.com/bonovoxly/bitcoin-node>
- Github repo for deploying to Google Kubernetes Engine - <https://github.com/bonovoxly/gke-bitcoin-node>


Credits to https://github.com/jamesob/docker-bitcoind for this Docker image.

## Quick start

Requires that [Docker be installed](https://docs.docker.com/engine/installation/) on the host machine.

```
# Create some directory where your bitcoin data will be stored.
$ mkdir /home/youruser/bitcoin_data

$ docker run --name bitcoind -d \
   --env 'BTC_RPCUSER=foo' \
   --env 'BTC_RPCPASSWORD=password' \
   --volume /home/youruser/bitcoin_data:/bitcoin \
   -p 8332:8332
   --publish 8333:8333
   bonovoxly/bitcoind:latest

$ docker logs -f bitcoind
[ ... ]
```

## Configuration

A custom `bitcoin.conf` file can be placed in the mounted data directory.
Otherwise, a default `bitcoin.conf` file will be automatically generated based
on environment variables passed to the container:

| name | default |
| ---- | ------- |
| BTC_PRUNE | 0 |
| BTC_RPCUSER | btc |
| BTC_RPCPASSWORD | changemeplz |
| BTC_RPCCLIENTTIMEOUT | 30 |
| BTC_RPCALLOWIP | ::/0 |
| BTC_RPCPORT | 8332 |
| BTC_PRINTTOCONSOLE | -1 |
| BTC_DISABLEWALLET | -1 |
| BTC_TXINDEX | 0 |
| BTC_TESTNET | 0 |


## Daemonizing

If you're using systemd, you can use a config file like

```
$ cat /etc/systemd/system/bitcoind.service

# bitcoind.service
[Unit]
Description=Bitcoind
After=docker.service
Requires=docker.service
 
[Service]
ExecStartPre=-/usr/bin/docker kill bitcoind
ExecStartPre=-/usr/bin/docker rm bitcoind
ExecStartPre=/usr/bin/docker pull bonovoxly/bitcoind:latest
ExecStart=/usr/bin/docker run \
    --name bitcoind \
    -p 8333:8333 \
    -p 8332:8332 \
    -v /data/bitcoind:/bitcoin \
    bonovoxly/bitcoind:latest
ExecStop=/usr/bin/docker stop bitcoind 
```

to ensure that bitcoind continues to run.


## Links

- <https://blog.billyc.io/2017/11/20/deploying-a-bitcoin-node-using-terraform-and-ansible-pull/>
- <https://blog.billyc.io/2017/11/23/deploying-a-bitcoin-node-to-google-cloud-using-terraform-and-ansible-pull/>
- <https://github.com/bonovoxly/bitcoin-node>
- <https://github.com/bonovoxly/gke-bitcoin-node>

