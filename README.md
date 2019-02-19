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
* ({Host}:8888) 같은 형태로 주피터 노트북에 접속 -> 접속 장애시 도메인 이름 대신 IP 주소 입력(http://{Host}:8888)
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
* (https://{Host}:8888) 같은 형태로 주피터 노트북에 접속 -> 접속 장애시 도메인 이름 대신 IP 주소 입력(https://{Host}:8888)
