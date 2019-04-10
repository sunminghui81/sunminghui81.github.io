---
layout: post
keywords: Hyperledger, Fabric, blockchain
description: Hyperledger Fabric 学习总结
title: Hyperledger Fabric 学习笔记
categories: [新语言]
tags: [Hyperledger, Fabric, blockchain]
group: archive
icon: chain
---
{% include mathsyouth/setup %}


### 在 Ubuntu Trusty 上准备 Hyperledger Fabric 运行的前提环境

#### 安装 Git client

```shell
sudo apt update && sudo apt upgrade -y
sudo apt-get install git -y
```

#### 安装 [Go - 1.7 or later](https://golang.org/)

Download the archive and extract it into `/usr/local`, creating a Go tree in `/usr/local/go`:

```
wget https://storage.googleapis.com/golang/go1.8.1.linux-amd64.tar.gz
sudo tar -C /usr/local -xzf go1.8.1.linux-amd64.tar.gz
```

创建 Go workspace (e.g., `myproject`)，包含三个子文件夹：

```
mkdir -vp myproject/{src,pkg,bin}
```

The `GOPATH` environment variable specifies the location of your workspace.
Add `/usr/local/go/bin` to the` PATH` environment variable. You can do this by adding the following two lines to your `$HOME/.bash_profile` (My environment is `/home/ubuntu/.bash_profile`):

```
export PATH=$PATH:/usr/local/go/bin
export GOPATH=/home/ubuntu/myproject
```

运行命令检查配置是否成功：

```
source .bash_profile
go env GOPATH
```
在你的 workspace 下创建文件夹 `src/hello`，并在该文件夹下创建名为 `hello.go` 的文件：

```
cd myproject/src && mkdir hello
vim hello/hello.go
```

`hello.go` 的文件内容为：

```Go
package main

import "fmt"

func main() {
    fmt.Printf("hello, world\n")
}
```

Then build it with the `go` tool:

```
cd /home/ubuntu/myproject/src/hello
go build
```

The command above will build an executable named `hello` in the directory `hello`. Execute it to see the greeting:

```
./hello
```

如果显示 `hello, world`，则说明 Go 安装成功。

#### 安装 [Docker - 1.12 or later](https://docs.docker.com/engine/installation/linux/ubuntu/)

```
sudo apt-get update
sudo apt-get install \
    linux-image-extra-$(uname -r) \
    linux-image-extra-virtual -y
sudo apt-get install \
    apt-transport-https \
    ca-certificates \
    curl \
    software-properties-common -y
# Add Docker’s official GPG key
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
# Verify that the key fingerprint is 9DC8 5822 9FC7 DD38 854A E2D8 8D81 803C 0EBF CD88.
sudo apt-key fingerprint 0EBFCD88
sudo add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
   $(lsb_release -cs) \
   stable"
sudo apt-get update && sudo apt-get install docker-ce -y
# Verify that Docker CE or Docker EE is installed correctly by running the hello-world image
sudo docker run hello-world
```

#### 安装 [Docker Compose - 1.8.1 or later](http://hyperledger-fabric.readthedocs.io/en/latest/dev-setup/devenv.html)

```
sudo curl -L "https://github.com/docker/compose/releases/download/1.11.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
docker-compose --version
```

显示为 `docker-compose version 1.11.2, build dfed245`，则说明安装成功。

#### 安装 pip, behave 和 docker-compose

```
sudo apt-get install python-pip -y
sudo pip install --upgrade pip
sudo pip install behave nose docker-compose
sudo apt-get install python-dev libssl-dev -y
sudo pip install -I flask==0.10.1 python-dateutil==2.2 pytz==2014.3 pyyaml==3.10 couchdb==1.0 flask-cors==2.0.1 requests==2.4.3 pyOpenSSL==16.2.0 pysha3==1.0b1 grpcio==1.0.4

# PIP packages required for some behave tests
sudo pip install urllib3 ndg-httpsclient pyasn1 ecdsa python-slugify grpcio-tools jinja2 b3j0f.aop six
```

