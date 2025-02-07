https://velog.io/@luckyprice1103/Netflix%EC%95%B1-%EC%BF%A0%EB%B2%84%EB%84%A4%ED%8B%B0%EC%8A%A4%EB%A1%9C-%EB%B0%B0%ED%8F%AC%ED%95%98%EA%B8%B0-aws-%ED%81%B4%EB%9D%BC%EC%9A%B0%EB%93%9C%EB%A5%BC-%ED%99%9C%EC%9A%A9%ED%95%98%EC%97%AC-CICD-%ED%8C%8C%EC%9D%B4%ED%94%84%EB%9D%BC%EC%9D%B8-%EA%B5%AC%EC%B6%95

![image](https://github.com/user-attachments/assets/e7487b4e-386a-413b-a9c7-1825276bf529)
![image](https://github.com/user-attachments/assets/9b9e1bd6-e2ec-4c12-ae57-35c9e385d6c8)
![image](https://github.com/user-attachments/assets/45c81d74-7e11-4c48-b5b0-0438f7a823f5)
![image](https://github.com/user-attachments/assets/66763b1e-3584-4072-9389-5be451decac4)
![image](https://github.com/user-attachments/assets/0ff3db5e-f43d-4b25-9865-56a754a627b6)
![image](https://github.com/user-attachments/assets/096c0fcb-c83c-48e0-9b8b-a5d84feacc45)
![image](https://github.com/user-attachments/assets/a212e3ab-a656-484d-9fea-580537636c21)
![image](https://github.com/user-attachments/assets/8873dc0f-60fb-43df-97d1-829f4eaf5394)




## 1단계: 초기 설정 및 배포
1. EC2 인스턴스 시작 (Ubuntu 22.04)
AWS에서 Ubuntu 22.04를 실행하는 EC2 인스턴스를 생성.
SSH를 통해 인스턴스에 연결.
ec2 프리티어를 사용해 봤지만 너무 느려지는 현상이 발생해서 m2.large를 사용

	

2. 코드 클론
모든 패키지를 업데이트한 후 예전에 만들어둔 넷플릭스 애플리케이션의 코드 저장소를 EC2 인스턴스로 클론.

```bash
git clone https://github.com/luckyPrice/Netflix.git
```

3. Docker 설치 및 컨테이너로 애플리케이션 실행
Docker를 설치하고 애플리케이션을 컨테이너로 실행.

```bash
sudo apt-get update
sudo apt-get install docker.io -y
sudo usermod -aG docker $USER
newgrp docker
sudo chmod 777 /var/run/docker.sock
docker build -t netflix .
docker run -d --name netflix -p 8081:80 netflix:latest
```
![](https://velog.velcdn.com/images/luckyprice1103/post/fab4f105-3432-4392-82e1-867e238f7cdd/image.png)![](https://velog.velcdn.com/images/luckyprice1103/post/1d989661-e14c-4e3b-8e54-7dbd8d63c480/image.png)![](https://velog.velcdn.com/images/luckyprice1103/post/f52d6441-e9c0-49f2-a6db-82a2867f85b3/image.png)
명령어 설명
docker run -d -p 8081:80 c25b42cb6f93에서:
-d: 컨테이너를 백그라운드(detached mode)에서 실행.
-p 8081:80: 호스트의 포트 8081을 컨테이너 내부의 포트 80에 매핑.
c25b42cb6f93: 실행할 도커 이미지를 지정하는 고유한 이미지 ID.

> 즉, c25b42cb6f93이라는 특정 이미지로부터 컨테이너를 생성하고, 해당 컨테이너를 백그라운드에서 실행하며, 호스트와 컨테이너 간 포트를 매핑해줌.
![](https://velog.velcdn.com/images/luckyprice1103/post/efc54b7f-cf7b-4a3e-b97e-40bd87e1d7ed/image.png)
참고로 보안그룹 인바운드를 통해서 포트를 열어주지 않으면 무한 로딩이 발생한다.

![](https://velog.velcdn.com/images/luckyprice1103/post/37c3aec2-c83c-4c4a-81ab-21ddac69054a/image.png)
주소는 정상적으로 잘 연결이 되었지만, api키를 발급 받지 않았기 때문에 에러가 발생.


TMDB API 키가 필요.

4. API 키 생성
TMDB 웹사이트에 접속해 계정을 생성하고 로그인합니다.
프로필 설정에서 "API" 섹션으로 이동하여 새 API 키를 생성.
API 키를 사용해 Docker 이미지를 다시 빌드.
```bash
docker build --build-arg TMDB_V3_API_KEY=<your-api-key> -t netflix .
```
명령어 설명

docker build
현재 디렉토리(마지막의 .)에 있는 Dockerfile을 기반으로 도커 이미지를 생성.
--build-arg TMDB_V3_API_KEY=<your-api-key>
  
--build-arg는 빌드 시 사용할 **빌드 인수(Build Argument)**를 전달하는 옵션.
여기서는 TMDB_V3_API_KEY라는 이름의 인수를 Dockerfile에 전달하고 있음. <your-api-key> 부분에는 실제 API 키 값이 들어가야 함. 이 값은 Dockerfile에서 ARG TMDB_V3_API_KEY로 정의된 변수와 연결되어, 빌드 과정에서 사용됨.
  
-t netflix
-t는 생성된 이미지에 태그(tag)를 붙이는 옵션입니다. 여기서는 이미지 이름을 netflix로 지정하고 있습니다.
  
. (현재 디렉토리)
마지막의 .은 Dockerfile이 위치한 디렉토리를 나타냄. 즉, 현재 디렉토리에서 Dockerfile을 찾아 이미지를 빌드한다는 의미.
![](https://velog.velcdn.com/images/luckyprice1103/post/75388128-8736-4b5f-bce5-9d9bb5edb0d9/image.png)
이젠 정상적으로 잘 작동이 된다....!

## 2단계: 보안
SonarQube 및 Trivy 설치
SonarQube와 Trivy를 설치하여 취약점을 스캔.

  
SonarQube
```bash
docker run -d --name sonar -p 9000:9000 sonarqube:lts-community
  ```
  ![](https://velog.velcdn.com/images/luckyprice1103/post/8089e9fa-d9c4-475e-a0e2-9838cc0dff98/image.png)

  Trivy
  
```bash
sudo apt-get install wget apt-transport-https gnupg lsb-release
wget -qO - https://aquasecurity.github.io/trivy-repo/deb/public.key | sudo apt-key add -
echo deb https://aquasecurity.github.io/trivy-repo/deb $(lsb_release -sc) main | sudo tee /etc/apt/sources.list.d/trivy.list
sudo apt-get update
sudo apt-get install trivy
```
  

## 3단계: CI/CD 설정
  
Jenkins 설치 및 자동화
Jenkins 설치:
bash
sudo apt update
sudo apt install fontconfig openjdk-17-jre
java -version
openjdk version "17.0.8" 2023-07-18
OpenJDK Runtime Environment (build 17.0.8+7-Debian-1deb12u1)
OpenJDK 64-Bit Server VM (build 17.0.8+7-Debian-1deb12u1, mixed mode, sharing)


#jenkins
sudo wget -O /usr/share/keyrings/jenkins-keyring.asc \
https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key
echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
/etc/apt/sources.list.d/jenkins.list > /dev/null
sudo apt-get update
sudo apt-get install jenkins
sudo systemctl start jenkins
sudo systemctl enable jenkins

 ![](https://velog.velcdn.com/images/luckyprice1103/post/f5cfe1d4-6b50-49e1-8a07-7dac9f6e000a/image.png)![](https://velog.velcdn.com/images/luckyprice1103/post/34077dc3-cc6a-463e-b740-f2874ddd2c36/image.png)

브라우저 새 탭에서 NodeIP:30007을 입력하면 애플리케이션이 실행 중인 것을 확인할 수 있음.



포트 30007에서 애플리케이션이 실행 된다.....!

7단계: 정리 작업 (Cleanup)
AWS EC2 인스턴스 정리:
더 이상 필요하지 않은 AWS EC2 인스턴스를 종료(Terminate).
