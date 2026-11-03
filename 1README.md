# 🖥️ 개발 워크스테이션 구축 프로젝트

> **"내 컴퓨터에서만 돌아가는 문제"를 Docker로 해결하는 프로젝트**
> 
> 작성자: 홍길동 | 작성일: 2025년 | 과정: 클라우드 컴퓨팅 기초

---

## 📌 목차

1. [내 컴퓨터에 개발자용 '작업실'꾸미기](#1-프로젝트-개요)
2. [내 컴퓨터 windows11 pro](#2-사전-준비-및-환경-설정)
3. [Docker 기본 개념](#3-docker-기본-개념)
4. [Dockerfile 작성](#4-dockerfile-작성)
5. [포트 매핑](#5-포트-매핑)
6. [바인드 마운트](#6-바인드-마운트)
7. [볼륨(Volume)](#7-볼륨volume)
8. [Git 설정 및 GitHub 연동](#8-git-설정-및-github-연동)
9. [최종 결과 확인](#9-최종-결과-확인)
10. [트러블슈팅](#10-트러블슈팅)

---

## 1. 프로젝트 개요

### 🎯 미션 목표

| 목표 | 설명 |
|------|------|
| 🐳 Docker 환경 구축 | 컨테이너 기반 개발 환경 설정 |
| 📝 Dockerfile 작성 | 나만의 이미지 빌드 |
| 🔌 포트 매핑 | 컨테이너와 외부 통신 연결 |
| 📁 바인드 마운트 | 로컬 파일을 컨테이너와 공유 |
| 💾 볼륨 | 데이터 영구 저장 |
| 🐙 GitHub 연동 | 코드 버전 관리 및 업로드 |

### 💡 왜 Docker를 사용하나요?

```
❌ Docker 없이 개발할 때의 문제
─────────────────────────────────
내 컴퓨터 : 잘 됨 ✅
친구 컴퓨터 : 안 됨 ❌
서버       : 안 됨 ❌

→ "내 컴퓨터에서는 되는데?" 문제 발생!

✅ Docker를 사용하면
─────────────────────────────────
내 컴퓨터 : 잘 됨 ✅
친구 컴퓨터 : 잘 됨 ✅
서버       : 잘 됨 ✅

→ 어디서든 동일한 환경 보장!
```

### 📁 최종 프로젝트 구조

```
dev-workstation/
├── 📄 Dockerfile          # Docker 이미지 설계도
├── 📄 .gitignore          # Git 제외 파일 목록
├── 📄 README.md           # 프로젝트 설명서 (이 파일)
├── 📁 app/                # 애플리케이션 코드
│   └── 📄 index.html      # 메인 페이지
└── 📁 data/               # 데이터 저장 폴더
```

---

## 2. 사전 준비 및 환경 설정

### 🛠️ 설치한 도구 목록

| 도구 | 용도 | 설치 확인 명령어 |
|------|------|----------------|
| OrbStack | Mac용 Docker 실행 환경 | `docker --version` |
| Docker | 컨테이너 실행 | `docker ps` |
| Git | 버전 관리 | `git --version` |
| VSCode | 코드 편집기 | - |

### ✅ 환경 확인 명령어

```bash
# Docker 버전 확인
$ docker --version
Docker version 24.0.0, build abcdef

# Docker 실행 확인
$ docker ps
CONTAINER ID   IMAGE   COMMAND   CREATED   STATUS   PORTS   NAMES

# Git 버전 확인
$ git --version
git version 2.39.0
```

---

## 3. Docker 기본 개념

### 🏗️ 핵심 용어 정리

```
📦 이미지 (Image)
─────────────────
컨테이너를 만들기 위한 설계도
예) 붕어빵 틀

🚀 컨테이너 (Container)
─────────────────────────
이미지로 실제 실행된 프로그램
예) 붕어빵 틀로 만든 붕어빵

📄 Dockerfile
─────────────────
이미지를 만드는 레시피 파일
예) 붕어빵 만드는 방법이 적힌 종이
```

### 🔄 Docker 기본 명령어

```bash
# 이미지 목록 확인
$ docker images

# 컨테이너 실행 목록 확인
$ docker ps

# 모든 컨테이너 확인 (중지된 것 포함)
$ docker ps -a

# 컨테이너 중지
$ docker stop 컨테이너이름

# 컨테이너 삭제
$ docker rm 컨테이너이름

# 이미지 삭제
$ docker rmi 이미지이름
```

---

## 4. Dockerfile 작성

### 📄 작성한 Dockerfile

```dockerfile
# 베이스 이미지 선택 (Ubuntu 22.04 사용)
FROM ubuntu:22.04

# 작성자 정보
LABEL maintainer="나의이름 <이메일@example.com>"

# 패키지 업데이트 및 필요한 도구 설치
RUN apt-get update && apt-get install -y \
    curl \
    git \
    vim \
    && rm -rf /var/lib/apt/lists/*

# 작업 디렉토리 설정
WORKDIR /app

# 로컬 파일을 컨테이너로 복사
COPY ./app /app

# 컨테이너가 사용할 포트 번호 명시
EXPOSE 8080

# 컨테이너 시작 시 실행할 명령어
CMD ["bash"]
```

### 📖 Dockerfile 명령어 설명

| 명령어 | 의미 | 예시 |
|--------|------|------|
| `FROM` | 베이스 이미지 선택 | `FROM ubuntu:22.04` |
| `LABEL` | 이미지 정보 추가 | `LABEL maintainer="이름"` |
| `RUN` | 이미지 빌드 시 실행할 명령어 | `RUN apt-get update` |
| `WORKDIR` | 작업 디렉토리 설정 | `WORKDIR /app` |
| `COPY` | 파일 복사 | `COPY ./app /app` |
| `EXPOSE` | 포트 번호 명시 | `EXPOSE 8080` |
| `CMD` | 컨테이너 시작 시 실행 | `CMD ["bash"]` |

### 🔨 이미지 빌드 및 실행

```bash
# 이미지 빌드
$ docker build -t my-workstation .

# 빌드 확인
$ docker images
REPOSITORY       TAG     IMAGE ID       CREATED        SIZE
my-workstation   latest  abc123def456   1 minute ago   150MB

# 컨테이너 실행
$ docker run -it --name my-container my-workstation
```

---

## 5. 포트 매핑

### 🔌 포트 매핑이란?

```
포트 매핑 = 컨테이너 안과 밖을 연결하는 통로

내 컴퓨터 (외부)          컨테이너 (내부)
─────────────────          ─────────────────
포트 8080       ←──────→   포트 80
                  연결!

브라우저에서
localhost:8080 접속
→ 컨테이너 내부 80번 포트로 전달됨
```

### 💻 포트 매핑 명령어

```bash
# 기본 형식
$ docker run -p [내컴퓨터포트]:[컨테이너포트] 이미지이름

# 실제 사용 예시
$ docker run -d -p 8080:80 --name web-container my-workstation

# 포트 매핑 확인
$ docker ps
CONTAINER ID  IMAGE           PORTS                  NAMES
abc123        my-workstation  0.0.0.0:8080->80/tcp   web-container
```

### ✅ 포트 매핑 확인

```bash
# 브라우저에서 확인
http://localhost:8080

# 터미널에서 확인
$ curl http://localhost:8080
```

---

## 6. 바인드 마운트

### 📁 바인드 마운트란?

```
바인드 마운트 = 내 컴퓨터 폴더를 컨테이너와 실시간 공유

내 컴퓨터                    컨테이너
─────────────────            ─────────────────
~/dev-workstation/app  ←──→  /app
                       동기화!

내 컴퓨터에서 파일 수정
→ 컨테이너 안에서도 즉시 반영!
```

### 💻 바인드 마운트 명령어

```bash
# 기본 형식
$ docker run -v [내컴퓨터경로]:[컨테이너경로] 이미지이름

# 실제 사용 예시
$ docker run -it \
  -v $(pwd)/app:/app \
  -p 8080:80 \
  --name bind-container \
  my-workstation

# 마운트 확인
$ docker inspect bind-container | grep Mounts -A 10
```

### ✅ 바인드 마운트 동작 확인

```bash
# 1. 내 컴퓨터에서 파일 수정
$ echo "Hello Docker!" > ./app/test.txt

# 2. 컨테이너 안에서 확인
$ docker exec bind-container cat /app/test.txt
Hello Docker!   ← 즉시 반영됨!
```

---

## 7. 볼륨(Volume)

### 💾 볼륨이란?

```
볼륨 vs 바인드 마운트 비교

바인드 마운트                    볼륨
─────────────────                ─────────────────
내 컴퓨터 특정 폴더 연결         Docker가 관리하는 저장공간
경로를 직접 지정                 이름만 지정
개발 중 코드 공유에 적합         데이터 영구 저장에 적합
컨테이너 삭제 시 파일 유지       컨테이너 삭제해도 데이터 유지
```

### 💻 볼륨 명령어

```bash
# 볼륨 생성
$ docker volume create my-volume

# 볼륨 목록 확인
$ docker volume ls
DRIVER    VOLUME NAME
local     my-volume

# 볼륨 상세 정보
$ docker volume inspect my-volume

# 볼륨 사용해서 컨테이너 실행
$ docker run -it \
  -v my-volume:/data \
  --name volume-container \
  my-workstation

# 볼륨 삭제
$ docker volume rm my-volume
```

### ✅ 볼륨 데이터 유지 확인

```bash
# 1. 컨테이너에서 데이터 저장
$ docker exec volume-container sh -c "echo '중요한 데이터' > /data/save.txt"

# 2. 컨테이너 삭제
$ docker rm -f volume-container

# 3. 새 컨테이너로 데이터 확인
$ docker run --rm -v my-volume:/data my-workstation cat /data/save.txt
중요한 데이터   ← 데이터가 살아있음!
```

---

## 8. Git 설정 및 GitHub 연동

### ⚙️ Git 초기 설정

```bash
# 사용자 이름 설정
$ git config --global user.name "나의이름"

# 이메일 설정
$ git config --global user.email "이메일@example.com"

# 설정 확인
$ git config --list
user.name=나의이름
user.email=이메일@example.com
```

### 📄 .gitignore 파일

```gitignore
# OS 관련 파일
.DS_Store
Thumbs.db

# 환경 변수 (민감한 정보)
.env
.env.local
*.env

# 로그 파일
*.log
logs/

# 임시 파일
*.tmp
*.temp

# Docker 관련
.docker/
```

### 🚀 GitHub 업로드 과정

```bash
# 1. Git 저장소 초기화
$ git init

# 2. 파일 스테이징
$ git add .

# 3. 현재 상태 확인
$ git status
Changes to be committed:
    new file: Dockerfile
    new file: README.md
    new file: .gitignore
    new file: app/index.html

# 4. 커밋
$ git commit -m "feat: 개발 워크스테이션 초기 설정 완료"

# 5. 브랜치 이름 설정
$ git branch -M main

# 6. 원격 저장소 연결
$ git remote add origin https://github.com/사용자명/저장소명.git

# 7. GitHub에 업로드
$ git push -u origin main
```

### 📝 커밋 메시지 규칙

| 타입 | 의미 | 예시 |
|------|------|------|
| `feat` | 새로운 기능 추가 | `feat: Docker 환경