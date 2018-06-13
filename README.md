SinkyCoin Project
=====================================

[![Build Status](https://travis-ci.org/bitbaba/sinkycoin.svg?branch=master)](https://travis-ci.org/bitbaba/sinkycoin)

What is SinkyCoin?
----------------

SinkyCoin [SKC] is an new digital gold that enables instant payments to
anyone, anywhere in the world. SinkyCoin uses peer-to-peer technology to operate
with no central authority: managing transactions and issuing money are carried
out collectively by the network. SinkyCoin is the name of open source
software which enables the use of this currency. 
see [Features](https://github.com/bitbaba/sinkycoin/blob/master/README.md#features)
and [RoadMaps](https://github.com/bitbaba/sinkycoin/blob/master/README.md#roadmaps).

For more information, as well as an immediately useable, binary version of
the SinkyCoin software, see https://bintray.bitbaba.com/bintray/sinkycoin, or read the
[sinkycoin design](http://blog.csdn.net/hacode/article/details/78369398) and
[bitcoin original whitepaper](https://bitcoincore.org/bitcoin.pdf).


License
-------

SinkyCoin[SKC] is released under the terms of the MIT license.

See [COPYING](COPYING) for more information or see https://opensource.org/licenses/MIT.

Downloads
-------------

- [Win32](https://bintray.bitbaba.com/sinkycoin/sinkycoin-win32.tar.gz)
- [Ubuntu](https://bintray.bitbaba.com/sinkycoin/sinkycoin-ubuntu64.tar.gz)
- [Mac](https://bintray.bitbaba.com/sinkycoin/sinkycoin-mac.tar.gz)
- [minerd](https://bintray.bitbaba.com/sinkycoin/sinkycoin-miner.zip)

Services
----------------

- [Pool](https://pool.bitbaba.com/)

- [Miner](https://github.com/bitbaba/cpuminer)

- [Explorer](https://sinkycoin.bitbaba.com/)

- [Exchange](https://ex.bitbaba.com/)


Features
--------

- 5 minutes per block
- 2-Mega-bytes serialized block size(2x of bitcoin core)
- 42,000,000 coins in PoW stage
- Seg-Witness
- GoldHash as PoW (now SHA256Q, asic-resistant)
- Miner-inside GUI wallet

Roadmaps
----------------

- Support chainstate retrieving in script stack machine
  - nonceOf(height) [example: use nonce to gamble](https://github.com/bitbaba/sinkycoin/blob/sinkycoin/doc/gamble.md)
  - hashOf(height)
  - timeOf(height)

- Support non-standard sub-script of P2SH

- Support state storage of non-utxo data

- Support Tx comments(e.g. "pay for coffe")

- Support Instant Messenaging Chat

- Support Mounting of Turing-complete VM for smart contract

Mining 
-------------------
- Pool

```
./minerd --algo=sha256q \
         --threads=1 \
         --url=stratum+tcp://pool.bitbaba.com:3333 \
         --no-getwork \
         --no-gbt \
         --user=GZmKHp12bDUiDCkvvzyZzytwRcNaW3viDM \
         --pass=x \
         --debug \
         --protocol-dump
```

- GBT(GetBlockTemplate) on remote rpc

```
./minerd --algo=sha256q \
	 --threads=1 \
	 --coinbase-sig="bitbaba" \
	 --coinbase-addr=GZmKHp12bDUiDCkvvzyZzytwRcNaW3viDM \
	 --url=http://api.bitbaba.com/ \
	 --no-getwork \
	 --user=rpcuser \
	 --pass=rpcpassword \
	 --debug \
	 --protocol-dump
```

- Solo-Mining Locally

mine.sh

```
while true; 
    do ./sinkycoin-cli generatetoaddress 1 GZmKHp12bDUiDCkvvzyZzytwRcNaW3viDM 10000000; 
done
```

mine.bat

```
@echo off
:restart

echo minging...

sinkycoin-cli generatetoaddress 1 GZmKHp12bDUiDCkvvzyZzytwRcNaW3viDM 10000000

ping -w 1 -n 5 1.0.0.1

goto :restart
```

>Note: remember to configure sinkycoin.conf as following:

```
server=1
rpcuser=rpcuser
rpcpassword=rpcpassword
```

Building from sources
------------------

As you known, the .travis.yml is used for automated building. 
Here is a localized script called *travis.sh*, which can be used 
to build sinkycoin on your local laptop.

Example Usage:

```
$>mkdir -p ~/home/user1/tarballs; \
$>ln -s ~/home/user1/tarballs depends/sources; \
$>mkdir -p depends/sdk-sources depends/SDKs; \
$>sudo apt-get update; \
$>sh travis.sh;
```

>Note: *~/home/user1/tarballs* is used to cache locale source tarballs of depends.

