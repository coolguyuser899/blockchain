# Blockchain quorum presentation on 28 Feb 2018 
# Demo of quorum privacy feature for enterprise blockchain

Environment

ubuntu 16.04 -a brand new VM 
uname -a
Linux 28573526-forcevm 4.13.0-32-generic #35~16.04.1-Ubuntu SMP Thu Jan 25 10:13:43 UTC 2018 x86_64 x86_64 x86_64 GNU/Linux

Setup dependencies
**install git **

sudo apt-get update

sudo apt-get upgrade  // press Y when asked Do you want to continue [Y/n]

sudo apt-get install git 

$  git --version
git version 2.7.4

**make folder for download and source file**

mkdir workspace

**install Constellation (self managing, peer to peer system) **
https://github.com/jpmorganchase/constellation

**install supporting libraries**

apt-get install libdb-dev libleveldb-dev libsodium-dev zlib1g-dev libtinfo-dev
'''
**download pre-build **
'''
cd workspace 

wget https://github.com/jpmorganchase/constellation/releases/download/v0.3.2/constellation-0.3.2-ubuntu1604.tar.xz

2018-02-24 13:04:31 (3.02 MB/s) - ‘constellation-0.3.2-ubuntu1604.tar.xz’ saved [4682556/4682556]

$ tar xvf constellation-0.3.2-ubuntu1604.tar.xz 

constellation-0.3.2-ubuntu1604/
constellation-0.3.2-ubuntu1604/constellation-node

$ ./constellation-0.3.2-ubuntu1604/constellation-node --version
Constellation Node 0.3.2

'''
**install go language**
'''

sudo apt-get update

sudo apt-get -y upgrade

cd workspace 

wget https://dl.google.com/go/go1.9.4.linux-amd64.tar.gz

tar -xvf go1.9.4.linux-amd64.tar.gz

sudo mv go /usr/local

/usr/local/go/bin/go version
go1.9.4.linux-amd64.tar.gz

// add /usr/local/go/bin to PATH
vi ~/.profile

export PATH=$PATH:/usr/local/go/bin
'''
please don't use this to install golang, default is 1.6 old version
'''

sudo apt-get install golang  // press Y at prompt
'''
**download quorum source code and build, required for privacy feature**
'''
cd workspace 

mkdir quorum_src

cd quorum_src

git clone https://github.com/jpmorganchase/quorum.git

cd quorum

. ~/.profile    // source PATH of go-1.9.4

$ make all

make all

build/env.sh go run build/ci.go install

>>> /usr/local/go/bin/go install -ldflags -X main.gitCommit=ee498061b5a74bf1f3290139a53840345fa038cb -v github.com/ethereum/go-ethereum github.com/ethereum/go-ethereum/accounts 
.....

make test
......
ok  	github.com/ethereum/go-ethereum/accounts	0.025s
ok  	github.com/ethereum/go-ethereum/accounts/abi	0.030s
......
'''

**add geth to PATH to load quorum **
~/workspace/quorum_src/quorum/build/bin

**download quarum examples**
// ignore virtual box and vagrant if it does not work for you
'''
git clone https://github.com/jpmorganchase/quorum-examples

cd  ~/workspace/quorum-examples/examples/7nodes

./raft-init.sh
// new folder created
drwxr-xr-x 10 force force 4096 Feb 24 15:47 qdata

./raft-start.sh   //start 7 nodes

./runscript.sh script1.js  //deploy contract and transaction

**7nodes instruction - https://github.com/jpmorganchase/quorum-examples/tree/master/examples/7nodes**

'''
$ geth attach  qdata/dd1/geth.ipc 

$ geth attach  qdata/dd5/geth.ipc 

$ geth attach  qdata/dd7/geth.ipc 

// change permission tm.key to avoid warning in log
$ chmod 600 qdata/c*/tm.key

// start again  
./raft-start.sh

All nodes configured. See 'qdata/logs' for logs, and run e.g. 'geth attach qdata/dd1/geth.ipc' to attach to the first Geth node.

To test sending a private transaction from Node 1 to Node 7, run './runscript script1.js'

// attach to node again 
$ geth attach  qdata/dd1/geth.ipc 

$ geth attach  qdata/dd5/geth.ipc 

$ geth attach  qdata/dd7/geth.ipc 

open 4th terminal and run
$ ./runscript.sh script1.js 

Contract transaction send: TransactionHash: 0x62f3ca71840af56f0a3bc5f45bad919234b1dabccae0de6673a320381aee8380 waiting to be mined...

true

// look for address, to address
INFO [02-24|16:28:36] Submitted contract creation   
           fullhash=0x62f3ca71840af56f0a3bc5f45bad91
9234b1dabccae0de6673a320381aee8380 to=0x1932c48b2bF8
102Ba33B4A6B545C32236e342f34

// in each javascript console type the following

> var address = "0x1932c48b2bf8102ba33b4a6b545c32236e342f34";

> var abi = [{"constant":true,"inputs":[],"name":"storedData","outputs":[{"name":"","type":"uint256"}],"payable":false,"type":"function"},{"constant":false,"inputs":[{"name":"x","type":"uint256"}],"name":"set","outputs":[],"payable":false,"type":"function"},{"constant":true,"inputs":[],"name":"get","outputs":[{"name":"retVal","type":"uint256"}],"payable":false,"type":"function"},{"inputs":[{"name":"initVal","type":"uint256"}],"type":"constructor"}];

> var private = eth.contract(abi).at(address)

// test again, vi scripts1.js, change value of 42 to 52
./runscripts.sh script1.js

$less qdata/logs/1.log  // find to address, test same steps again

// node5, can't see value
> var address = "0x9d13C6D3aFE1721BEef56B55D303B09E021E27ab"
undefined

> var private = eth.contract(abi).at(address)
undefined

> private.get()
0


//node1 and node7 can see values, address is new from 1.log
> var address = "0x9d13C6D3aFE1721BEef56B55D303B09E021E27ab"
undefined

> var private = eth.contract(abi).at(address)
undefined

> private.get()
52

'''


