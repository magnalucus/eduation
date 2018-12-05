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

