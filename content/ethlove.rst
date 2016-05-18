========================
 Love locks on Ethereum
========================

:date: 2016-05-12 20:00
:category: ethereum
:status: published

We had our first Ethereum workshop at `La Main`_. Some people there had never
heard about Ethereum before, the goal was to guide them in setting up geth /
Mist on a private testnet on a LAN, and have them interact with a simple smart contract.

All the source code is `here`_


Setting up a private testnet
============================

Every participant should be on the same LAN, install `geth`_ and run :

.. code-block:: shell

  geth --identity <ID> --genesis <GENESIS.JSON> --datadir <DATADIR> --networkid 9891 \
       --ipcpath "~/.ethereum/geth.ipc" \
       --nodiscover \
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

A Love Lock contract
====================

To illustrate the permanency of data stored on the blockchain, we decided to
implemented `love locks`_. The basic idea is that if two persons mutually agree to
be linked, they become publicly binded, and that lock cannot be removed.

The solidity source code is pretty straightforward :

.. code-block:: none

   contract EthLove {
     mapping (address => address) public links;

     function EthLove() {}

     function link(address with) {
       if (links[msg.sender] != 0) throw;
       links[msg.sender] = with;
     }

     function areLinked(address a, address b) returns (bool) {
       return (links[a] == b && links[b] == a);
     }
   }


We have a public mapping storing people's intention of associating with someone
else (using the ``link()`` method) and we consider two person bound if they
published mutual intentions. Note how divorces are not allowed with the use of
``throw`` ; No sir, not in this contract.

Publish this contract using Mist & import it into geth (look into
`are_linked.js`_ for some inspiration ; geth have a very useful ``loadScript()``
function that loads a js file and executes it).

Take a moment to consider how the contract methods differs :

- ``link()`` changes the internal state of the blockchain-based database and as
  such requires a proper transaction that will need to be included into a new
  block by a miner and thus will cost gas.

- ``areLinked()`` leaves the database untouched, and can be called locally in
  the geth console, without the need to publish a transaction.

As a matter of fact, the link check can be done by reading inside the mapping
directly (``areLinked()`` function in `are_linked.js`_). It is however
interesting to note that by using the contract function locally we execute code
that we read on the blockchain, that is public, immutable and that allow us to
describe without ambiguity what we mean by *linked*.

The diamond
===========

An important part of a love lock is throwing the key away. Luckily Ethereum
don't lack keys.

In addition to the linking above, we had people send ether to an address
obtained by XOR'ing the couple's addresses (the code is in `are_linked.js`_
too). Since the private key needed to transfer the ether from the resulting
adresse has no cryptographic chance to exist, those Ether are in effect an
everlasting token of love, that you can put a price tag on.


.. _are_linked.js: https://github.com/colibriste/ethereum/blob/master/01_ethlove/are_linked.js
.. _geth: https://github.com/ethereum/go-ethereum/releases
.. _here: https://github.com/colibriste/ethereum/tree/master/01_ethlove
.. _La Main: https://lamaincollectif.wordpress.com/
.. _love locks: https://en.wikipedia.org/wiki/Love_lock
.. _Mist: https://github.com/ethereum/mist/releases
