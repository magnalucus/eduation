# 사전준비

## 개발환경
* OS : Ubuntu 16.04 LTS
* Curl : 7.47.0
* Docker : 18.09.0
* Docker Compose : 1.8.0
* Golang : 1.11.1
* Node.js : 8.9
* Python : 2.7

## 업데이트
    $ sudo apt-get update

## Curl 설치
    $ sudo apt-get install curl

## Docker, Docker compose 설치
(Docker의 버전이 오래된 경우 오류 발생가능. 기존의 구버전의  Docker를 삭제하고 재설치)

    $ sudo apt-get remove docker docker-engine docker.io
    $ curl -fsSL https://get.docker.com/ | sudo sh
    $ sudo apt install docker-compose

## Docker 권한설정
Docker가 root 권한으로 설치되므로 일반 사용자도 Docker를 사용하기 위해서는 권한을 설정해야 합니다.

로그아웃 후 다시 로그인을 하면 권한이 적용됩니다.

    $ sudo usermod -aG docker $USER


## Docker 설치 확인
    $ docker version
    $ docker run hello-world
    
    
## Golang 설치
하이퍼렛저는 Go Lang으로 개발되었습니다. Go version 1.10이상이어야 합니다.

    $ cd
    $ wget https://dl.google.com/go/go1.11.1.linux-amd64.tar.gz
    $ tar zxvf go1.11.1.linux-amd64.tar.gz

Go를 설치하면 path를 설정해야 제대로 go를 사용할 수 있습니다. 로그인시 불러들이는 .profile 파일을 수정합니다.

    $ vi ~/.profile

    export GOROOT=$HOME/go
    export GOPATH=$HOME/workspace
    export PATH=$PATH:$GOPATH/bin:$GOROOT/bin

PATH가 적용되려면 로그아웃후 다시 로그인 해야합니다.


## Node.js 설치
Node.js를 이용한 하이퍼렛저 패브릭 SDK로 개발을 한다면 Node.js를 설치해야합니다.

버전은 8.9을 사용해야합니다. 9.x는 아직 지원하지 않습니다.

초기설정대로 apt install nodejs 로 설치하면 낮은 버전이 설치됩니다.

버전을 8.X 로 설치하기 위해서 PPA 를 등록하고 설치합니다.

    $ cd ~
    $ curl -sL https://deb.nodesource.com/setup_8.x -o nodesource_setup.sh
    $ sudo bash nodesource_setup.sh
    $ sudo apt-get install nodejs
    $ sudo npm install npm
    $ sudo chown -R $USER:$(id -gn $USER) /home/ubuntu/.config 


## 개발도구 설치
    $ sudo apt-get install gcc g++ make
    
## Python 설치
우분투 16.04 버전에는 Python 3.5.1이 설치됩니다. 

Fabric Node.js SDK는 Python 2.7를 사용해야 npm 설정이 잘 작동합니다. 

다음과 같이 2.7버전을 설치하고 버전을 확인합니다.

    $ sudo apt-get install python
    $ python --version


***

# Fabric 빌드

# 소스를 받을 폴더 만들기
    $ mkdir -p $GOPATH/src/github.com/hyperledger 
    $ cd $GOPATH/src/github.com/hyperledger/

## 소스 받기
    $ git clone https://github.com/hyperledger/fabric
    $ git clone https://github.com/hyperledger/fabric-ca

## Fabric 빌드하기
    $ cd $GOPATH/src/github.com/hyperledger/fabric
    $ make native docker

## Fabric-ca 빌드하기
    $ cd $GOPATH /src/github.com/hyperledger/fabric-ca
    $ make docker


## 빌드된 이미지 확인하기
    $ docker images



***    

# Fabric 실습

## 이미지 받기
    $ cd
    $ curl -sSL http://bit.ly/2ysbOFE | bash -s 1.3.0


## 인증서 설치

first-network로 이동

    $ cd ~/fabric-samples/first-network/

설정파일 : ~/fabric-samples/first-network/crypto-config.yaml

    $ vi ~/fabric-samples/first-network/crypto-config.yaml

인증서 생성

    $ ../bin/cryptogen generate --config=./crypto-config.yaml


