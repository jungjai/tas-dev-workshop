

# CI/CD(GitLab/Jenkins)
이번 CI/CD 장에서는 Jenkins Pipeline을 사용하여 소스를 build하고 TAS 컨테이너를 배포하는 과정에 대해 설명합니다.

## Prerequisites
- [app prerequisites](lab-prerequisites-app.md)
- [build환경 구성](lab-developing-spring-boot-app.md)
- GitLab 소스 업로드(https://docs.gitlab.com/12.10/ee)

## Jenkins Pipeline 생성 및 환경 변수 설정

### 1. Jenkins Pipeline 생성
**새 작업** 항목을 선택하고, **item name**을 입력합니다. 그리고, **Pipeline**를 선택해서 새로운 job을 생성합니다.

![image](https://user-images.githubusercontent.com/85478109/121808627-5b110400-cc94-11eb-862a-e04f4c1d27c0.png)

### 2. Jenkins Pipeline 스크립트 작성
- GitLab 소스 다운로드
- Application 빌드 
- 컨테이너 배포






*The End of this lab*

---
