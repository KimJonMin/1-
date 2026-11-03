# 🖥️ 개발 워크스테이션 구축 보고서

> **미션 목표**: "내 컴퓨터에서만 돌아가는" 문제를 없애고,  
> 터미널·Docker·Git을 활용해 누구나 동일하게 실행·배포·디버깅할 수 있는  
> 재현 가능한 개발 환경을 구성한다.

---

## 📋 목차

1. [내 컴퓨터에 개발자용 '작업실'꾸미기](#1-프로젝트-개요)
2. [Windows11 pro](#2-실행-환경)
3. [저장소 구조](#3-저장소-구조)
4. [수행 항목 체크리스트](#4-수행-항목-체크리스트)
5. [터미널 조작 로그](#5-터미널-조작-로그)
6. [Docker 운영·검증 로그](#6-docker-운영검증-로그)
7. [Dockerfile 기반 웹 서버](#7-dockerfile-기반-웹-서버)
8. [포트 매핑 접속 증거](#8-포트-매핑-접속-증거)
9. [바인드 마운트 반영 + 볼륨 영속성 증거](#9-바인드-마운트-반영--볼륨-영속성-증거)
10. [Git 설정 및 GitHub·VSCode 연동 증거](#10-git-설정-및-githubvscode-연동-증거)
11. [검증 방법 및 결과 위치](#11-검증-방법-및-결과-위치)
12. [트러블슈팅](#12-트러블슈팅)

---

## 1. 프로젝트 개요

| 항목 | 내용 |
|------|------|
| 미션 | 재현 가능한 개발 워크스테이션 구축 |
| 핵심 도구 | Linux CLI, Docker, Git/GitHub |
| 주요 목표 | ① 터미널로 작업 환경 정리 → ② Docker로 컨테이너화 → ③ Git으로 버전 관리 |
| 기대 효과 | 팀원 누구나 동일 명령으로 동일 결과를 얻는 환경 확보 |

### 왜 이 도구들이 필요한가?

```
[문제]  "내 컴퓨터에서는 되는데요..."
           ↓
[원인]  개발자마다 OS·언어 버전·라이브러리가 다름
           ↓
[해결]  Docker로 실행 환경 자체를 패키징 → 어디서나 동일 실행
        Git으로 코드·설정을 버전 관리   → 변경 이력 추적·협업
        CLI로 모든 작업을 명령어로 기록 → 자동화·재현 가능
```

---

## 2. 실행 환경

```
OS          : [예: Ubuntu 22.04 LTS / macOS 14.x / Windows 11 + WSL2]
Shell       : [예: bash 5.1.16]
터미널      : [예: GNOME Terminal / iTerm2 / Windows Terminal]
Docker      : [예: Docker version 24.0.7, build afdd53b]
Docker Compose : [예: v2.23.0]
Git         : [예: git version 2.43.0]
VSCode      : [예: 1.89.0]
```

> 위 버전 확인 명령어:
> ```bash
> uname -a          # OS 정보
> echo $SHELL       # 현재 쉘
> docker --version  # Docker 버전
> git --version     # Git 버전
> code --version    # VSCode 버전
> ```

---

## 3. 저장소 구조

```
my-workstation/
├── README.md                  ← 이 문서 (전체 보고서)
├── Dockerfile                 ← 웹 서버 컨테이너 설정
├── .gitignore                 ← Git 추적 제외 목록
├── app/
│   └── index.html             ← 웹 서버 소스코드
└── docs/
    ├── screenshots/           ← 스크린샷 모음
    │   ├── 01_port_mapping.png
    │   ├── 02_bind_mount_before.png
    │   ├── 03_bind_mount_after.png
    │   ├── 04_volume_persist.png
    │   └── 05_github_vscode.png
    └── logs/                  ← 터미널 로그 텍스트
        ├── terminal_log.txt
        └── docker_log.txt
```

---

## 4. 수행 항목 체크리스트

### 🖥️ 터미널 / 권한

- [x] 작업 디렉토리 생성 및 구조 정리 (`mkdir`, `cd`, `ls -la`)
- [x] 파일 권한 확인 및 변경 (`chmod`, `ls -la`)
- [x] 스크립트 실행 권한 부여 (`chmod +x`)

### 🐳 Docker 설치 / 점검

- [x] Docker 버전 확인 (`docker --version`)
- [x] Docker 시스템 정보 확인 (`docker info`)
- [x] hello-world 컨테이너 실행 테스트

### 📦 Docker 컨테이너 운영

- [x] 이미지 빌드 (`docker build`)
- [x] 컨테이너 실행·중지·삭제 (`docker run`, `stop`, `rm`)
- [x] 실행 중 컨테이너 목록 확인 (`docker ps`)
- [x] 컨테이너 로그 확인 (`docker logs`)
- [x] 리소스 사용량 확인 (`docker stats`)

### 🌐 Dockerfile / 웹 서버

- [x] 웹 서버 소스코드 작성 (`app/index.html`)
- [x] Dockerfile 작성 및 이미지 빌드
- [x] 빌드 성공 로그 확인

### 🔌 포트 매핑

- [x] `-p <host>:<container>` 옵션으로 실행
- [x] 브라우저 접속 성공 확인 (스크린샷 첨부)

### 💾 스토리지

- [x] 바인드 마운트: 호스트 파일 변경 → 컨테이너 즉시 반영 확인
- [x] Docker 볼륨: 생성·연결·컨테이너 삭제 후 데이터 유지 확인

### 🗂️ Git / GitHub

- [x] Git 사용자 정보 설정 (`git config`)
- [x] 기본 브랜치 설정 (`main`)
- [x] `.gitignore` 작성
- [x] 커밋 및 GitHub 푸시
- [x] VSCode에서 GitHub 연동 확인

---

## 5. 터미널 조작 로그

### 5-1. 작업 디렉토리 생성

```bash
$ pwd
/home/[username]

$ mkdir -p my-workstation/app my-workstation/docs/screenshots my-workstation/docs/logs
$ cd my-workstation
$ ls -la
total 16
drwxr-xr-x 4 [username] [username] 4096 Jan  1 00:00 .
drwxr-xr-x 8 [username] [username] 4096 Jan  1 00:00 ..
drwxr-xr-x 2 [username] [username] 4096 Jan  1 00:00 app
drwxr-xr-x 4 [username] [username] 4096 Jan  1 00:00 docs
```

### 5-2. 파일 권한 확인 및 변경

```bash
# 스크립트 파일 생성 후 권한 확인
$ touch setup.sh
$ ls -la setup.sh
-rw-r--r-- 1 [username] [username] 0 Jan  1 00:00 setup.sh
#  ↑ 실행 권한(x) 없음

# 실행 권한 부여
$ chmod +x setup.sh
$ ls -la setup.sh
-rwxr-xr-x 1 [username] [username] 0 Jan  1 00:00 setup.sh
#  ↑ 실행 권한(x) 추가됨

# 권한 숫자 방식으로 설정
$ chmod 644 app/index.html   # 소유자: rw-, 그룹: r--, 기타: r--
$ ls -la app/index.html
-rw-r--r-- 1 [username] [username] [size] Jan  1 00:00 app/index.html
```

---

## 6. Docker 운영·검증 로그

### 6-1. 설치 점검

```bash
$ docker --version
Docker version 24.0.7, build afdd53b

$ docker info
Client: Docker Engine - Community
 Version:    24.0.7
 Context:    default
 ...
Server:
 Containers: 0
  Running: 0
  Paused:  0
  Stopped: 0
 Images: 0
 Server Version: 24.0.7
 Storage Driver: overlay2
 ...

$ docker run hello-world
Hello from Docker!
This message shows that your installation appears to be working correctly.
```

### 6-2. 이미지 목록

```bash
$ docker images
REPOSITORY       TAG       IMAGE ID       CREATED         SIZE
my-webserver     1.0       [image-id]     2 minutes ago   43.3MB
nginx            alpine    [image-id]     2 weeks ago     41.1MB
hello-world      latest    [image-id]     3 months ago    13.3kB
```

### 6-3. 컨테이너 목록 (전체)

```bash
$ docker ps -a
CONTAINER ID   IMAGE            COMMAND                  CREATED         STATUS                     PORTS                  NAMES
[id]           my-webserver:1.0 "/docker-entrypoint.…"   5 min ago       Up 5 minutes               0.0.0.0:8080->80/tcp   webserver
[id]           hello-world      "/hello"                 10 min ago      Exited (0) 10 minutes ago                         hello
```

### 6-4. 컨테이너 로그

```bash
$ docker logs webserver
/docker-entrypoint.sh: /docker-entrypoint.d/ is not empty, will attempt to perform configuration
/docker-entrypoint.sh: Looking for shell scripts in /docker-entrypoint.d/
...
[notice] 1#1: start worker processes

# 접속 로그 (브라우저로 접속 후)
[timestamp] "GET / HTTP/1.1" 200 [size] "-" "Mozilla/5.0 ..."
```

### 6-5. 리소스 사용량

```bash
$ docker stats webserver --no-stream
CONTAINER ID   NAME        CPU %   MEM USAGE / LIMIT   MEM %   NET I/O       BLOCK I/O
[id]           webserver   0.00%   2.3MiB / 7.7GiB     0.03%   1.2kB / 0B    0B / 0B
```

---

## 7. Dockerfile 기반 웹 서버

### 7-1. 웹 서버 소스코드 (`app/index.html`)

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Docker 웹서버</title>
    <style>
        * { margin: 0; padding: 0; box-sizing: border-box; }
        body {
            font-family: 'Segoe UI', sans-serif;
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            min-height: 100vh;
            display: flex;
            align-items: center;
            justify-content: center;
        }
        .card {
            background: white;
            border-radius: 16px;
            padding: 48px;
            text-align: center;
            box-shadow: 0 20px 60px rgba(0,0,0,0.2);
            max-width: 480px;
        }
        h1 { color: #2d3748; font-size: 2rem; margin-bottom: 12px; }
        p  { color: #718096; line-height: 1.6; margin-bottom: 8px; }
        .badge {
            display: inline-block;
            background: #ebf8ff;
            color: #2b6cb0;
            padding: 4px 12px;
            border-radius: 20px;
            font-size: 0.85rem;
            margin-top: 16px;
        }
    </style>
</head>
<body>
    <div class="card">
        <h1>🐳 Docker 웹서버</h1>
        <p>컨테이너 환경에서 정상 실행 중입니다.</p>
        <p>어떤 컴퓨터에서도 동일하게 동작합니다.</p>
        <span class="badge">nginx:alpine 기반</span>
    </div>
</body>
</html>
```

### 7-2. Dockerfile

```dockerfile
# ── 베이스 이미지 ──────────────────────────────────────────
# nginx:alpine = nginx 웹서버 + 경량 Alpine Linux
# alpine 사용 이유: 전체 이미지 크기를 최소화하기 위함
FROM nginx:alpine

# ── 메타데이터 ────────────────────────────────────────────
LABEL maintainer="[your-email@example.com]"
LABEL description="재현 가능한 정적 웹서버 컨테이너"
LABEL version="1.0"

# ── 파일 복사 ─────────────────────────────────────────────
# 로컬 app/index.html → 컨테이너 내 nginx 기본