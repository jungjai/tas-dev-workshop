

# CI/CD(GitLab/Jenkins)
이번 CI/CD 장에서는 Jenkins pipeline을 활용하여 자동 빌드 환경을 구성해 보도록 하겠습니다. pipeline이란 말 그대로 파이프를 이어 붙인것과 같은 형태로 Step by Step 형식의 각 단계를 이어 붙여 실행하는 방식입니다. 이를 적용하면, Continuous Delivery & Continuous Deploy 손쉽게 구현할 수 있습니다.

## Prerequisites
- [app prerequisites](lab-prerequisites-app.md)
- [build환경 구성](lab-developing-spring-boot-app.md)
- GitLab 소스 업로드(https://docs.gitlab.com/12.10/ee)

## Jenkins Pipeline 단계
- 1단계 : GitLab 프로젝트 다운로드
- 2단계 : Application 빌드(maven, gradle)
- 3단계 : cf cli를 사용하여 컨테이너 배포
- 4단계 : Pipeline 작업 디렉토리 삭제

## Jenkins Pipeline 생성 및 환경 변수 설정
### 1. Jenkins Pipeline 생성
**새 작업** 항목을 선택하고, **item name**을 입력합니다. 그리고, **Pipeline**를 선택해서 새로운 job을 생성합니다.

![image](https://user-images.githubusercontent.com/85478109/121808627-5b110400-cc94-11eb-862a-e04f4c1d27c0.png)

### 2. Pipeline 스크립트 작성
scripted 문법은 쉘 스크립트를 짜듯이 자유롭게 Pipeline을 구성할 수 있도록 지원합니다. Scripted 문법은 Groovy 문법을 사용합니다. Scripted 문법을 사용하여 자동 빌드 환경을 구성합니다.
```
node('USER01') {
    
   stage('GitLab repository Download') { 
        git branch: 'dev', credentialsId: 'edu1', url: 'http://192.168.150.191:18080/edu01/test/test.git'
   }

   stage('Application Build') {
       sh '$gradle -v'   
       sh '$mvn -v'       
   }

   stage('TAS Deploy') {
       sh 'cf -v'
   }

	stage('Directory delete') {
       deleteDir()
   }

}
```

### 3. GitLab Credential 추가하기
Jenkins와 GitLab 연동을 위해 Credential을 등록합니다.
- Jenkins 관리 항목을 선택하고, Security 부분의 manage credentials를 선택합니다.
![image](https://user-images.githubusercontent.com/85478109/121811039-feb2e200-cc9d-11eb-9c02-c0969ee9147b.png)


### 4. Jenkins 환경 변수 설정
Jenkins Pipeline은 환경 변수 설정을 통해 중복되는 특정 파일의 경로나, 특수한 값에 대해 설정하여 보다 간편하게 수행가능 합니다. 이번 CI/CD장에서 수행하는 Pipeline에 필요한 환경변수에 대해 설정하도록 합니다.
- Java 
- Gradle
- Maven
- cf CLI






*The End of this lab*

---