### Clone the Fabric code base

Since the Fabric project is a `Go` project, you’ll need to clone the Fabric repo to your `$GOPATH/src` directory:

```
cd $GOPATH/src
mkdir -p github.com/hyperledger && cd github.com/hyperledger
git clone https://github.com/hyperledger/fabric.git
```

### Build and test the Fabric project

#### Build the Fabric project

To build the Fabric, the user should be `root` (other users who have `sudo` privileges do not work):

```
sudo passwd root
su
source /home/ubuntu/.bash_profile
cd $GOPATH/src/github.com/hyperledger/fabric
make dist-clean all
# If you see an error stating - 'ltdl.h' file not found
apt install libtool libltdl-dev -y
# Then run the make again
make dist-clean all
```

#### Run the unit tests

Use the following sequence to run all unit tests:

```
cd $GOPATH/src/github.com/hyperledger/fabric
make unit-test
```

To run a specific test use the `-run RE` flag where `RE` is a regular expression that matches the test case name. To run tests with verbose output use the `-v` flag. For example, to run the `TestPull` test case, change to the directory `gossip/gossip` containing the `gossip_test.go` and call/excecute:

```shell
go test -v -run=TestPull
# The test result for the following test case is Failed.
# "Test killed with quit: ran too long (10m0s)""
go test -v -run=TestEndedGoroutines
go test -v -run=Test
```

### Prerequisites

Build the `configtxgen` tool:

```
cd $GOPATH/src/github.com/hyperledger/fabric
make configtxgen
```

The executable is placed in the `build/bin/configtxgen` directory of the Fabric source tree.

### All in one with docker images

A script will generate the configuration artifacts, spin up a local network, and drive the chaincode tests. Navigate to the `examples/e2e_cli` directory.

First, pull the images from Docker Hub for your Fabric network:

```shell
cd examples/e2e_cli
# make the script an executable
chmod +x download-dockerimages.sh
# run the script
./download-dockerimages.sh
```

Now run the all-in-one script:

```shell
./network_setup.sh up <channel-ID>
```

If you choose not to pass the `channel-ID` parameter, then your channel name will default to `mychannel`. In your terminal you will see the chaincode logs for the various commands being executed within the script.

When the script completes successfully, you should see the following message in your terminal:

```
===================== Query on PEER3 on channel 'mychannel' is successful =====================

===================== All GOOD, End-2-End execution completed =====================
```

#### Clean up

Shut down your network:

```
# make sure you're in the e2e_cli directory
docker rm -f $(docker ps -aq)
```

Next, execute a `docker images` command in your terminal to view the chaincode images. Remove these images:

```
docker images
docker rmi -f dev-peer3-mycc-1.0 dev-peer0-mycc-1.0 dev-peer2-mycc-1.0
docker images
```

Finally, delete the config artifacts. Navigate to the `crypto/orderer` directory and remove `orderer.block` and `channel.tx`.

### Configuration Transaction Generator