결과

    org1.example.com
    org2.example.com


## Orderer Genesis Block 생성

설정파일 

    $ vi ~/fabric-samples/first-network/configtx.yaml


변수설정

    $ export FABRIC_CFG_PATH=$PWD


Generating Orderer Genesis block

    $ ../bin/configtxgen -profile TwoOrgsOrdererGenesis -outputBlock ./channel-artifacts/genesis.block

디렉토리 channel-artifacts에 genesis.block 이 생성됨

결과

    2018-12-05 19:21:56.301 EDT [common/tools/configtxgen] main -> INFO 001 Loading configuration
    2018-12-05 19:21:56.309 EDT [common/tools/configtxgen] doOutputBlock -> INFO 002 Generating genesis block
    2018-12-05 19:21:56.309 EDT [common/tools/configtxgen] doOutputBlock -> INFO 003 Writing genesis block
    
## Channel configuration transaction 생성

변수설정

    $ export CHANNEL_NAME=mychannel

Generating channel configuration transaction 'channel.tx’

    $ ../bin/configtxgen -profile TwoOrgsChannel -outputCreateChannelTx ./channel-artifacts/channel.tx -channelID $CHANNEL_NAME

디렉토리 channel-artifacts에 channel.tx (channel configuration transaction)가 channel.tx 생성됨

결과

    2018-12-05 09:39:41.270 UTC [common/tools/configtxgen] main -> INFO 001 Loading configuration
    2018-12-05 09:39:41.294 UTC [common/tools/configtxgen] doOutputChannelCreateTx -> INFO 002 Generating new channel configtx
    2018-12-05 09:39:41.295 UTC [common/tools/configtxgen] doOutputChannelCreateTx -> INFO 003 Writing new channel tx
    

## Anchor peer update

변수설정
    $ export CHANNEL_NAME=mychannel

Generating anchor peer update for Org1MSP

    $ ../bin/configtxgen -profile TwoOrgsChannel -outputAnchorPeersUpdate ./channel-artifacts/Org1MSPanchors.tx -channelID $CHANNEL_NAME -asOrg Org1MSP

결과

    2018-12-05 09:42:01.953 UTC [common/tools/configtxgen] main -> INFO 001 Loading configuration
    2018-12-05 09:42:01.977 UTC [common/tools/configtxgen] doOutputAnchorPeersUpdate -> INFO 002 Generating anchor peer update
    2018-12-05 09:42:01.978 UTC [common/tools/configtxgen] doOutputAnchorPeersUpdate -> INFO 003 Writing anchor peer update


Generating anchor peer update for Org1MSP

    $ ../bin/configtxgen -profile TwoOrgsChannel -outputAnchorPeersUpdate ./channel-artifacts/Org2MSPanchors.tx -channelID $CHANNEL_NAME -asOrg Org2MSP

결과

    2018-12-05 09:42:07.568 UTC [common/tools/configtxgen] main -> INFO 001 Loading configuration
    2018-12-05 09:42:07.591 UTC [common/tools/configtxgen] doOutputAnchorPeersUpdate -> INFO 002 Generating anchor peer update
    2018-12-05 09:42:07.591 UTC [common/tools/configtxgen] doOutputAnchorPeersUpdate -> INFO 003 Writing anchor peer update

디렉토리 channel-artifacts에 Org1MSPanchors.tx  Org2MSPanchors.tx 가 생성됨


***


# Fabric 네트워크 시작 

Fabric 네트워크 시작

    $ docker-compose -f docker-compose-cli.yaml up -d
    
결과

    Creating network "net_byfn" with the default driver
    Creating volume "net_peer0.org2.example.com" with default driver
    Creating volume "net_peer1.org2.example.com" with default driver
    Creating volume "net_peer1.org1.example.com" with default driver
    Creating volume "net_peer0.org1.example.com" with default driver
    Creating volume "net_orderer.example.com" with default driver
    Creating peer0.org1.example.com
    Creating peer0.org2.example.com
    Creating orderer.example.com
    Creating peer1.org2.example.com
    Creating peer1.org1.example.com
    Creating cli
    
    
이미지 확인

    $ docker ps

