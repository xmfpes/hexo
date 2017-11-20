---
title: 개발 서버 환경 구축
date: 2017-11-16 20:56:59
tags: Server
categories: 
- 기타
- Server
- Linux
---

### Ec2 원격 서버 개발환경 구축하기(Ubuntu)

![](/images/aws/aws.png)

- #### Create a New Sudo User


```
# adduser username
```

user를 추가하고, 추가한 계정에 sudo 권한을 실행할 수 있도록 권한 추가한다.  sudoers 파일을 수정해야 한다.

```
[ec2-user@ip-172-31-4-238 ~]$ sudo vi /etc/sudoers
```

sudoers 파일에 아래와 같이 작성해서 추가한다.

```
root ALL=(ALL:ALL) ALL
사용자명 ALL=(ALL:ALL) ALL
```

root ALL=(ALL:ALL) ALL 아래에 사용자명 ALL=(ALL:ALL) ALL를 추가해 준다.



패스워드 재 설정

```bash
# sudo passwd
```

사용자 전환

```bash
sudo - // root로 전환
sudo kyunam // kyunam 유저로 전환
```

- ### 언어 설정하기

```bash
# sudo locale-gen ko_KR.EUC-KR ko_KR.UTF-8
# sudo dpkg-reconfigure locales
```

ko_KR.UTF-8로 설정한다.

```bash
계정 Home 디렉토리(계정이 ubuntu라면 /home/ubuntu이다.)의 .bash_profile에 다음 설정 추가
LANG="ko_KR.UTF-8"
LANGUAGE="ko_KR:ko:en_US:en"
```

```bash
sudo vi .bash_profile
```

```bash
$ source .bash_profile
"$ env” 실행해 설정 확인
```

- ### Java 설치하기
  /home/ubuntu 경로에 자바를 설치한다.

 ```bash
sudo wget --header "Cookie: oraclelicense=accept-securebackup-cookie" http://download.oracle.com/otn-pub/java/jdk/8u151-b12/e758a0de34e24606bca991d704f6dcbf/jdk-8u151-linux-x64.tar.gz
 ```

압축만 풀면 설치는 완료된다.(tar -xvf 명령 사용)
```bash
tar -xvf jdk-8u151-linux-x64.tar.gz 
압축 해제

ln 명령을 활용해 symbolic link를 추가한다.(ln -s 원본링크파일)
ln -s jdk1.8.0_151 ~/java

계정 Home 디렉토리의 .bash_profile 파일에 JAVA_HOME/bin 디렉토리 path로 설정한다.
PATH=$PATH:~/java/bin 을 .bash_profile에 path를 추가하고 저장 후

source .bash_profile

java -version으로 확인한다.
```

다중 계정에서 위와 같이 java path가 적용이 안돼서 ubuntu 단일 계정에 java path를 적용했다.

```bash
다중 계정에서 위와 같은 Java 패스 설정이 안되서, ubuntu 계정으로 진행했다.

cd /home/ubuntu
sudo vim .bash_profile 

.bash_profile 내용
LANG="ko_KR.UTF-8"
LANGUAGE="ko_KR:ko:en_US:en"
export JAVA_HOME=/home/ubuntu/jdk1.8.0_151
PATH=$PATH:$JAVA_HOME/bin

soruce .bash_profile
```



- ### MYSQL 설치하기

```bash
sudo apt-cache search mysql-server // 검색
mysql-server - MySQL database server (metapackage depending on the latest version)
mysql-server-5.7 - MySQL database server binaries and system database setup
mysql-server-core-5.7 - MySQL database server binaries

sudo apt-get install mysql-server-5.7

mysql -uroot -ppassword
```

프로젝트에 쓰일 database도 생성해 주어야 한다.



- ### 프로젝트 가져와서 빌드하기


계정 home 디렉토리에 앞에서 fork한 github 저장소를 clone한다.

```bash
git clone https://github.com/xmfpes/jwp-trello-be.git

cd my-project

git checkout working-branch

./gradlew clean build
```

EC2의 inbound 규칙에서 8080 포트를 열어주고,

```bash
java -jar project-name.jar &
```

를 통해 서버를 실행하고, 로컬에서 EC2 서버가 접속 되는지 체크한다.



- ### 빌드 자동화하기

위에서 하나하나 해 줬던 설정들을, 쉘 스크립트를 통해 빌드 자동화를 해보자.

```bash
sudo vim deploy.sh
deploy.sh 스크립트 생성
```

```bash
#!/bin/bash
Bash/shell 임을 명시하는 코드를 첫 줄에 작성한다.
```

```bash
sh -x ./deploy.sh 
쉘 스크립트 실행 시 옵션을 줘서 디버깅을 할 수 있다.
```

```bash
#!/bin/bash
//쉘 스크립트 임을 첫줄에 알려준다.
LOG_PATH=/home/ubuntu/build
//log 데이터를 남길 디렉토리 경로
BUILD_PATH=/home/ubuntu/jwp-trello-be
//실제 build될 프로젝트 경로
C_TIME=$(date +%s)
//build되는 시점의 시간을 구한다.
LOG_DIR_PATH=${BUILD_PATH}/log/${C_TIME}/
//빌드된 결과물을, 시간에 따라 분류해서 저장할 디렉토리 생성
cd $BUILD_PATH
//실제 프로젝트 경로로 이동한다.
git pull
PROCESS_PID=`ps -ef | grep jwp | grep -v 'grep' | awk {'print $2'}`
kill -9 $PROCESS_PID
//현재 실행되고 있는 서버를 kill 한다. grep 명령어를 가진 프로세스는 제외 2번째 행에서 나온 PID값을 받아온다.
./gradlew clean build
if [ $? -eq 0 ];then
        mkdir -p $LOG_DIR_PATH
        mkdir -p $LOG_PATH
        cp -r ${BUILD_PATH}/build/libs/* $LOG_DIR_PATH
        cd $LOG_DIR_PATH
        BUILD_ID=dontKillMe java -Dserver.port=8000 -jar jwp-trello-be-0.0.1-SNAPSHOT.jar >> $LOG_PATH/trello.log.out 2>&1 &
//Jenkins를 통해 실행되는 프로세스의 작업들은 작업 끝남과 동시에 모두 종료된다. 서버는 계속 유지되어야 하므로 BUILD_ID=dontKIllme 옵션을 통해 서버를 유지시킨다.
else
        echo "build Failure!"
fi
```

