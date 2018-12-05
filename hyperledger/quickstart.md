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


## 개발도구 설치
    $ sudo apt-get install gcc g++ make
    
## Python 설치
우분투 16.04 버전에는 Python 3.5.1이 설치됩니다. 

Fabric Node.js SDK는 Python 2.7를 사용해야 npm 설정이 잘 작동합니다. 

다음과 같이 2.7버전을 설치하고 버전을 확인합니다.

    $ sudo apt-get install python
    $ python --version


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
    $ docker image






    
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




# Fabric 네트워크 시작

네트워크 시작

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





