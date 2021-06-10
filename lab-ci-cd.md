

# CI/CD(GitLab/Jenkins)
이번 CI/CD 실습에서는 GitLab 사용에 기본이 될 수 있는 프로젝트 생성부터, git clone, push, pull 에 대한 내용과 Jenkins 사용에 기본이 될 수 있는 pipeline 생성부터, 스크립트 작성, 수행, 로그확인에 대한 내용을 수행합니다.

## Prerequisites
[app prerequisites](lab-prerequisites-app.md)


## GitLab 사용 가이드 문서
https://docs.gitlab.com/12.10/ee


## GitLab CLI 사용법
### git clone
git clone은 원하는 프로젝트를 내려받기 위해서 사용
```
git clone <clone URL>
```

### git add
add는 작업 디렉토리(working directory) 상의 변경 내용을 스테이징 영역(staging area)에 추가하기 위해서 사용
```
git add  
```

### git commit
git commit 명령어는 로컬 저장소(local repository)에 코드 변경 이력을 남기기 위해서 사용
```
git commit -m "<message>"  
```

### git push
git push는 원격 저장소(remote repository)에 코드 변경분을 업로드하기 위해서 사용
```
git push -u <저장소명> <>
```

## Jenkins 사용 가이드 문서
https://www.jenkins.io/doc/

*The End of this lab*

---
