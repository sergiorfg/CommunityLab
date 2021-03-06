# Installation

## Prerequisites

[Docker installation](https://docs.docker.com/engine/installation/)

## Docker image creation

The simplest case is to generating it with the default parameters:

```
docker-compose -f docker-compose-init.yml up
docker-compose -f docker-compose.yml up
```

### Docker image customization

In case of conflict with any resource, the following parameters are accepted

| **Name** | **Default value** | **Description** | 
|------------|-----------------------|-----------------|
| **HOSTNAME** | 0.0.0.0 | IP Address |
| **PORT**   | 30303 | Network listening port |
| **RPCPORT** | 8545 | HTTP-RPC server listening port |
| **RPCCORSDOMAIN** | * | Comma separated list of domains from which to accept cross origin requests |
| **WSPORT**  | 8546 | WS-RPC server listening port |
| **NETWORKID** | 201710 | Custom NetworkID. (integer, 1=Frontier, 3=Ropsten ) |
| **RPCAPI** | admin,db,eth,debug,miner,net,shh,txpool,personal,web3|API's offered over the HTTP-RPC interface |
| **VERBOSITY** | 3 | Logging verbosity: 0=silent, 1=error, 2=warn, 3=info, 4=core, 5=debug, 6=detail |
| **MINERTHREADS** | 1 | Number of CPU threads to use for mining |
    

The simplest way is to execute it with environment variables:

```
PORT=40404 RPCPORT=18545 docker-compose -f docker-compose.yml up 
```

For any change we need, it is necessary to regenerate the docker image:

```
docker rm `docker ps -a| grep ethereum-behq | awk {'print $1'}`
docker-compose -f docker-compose-init.yml up
VAR1=VALUE1 VAR2=VALUE2 docker-compose -f docker-compose.yml up 
```

## Basic commands

### Start docker image (once created)

```
docker start ethereum-behq
```

### Stop docker image (once created)

```
docker stop ethereum-behq
```

### IP address lookup of docker image

```
docker inspect ethereum-behq | grep IPAddress | cut -d '"' -f 4 | grep -v ^$
```

### Logs of docker image

```
docker logs -f `docker ps -a| grep ethereum-behq| awk {'print $1'}`
```

### Software information

```
docker exec -ti ethereum-behq geth version
```

### New account creation

```
docker exec -ti ethereum-behq geth account new
```

### Attach Javascript console

```
docker exec -ti ethereum-behq geth attach
```

### Connection via [Mist](https://github.com/ethereum/mist/releases)

```
mist --rpc http://0.0.0.0:8545 --swarmurl "null"
```

## Basic operations within the Javascript console

```
docker exec -ti ethereum-behq geth attach

Welcome to the Geth JavaScript console!

instance: Geth/ethereum-behq/v1.7.1-unstable/linux-amd64/go1.9
coinbase: 0x6a45ab210b8ad40b34167cd49be056ce2ac2f864
at block: 3349 (Sat, 28 Oct 2017 00:38:22 UTC)
 datadir: /root/.ethereum
 modules: admin:1.0 debug:1.0 eth:1.0 miner:1.0 net:1.0 personal:1.0 rpc:1.0 txpool:1.0 web3:1.0

# Stop minning
> miner.stop()
true

# Start minning
> miner.start(1)
null

# Get coinbase address
> eth.coinbase
"0x6a45ab210b8ad40b34167cd49be056ce2ac2f864"

# Get balance
> web3.fromWei(eth.getBalance(eth.coinbase), "ether");
15465

# Get actual block
> eth.blockNumber
3349

# Account list:
> personal.listAccounts
["0x6a45ab210b8ad40b34167cd49be056ce2ac2f864"]

> sender = personal.listAccounts[0]     # It's also `eth.coinbase`
> new_recipient = personal.newAccount()
Passphrase: ****         # demo
Repeat passphrase: ****  # demo
"0xf3ae33adfc3e42abe054fc77a6691063d9218cf3"

# Account list:
> personal.listAccounts

["0x6a45ab210b8ad40b34167cd49be056ce2ac2f864", "0xf3ae33adfc3e42abe054fc77a6691063d9218cf3"]

# Send transaction from `sender` to `new_recipient`
> personal.unlockAccount(sender)
Unlock account 0x6a45ab210b8ad40b34167cd49be056ce2ac2f864
Passphrase ****   # behq
true
> personal.unlockAccount(new_recipient, "demo")
true
> eth.sendTransaction({from: sender, to: new_recipient, value: web3.toWei(1000, "ether")})
"0x773d6aaf1f130514e5f8844c6e830b377c4fec58521a25b462c51facce3ad613"

> web3.fromWei(eth.getBalance(sender), "ether")
15465

> web3.fromWei(eth.getBalance(new_recipient), "ether")
0

# Pending transactions
> eth.pendingTransactions
[{
    blockHash: null,
    blockNumber: null,
    from: "0x6a45ab210b8ad40b34167cd49be056ce2ac2f864",
    gas: 90000,
    gasPrice: 18000000000,
    hash: "0x773d6aaf1f130514e5f8844c6e830b377c4fec58521a25b462c51facce3ad613",
    input: "0x",
    nonce: 2,
    r: "0xbc3b866265e48cd1b04b0bd4e6398b0a916c3ac5d2b6fc559684c3ea97155511",
    s: "0x20270f35a795d1f4ac39be5db730d764021143ff1ff7e5ffdd81a947c26517cb",
    to: "0xf3ae33adfc3e42abe054fc77a6691063d9218cf3",
    transactionIndex: 0,
    v: "0x2a",
    value: 1e+21
}]

# ... Wating until transaction be done ...
> eth.pendingTransactions
[]
> eth.blockNumber
3380
> web3.fromWei(eth.getBalance(sender), "ether")
14675

> web3.fromWei(eth.getBalance(new_recipient), "ether")
1000
```

# References

* https://github.com/eduadiez/private_chain_ethereum_docker
* https://hub.docker.com/r/ethereum/client-go/
* https://github.com/ethereum/go-ethereum
* https://github.com/ethereum/go-ethereum/wiki/Command-Line-Options