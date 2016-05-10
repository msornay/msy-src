Love locks on Ethereum
######################

:date: 2016-05-09 20:00
:category: ethereum
:status: draft

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

- *identity* : identity of the node on the testnet ;

- *genesis* : Path to a genesis.json file describing the genesis block. You can
  find the one we used on our github. Every participant must use the same ;

- *datadir* : path to a folder where the blockchain data & your accounts will be
  stored. Useful to not overwrite the ``~/.ethereum`` default location ;

- *networkid* : An integer identifying the network. Every participant should use
  the same (1 is the main network, 0 & 2 are "officials" testnets) ;

- *ipcpath* : Mist (the graphical wallet) looks for a socket at
  ``~/.ethereum/geth.ipc`` to talk with a geth process (and launch its own if
  it doesn't find one). Since we moved the ``datadir``, we need to move
  ``geth.ipc`` back ;

- *nodiscover* : Disable peer discovery since we will use a static peer
  list. Maybe it's possible to enable peer discovery on the LAN only ? I
  haven't really looked into it.

You should now have access to the geth javascript console where you can type :

.. code-block:: javascript

   > admin.nodeInfo

To know your ``nodeId``. Share with other participant your ``nodeUrl`` :

.. code-block:: javascript

   enode://<nodeId>@<NODE IP ADDRESS ON THE LAN>:30303

Every other node can now add you as a peer, either by using
``admin.addPeer(nodeURL)`` or by creationg a JSON list of node urls in
``<datadir>/static-nodes.json`` :

.. code-block:: javascript

   [
     enode://<nodeId 1>@<NODE 1 IP ADDRESS ON THE LAN>:30303,
     enode://<nodeId 2>@<NODE 2 IP ADDRESS ON THE LAN>:30303
   ]

You can now start `Mist`_. It should warn you that you are on a testnet, and
display the number of peers you added.

To mine some ether, create an account (using Mist) and type the following
command in geth console :

.. code-block:: javascript

   > miner.start(1)


The number of block should increase rapidly (as the difficulty is low) and
peers should sync accordingly. Have fun sending those fresh ether around using
other participants' addresses. To see the transactions being taken into account
only when someone is mining and the blockchain growing is already quite
educationnal in itself.


.. _geth: https://github.com/ethereum/go-ethereum/releases
.. _La Main: https://lamaincollectif.wordpress.com/
.. _Mist: https://github.com/ethereum/mist/releases