결과

    CONTAINER ID        IMAGE                               COMMAND             CREATED             STATUS              PORTS                                              NAMES
    8f5103e26d79        hyperledger/fabric-tools:latest     "/bin/bash"         49 seconds ago      Up 48 seconds                                                          cli
    35cfe9c7b062        hyperledger/fabric-peer:latest      "peer node start"   54 seconds ago      Up 51 seconds       0.0.0.0:8051->7051/tcp, 0.0.0.0:8053->7053/tcp     peer1.org1.example.com
    1ef857b24afc        hyperledger/fabric-peer:latest      "peer node start"   54 seconds ago      Up 49 seconds       0.0.0.0:10051->7051/tcp, 0.0.0.0:10053->7053/tcp   peer1.org2.example.com
    d85a5bcd4855        hyperledger/fabric-orderer:latest   "orderer"           54 seconds ago      Up 50 seconds       0.0.0.0:7050->7050/tcp                             orderer.example.com
    f36dad7aa063        hyperledger/fabric-peer:latest      "peer node start"   54 seconds ago      Up 52 seconds       0.0.0.0:9051->7051/tcp, 0.0.0.0:9053->7053/tcp     peer0.org2.example.com
    15abb3e17781        hyperledger/fabric-peer:latest      "peer node start"   54 seconds ago      Up 52 seconds       0.0.0.0:7051->7051/tcp, 0.0.0.0:7053->7053/tcp     peer0.org1.example.com




## docker cli 환경변수

### peer0.org1 환경변수

    export CHANNEL_NAME=mychannel
    CORE_PEER_MSPCONFIGPATH=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org1.example.com/users/Admin@org1.example.com/msp
    CORE_PEER_ADDRESS=peer0.org1.example.com:7051
    CORE_PEER_LOCALMSPID="Org1MSP"
    CORE_PEER_TLS_ROOTCERT_FILE=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls/ca.crt


### peer1.org1 환경변수

    export CHANNEL_NAME=mychannel
    CORE_PEER_MSPCONFIGPATH=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org1.example.com/users/Admin@org1.example.com/msp
    CORE_PEER_ADDRESS=peer1.org1.example.com:7051
    CORE_PEER_LOCALMSPID="Org1MSP"
    CORE_PEER_TLS_ROOTCERT_FILE=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org1.example.com/peers/peer1.org1.example.com/tls/ca.crt


### peer0.org2 환경변수

    export CHANNEL_NAME=mychannel
    CORE_PEER_MSPCONFIGPATH=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org2.example.com/users/Admin@org2.example.com/msp
    CORE_PEER_ADDRESS=peer0.org2.example.com:7051
    CORE_PEER_LOCALMSPID="Org2MSP"
    CORE_PEER_TLS_ROOTCERT_FILE=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org2.example.com/peers/peer0.org2.example.com/tls/ca.crt


### peer1.org2 환경변수

    export CHANNEL_NAME=mychannel
    CORE_PEER_MSPCONFIGPATH=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org2.example.com/users/Admin@org2.example.com/msp
    CORE_PEER_ADDRESS=peer1.org2.example.com:7051
    CORE_PEER_LOCALMSPID="Org2MSP"
    CORE_PEER_TLS_ROOTCERT_FILE=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org2.example.com/peers/peer1.org2.example.com/tls/ca.crt



## Fabric 실습
Fabric cli 컨테이너로 접속

    $ docker exec -it cli bash


결과

    root@8f5103e26d79:/opt/gopath/src/github.com/hyperledger/fabric/peer#

이 컨테이너에 처음 접속한 것이므로 초기상태임. 새로운 프로그램이나 설정들은 새로 해야 함.

환경변수설정

    $ export CHANNEL_NAME=mychannel


## Fabric 채널 생성

### peer0.org1 에서 mychannel 생성

    export CHANNEL_NAME=mychannel
    CORE_PEER_MSPCONFIGPATH=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org1.example.com/users/Admin@org1.example.com/msp
    CORE_PEER_ADDRESS=peer0.org1.example.com:7051
    CORE_PEER_LOCALMSPID="Org1MSP"
    CORE_PEER_TLS_ROOTCERT_FILE=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls/ca.crt
    peer channel create -o orderer.example.com:7050 -c $CHANNEL_NAME -f ./channel-artifacts/channel.tx --tls --cafile /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem





