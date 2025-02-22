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

  
  
Jenkins 플러그인 설치:
Eclipse Temurin Installer, SonarQube Scanner, NodeJs Plugin, Email Extension Plugin 등을 설치.
  ![](https://velog.velcdn.com/images/luckyprice1103/post/ecb98143-641c-4345-9e79-bcb8f6f13c31/image.png)


  
  
  
  
CI/CD 파이프라인 구성:
Jenkinsfile 예제:
  
  
```
pipeline {
    agent any
    tools {
        jdk 'jdk17'
        nodejs 'node16'
    }
    environment {
        SCANNER_HOME = tool 'sonar-scanner'
    }
    stages {
        stage('clean workspace') {
            steps {
                cleanWs()
            }
        }
        stage('Checkout from Git') {
            steps {
                git branch: 'main', url: 'https://github.com/luckyPrice/Netflix.git'
            }
        }
        stage("Sonarqube Analysis") {
            steps {
                withSonarQubeEnv('sonar-server') {
                    sh '''$SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=Netflix \
                    -Dsonar.projectKey=Netflix'''
                }
            }
        }
        stage("quality gate") {
            steps {
                script {
                    waitForQualityGate abortPipeline: false, credentialsId: 'Sonar-token'
                }
            }
        }
        stage('Install Dependencies') {
            steps {
                sh "npm install"
            }
        }
    }
}
```
![](https://velog.velcdn.com/images/luckyprice1103/post/f2dcceed-20c6-49e7-ba7c-0bb1dc8de420/image.png)

![](https://velog.velcdn.com/images/luckyprice1103/post/485811d8-487f-4b81-8122-1f4647dcd4f0/image.png)
> 전체적인 흐름
워크스페이스 정리 → Git에서 코드 가져오기 → SonarQube로 코드 품질 분석 → 품질 게이트 확인 → 의존성 설치
이 파이프라인은 DevSecOps 관점에서 코드 품질 관리(SonarQube)를 포함하고 있으며, 이후 단계에서 애플리케이션 빌드, 테스트, 배포 등의 작업으로 확장될 수 있음.
 
  
 
  Jenkins에 Dependency-Check 및 Docker 도구 설치

  
  1. Dependency-Check 플러그인 설치
Jenkins 웹 인터페이스에서 **"Dashboard"**로 이동.
**"Manage Jenkins" → "Manage Plugins"**로 이동.
"Available" 탭을 클릭하고, **"OWASP Dependency-Check"**를 검색.
"OWASP Dependency-Check" 플러그인 옆의 체크박스를 선택한 후, "Install without restart" 버튼을 클릭하여 설치.
  
  owasp dependency-check
  ![](https://velog.velcdn.com/images/luckyprice1103/post/c94fdc2c-e420-4758-83cf-5b22072f4d75/image.png)


 https://plugins.jenkins.io/dependency-check-jenkins-plugin/
사용 사례
CI/CD 파이프라인에서 의존성 보안 검사를 자동화하여 소프트웨어의 안전성을 유지.
프로젝트의 보안 트렌드를 시각화하고, 시간이 지남에 따라 개선 여부를 모니터링.
취약점이 발견되었을 때 빌드를 중단하거나 경고 상태로 설정해 문제 해결을 유도.
  
  
>  Log4j와 같은 오픈 소스 라이브러리는 편리하지만, 보안 취약점이 발생할 경우 큰 위험을 초래할 수 있습니다. Dependency-Check는 이러한 위험을 사전에 탐지하고 완화할 수 있는 필수적인 도구로, 특히 DevSecOps 환경에서 중요한 역할을 합니다. 이를 통해 개발자는 안전한 소프트웨어를 구축하고 배포할 수 있음.
  
2. Dependency-Check 도구 구성
Dependency-Check 플러그인을 설치한 후, 도구를 구성해야 함.
**"Dashboard" → "Manage Jenkins" → "Global Tool Configuration"**으로 이동합니다.
"OWASP Dependency-Check" 섹션을 찾음.
도구 이름(예: "DP-Check")을 추가.
설정을 저장합니다.
  

3. Docker 도구 및 Docker 플러그인 설치
Jenkins 웹 인터페이스에서 **"Dashboard"**로 이동.
**"Manage Jenkins" → "Manage Plugins"**로 이동.
"Available" 탭을 클릭하고, **"Docker"**를 검색.
다음 Docker 관련 플러그인을 선택:
Docker
Docker Commons
Docker Pipeline
Docker API
docker-build-step
"Install without restart" 버튼을 클릭하여 이 플러그인들을 설치.
 
  ![](https://velog.velcdn.com/images/luckyprice1103/post/5c8a7b23-3185-465d-aafb-7402182fcf43/image.png)
  
4. DockerHub 자격 증명 추가
DockerHub 자격 증명을 안전하게 관리하려면 다음 단계를 따라야함:
**"Dashboard" → "Manage Jenkins" → "Manage Credentials"**로 이동.
"System", 그다음 **"Global credentials (unrestricted)"**를 클릭.
왼쪽의 **"Add Credentials"**를 클릭.
자격 증명 종류로 **"Secret text"**를 선택.
DockerHub 자격 증명(사용자 이름 및 비밀번호)을 입력하고, 자격 증명 ID(예: "docker")를 지정.
OK 버튼을 눌러 DockerHub 자격 증명을 저장.
  
![](https://velog.velcdn.com/images/luckyprice1103/post/2ce9f72a-6973-4dac-bcb6-6eb7aac60ef5/image.png)

  
  
  
## 4단계: 모니터링
  
> Prometheus와 Grafana 설치:
  
애플리케이션을 모니터링하기 위해 Prometheus와 Grafana를 설정합니다.
1. Prometheus 설치
Prometheus 전용 Linux 사용자 생성 및 다운로드:
```bash
sudo useradd --system --no-create-home --shell /bin/false prometheus
wget https://github.com/prometheus/prometheus/releases/download/v2.47.1/prometheus-2.47.1.linux-amd64.tar.gz
```

2. Prometheus 파일 추출, 이동 및 디렉토리 생성:
```bash
tar -xvf prometheus-2.47.1.linux-amd64.tar.gz
cd prometheus-2.47.1.linux-amd64/
sudo mkdir -p /data /etc/prometheus
sudo mv prometheus promtool /usr/local/bin/
sudo mv consoles/ console_libraries/ /etc/prometheus/
sudo mv prometheus.yml /etc/prometheus/prometheus.yml
```

3. 디렉토리 소유권 설정:
```bash
sudo chown -R prometheus:prometheus /etc/prometheus/ /data/
```

4. Prometheus용 systemd 유닛 구성 파일 생성:
bash
sudo nano /etc/systemd/system/prometheus.service

아래 내용을 추가:
```
text
[Unit]
Description=Prometheus
Wants=network-online.target
After=network-online.target
StartLimitIntervalSec=500
StartLimitBurst=5

[Service]
User=prometheus
Group=prometheus
Type=simple
Restart=on-failure
RestartSec=5s
ExecStart=/usr/local/bin/prometheus \
  --config.file=/etc/prometheus/prometheus.yml \
  --storage.tsdb.path=/data \
  --web.console.templates=/etc/prometheus/consoles \
  --web.console.libraries=/etc/prometheus/console_libraries \
  --web.listen-address=0.0.0.0:9090 \
  --web.enable-lifecycle
  
[Install]
WantedBy=multi-user.target
  ```

5. Prometheus 활성화 및 시작:
  
```bash
sudo systemctl enable prometheus
sudo systemctl start prometheus
```

6. Prometheus 상태 확인:

```bash
sudo systemctl status prometheus
```
  
 ![](https://velog.velcdn.com/images/luckyprice1103/post/f09f9560-0b3e-404c-ab2c-0f923354f083/image.png)
  
  역시 아까처럼 ec2 보안그룹에서 포트를 열어줘야함(9090)
  ![](https://velog.velcdn.com/images/luckyprice1103/post/3f0e3eab-46c5-475f-b6a2-7b0ca776a51d/image.png)



7. 웹 브라우저에서 Prometheus 접속:
서버 IP와 포트 9090을 사용:
http://<your-server-ip>:9090
  
  
> Node Exporter 설치

  1. Node Exporter 전용 사용자 생성 및 다운로드:

```bash
sudo useradd --system --no-create-home --shell /bin/false node_exporter
wget https://github.com/prometheus/node_exporter/releases/download/v1.6.1/node_exporter-1.6.1.linux-amd64.tar.gz
```

  
 2. Node Exporter 파일 추출 및 이동:
  

```bash
tar -xvf node_exporter-1.6.1.linux-amd64.tar.gz
sudo mv node_exporter-1.6.1.linux-amd64/node_exporter /usr/local/bin/
rm -rf node_exporter*
```

3. Node Exporter용 systemd 유닛 구성 파일 생성:

```bash
sudo nano /etc/systemd/system/node_exporter.service
```

아래 내용을 추가:
```
text
[Unit]
Description=Node Exporter
Wants=network-online.target
After=network-online.target

[Service]
User=node_exporter
Group=node_exporter
Type=simple
Restart=on-failure
RestartSec=5s
ExecStart=/usr/local/bin/node_exporter --collector.logind

[Install]
WantedBy=multi-user.target
```

4. Node Exporter 활성화 및 시작:

```bash
sudo systemctl enable node_exporter
sudo systemctl start node_exporter
```

5. Node Exporter 상태 확인:
```bash
sudo systemctl status node_exporter
```

> Prometheus 플러그인 통합 구성 (Jenkins 포함)
  
1. Prometheus 설정:
Node Exporter와 Jenkins에서 메트릭 수집을 위해 prometheus.yml 파일 수정:
아래 예제는 Prometheus 설정 파일의 내용:

  
```
global:
  scrape_interval: 15s

scrape_configs:
  - job_name: 'node_exporter'
    static_configs:
      - targets: ['localhost:9100']

  - job_name: 'jenkins'
    metrics_path: '/prometheus'
    static_configs:
      - targets: ['<your-jenkins-ip>:<your-jenkins-port>']
```

<your-jenkins-ip>와 <your-jenkins-port>를 Jenkins 환경에 맞게 변경합니다.
구성 파일 검증 및 적용:

  1. 구성 파일 유효성 검사:

```bash
promtool check config /etc/prometheus/prometheus.yml
```

2. Prometheus 재시작 없이 구성 재로드:

```bash
curl -X POST http://localhost:9090/-/reload
```

Prometheus 타겟 확인:
웹 브라우저에서 다음 URL로 접속하여 타겟 확인:
http://<your-prometheus-ip>:9090/targets
![](https://velog.velcdn.com/images/luckyprice1103/post/eaf6707f-f0ef-4649-8566-c3fccd5f8d1a/image.png)
  
  
> Grafana 설치 및 설정
  
Grafana 설치 (Ubuntu 22.04 기준):
필수 의존성 설치:
```bash
sudo apt-get update 
sudo apt-get install -y apt-transport-https software-properties-common 
```

GPG 키 추가:
```bash
wget -q -O - https://packages.grafana.com/gpg.key | sudo apt-key add -
```

Grafana 저장소 추가:
```bash
echo "deb https://packages.grafana.com/oss/deb stable main" | sudo tee -a /etc/apt/sources.list.d/grafana.list 

```
패키지 목록 갱신 및 Grafana 설치:
bash
```
sudo apt-get update 
sudo apt-get -y install grafana 
```

Grafana 서비스 활성화 및 시작:
```bash
sudo systemctl enable grafana-server 
sudo systemctl start grafana-server 
```

Grafana 상태 확인:
Grafana가 정상적으로 실행 중인지 확인.
```bash
sudo systemctl status grafana-server 

```
웹 브라우저에서 Grafana 접속:
서버 IP와 포트 3000을 사용하여 접속:
http://<your-server-ip>:3000
  
  ![](https://velog.velcdn.com/images/luckyprice1103/post/1c792cd5-32cf-4522-8ac4-b67cddc84222/image.png)

기본 로그인 정보는 다음과 같습니다:
사용자 이름: admin
비밀번호: admin (첫 로그인 시 변경 요청)
  
  
Prometheus 데이터 소스 추가:
Grafana 인터페이스에서 다음 단계를 수행합니다:
왼쪽 사이드바에서 ⚙️(설정) 아이콘 클릭 → "Data Sources" 선택.
"Add data source" 클릭 → "Prometheus" 선택.
HTTP URL에 http://localhost:9090 입력.
"Save & Test" 클릭하여 연결 확인.

대시보드 가져오기:
미리 구성된 대시보드를 가져오려면 다음 단계를 따릅니다:
왼쪽 사이드바에서 "+"(추가) 아이콘 클릭 → "Import" 선택.
대시보드 코드 입력(예: 1860) → "Load" 클릭.
데이터 소스(Prometheus) 선택 → "Import" 클릭.
 ![](https://velog.velcdn.com/images/luckyprice1103/post/3bc608fb-ec8e-4eb8-9bef-1b94e0ad95eb/image.png)

요약:
이 단계를 완료하면 Prometheus와 Grafana를 통해 애플리케이션 및 CI/CD 파이프라인(Jenkins 포함)을 효과적으로 모니터링할 수 있습니다!
  
 ![](https://velog.velcdn.com/images/luckyprice1103/post/9c56d220-c3af-4fb7-bab5-1e9dbb1f457a/image.png)
![](https://velog.velcdn.com/images/luckyprice1103/post/6aa04322-8fde-4f9d-8031-8222d9d46f99/image.png)
  ![](https://velog.velcdn.com/images/luckyprice1103/post/5888aa22-2e15-4296-8a31-0c67c18e59b9/image.png)



## 5단계: 알림
  알림 서비스 실행: Jenkins에서 이메일 알림 설정 또는 기타 알림 메커니즘 설정하기 
  
## 6단계: Kubernetes
> Kubernetes 클러스터 생성 및 노드 그룹 설정
  
이 단계에서는 Kubernetes 클러스터를 노드 그룹과 함께 설정. 이를 통해 애플리케이션을 배포하고 관리할 수 있는 확장 가능한 환경을 제공합니다.
  
![](https://velog.velcdn.com/images/luckyprice1103/post/6ca6ba12-9325-463e-b358-85319f2c14be/image.png)
![](https://velog.velcdn.com/images/luckyprice1103/post/01703209-f8ae-4727-ad67-ca8695d7406b/image.png)![](https://velog.velcdn.com/images/luckyprice1103/post/6806cbdd-478b-448e-839f-20545c7c99b0/image.png)

> Prometheus로 Kubernetes 모니터링
  
Prometheus는 강력한 모니터링 및 경고 도구입니다. 이를 사용하여 Kubernetes 클러스터를 모니터링합니다. 추가적으로, Helm을 사용하여 Node Exporter를 설치하고 클러스터 노드에서 메트릭을 수집합니다.
  
> Helm을 사용한 Node Exporter 설치
Kubernetes 클러스터를 모니터링하려면 Prometheus Node Exporter를 설치해야 합니다. 이 구성 요소는 클러스터 노드에서 시스템 수준의 메트릭을 수집할 수 있도록 합니다. Helm을 사용하여 Node Exporter를 설치하는 단계는 다음과 같습니다:
  
1. Prometheus Community Helm 저장소 추가: 
```bash
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
```

2. Node Exporter용 Kubernetes 네임스페이스 생성:

```bash
kubectl create namespace prometheus-node-exporter
```

3. Helm으로 Node Exporter 설치:
```bash
helm install prometheus-node-exporter prometheus-community/prometheus-node-exporter --namespace prometheus-node-exporter

```
  
  ![](https://velog.velcdn.com/images/luckyprice1103/post/9626a192-c78b-41db-b3ff-a4aececbeacd/image.png)

  
4. Prometheus 설정 파일(prometheus.yml)에 nodeip:9001/metrics 스크래핑 작업 추가:
Prometheus 설정 파일에 새로운 작업(job)을 추가하여 nodeip:9001/metrics에서 메트릭을 수집하도록 설정
 
  다음 구성을 prometheus.yml 파일에 추가:
 
```
 - job_name: 'Netflix'
    metrics_path: '/metrics'
    static_configs:
      - targets: ['node1Ip:9100']

```
  ![](https://velog.velcdn.com/images/luckyprice1103/post/71cf2f12-1db9-415d-bda9-3bcd1df5069b/image.png)

  


job_name에는 작업의 설명적인 이름을 입력.
- static_configs 섹션에서는 메트릭을 스크래핑할 대상을 지정합니다(여기서는 nodeip:9001).
  
5. Prometheus를 다시 로드하거나 재시작하여 변경 사항을 적용.
  
  ![](https://velog.velcdn.com/images/luckyprice1103/post/5f168f24-74a0-46d8-bc86-cc9e7615da3b/image.png)

![](https://velog.velcdn.com/images/luckyprice1103/post/84692172-ee25-46d5-aa79-31f63318d6f4/image.png)

  
> ArgoCD로 애플리케이션 배포
  https://archive.eksworkshop.com/intermediate/290_argocd/install/
![](https://velog.velcdn.com/images/luckyprice1103/post/89d14180-de56-4dc5-94d2-f8d876ee34e2/image.png)
  
1. ArgoCD 설치
Kubernetes 클러스터에 ArgoCD를 설치합니다. 설치 방법은 EKS Workshop 문서에 제공된 지침을 참조.
  
> 1. aws eks update-kubeconfig --name Netflix --region ap-northeast-2
역할: AWS CLI를 사용하여 EKS 클러스터와 로컬 kubectl 클라이언트를 연결.
 --name Netflix: 이름이 Netflix인 EKS 클러스터에 연결.
--region ap-northeast-2: 클러스터가 위치한 AWS 리전(서울)을 지정함.
이 명령어를 실행하면 현재 컴퓨터의 kubeconfig 파일(~/.kube/config)에 해당 클러스터 정보가 추가되며, 이후 kubectl 명령어를 통해 클러스터와 통신할 수 있음.

>  2. kubectl create namespace argocd
역할: Kubernetes 클러스터에 argocd라는 네임스페이스(namespace)를 생성
 네임스페이스는 Kubernetes 리소스를 논리적으로 그룹화하는 데 사용. ArgoCD와 관련된 리소스(예: Pod, Service 등)를 모두 이 네임스페이스에 배치하기 위해 미리 생성하는 단계

>  3. kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": "LoadBalancer"}}'
역할: argocd-server라는 Kubernetes 서비스(Service)의 유형을 LoadBalancer로 변경함.
svc argocd-server: ArgoCD 서버의 서비스(Service) 이름.
-n argocd: argocd 네임스페이스에 있는 리소스를 대상으로 함.
-p '{"spec": {"type": "**LoadBalancer**"}}': 서비스의 스펙(spec)을 수정하여 타입을 LoadBalancer로 설정. 이 설정은 외부에서 접근 가능한 IP 주소를 할당해주는 역할을 하는데,
이를 통해 ArgoCD UI에 브라우저로 접근할 수 있음.
  
2. GitHub 리포지토리를 소스로 설정
ArgoCD를 설치한 후, GitHub 리포지토리를 애플리케이션 배포의 소스로 설정해야 함. 여기에는 리포지토리 연결 구성과 ArgoCD 애플리케이션의 소스 정의가 포함됩니다. 구체적인 단계는 설정 및 요구 사항에 따라 달라질 수 있음.
  
3. ArgoCD 애플리케이션 생성
다음 요소를 정의하여 ArgoCD 애플리케이션을 생성합니다:
- name: 애플리케이션 이름 설정.
- destination: 애플리케이션이 배포될 위치 정의.
- project: 애플리케이션이 속한 프로젝트 지정.
- source: 애플리케이션의 소스를 설정(GitHub 리포지토리 URL, 리비전, 리포지토리 내 애플리케이션 경로 포함).
- syncPolicy: 자동 동기화, 정리(pruning), 셀프 힐링(self-healing)을 포함한 동기화 정책 구성.
  
![](https://velog.velcdn.com/images/luckyprice1103/post/da8c8a64-ec67-438d-8a84-92d37bbfadc5/image.png)![](https://velog.velcdn.com/images/luckyprice1103/post/597d70d5-741b-42c5-b2b2-2f3eaa2e3d46/image.png)

  

4. 애플리케이션 액세스
- 보안 그룹에서 포트 30007이 열려 있는지 확인.
- 브라우저 새 탭에서 NodeIP:30007을 입력하면 애플리케이션이 실행 중인 것을 확인할 수 있음.
  
  ![](https://velog.velcdn.com/images/luckyprice1103/post/32d12e73-abf5-42aa-901f-46eae6258014/image.png)![](https://velog.velcdn.com/images/luckyprice1103/post/bbbf9ad9-c115-4841-8c44-06560d4c425e/image.png)![](https://velog.velcdn.com/images/luckyprice1103/post/844023ce-a21a-4234-bb3d-2e1d24db335f/image.png)

포트 30007에서 애플리케이션이 실행 된다.....!
  
  
## 7단계: 정리 작업 (Cleanup)
AWS EC2 인스턴스 정리:
더 이상 필요하지 않은 AWS EC2 인스턴스를 종료(Terminate).
