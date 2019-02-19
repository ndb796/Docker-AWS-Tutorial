# EMPO AWS EC2 서버 구축 매뉴얼
## 인스턴스 생성 및 시작
1. AWS EC2 관리 페이지로 이동: [EC2 관리 페이지](https://ap-northeast-2.console.aws.amazon.com/ec2/v2/home)
2. [인스턴스 시작] 버튼을 눌러 [Ubuntu Server 18.04 LTS (HVM), SSD Volume Type]로 인스턴스 생성
3. [t2.micro] 인스턴스 유형을 선택한 뒤 [검토 및 시작]
4. 기존에 사용되던 EMPO 키 페어를 이용하여 [인스턴스 시작]
## 인스턴스 접속
1. 키 페어 파일 준비
2. 키 페어에 적절한 권한 주기
> 리눅스: 다음의 명령어로 권한 설정
<pre>
chmod 400 empo_dev.pem
</pre>
> 윈도우: [속성] - [보안] - [고급] - [상속 사용 안 함] - [명시적 사용] - [관리자 제외하고 모든 그룹 삭제] - [모든 그룹에 대해서 '읽기 및 실행' 권한만 사용] - [관리자 권한으로 CMD 실행]
3. SSH 명령어로 서버 접속
<pre>
ssh -i "empo_dev.pem" ubuntu@ec2-52-79-233-37.ap-northeast-2.compute.amazonaws.com
</pre>