## 피어를 채널에 참여시킴

### peer0.org1 을 mychannel에 참여

    export CHANNEL_NAME=mychannel
    CORE_PEER_MSPCONFIGPATH=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org1.example.com/users/Admin@org1.example.com/msp
    CORE_PEER_ADDRESS=peer0.org1.example.com:7051
    CORE_PEER_LOCALMSPID="Org1MSP"
    CORE_PEER_TLS_ROOTCERT_FILE=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls/ca.crt
    peer channel join -b $CHANNEL_NAME.block


결과

    2018-12-05 11:00:24.809 UTC [channelCmd] InitCmdFactory -> INFO 001 Endorser and orderer connections initialized
    2018-12-05 11:00:24.930 UTC [channelCmd] executeJoin -> INFO 002 Successfully submitted proposal to join channel


### peer1.org1 을 mychannel에 참여


    export CHANNEL_NAME=mychannel
    CORE_PEER_MSPCONFIGPATH=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org1.example.com/users/Admin@org1.example.com/msp
    CORE_PEER_ADDRESS=peer1.org1.example.com:7051
    CORE_PEER_LOCALMSPID="Org1MSP"
    CORE_PEER_TLS_ROOTCERT_FILE=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org1.example.com/peers/peer1.org1.example.com/tls/ca.crt
    peer channel join -b $CHANNEL_NAME.block
    
    
### peer0.org2 을 mychannel에 참여

    export CHANNEL_NAME=mychannel
    CORE_PEER_MSPCONFIGPATH=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org2.example.com/users/Admin@org2.example.com/msp
    CORE_PEER_ADDRESS=peer0.org2.example.com:7051
    CORE_PEER_LOCALMSPID="Org2MSP"
    CORE_PEER_TLS_ROOTCERT_FILE=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org2.example.com/peers/peer0.org2.example.com/tls/ca.crt
    peer channel join -b $CHANNEL_NAME.block



### peer1.org2 을 mychannel에 참여


    export CHANNEL_NAME=mychannel
    CORE_PEER_MSPCONFIGPATH=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org2.example.com/users/Admin@org2.example.com/msp
    CORE_PEER_ADDRESS=peer1.org2.example.com:7051
    CORE_PEER_LOCALMSPID="Org2MSP"
    CORE_PEER_TLS_ROOTCERT_FILE=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org2.example.com/peers/peer1.org2.example.com/tls/ca.crt
    peer channel join -b $CHANNEL_NAME.block    


## Anchor Peer Update

모든 피어가 채널에 참여했으면 각 조직(Org1, Org2)별로 Anchor 피어 설정을 하며, Peer0.org1, Peer0.org2를 Anchor 피어로 업데이트한다

### peer0.org1 Anchor Peer 설정

    export CHANNEL_NAME=mychannel
    CORE_PEER_MSPCONFIGPATH=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org1.example.com/users/Admin@org1.example.com/msp
    CORE_PEER_ADDRESS=peer0.org1.example.com:7051
    CORE_PEER_LOCALMSPID="Org1MSP"
    CORE_PEER_TLS_ROOTCERT_FILE=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls/ca.crt
    peer channel update -o orderer.example.com:7050 -c $CHANNEL_NAME -f ./channel-artifacts/Org1MSPanchors.tx --tls --cafile /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem



결과

    2018-12-05 11:13:02.551 UTC [channelCmd] InitCmdFactory -> INFO 001 Endorser and orderer connections initialized
    2018-12-05 11:13:02.565 UTC [channelCmd] update -> INFO 002 Successfully submitted channel update


### peer0.org2 Anchor Peer 설정
    export CHANNEL_NAME=mychannel
    CORE_PEER_MSPCONFIGPATH=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org2.example.com/users/Admin@org2.example.com/msp
    CORE_PEER_ADDRESS=peer0.org2.example.com:7051
    CORE_PEER_LOCALMSPID="Org2MSP"
    CORE_PEER_TLS_ROOTCERT_FILE=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org2.example.com/peers/peer0.org1.example.com/tls/ca.crt
    peer channel update -o orderer.example.com:7050 -c $CHANNEL_NAME -f ./channel-artifacts/Org2MSPanchors.tx --tls --cafile /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem
    

