## 인스턴스 생성 및 시작
1. AWS EC2 관리 페이지로 이동: [EC2 관리 페이지](https://ap-northeast-2.console.aws.amazon.com/ec2/v2/home)
2. [인스턴스 시작] 버튼을 눌러 [Ubuntu Server 18.04 LTS (HVM), SSD Volume Type]로 인스턴스 생성
3. [t2.micro] 인스턴스 유형을 선택한 뒤 [검토 및 시작]
4. 키 페어를 이용하여 [인스턴스 시작]
## 인스턴스 접속
1. 키 페어 파일 준비
2. 키 페어에 적절한 권한 주기
> 리눅스: 다음의 명령어로 권한 설정
<pre>
chmod 400 {키 페어}
</pre>
> 윈도우: [속성] - [보안] - [고급] - [상속 사용 안 함] - [명시적 사용] - [관리자 제외하고 모든 그룹 삭제] - [모든 그룹에 대해서 '읽기 및 실행' 권한만 사용] - [관리자 권한으로 CMD 실행]
3. SSH 명령어로 서버 접속
<pre>
ssh -i "{키 페어}" {AWS 인스턴스 URL}
</pre>
## [부록] Jupyter Notebook 이용 방법
* 별도의 개발 리눅스 서버가 존재하는 경우 Jupyter Notebook 이용 가능. 실 서버에는 보안상 적용하지 않음.
* 파이썬, pip, jupyter 설치
```
sudo apt-get update
sudo apt-get install python3-pip
sudo pip3 install notebook
```
* jupyter 접속 비밀번호 설정
```
python3
>> from notebook.auth import passwd
>> passwd()
# 비밀번호 설정한 뒤에 SHA1 값 기록하기
```
* jupyter 환경 설정 파일 만들기
```
jupyter notebook --generate-config
sudo vi /home/ubuntu/.jupyter/jupyter_notebook_config.py
```
* jupyter 환경 설정하기
```
c = get_config()
c.NotebookApp.password = u'sha1:{해시 값}'
c.NotebookApp.ip = '{내부 아이피}'
c.NotebookApp.notebook_dir = '/home/empo'
# 내부 아이피로는 SSH로 접속했을 때 콘솔 창에 나오는 아이피를 입력하기
```
* jupyter 실행하기
```
sudo jupyter-notebook --allow-root
```
* [인바운드] - [편집] - [규칙 추가] - [사용자 지정 TCP] - [8888]번 포트 열기 - 허용 IP로 [0.0.0.0/0] 설정
* (http://{Host}:8888) 같은 형태로 주피터 노트북에 접속
* jupyter notebook 항상 실행 상태로 만들기
```
sudo jupyter-notebook --allow-root
# [Ctrl] + Z 입력하여 실행 종료하기
bg
disown -h
```
## [부록] Jupyter Notebook에 HTTPS 적용하기
* SSL 키 생성하기
```
cd /home/ubuntu
mkdir ssl
cd ssl
sudo openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout "cert.key" -out "cert.pem" -batch
```
* jupyter 환경 설정하기
```
sudo vi /home/ubuntu/.jupyter/jupyter_notebook_config.py
# 다음의 내용 입력하기
c.NotebookApp.certfile = u'/home/ubuntu/ssl/cert.pem'
c.NotebookApp.keyfile = u'/home/ubuntu/ssl/cert.key'
```
* (https://{Host}:8888) 같은 형태로 주피터 노트북에 접속
## [부록] Docker 설치하기 (Ubuntu 18.04)
```
# 시작하기 전에 볼륨 크기 넉넉하게 만들기
sudo apt update
sudo apt install apt-transport-https ca-certificates curl software-properties-common
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu bionic stable"
sudo apt update
apt-cache policy docker-ce
sudo apt install docker-ce
# 설치 완료 후에 도커 상태 확인
sudo systemctl status docker
# Hello World 이미지 다운로드 및 실행
docker pull hello-world
docker images
docker run hello-world
docker ps -a
docker rm [Container ID]
```
## [부록] Bitbucket과 Visual Studio Code를 이용한 Node.js 개발환경 구축하기
```
# [Bitbucket](https://bitbucket.org) 회원가입 및 로그인
# [대시보드] - [Create repository] - [docker-node] 이름 설정
# C:/Node Example 폴더 생성 및 Visual Studio Code로 연 뒤에 터미널 창 열기
git init
git remote add origin https://DongbinNa@bitbucket.org/DongbinNa/docker-node.git
npm init
# package.json 작성하기 (기본 설정 그대로)
{
  "name": "node-example",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "repository": {
    "type": "git",
    "url": "git+https://DongbinNa@bitbucket.org/DongbinNa/docker-node.git"
  },
  "author": "",
  "license": "ISC",
  "homepage": "https://bitbucket.org/DongbinNa/docker-node#readme"
}
# Express 설치하기
npm install --save express
# server.js 파일 생성하기
const app = require('express')();
app.get('/', (req, res, next) => {
    res.send('Hello World!');
});
app.listen(3000, () => {
    console.log('Server is running!');
;})
# 터미널에서 서버 실행하기
node server.js
# .gitignore 파일 생성하기
/node_modules
# 터미널에서 Git Push 진행하기
git add .
git commit -m "Initialize Project"
git push --set-upstream origin master -f
```
## [부록] PM2를 활용해 Node.js 서버 구동시키기
```
# EC2 인스턴스에 접속해 클론하기
curl -sL https://deb.nodesource.com/setup_8.x | sudo -E bash -
sudo apt-get install -y nodejs
sudo apt-get install -y build-essential
sudo npm install -g pm2
git clone https://DongbinNa@bitbucket.org/DongbinNa/docker-node.git
# 서버 구동시키기
cd docker-node
npm install
pm2 start server.js
# 서버를 끌 땐 다음과 같이 하기
pm2 stop server.js
```
## [부록] Docker File 작성 및 실행
```
# cd /home/ubuntu/docker-node
# sudo vi Dockerfile
FROM node
MAINTAINER Dongbin Na "ndb796@naver.com"
RUN npm install -g pm2 node-gyp
ENV NODE_ENV production
EXPOSE 3000
COPY ./ /docker-node
RUN npm install --prefix /docker-node
CMD ["pm2-docker", "docker-node/server.js"]
# Docker File 빌드하기
docker build -t node-example ./
# Docker 이미지 실행하기 (포트 포워딩 수행)
docker run -p 3000:3000 node-example
# [보안 그룹] - [3000번 포트 열기] - 접속하기
# 남은 용량을 확인할 때는 df -h
git add .
git commit -m "Add Dockerfile"
git push
```
## [부록] Docker File 자동 빌드
```
# [도커 허브](https://hub.docker.com/) 회원가입 및 로그인
# [Create Repository] - [이름으로 node-example 넣기] - [BitBucket 아이콘 클릭] - [Connect] - [docker-node 프로젝트 선택] - [build settings] - [Branch master 확인] - [Create & Build] - [Recent builds] 탭에서 실시간으로 Build가 이루어지고 있는 것을 확인
# 도커 허브에서 빌드가 완료되고 [Success]를 확인한 뒤에 이미지 가져와 실행
docker pull ndb796/node-example
docker run -p 3000:3000 ndb796/node-example
```
## [부록] 서버에 추가 기능 넣기
```
# AWS EC2 인스턴스에 접속하여 도커 컨테이너를 종료하기
git clone https://DongbinNa@bitbucket.org/DongbinNa/docker-node.git
cd docker-node
sudo apt-get install npm
sudo npm install -g pm2 node-gyp
npm install
# 이후에 server.js 소스코드 수정하기
const app = require('express')();
var randomstring = require("randomstring");
const fse = require('fs-extra');

require('data-utils');

app.get('/save', (req, res, next) => {
    var dt = new Date();
    fse.outputFile('/saved/' + randomstring.generate(), dt, err => {
        if(err) {
            res.send('Error!');
            console.log('Error!');
        } else {
            res.send('Saved!');
            console.log('Saved!');
        }
    });
});

app.get('/', (req, res, next) => {
    res.send('Hello World!');
});

app.listen(3000, () => {
    console.log('Server is running!');
;})
# .gitignore 설정
/node_modules
# 관련 모듈 설정
npm install --save randomstring
npm install --save data-utils
npm install --save fs-extra
# 서버 구동시키기
pm2 start server.js
# 테스트 결과 파일이 잘 저장되는 것을 확인
```
## [부록] Docker 재빌드
```
# 변경된 서버 프로그램 반영하기
git add .
git commit -m "Add File Write Module"
git push
# [Docker Hub 접속] - [Builds 탭] - Docker 빌드가 이루어지고 있는지 확인 - 필요에 따라서 [Trigger] - 빌드 
docker pull ndb796/node-example
docker run -p 3000:3000 -v /saved:/saved ndb796/node-example
# 테스트 결과 호스트에 파일이 잘 저장되는 것을 확인
# 다른 터미널 세션에서 해당 컨테이너에 접근할 수 있음
docker ps -a
docker exec -it {컨테이너 ID} /bin/bash
```
