Love locks on Ethereum
######################

:date: 2016-05-09 20:00
:category: ethereum

We had our first Ethereum workshop at `La Main`_. Some people there had never
heard about Ethereum before, the goal was to guide them in setting up geth /
Mist on a private testnet on a LAN, and have them interact with a simple smart contract.

Setting up a private testnet
============================

Every participant should be on the same LAN, install `geth`_ and run :

.. code-block:: shell

  geth --identity <ID> --genesis <GENESIS.JSON> --datadir <DATADIR> --networkid 9891 \
       --ipcpath "~/.ethereum/geth.ipc" \
       --nodiscover \
       --rpc --rpcport "8080" --rpccorsdomain "*" --rpcapi "db,eth,net,web3" \
       --port "30303"  --verbosity 6 console 2>> geth.log

- *identity* : identity of each node on the network
- *genesis* : Path to a genesis.json file describing the genesis block. You can
  find the one we used on our github. Every participant must use the same.
- *datadir* : path to a folder where the blockchain data & your accounts will be
  stored. Useful to not overwrite the ``~/.ethereum`` default location
- *networkid* : An integer identifying the network. Every participant should use
  the same (1 is the main network, 0 & 2 are "officials" testnets)
- *ipcpath* : Mist (the graphical wallet) because we moved the *datadir*



.. _geth: http://ethereum.github.io/go-ethereum/
.. _La Main: https://lamaincollectif.wordpress.com/