## Chaincode 인스톨과 초기화
체인코드를 사용하기 위해서 체인코드를 인스톨하고 초기화한다. 

peer의 명령어 옵션은 체인코드를 설치하기 위한 install, 데이터를 쓰기 위한 invoke, 데이터를 조회하는 query가 있다.

peer0.org1 에 체인코드 인스톨

    CORE_PEER_MSPCONFIGPATH=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org1.example.com/users/Admin@org1.example.com/msp
    CORE_PEER_ADDRESS=peer0.org1.example.com:7051
    CORE_PEER_LOCALMSPID="Org1MSP"
    CORE_PEER_TLS_ROOTCERT_FILE=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls/ca.crt
    peer chaincode install -n mycc -v 1.0 -p github.com/chaincode/chaincode_example02/go



peer0.org2 에 체인코드 인스톨

    CORE_PEER_MSPCONFIGPATH=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org2.example.com/users/Admin@org2.example.com/msp
    CORE_PEER_ADDRESS=peer0.org2.example.com:7051
    CORE_PEER_LOCALMSPID="Org2MSP"
    CORE_PEER_TLS_ROOTCERT_FILE=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org2.example.com/peers/peer0.org2.example.com/tls/ca.crt
    peer chaincode install -n mycc -v 1.0 -p github.com/chaincode/chaincode_example02/go


### chaincode_example02.go 
https://github.com/hyperledger/fabric-samples/blob/release-1.3/chaincode/chaincode_example02/go/chaincode_example02.go


## Chaincode 초기화
peer0.org1에서 체인코드를 초기화한다.

    export CHANNEL_NAME=mychannel
    CORE_PEER_MSPCONFIGPATH=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org1.example.com/users/Admin@org1.example.com/msp
    CORE_PEER_ADDRESS=peer0.org1.example.com:7051
    CORE_PEER_LOCALMSPID="Org1MSP"
    CORE_PEER_TLS_ROOTCERT_FILE=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls/ca.crt
    peer chaincode instantiate -o orderer.example.com:7050 --tls $CORE_PEER_TLS_ENABLED --cafile /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem -C $CHANNEL_NAME -n mycc -v 1.0 -c '{"Args":["init","a", "100", "b","200"]}' -P "AND ('Org1MSP.member','Org2MSP.member')"


결과

    2018-12-05 11:51:13.950 UTC [chaincodeCmd] checkChaincodeCmdParams -> INFO 001 Using default escc
    2018-12-05 11:51:13.950 UTC [chaincodeCmd] checkChaincodeCmdParams -> INFO 002 Using default vscc


## Chaincode-level Endorsement 정책
Endorsement 정책

-P 옵션 : Endorsement 정책. 

"AND('Org1.member', 'Org2.member’)”

AND 조건 : Org1 멤버중의 하나 && Org2 멤버중의 하나

