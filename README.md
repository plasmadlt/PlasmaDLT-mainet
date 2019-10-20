# PlasmaDLT Mainet Set-up Guide

## Minimum System Requirements
- The hardware must meet certain requirements to run a full node.
- 500 GB of free disk space
- Accessible at a minimum read/write speed of 100 MB/s
- 4 cores of CPU and 8 gigabytes of memory (RAM)

## Run mainet node
* Create dirs for producer node data

```
mkdir /data
mkdir /data/<producer name>
mkdir /data/<produer name>/producer/conf
```
* Get the docker image
```
docker pull plasmachain/mainet:latest
```
* Get the docker image
```
docker pull plasmachain/mainet:latest
```
* Edit config
```
producer-name = <producer name>
p2p-peer-address = <peer address (see https://plasmadlt.com)>
signature-provider = <Public key>=KEY:<Private key> (keys taken from account's active permission)
http-server-address = 0.0.0.0:8801
agent-name=<name to identify this node>
```
## Copy config files
```
cp config.ini /data/<producer name>/producer/conf
cp genesis.json /data/<producer name>/producer/conf
```
## Create network
```
docker network create --driver bridge <producer name>
```

## Start producer node
```
docker run -d --net <producer name>  -v /data/<producer name>/producer:/<producer name> -p 8801:8888 -p 9876:9876 --name producer-<producer name> --restart on-failure plasmachain/mainet:latest ionode --replay-blockchain --verbose-http-errors --genesis-json /<producer name>/conf/genesis.json --max-transaction-time=100 --config /<producer name>/conf/config.ini --data-dir /<producer name>/blockchain

```

### The launched container must have access to the Internet, it is accessible by the public ip address and is accessible by the ports 8888 and 9876. It is necessary to provide a public key for registering the producer on the mainet network


## Setting up Configurations

- Get file  config.ini mainet
```
cd config.ini
```
* Create account
- You need to get an official account testnet or mainet on https://app.plasmapay.com/id/profile/developers
- Get P2P endpoint  http://plasmadlt.com/monitor

## Get and edit the configuration file accordingly config.ini :
- http-server-address = 0.0.0.0:8888  register the port on which the host will listen to http requests
- p2p-listen-endpoint = 0.0.0.0:9876  set the port on which the host will listen for p2p incoming connections. Also, the value of this parameter should be given to the network administrator, so that he would include this host in the list of p2p-peer-address addresses of all producer nodes of the network.
- p2p-peer-address fill in the list of p2p network producer nodes. It must be obtained from the network testnet or mainet from http://plasmadlt.com/monitor.
- agent-name  set the name of the node.
- producer-name set the node name equal to the name of the account that the node owner received in step 1
- signature-provider - set the key values ​​that the node owner received from  https://app.plasmapay.com/id/profile/developers


## Plugin - fill in the plugins section:
- ion :: chain_api_plugin,
- ion :: http_plugin,
- ion :: net_api_plugin
- optional (ion :: history_plugin, ion :: history_api_plugin)


* After the node has played all the blocks of blockchain and created its own replica, the following commands can be executed as a command line utility as a test
```
docker exec -i <network>-bios-node sol get table plasma.token EURP stat
docker exec -i <network>-bios-node sol get account plasma.token
docker exec -i <network>-bios-node sol get abi plasma.token
```

- Check if you can access you node using link http://you_server:your_http_port/v1/chain/get_info
-  If you would like to run a Block Producer  node you need register your node at https://plasmadlt.com/#register or write on support

## Deploy contract in Mainet using active key
*  Choice producer from the network testnet or mainet from http://plasmadlt.com/monitor
```
docker exec -i <network> sol --url http://bnpparibas.friedman.plasmadlt.com --wallet-url http://127.0.0.1:9999 set contract accountname /host-share/helllloworld
```

* Test deployed contract

```
docker exec -i <network> sol --url http://swisubs.friedman.plasmadlt.com --wallet-url http://127.0.0.1:9999 push action accountname <action name>  '{"user": "accountname"}' -p accountname@active
```

```
docker exec -i <network> ol --url http://swisubs.friedman.plasmadlt.com --wallet-url http://127.0.0.1:9999 push action accountname <action name>  '{"user": "accountname"}' -p accountname@active
```