기타 Shell 스크립트 문법들..

```bash
:.,%d
shell 안에 모든 코드 제거

u 
undo 기능

:%s/echo/dddd/g 
echo를 dddd로 바꿈
```

### 전체 쉘 스크립트 코드

```bash
#!/bin/bash
LOG_PATH=/home/ubuntu/build
BUILD_PATH=/home/ubuntu/jwp-trello-be
C_TIME=$(date +%s)
LOG_DIR_PATH=${BUILD_PATH}/log/${C_TIME}/
cd $BUILD_PATH
git pull
PROCESS_PID=`ps -ef | grep jwp | grep -v 'grep' | awk {'print $2'}`
kill -9 $PROCESS_PID
./gradlew clean build
if [ $? -eq 0 ];then
        mkdir -p $LOG_DIR_PATH
        mkdir -p $LOG_PATH
        cp -r ${BUILD_PATH}/build/libs/* $LOG_DIR_PATH
        cd $LOG_DIR_PATH
        BUILD_ID=dontKillMe java -Dserver.port=8000 -jar jwp-trello-be-0.0.1-SNAPSHOT.jar >> $LOG_PATH/trello.log.out 2>&1 &
else
        echo "build Failure!"
fi
```

이제 저 스크립트를 실행하면, 자동으로 빌드와 배포를 다시하게 된다.



### CI 환경 구축 - Jenkins

Jenkins 서버 설치를 하기 앞서, 톰캣을 설치한다.

```bash
sudo wget http://mirror.navercorp.com/apache/tomcat/tomcat-9/v9.0.1/bin/apache-tomcat-9.0.1.tar.gz
```

압축 풀기

```
rm -rf apache-tomcat-9.0.1.tar.gz
```

Jenkins .war 설치

```bash
sudo wget http://mirrors.jenkins.io/war-stable/latest/jenkins.war
```

Jenkins를 아파치의 경로로 옮긴다.
```bash
sudo cp jenkins.war apache-tomcat-9.0.1/webapps
```

톰캣 실행

```bash
cd bin
./startup.sh 
```

이제 실행된 톰캣에서 jenkins 세팅 페이지로 들어간다.

[ec2:8080/jenkins](/home/ubuntu/.jenkins/secrets/initialAdminPassword) 해당 경로로 들어가면 아래 경로에 생긴 패스워드를 입력하라는 창이 나온다.

```bash
/home/ubuntu/.jenkins/secrets/initialAdminPassword
```

위 경로에 생긴 패스워드를 입력하고 Install suggested plugins 클릭

가입하고 Jenkins를 시작할 수 있다.



이제 위에서 만든 쉘 스크립트를 젠킨스를 통해 실행해서 빌드하고, 배포하도록 설정해보자.



FreeStyle project 생성

- 소스 코드 관리 항목의 git을 선택하고 clone할 git 주소 입력

- Build 항목에서 Excute Shell을 선택한 후 빌드할 아래의 스크립트를 실행해 빌드 / 배포가 되는지 확인

```bash
sh /home/ubuntu/deploy.sh
```

빌드가 정상적으로 된다면, 이제 github에 변경이 있을 때 자동으로 빌드 / 배포를 하는 스크립트를 실행하도록 해보자.



#### Jenkins 자동 빌드 설정하기 - Github 변경과 연동

Jenkins 프로젝트 설정

- 빌드 유발 항목에서 GitHub hook trigger for GITScm polling 를 체크하고 저장한다.

Github 레파지토리 설정

- GitHub 레파지토리에서 Setting - integrations & services 탭 클릭
- add service - Jenkins(Github plugin) 선택 - 이걸 하기 앞서서 Jenkins에서 GitHub plugin을 설치하자
- Jenkins hook url에 아래와 같은 형식의 url을 추가한다.

```bash
http://{your-jenkins-url}/github-webhook/
```

이제 코드를 push하면, 자동으로 Jenkins가 빌드하게 된다.



#### Jenkins 빌드 후, Slack으로 빌드 알림 보내기

- **Slack 설정**

https://codesquad-members.slack.com/apps/

Jenkins CI을 검색 - Add Configuration - Channel 선택

Setup Instructions에서 Base Url과 Integration Token값을 받아온다.

```bash
Base Url:https://codesquad-members.slack.com/services/hooks/jenkins-ci/
Integration Token:your-token
```



- **Jenkins 프로젝트 설정**

빌드 후 조치에 Slack Notifications 설정 추가(Jenkins Slack Notification plugin 설치 먼저 해야 나타남)

고급 - Base Url / Integration Token을 입력하고, Project Channel에 알림을 할 채널을 입력한다.

```bash
Base Url:https://codesquad-members.slack.com/services/hooks/jenkins-ci/
Integration Token:your-token-value
Project Channel:jenkins
```

위 와 같이 직접 입력하는 방식은 보안에 문제가 있다고 하니 Integration Token Credential ID를 생성해서 활용하자.

Test Connection 후 정상 작동 한다면, 저장하고 테스트를 해보자.

