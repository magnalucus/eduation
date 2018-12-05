# 개발환경
OS : Ubuntu 16.04 LTS
Curl : 7.47.0
Docker : 18.09.0
Docker Compose : 1.8.0
Golang : 1.11.1
Node.js : 8.9
Python : 2.7

# 업데이트
$ sudo apt-get update

# Curl 설치
$ sudo apt-get install curl

# Docker, Docker compose 설치
(Docker의 버전이 오래된 경우 오류 발생가능. 기존의 구버전의  Docker를 삭제하고 재설치)
$ sudo apt-get remove docker docker-engine docker.io
$ curl -fsSL https://get.docker.com/ | sudo sh
$ sudo apt install docker-compose

# Docker 권한설정
Docker가 root 권한으로 설치되므로 일반 사용자도 Docker를 사용하기 위해서는 권한을 설정해야 합니다.
로그아웃 후 다시 로그인을 하면 권한이 적용됩니다.
$ sudo usermod -aG docker $USER