The [configtxgen](http://hyperledger-fabric.readthedocs.io/en/latest/configtxgen.html) tool is used to create two artifacts: Orderer **bootstrap block** and Fabric **channel configuration transaction**.

#### Run the `generateCfgTrx.sh` shell script

Make sure you are in the `e2e_cli` directory:

```shell
cd $GOPATH/src/github.com/hyperledger/fabric/examples/e2e_cli
# as mentioned above, the <channel-ID> parm is optional
./generateCfgTrx.sh <channel-ID>
```

The script generates two files - `orderer.block` and `channel.tx` and outputs them into the `crypto/orderer` directory.

### Start the network

Make sure you are in the `e2e_cli` directory. Then use docker-compose to spawn the network entities and kick off the embedded script:

```
CHANNEL_NAME=<channel-id> docker-compose up -d
```

If you created a unique channel name, be sure to pass in that parameter. Otherwise, pass in the default `mychannel` string. For example:

```
CHANNEL_NAME=mychannel docker-compose up -d
```

Execute a `docker ps` to view your active containers.

#### How do I see these transactions?

Check the logs for the CLI docker container:

```
docker logs -f cli
```

#### How can I see the chaincode logs?

Inspect the individual chaincode containers to see the separate transactions executed against each container:

```shell
docker logs dev-peer2-mycc-1.0
docker logs dev-peer0-mycc-1.0
docker logs dev-peer3-mycc-1.0
```

### Manually execute transactions

From your current working directory, kill your containers:

```
docker rm -f $(docker ps -aq)
```

Next, execute a `docker images` command in your terminal to view the chaincode images. Ensure you have the configuration artifacts. If you deleted them, run the shell script again:

```
./generateCfgTrx.sh <channel-id>
```

#### Modify the docker-compose file

Open the docker-compose file `docker-compose.yaml` and comment out the command to run `script.sh`. Navigate down to the `cli` image and place a `#` to the left of the command. For example:

```
working_dir: /opt/gopath/src/github.com/hyperledger/fabric/peer
# command: /bin/bash -c './scripts/script.sh ${CHANNEL_NAME}'
```

Save the file. Now restart your network:

```
# make sure you are in the e2e_cli directory where you docker-compose script resides
# add the appropriate CHANNEL_NAME parm
CHANNEL_NAME=mychannel docker-compose up -d
```

#### Command syntax

For the following `cli` commands against `PEER0` to work, you need to set the values for the four global environment variables, given below. Please make sure to override the values accordingly when calling commands against other peers and the orderer.

```
# Environment variables for PEER0
CORE_PEER_MSPCONFIGPATH=$GOPATH/src/github.com/hyperledger/fabric/peer/crypto/peer/peer0/localMspConfig
CORE_PEER_TLS_ROOTCERT_FILE=$GOPATH/src/github.com/hyperledger/fabric/peer/crypto/peer/peer0/localMspConfig/cacerts/peerOrg0.pem
```

These environment variables for each peer are defined in the supplied docker-compose file `docker-compose.yaml`.

#### Create channel

Exec into the `cli` container:

```
docker exec -it cli bash
```

Specify your channel name with the `-c` flag. Specify your channel configuration transaction with the `-f` flag. In this case it is `channel.tx`, however you can mount your own configuration transaction with a different name:

```
# the channel.tx and orderer.block are mounted in the crypto/orderer directory within your cli container
# as a result, we pass the full path for the file
peer channel create -o orderer0:7050 -c mychannel -f crypto/orderer/channel.tx --tls $CORE_PEER_TLS_ENABLED --cafile $GOPATH/src/github.com/hyperledger/fabric/peer/crypto/orderer/localMspConfig/cacerts/ordererOrg0.pem
```

Since the `channel create` command runs against the orderer, we need to override the four environment variables set before. So the above command in its entirety would be:

```
CORE_PEER_MSPCONFIGPATH=$GOPATH/src/github.com/hyperledger/fabric/peer/crypto/orderer/localMspConfig CORE_PEER_LOCALMSPID="OrdererMSP" peer channel create -o orderer0:7050 -c mychannel -f crypto/orderer/channel.tx --tls $CORE_PEER_TLS_ENABLED --cafile $GOPATH/src/github.com/hyperledger/fabric/peer/crypto/orderer/localMspConfig/cacerts/ordererOrg0.pem
```

**Note**: You will remain in the CLI container for the remainder of these manual commands.

#### Join channel

Join specific peers to the channel:

```
# By default, this joins PEER0 only
# the mychannel.block is also mounted in the crypto/orderer directory
peer channel join -b mychannel.block
```

This full command in its entirety would be:

```
CORE_PEER_MSPCONFIGPATH=$GOPATH/src/github.com/hyperledger/fabric/peer/crypto/peer/peer0/localMspConfig CORE_PEER_ADDRESS=peer0:7051 CORE_PEER_LOCALMSPID="Org0MSP" CORE_PEER_TLS_ROOTCERT_FILE=$GOPATH/src/github.com/hyperledger/fabric/peer/crypto/peer/peer0/localMspConfig/cacerts/peerOrg0.pem peer channel join -b mychannel.block
```

You can make other peers join the channel as necessary by making appropriate changes in the four environment variables.

#### Install chaincode onto a remote peer

Install the sample go code onto one of the four peer nodes:

```
# remember to preface this command with the global environment variables for the appropriate peer
peer chaincode install -n mycc -v 1.0 -p github.com/hyperledger/fabric/examples/chaincode/go/chaincode_example02
```

#### Instantiate chaincode and define the endorsement policy

Instantiate the chaincode on a peer. This will launch a chaincode container for the targeted peer and set the endorsement policy for the chaincode. In this snippet, we define the policy as requiring an endorsement from one peer node that is a part of either `Org0` or `Org1`. The command is:

```
# remember to preface this command with the global environment variables for the appropriate peer
# remember to pass in the correct string for the -C argument. The default is mychannel
peer chaincode instantiate -o orderer0:7050 --tls $CORE_PEER_TLS_ENABLED --cafile $GOPATH/src/github.com/hyperledger/fabric/peer/crypto/orderer/localMspConfig/cacerts/ordererOrg0.pem -C mychannel -n mycc -v 1.0 -p github.com/hyperledger/fabric/examples/chaincode/go/chaincode_example02 -c '{"Args":["init","a", "100", "b","200"]}' -P "OR ('Org0MSP.member','Org1MSP.member')"
```

#### Invoke chaincode

```
# remember to preface this command with the global environment variables for the appropriate peer
peer chaincode invoke -o orderer0:7050  --tls $CORE_PEER_TLS_ENABLED --cafile $GOPATH/src/github.com/hyperledger/fabric/peer/crypto/orderer/localMspConfig/cacerts/ordererOrg0.pem  -C mychannel -n mycc -c '{"Args":["invoke","a","b","10"]}'
```

#### Query chaincode

```
# remember to preface this command with the global environment variables for the appropriate peer
peer chaincode query -C mychannel -n mycc -c '{"Args":["query","a"]}'
```

The result of the above command should be as below:

```
Query Result: 90
```

### Use CouchDB

The state database can be switched from the default (goleveldb) to CouchDB. To use CouchDB instead of the default database (goleveldb), follow the same procedure in the [Prerequisites](#prerequisites) section, and additionally perform the following two steps to enable the CouchDB containers and associate each peer container with a CouchDB container:

1. Make the CouchDB image:

   ```
   # make sure you are in the fabric directory
   make couchdb
   ```
1. Open the `fabric/examples/e2e_cli/docker-compose.yaml` and uncomment all commented statements relating to CouchDB containers and peer container use of CouchDB. These instructions are also outlined in the same `docker-compose.yaml` file.

**chaincode_example02** should now work using CouchDB underneath.

Install, instantiate, invoke, and query **marbles02** chaincode by following the same general steps outlined above for **chaincode_example02** in the [Manually execute transactions](#manually-execute-transactions) section. After the [Join channel](#join-channel) step (**Error: Error getting endorser client channel: PEER_CONNECTIONERROR - Error trying to connect to local peer: grpc: timed out when dialing**), use the commands in the [Using CouchDB document](http://hyperledger-fabric.readthedocs.io/en/latest/getting_started.html#using-couchdb) to interact with the **marbles02** chaincode.

### 参考资料

1. [Setting up the development environment](http://hyperledger-fabric.readthedocs.io/en/latest/dev-setup/devenv.html) -- 安装 Fabric 开发环境的官方指南
1. [Getting Started](http://hyperledger-fabric.readthedocs.io/en/latest/getting_started.html) -- Fabric 入门指南
1. [How to Write Go Code](https://golang.org/doc/code.html) -- Google 官方开发文档
1. [Get Docker for Ubuntu](https://docs.docker.com/engine/installation/linux/ubuntu/)
1. [Install Docker Compose](https://docs.docker.com/compose/install/)