* "AND('Org1.peer', 'Org2.peer’)” : AND 조건 : Org1 Peer 중의 하나 && Org2 Peer중의 하나
* OR('Org1.member', 'Org2.member’) : OR 조건 : Org1 멤버중의 하나 || Org2 멤버중의 하나
* OR(   AND('Org1.member', 'Org2.member’), AND('Org1.member', 'Org3.member’), AND('Org2.member', 'Org3.member’)  )


|옵션|설명|
|-----|-----|
| 'Org0.admin’ |Org0 MSP의 어드민중 아무나|
| 'Org1.member’|Org1 MSP의 멤버중 아무나|
| 'Org1.client’|Org1 MSP 클라이언트중 아무나|
| 'Org1.peer’|Org1 MSP 피어중 아무나|


## 초기화 결과 Query

    export CHANNEL_NAME=mychannel
    CORE_PEER_MSPCONFIGPATH=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org1.example.com/users/Admin@org1.example.com/msp
    CORE_PEER_ADDRESS=peer0.org1.example.com:7051
    CORE_PEER_LOCALMSPID="Org1MSP"
    CORE_PEER_TLS_ROOTCERT_FILE=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls/ca.crt
    peer chaincode query -C $CHANNEL_NAME -n mycc -c '{"Args":["query","a"]}'


결과

    100
    
    
## Invoke
### a의 10을 b로 이체하는 체인코드 invoke

    export CHANNEL_NAME=mychannel
    CORE_PEER_MSPCONFIGPATH=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org1.example.com/users/Admin@org1.example.com/msp
    CORE_PEER_ADDRESS=peer0.org1.example.com:7051
    CORE_PEER_LOCALMSPID="Org1MSP"
    CORE_PEER_TLS_ROOTCERT_FILE=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls/ca.crt
    peer chaincode invoke -o orderer.example.com:7050 --tls true --cafile /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem -C $CHANNEL_NAME -n mycc --peerAddresses peer0.org1.example.com:7051 --tlsRootCertFiles /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls/ca.crt --peerAddresses peer0.org2.example.com:7051 --tlsRootCertFiles /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org2.example.com/peers/peer0.org2.example.com/tls/ca.crt -c '{"Args":["invoke","a","b","10"]}'


결과

    2018-12-05 12:22:01.254 UTC [chaincodeCmd] chaincodeInvokeOrQuery -> INFO 001 Chaincode invoke successful. result: status:200


## Query 확인

### Invoke의 결과로 a에 90이 저장됐는지 확인.

    export CHANNEL_NAME=mychannel
    CORE_PEER_MSPCONFIGPATH=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org1.example.com/users/Admin@org1.example.com/msp
    CORE_PEER_ADDRESS=peer0.org1.example.com:7051
    CORE_PEER_LOCALMSPID="Org1MSP"
    CORE_PEER_TLS_ROOTCERT_FILE=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls/ca.crt
    peer chaincode query -C $CHANNEL_NAME -n mycc -c '{"Args":["query","a"]}'

결과

    900


    peer chaincode query -C $CHANNEL_NAME -n mycc -c '{"Args":["query","a"]}'

결과

    210


### 다른 피어, peer1.org2에 원장이 복제되었는지 확인

### peer1.org2에 체인코드 인스톨
    export CHANNEL_NAME=mychannel
    CORE_PEER_MSPCONFIGPATH=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org2.example.com/users/Admin@org2.example.com/msp
    CORE_PEER_ADDRESS=peer1.org2.example.com:7051
    CORE_PEER_LOCALMSPID="Org2MSP"
    CORE_PEER_TLS_ROOTCERT_FILE=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org2.example.com/peers/peer1.org2.example.com/tls/ca.crt
    peer chaincode install -n mycc -v 1.0 -p github.com/chaincode/chaincode_example02/go

Query

    peer chaincode query -C $CHANNEL_NAME -n mycc -c '{"Args":["query","a"]}'


결과

    90
    
    
***

# Hyperledger Explorer

## 사전설치

nodejs 8.14.x (9.x 버전은 지원하지 않음), PostgreSQL 9.5 or greater, Jq[https://stedolan.github.io/jq/]

    $ sudo apt install -y postgresql jq


### Hyperledger Explorer 설치

    $ cd
    $ git clone -b release-3.7 https://github.com/hyperledger/blockchain-explorer


### postgresql 접속 설정

    $ vi ~/blockchain-explorer/app/explorerconfig.json


### DB 생성

    $ cd ~/blockchain-explorer/app/persistence/fabric/postgreSQL/db
    $ ./createdb.sh


### DB 생성확인

    $ sudo -u postgres psql
    postgres=# \l


### 접속종료

    postgres-# \q


### Explorer에 인증서 경로 지정

    $ vi ~/blockchain-explorer/app/platform/fabric/config.json
    > :%s /fabric-path/\/home\/ubuntu/g

### 오류수정

    $ vi ~/blockchain-explorer/client/src/components/View/LandingPage.spec.js
    > getBlockActivity: jest.fn(), // 추가

### Explorer 빌드
터미널을 하나 더 열고 아래를 실행한다.

    $ cd ~/blockchain-explorer
    $ npm install
    $ cd ~/blockchain-explorer/client/
    $ npm install
    $ npm test -- -u --coverage
    $ npm run build
