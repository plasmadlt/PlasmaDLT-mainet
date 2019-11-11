# PlasmaDLT Mainet Set-up Guide

## Minimum System Requirements
- The hardware must meet certain requirements to run a full node.
- 500 GB of free disk space
- Accessible at a minimum read/write speed of 100 MB/s
- 4 cores of CPU and 8 gigabytes of memory (RAM)

## Networks name
Testnet - Friedman
Mainet - Liberty (mainet)
Public Explorer - https://plasmadlt.com/
Official wallet - https://plasmapay.com/
Github - https://github.com/plasmadlt

## Run mainet node
Create dirs for producer node data

```
mkdir /data
mkdir /data/<producer name>
mkdir /data/<produer name>/producer/conf
```
Get the docker image
```
docker pull plasmachain/mainet:latest
```
## Setting up Configurations

* Create account
- If you run Testnet Node you need to create an official testnet account in official wallet PlasmaPay settings: https://app.plasmapay.com/id/profile/developers
- If you run Mainet Node, you need to activate the main account in official wallet PlasmaPay on the dashboard: https://app.plasmapay.com/dashboard and decrypt your private keys (key icon on the wallet).
**Attention:** you can create only one account in mainet and the account's name will be your node's name.
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



If you would like to run a Block Producer node and register your node at public Plasma blockchain explorer https://plasmadlt.com/ write to support chat at web or to our email message: your account name, your public key, your website address.

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
## Create docker-compose.yml file
```
version: '3.5'

services:
  plasma_node:
    image: plasmachain/mainet:latest
    hostname: localhost
    container_name: <producer name>
    command: >
      bash -c "ionode --replay-blockchain
      --genesis-json <producer name>/conf/genesis.json
      --config <producer name>/conf/config.ini
      --verbose-http-errors
      --max-transaction-time=100
      --data-dir <producer name>/blockchain
    volumes:
      - ./data/<producer name>/producer:/<producer name>
    restart: always
    ports:
      - 8888:8888
      - 9876:9876
    networks:
      - <producer name>  # Set your network what described in origin docs

networks:             # Here we are invokes network to work
  <producer name>:
    name: <producer name>
    driver: bridge
```

## Start producer node
```
docker-compose up --build

Creating network <producer name> with driver "bridge"
Creating <producer name> ... done
Attaching to <producer name>
<producer name>        | APPBASE: Warning: The following configuration items in the config.ini file are redundantly set to
<producer name>        |          their default value:
<producer name>        |              https-client-validate-peers, p2p-listen-endpoint, allowed-connection
<producer name>        |          Explicit values will override future changes to application defaults. Consider commenting out or
<producer name>        |          removing these items.
<producer name>        | info  2019-10-27T09:31:35.356 thread-0  chain_plugin.cpp:333          plugin_initialize    ] initializing chain plugin
<producer name>        | info  2019-10-27T09:31:35.357 thread-0  chain_plugin.cpp:512          plugin_initialize    ] Replay requested: deleting state database
<producer name>        | info  2019-10-27T09:31:35.388 thread-0  block_log.cpp:125             open                 ] Log is nonempty
<producer name>        | info  2019-10-27T09:31:35.389 thread-0  block_log.cpp:152             open                 ] Index is nonempty
<producer name>        | info  2019-10-27T09:31:35.394 thread-0  http_plugin.cpp:452           plugin_initialize    ] configured http to listen on 0.0.0.0:8888
<producer name>        | warn  2019-10-27T09:31:35.394 thread-0  net_api_plugin.cpp:96         plugin_initialize    ]
<producer name>        | **********SECURITY WARNING**********
<producer name>        | *                                  *
<producer name>        | * --         Net API            -- *
<producer name>        | * - EXPOSED to the LOCAL NETWORK - *
<producer name>        | * - USE ONLY ON SECURE NETWORKS! - *
<producer name>        | *                                  *
<producer name>        | ************************************
<producer name>        |
<producer name>        | warn  2019-10-27T09:31:35.394 thread-0  history_plugin.cpp:321        plugin_initialize    ] --filter-on * enabled. This can fill shared_mem, causing ionode to stop.
<producer name>        | info  2019-10-27T09:31:35.394 thread-0  http_plugin.cpp:399           operator()           ] configured http with Access-Control-Allow-Origin: *
<producer name>        | info  2019-10-27T09:31:35.395 thread-0  main.cpp:99                   main                 ] ionode version v.0.1.0-dirty
<producer name>        | info  2019-10-27T09:31:35.395 thread-0  main.cpp:100                  main                 ] ion root is /root/.local/share
<producer name>        | info  2019-10-27T09:31:35.395 thread-0  main.cpp:101                  main                 ] ionode using configuration file /<producer name>/conf/config.ini
<producer name>        | info  2019-10-27T09:31:35.395 thread-0  main.cpp:102                  main                 ] ionode data directory is /<producer name>/blockchain
<producer name>        | error 2019-10-27T09:31:35.395 thread-0  controller.cpp:1735           startup              ] No head block in fork db, perhaps we need to replay
<producer name>        | warn  2019-10-27T09:31:35.396 thread-0  controller.cpp:576            initialize_fork_db   ]  Initializing new blockchain with genesis state                  
<producer name>        | info  2019-10-27T09:31:35.450 thread-0  controller.cpp:307            replay               ] existing block log, attempting to replay from 2 to 8213126 blocks
```

* The launched container must have access to the Internet, it is accessible by the public ip address and is accessible by the ports 8888 and 9876. It is necessary to provide a public key for registering the producer on the mainet network

* Check if you can access you node using link http://you_server:your_http_port/v1/chain/get_info

## Verifying that the Producer is registered
```
docker exec -it <producer name> sol --url <peer address> get table ion ion producers --index 2 --key-type i128 -r -l 100

.....
      "url": "plasmapay.com",
      "memo": "tdominionb"
    },{
      "owner": "<producer name>",
      "stake": {
        "symbol": "18,PLASMA",
        "amount": "0x000064a7b3b6e00d0000000000000000",
        "value": "1.000000000000000000 PLASMA"
      },
      "producerKey": "<Public key>",
      "isActive": 1,
      "url": "",
      "memo": "add <producer name>"
    }
  ],
  "more": false
}
```



## Deploy contract in Mainet using active key
Choice producer from the network testnet or mainet from http://plasmadlt.com/monitor
```
docker exec -i <network> sol --url  --wallet-url  set contract accountname /host-share/helllloworld
```

Test deployed contract

```
docker exec -i <network> sol --url --wallet-url  push action accountname <action name>  '{"user": "accountname"}' -p accountname@active
```

```
docker exec -i <network> ol --url --wallet-url http://127.0.0.1:9999 push action accountname <action name>  '{"user": "accountname"}' -p accountname@active
```

## You can also run a node using our docker image on [DigitalOcean Marketplace](url:https://marketplace.digitalocean.com)
