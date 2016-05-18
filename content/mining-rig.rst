===============================
No-Nonsense Ethereum Mining Rig
===============================

:date: 2016-05-18 20:00
:category: ethereum
:status: draft


This article is the first element of a set of tools/tutorials that aims to make
mining Ether with free software painless and secure. Given the number of
conflicting / outdated / plain wrong instructions we_ found online when setting
up our own rigs, we think that there is room for something minimal, easy and
up-to-date.

Moreover some people are selling this kind of configuration (no, i'm not
linking) on pre-installed SSDs. Look at the scripts below, I don't think it is
worth what they are asking for.

The instructions here are kind of raw, we are working on making this accessible
to first-time linux users. In the mean time, here what we suggest (if you are
using AMD GPUs):


The OS
======

Install `Ubuntu Server 14.04`_


The Dependencies
================

.. code-block:: shell

   sudo add-apt-repository ppa:ethereum/ethereum
   sudo add-apt-repository ppa:ethereum/ethereum-qt

   sudo apt-get update
   sudo apt-get install nano git fglrx-updates cpp-ethereum python-twisted

   git clone --branch 0.0.5 https://github.com/Atrides/eth-proxy

Yes, this will install xorg but we found out that none of that ``aticonfig
--initial`` stuff was necessary. You can now check with ``ethminer
--list-devices`` that your GPUs are ready to use.

No, ``aticonfig --odgt`` to check temperature doesn't work with this setup. We
are working on it.

The Configuration
=================

``nano eth-proxy/eth-proxy.conf`` to edit eth-proxy configuration. You need to edit :

- ``HOST = 127.0.0.1``
- ``WALLET = 0x<YOUR_ADDRESS_HERE>`` (Triple check this)
- ``ENABLE_WORKER_ID = True``
- ``POOL_HOST`` & ``POOL_HOST_FAILOVER`` (We use ethermine_, but the choice is
  entirely up to you).

It should look like this :

.. code-block:: python

   ###
   # Examples of command line for miners:
   #
   #   ethminer.exe --farm-recheck 200 -G -F http://HOST:PORT/
   #   ethminer.exe --farm-recheck 300 -G -F http://HOST:PORT/rig1
   #
   #   ethminer.exe -G -F http://127.0.0.1:8080/
   #   ethminer.exe --farm-recheck 100 -G -F http://192.168.0.33:8080/rig1
   #
   #  farm-recheck parameter is very individual. Just test different values.
   #
   #  You can submit shares without workername or
   #  You can provide workername:
   #   - with url like "/rig1"
   #   - or use automatically numbering(integer) based on IP of miner
   #
   #  Servers:
   #    EU-Server:  eth-eu.dwarfpool.com  (France)
   #    US-Server:  eth-us.dwarfpool.com  (EastCoast: Montreal,Canada)
   #    US-Server:  eth-us2.dwarfpool.com (WestCoast: Las Vegas)
   #    RU-Server:  eth-ru.dwarfpool.com  (Moscow)
   #    HK-Server:  eth-hk.dwarfpool.com  (Hong-Kong)
   #    CN-Server:  eth-cn.dwarfpool.com  (Shanghai)
   #    SG-Server:  eth-sg.dwarfpool.com  (Singapore)
   #    AU-Server:  eth-au.dwarfpool.com  (Melbourne)
   #
   ###

   # Select Ethereum ETH or Expanse EXP
   COIN = "ETH"

   # Host and port for your workers
   HOST = "127.0.0.1"
   PORT = 8080

   # Coin address where money goes
   WALLET = "0x<YOUR ADDRESS HERE>"

   # To donate please use wallet "0xea7263feb7d8a8ab0a11eedd8f1ce04412ab0820"

   # It's useful for individually monitoring and statistic
   ENABLE_WORKER_ID = True

   # On DwarfPool you have option to monitor your workers via email.
   # If WORKER_ID is enabled, you can monitor every worker/rig separately.
   MONITORING = False
   MONITORING_EMAIL = "mail@example.com"

   # Main pool
   POOL_HOST = "eu1.ethermine.org"
   POOL_PORT = 4444

   # Failover pool
   POOL_FAILOVER_ENABLE = True

   POOL_HOST_FAILOVER1 = "us1.ethermine.org"
   POOL_PORT_FAILOVER1 = 4444

   POOL_HOST_FAILOVER2 = "us2.ethermine.org"
   POOL_PORT_FAILOVER2 = 4444

   POOL_HOST_FAILOVER3 = "asia1.ethermine.org"
   POOL_PORT_FAILOVER3 = 4444

   # Logging
   LOG_TO_FILE = True

   # Enable debug
   DEBUG = False

The Mining
==========

Launch eth-proxy and ethminer, replacing ``<RIG_NAME>`` with whichever name you
like

.. code-block:: shell

   python eth-proxy/eth-proxy.py 2> ethproxy.log &
   ethminer --farm-recheck 200 -G -F http://127.0.0.1:8080/<RIG_NAME>


.. _ethermine: http://ethermine.org/
.. _`Ubuntu Server 14.04`: http://cdimage.ubuntu.com/netboot/14.04/
.. _we : https://github.com/colibriste
