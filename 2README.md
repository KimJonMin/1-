markdown
📋 복사
# 🖥️ 개발 워크스테이션 구축 결과물

## 📌 프로젝트 개요

> 재현 가능한 개발 환경을 CLI, Docker, Git을 활용하여 구축하는 미션입니다.
> 어떤 환경에서도 동일한 개발 환경을 빠르게 구성할 수 있도록
> Dockerfile 기반 커스텀 이미지를 제작하고, GitHub로 관리합니다.

### 미션 목표
- CLI 명령어로 파일/디렉토리 조작 및 권한 관리
- Docker를 활용한 컨테이너 환경 구축
- Dockerfile로 커스텀 이미지 제작
- 포트 매핑, 볼륨 마운트로 실용적인 컨테이너 운영
- Docker Compose로 멀티 컨테이너 환경 구성
- 환경 변수로 설정과 코드 분리
- Git/GitHub SSH 키 기반 버전 관리

---

## 💻 실행 환경

| 항목 | 내용 |
|------|------|
| OS | Windows 11 |
| 쉘/터미널 | PowerShell 7.x |
| Docker 버전 | `docker --version` 결과 기재 |
| Git 버전 | `git --version` 결과 기재 |

```powershell
PS C:\> docker --version
Docker version 27.x.x, build xxxxxxx

PS C:\> git --version
git version 2.x.x.windows.x
```

---

## ✅ 수행 항목 체크리스트

| 번호 | 항목 | 수행 여부 | 섹션 |
|------|------|-----------|------|
| 1 | 터미널 조작 (이동/생성/복사/삭제) | ✅ | 섹션 1 |
| 2 | 파일 권한 확인/변경 | ✅ | 섹션 2 |
| 3 | Docker 설치 및 버전 확인 | ✅ | 섹션 3 |
| 4 | Docker 기본 운영 명령 | ✅ | 섹션 4 |
| 5 | hello-world 컨테이너 실행 | ✅ | 섹션 5 |
| 6 | ubuntu 컨테이너 진입 및 명령 수행 | ✅ | 섹션 5 |
| 7 | 커스텀 이미지 제작 (Dockerfile) | ✅ | 섹션 6 |
| 8 | 포트 매핑 및 접속 확인 | ✅ | 섹션 7 |
| 9 | Docker 볼륨 영속성 검증 | ✅ | 섹션 8 |
| 10 | Git 설정 및 GitHub 연동 | ✅ | 섹션 9 |
| 11 | Docker Compose 기초 (단일 서비스) | ✅ | 섹션 10 |
| 12 | Docker Compose 멀티 컨테이너 | ✅ | 섹션 11 |
| 13 | Compose 운영 명령어 | ✅ | 섹션 12 |
| 14 | 환경 변수 활용 | ✅ | 섹션 13 |
| 15 | GitHub SSH 키 설정 | ✅ | 섹션 14 |

---

## 📋 수행 결과 상세

---

### [섹션 1] 터미널 조작 로그

#### [1-1] 현재 위치 확인
```powershell
PS C:\> pwd
Path
----
C:\Users\[사용자명]
```

#### [1-2] 목록 확인 (숨김 파일 포함)
```powershell
PS C:\> ls -Force

    디렉터리: C:\Users\[사용자명]

Mode    LastWriteTime    Length  Name
----    -------------    ------  ----
d--h-   2025-01-01       .ssh
d----   2025-01-01       Documents
-a---   2025-01-01  100  .gitconfig
```

#### [1-3] 디렉토리 이동
```powershell
PS C:\> cd C:\Users\[사용자명]\Documents
PS C:\Users\[사용자명]\Documents>
```

#### [1-4] 디렉토리 생성
```powershell
PS C:\> mkdir my-dev-workspace
PS C:\> mkdir my-dev-workspace\src
PS C:\> mkdir my-dev-workspace\docker
PS C:\> mkdir my-dev-workspace\logs
PS C:\> mkdir my-dev-workspace\screenshots
```

#### [1-5] 빈 파일 생성
```powershell
PS C:\my-dev-workspace> New-Item -ItemType File -Name "test.txt"

    디렉터리: C:\my-dev-workspace

Mode    LastWriteTime    Length  Name
----    -------------    ------  ----
-a---   2025-01-01  0           test.txt
```

#### [1-6] 파일 복사
```powershell
PS C:\my-dev-workspace> Copy-Item test.txt test_backup.txt
```

#### [1-7] 파일 이동 / 이름 변경
```powershell
# 이동
PS C:\my-dev-workspace> Move-Item test_backup.txt logs\test_backup.txt

# 이름 변경
PS C:\my-dev-workspace> Rename-Item test.txt renamed_test.txt
```

#### [1-8] 파일 내용 확인
```powershell
PS C:\my-dev-workspace> Get-Content renamed_test.txt
(내용 없음 - 빈 파일)
```

#### [1-9] 파일 삭제
```powershell
PS C:\my-dev-workspace> Remove-Item renamed_test.txt

# 삭제 후 목록 확인
PS C:\my-dev-workspace> ls
    디렉터리: C:\my-dev-workspace

Mode    LastWriteTime    Length  Name
----    -------------    ------  ----
d----   2025-01-01       logs
d----   2025-01-01       screenshots
d----   2025-01-01       src
```

---

### [섹션 2] 권한 실습

#### [2-1] 파일 권한 확인 (변경 전)
```powershell
PS C:\my-dev-workspace> Get-Acl test.txt | Format-List

Path   : test.txt
Owner  : DESKTOP-XXXXX\[사용자명]
Access : [사용자명] Allow FullControl
```

#### [2-2] 파일 권한 변경 (읽기 전용 설정)
```powershell
PS C:\my-dev-workspace> Set-ItemProperty test.txt -Name IsReadOnly -Value $true

# 변경 후 확인
PS C:\my-dev-workspace> Get-ItemProperty test.txt | Select-Object IsReadOnly

IsReadOnly
----------
      True
```

#### [2-3] 읽기 전용 해제
```powershell
PS C:\my-dev-workspace> Set-ItemProperty test.txt -Name IsReadOnly -Value $false

# 해제 후 확인
PS C:\my-dev-workspace> Get-ItemProperty test.txt | Select-Object IsReadOnly

IsReadOnly
----------
     False
```

#### [2-4] 디렉토리 권한 확인
```powershell
PS C:\my-dev-workspace> Get-Acl logs | Format-List

Path   : logs
Owner  : DESKTOP-XXXXX\[사용자명]
Access : [사용자명] Allow FullControl
```

> 📌 **Linux 환경 참고 (chmod 기준)**
> ```bash
> # 변경 전
> $ ls -l test.txt
> -rw-r--r-- 1 user group 0 Jan 1 00:00 test.txt
>
> # 실행 권한 추가
> $ chmod +x test.txt
>
> # 변경 후
> $ ls -l test.txt
> -rwxr-xr-x 1 user group 0 Jan 1 00:00 test.txt
> ```

---

### [섹션 3] Docker 설치 및 기본 점검

#### [3-1] Docker 버전 확인
```powershell
PS C:\> docker --version
Docker version 27.x.x, build xxxxxxx
```

#### [3-2] Docker 데몬 동작 확인
```powershell
PS C:\> docker info
Client:
 Version: 27.x.x
Server:
 Containers: 0
  Running: 0
  Paused: 0
  Stopped: 0
 Images: 0
 Server Version: 27.x.x
 OS/Arch: linux/amd64
```

---

### [섹션 4] Docker 기본 운영 명령

#### [4-1] 이미지 다운로드
```powershell
PS C:\> docker pull nginx
Using default tag: latest
latest: Pulling from library/nginx
Status: Downloaded newer image for nginx:latest
docker.io/library/nginx:latest
```

#### [4-2] 이미지 목록 확인
```powershell
PS C:\> docker images
REPOSITORY   TAG       IMAGE ID       CREATED        SIZE
nginx        latest    xxxxxxxxxx     2 weeks ago    192MB
```

#### [4-3] 컨테이너 실행
```powershell
PS C:\> docker run -d --name my-nginx -p 8080:80 nginx
xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
```

#### [4-4] 실행 중인 컨테이너 목록
```powershell
PS C:\> docker ps
CONTAINER ID   IMAGE   COMMAND                  CREATED        STATUS        PORTS                  NAMES
xxxxxxxxxxxx   nginx   "/docker-entrypoint…"   1 minute ago   Up 1 minute   0.0.0.0:8080->80/tcp   my-nginx
```

#### [4-5] 모든 컨테이너 목록 (중지 포함)
```powershell
PS C:\> docker ps -a
CONTAINER ID   IMAGE         COMMAND       CREATED        STATUS                    NAMES
xxxxxxxxxxxx   nginx         "..."         2 minutes ago  Exited (0) 1 minute ago   my-nginx
yyyyyyyyyyyy   hello-world   "/hello"      5 minutes ago  Exited (0) 5 minutes ago  hello
```

#### [4-6] 컨테이너 로그 확인
```powershell
PS C:\> docker logs my-nginx
/docker-entrypoint.sh: /docker-entrypoint.d/ is not empty
172.17.0.1 - - [01/Jan/2025:00:00:00 +0000] "GET / HTTP/1.1" 200 615
```

#### [4-7] 리소스 확인
```powershell
PS C:\> docker stats --no-stream
CONTAINER ID   NAME       CPU %   MEM USAGE / LIMIT   MEM %   NET I/O     BLOCK I/O
xxxxxxxxxxxx   my-nginx   0.00%   5MiB / 8GiB         0.06%   1kB / 1kB   0B / 0B
```

#### [4-8] 컨테이너 중지 및 삭제
```powershell
# 중지
PS C:\> docker stop my-nginx
my-nginx

# 삭제
PS C:\> docker rm my-nginx
my-nginx
```

---

### [섹션 5] 컨테이너 실행 실습

#### [5-1] hello-world 실행
```powershell
PS C:\> docker run hello-world

Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
 3. The Docker daemon created a new container from that image.
 4. The Docker daemon streamed that output to the Docker client.
```

#### [5-2] ubuntu 컨테이너 실행 및 내부 진입
```powershell
PS C:\> docker run -it ubuntu bash

root@xxxxxxxxxxxx:/# ls
bin  boot  dev  etc  home  lib  lib32  lib64  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var

root@xxxxxxxxxxxx:/# echo "Hello from Ubuntu Container!"
Hello from Ubuntu Container!

root@xxxxxxxxxxxx:/# cat /etc/os-release
PRETTY_NAME="Ubuntu 22.04.x LTS"
NAME="Ubuntu"
VERSION="22.04.x LTS (Jammy Jellyfish)"

root@xxxxxxxxxxxx:/# exit
exit
```

#### [5-3] attach vs exec 차이 정리

| 구분 | 명령어 | 특징 |
|------|--------|------|
| attach | `docker attach 컨테이너명` | 메인 프로세스 연결. exit 시 컨테이너 종료 |
| exec | `docker exec -it 컨테이너명 bash` | 새 프로세스 진입. exit 해도 컨테이너 유지 |

```powershell
# exec 방식 (권장)
PS C:\> docker exec -it my-nginx bash
root@xxxxxxxxxxxx:/# exit
# → 컨테이너는 계속 실행 중 ✅

# attach 방식
PS C:\> docker attach my-nginx
# Ctrl+P, Ctrl+Q → 컨테이너 유지하며 빠져나오기
# exit 입력 → 컨테이너 종료 ⚠️
```

---

[섹션 6] 커스텀 이미지 제작 
[6-1] 프로젝트 구조

my-dev-workspace/
├── Dockerfile
└── src/
    └── index.html

[6-2] src/index.html

<!DOCTYPE html>
<html lang="ko">
<head>
  <meta charset="UTF-8">
  <title>개발 워크스테이션</title>
</head>
<body>
  <h1>🐳 Docker 개발 환경 구축 완료!</h1>
  <p>커스텀 NGINX 이미지로 서비스 중입니다.</p>
</body>
</html>

[6-3] Dockerfile

# 베이스 이미지: 공식 NGINX
FROM nginx:latest

# 커스텀 포인트 1: 정적 파일 교체
COPY src/index.html /usr/share/nginx/html/index.html

# 커스텀 포인트 2: 메타 레이블
LABEL maintainer="[사용자명]"
LABEL version="1.0"
LABEL description="개발 워크스테이션 커스텀 NGINX 이미지"

# 포트 선언
EXPOSE 80

[6-4] 이미지 빌드
powershell

PS C:\my-dev-workspace> docker build -t my-nginx:1.0 .

[+] Building 3.2s (7/7) FINISHED
 => [internal] load build definition from Dockerfile          0.1s
 => [internal] load .dockerignore                             0.1s
 => [internal] load metadata for docker.io/library/nginx      1.2s
 => [1/2] FROM docker.io/library/nginx:latest                 0.0s
 => [2/2] COPY src/index.html /usr/share/nginx/html/index.html 0.1s
 => exporting to image                                        0.1s
 => => naming to docker.io/library/my-nginx:1.0               0.0s

[6-5] 빌드된 이미지 확인
powershell

PS C:\my-dev-workspace> docker images
REPOSITORY   TAG       IMAGE ID       CREATED          SIZE
my-nginx     1.0       aabbccddee11   10 seconds ago   192MB
nginx        latest    xxxxxxxxxxxx   2 weeks ago      192MB

[6-6] 커스텀 이미지 실행 테스트
powershell

PS C:\my-dev-workspace> docker run -d --name test-custom -p 9090:80 my-nginx:1.0
xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx

PS C:\my-dev-workspace> docker ps
CONTAINER ID   IMAGE          COMMAND                  CREATED        STATUS        PORTS                  NAMES
xxxxxxxxxxxx   my-nginx:1.0   "/docker-entrypoint…"   5 seconds ago  Up 4 seconds  0.0.0.0:9090->80/tcp   test-custom

[섹션 7] 포트 매핑 및 접속 증거
[7-1] 포트 매핑 개념

구분	설명
-p 호스트포트:컨테이너포트	호스트의 포트를 컨테이너 포트에 연결
0.0.0.0:8080->80/tcp	모든 인터페이스의 8080 → 컨테이너 80

[7-2] 컨테이너 실행 (포트 매핑 적용)
powershell

PS C:\my-dev-workspace> docker run -d --name my-web -p 8080:80 my-nginx:1.0
xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx

# 포트 매핑 확인
PS C:\my-dev-workspace> docker ps
CONTAINER ID   IMAGE          PORTS                  NAMES
xxxxxxxxxxxx   my-nginx:1.0   0.0.0.0:8080->80/tcp   my-web

[7-3] curl 접속 확인
powershell

PS C:\my-dev-workspace> curl http://localhost:8080

StatusCode        : 200
StatusDescription : OK
Content           : <!DOCTYPE html>
                    <html lang="ko">
                    <head>
                      <meta charset="UTF-8">
                      <title>개발 워크스테이션</title>
                    </head>
                    <body>
                      <h1>🐳 Docker 개발 환경 구축 완료!</h1>
                      <p>커스텀 NGINX 이미지로 서비스 중입니다.</p>
                    </body>
                    </html>

[7-4] 브라우저 접속 확인

접속 URL: http://localhost:8080

[브라우저 스크린샷 첨부]
→ screenshots/section7-browser.png
📌 스크린샷 첨부 방법

브라우저에서 http://localhost:8080 접속
화면 캡처 후 screenshots/ 폴더에 저장
아래와 같이 README에 삽입

![브라우저 접속 화면](screenshots/section7-browser.png)

[섹션 8] Docker 볼륨 영속성 검증
[8-1] 볼륨 영속성 개념

컨테이너 삭제 → 내부 데이터 소멸 ❌
볼륨 사용 시  → 컨테이너 삭제 후에도 데이터 유지 ✅

[8-2] 볼륨 생성
powershell

PS C:\> docker volume create my-data-volume
my-data-volume

# 볼륨 목록 확인
PS C:\> docker volume ls
DRIVER    VOLUME NAME
local     my-data-volume

# 볼륨 상세 정보
PS C:\> docker volume inspect my-data-volume
[
    {
        "CreatedAt": "2025-01-01T00:00:00Z",
        "Driver": "local",
        "Mountpoint": "/var/lib/docker/volumes/my-data-volume/_data",
        "Name": "my-data-volume",
        "Scope": "local"
    }
]
[8-3] 볼륨 연결 후 데이터 생성 (컨테이너 1)
powershell

# 볼륨 마운트하여 컨테이너 실행
PS C:\> docker run -it --name container-1 -v my-data-volume:/app/data ubuntu bash

# 컨테이너 내부에서 데이터 생성
root@xxxxxxxxxxxx:/# echo "볼륨 영속성 테스트 데이터" > /app/data/test.txt
root@xxxxxxxxxxxx:/# cat /app/data/test.txt
볼륨 영속성 테스트 데이터

root@xxxxxxxxxxxx:/# ls /app/data/
test.txt

root@xxxxxxxxxxxx:/# exit
exit

[8-4] 컨테이너 삭제 (데이터 유지 확인 전)
powershell

# 컨테이너 삭제
PS C:\> docker rm container-1
container-1

# 컨테이너가 삭제되었는지 확인
PS C:\> docker ps -a
CONTAINER ID   IMAGE   COMMAND   CREATED   STATUS   PORTS   NAMES
(container-1 없음 - 삭제 완료 ✅)

# 볼륨은 여전히 존재하는지 확인
PS C:\> docker volume ls
DRIVER    VOLUME NAME
local     my-data-volume  ← 볼륨 유지 ✅

[8-5] 새 컨테이너로 데이터 유지 확인 (컨테이너 2)
powershell

# 동일 볼륨으로 새 컨테이너 실행
PS C:\> docker run -it --name container-2 -v my-data-volume:/app/data ubuntu bash

# 이전 컨테이너에서 생성한 데이터 확인
root@yyyyyyyyyyyy:/# ls /app/data/
test.txt  ← 데이터 유지 확인 ✅

root@yyyyyyyyyyyy:/# cat /app/data/test.txt
볼륨 영속성 테스트 데이터  ← 내용도 동일 ✅

root@yyyyyyyyyyyy:/# exit
exit

[8-6] 검증 결과 요약
단계	명령	결과
볼륨 생성	docker volume create my-data-volume	✅ 생성 완료
컨테이너1 연결 + 데이터 작성	-v my-data-volume:/app/data	✅ test.txt 생성
컨테이너1 삭제	docker rm container-1	✅ 컨테이너 삭제
볼륨 유지 확인	docker volume ls	✅ 볼륨 존재
컨테이너2 연결 + 데이터 확인	-v my-data-volume:/app/data	✅ 데이터 유지
🎯 결론: 컨테이너가 삭제되어도 볼륨에 저장된 데이터는 유지됩니다.

[섹션 9] Git 설정 및 GitHub 연동
[9-1] Git 사용자 정보 설정
powershell

# 사용자 이름 설정
PS C:\> git config --global user.name "[사용자명]"

# 이메일 설정
PS C:\> git config --global user.email "[이메일@example.com]"

# 기본 브랜치 설정
PS C:\> git config --global init.defaultBranch main

# 줄바꿈 설정 (Windows)
PS C:\> git config --global core.autocrlf true

[9-2] git config --list 결과
powershell

PS C:\> git config --list

core.symlinks=false
core.autocrlf=true
core.fscache=true
color.diff=auto
color.status=auto
color.branch=auto
color.interactive=true
user.name=[사용자명]
user.email=[이메일@example.com]
init.defaultbranch=main

[9-3] 로컬 저장소 초기화 및 첫 커밋
powershell

PS C:\my-dev-workspace> git init
Initialized empty Git repository in C:/my-dev-workspace/.git/

PS C:\my-dev-workspace> git add .
PS C:\my-dev-workspace> git commit -m "feat: 개발 워크스테이션 초기 구성"

[main (root-commit) xxxxxxx] feat: 개발 워크스테이션 초기 구성
 5 files changed, 50 insertions(+)
 create mode 100644 Dockerfile
 create mode 100644 README.md
 create mode 100644 src/index.html

[9-4] GitHub 원격 저장소 연동
powershell

# 원격 저장소 추가
PS C:\my-dev-workspace> git remote add origin https://github.com/[사용자명]/my-dev-workspace.git

# 원격 저장소 확인
PS C:\my-dev-workspace> git remote -v
origin  https://github.com/[사용자명]/my-dev-workspace.git (fetch)
origin  https://github.com/[사용자명]/my-dev-workspace.git (push)

# 푸시
PS C:\my-dev-workspace> git push -u origin main
Enumerating objects: 7, done.
Counting objects: 100% (7/7), done.
Writing objects: 100% (7/7), 1.23 KiB | 1.23 MiB/s, done.
Branch 'main' set up to track remote branch 'main' from 'origin'.
[9-5] GitHub 연동 증거

[GitHub 저장소 스크린샷 첨부]
→ screenshots/section9-github.png

확인 항목:
✅ 저장소 생성 확인
✅ 커밋 내역 확인
✅ 파일 목록 확인 (Dockerfile, README.md, src/index.html)
📌 스크린샷 삽입

![GitHub 연동 확인](screenshots/section9-github.png)

[섹션 10] Docker Compose 기초 (단일 서비스)
[10-1] Docker Compose 개념
항목	설명
목적	여러 컨테이너를 YAML 파일로 일괄 관리
파일명	docker-compose.yml
핵심 명령	docker compose up / down / ps / logs

[10-2] 디렉토리 구조

my-dev-workspace/
├── docker-compose.yml
├── Dockerfile
└── src/
    └── index.html

[10-3] docker-compose.yml (단일 서비스)
yaml

version: "3.8"

services:
  web:
    image: my-nginx:1.0
    container_name: compose-web
    ports:
      - "8080:80"
    volumes:
      - ./src:/usr/share/nginx/html
    restart: unless-stopped

[10-4] Compose 실행
powershell

PS C:\my-dev-workspace> docker compose up -d

[+] Running 1/1
 ✔ Container compose-web  Started

PS C:\my-dev-workspace> docker compose ps

NAME          IMAGE          COMMAND                  SERVICE   STATUS    PORTS
compose-web   my-nginx:1.0   "/docker-entrypoint…"   web       running   0.0.0.0:8080->80/tcp

[10-5] 접속 확인
powershell

PS C:\my-dev-workspace> curl http://localhost:8080

StatusCode : 200
Content    : <!DOCTYPE html>...🐳 Docker 개발 환경 구축 완료!...

[섹션 11] Docker Compose 멀티 컨테이너
[11-1] 멀티 서비스 구성 개요

code
📋 복사
┌──────────────────────────────────────┐
│         Docker Compose Network        │
│                                      │
│  ┌─────────────┐   ┌─────────────┐  │
│  │  web 서비스  │──▶│ redis 서비스 │  │
│  │ (NGINX:1.0) │   │  (Redis)    │  │
│  │  port:8080  │   │  port:6379  │  │
│  └─────────────┘   └─────────────┘  │
└──────────────────────────────────────┘
[11-2] docker-compose.yml (멀티 서비스)
yaml
📋 복사
version: "3.8"

services:
  web:
    image: my-nginx:1.0
    container_name: multi-web
    ports:
      - "8080:80"
    volumes:
      - ./src:/usr/share/nginx/html
    depends_on:
      - redis
    networks:
      - app-network
    restart: unless-stopped

  redis:
    image: redis:latest
    container_name: multi-redis
    ports:
      - "6379:6379"
    networks:
      - app-network
    restart: unless-stopped

networks:
  app-network:
    driver: bridge
[11-3] Compose 실행
powershell
📋 복사
PS C:\my-dev-workspace> docker compose up -d

[+] Running 3/3
 ✔ Network my-dev-workspace_app-network  Created
 ✔ Container multi-redis                 Started
 ✔ Container multi-web                   Started
[11-4] 컨테이너 목록 확인
powershell
📋 복사
PS C:\my-dev-workspace> docker compose ps

NAME           IMAGE          SERVICE   STATUS    PORTS
multi-redis    redis:latest   redis     running   0.0.0.0:6379->6379/tcp
multi-web      my-nginx:1.0   web       running   0.0.0.0:8080->80/tcp
[11-5] 네트워크 통신 확인
powershell
📋 복사
# web 컨테이너에서 redis로 ping
PS C:\my-dev-workspace> docker exec -it multi-web ping -c 3 redis

PING redis (172.18.0.2): 56 data bytes
64 bytes from 172.18.0.2: seq=0 ttl=64 time=0.123 ms
64 bytes from 172.18.0.2: seq=1 ttl=64 time=0.098 ms
64 bytes from 172.18.0.2: seq=2 ttl=64 time=0.101 ms

# redis 컨테이너 직접 접속 확인
PS C:\my-dev-workspace> docker exec -it multi-redis redis-cli ping
PONG
[11-6] 네트워크 상세 확인
powershell
📋 복사
PS C:\my-dev-workspace> docker network ls

NETWORK ID     NAME                            DRIVER    SCOPE
xxxxxxxxxxxx   my-dev-workspace_app-network    bridge    local
yyyyyyyyyyyy   bridge                          bridge    local
zzzzzzzzzzzz   host                            host      local

PS C:\my-dev-workspace> docker network inspect my-dev-workspace_app-network
[
    {
        "Name": "my-dev-workspace_app-network",
        "Driver": "bridge",
        "Containers": {
            "xxx...": { "Name": "multi-web",   "IPv4Address": "172.18.0.3/16" },
            "yyy...": { "Name": "multi-redis", "IPv4Address": "172.18.0.2/16" }
        }
    }
]
[섹션 12] Compose 운영 명령어
[12-1] 주요 명령어 정리
명령어	설명
docker compose up -d	백그라운드로 전체 서비스 시작
docker compose down	전체 서비스 중지 및 컨테이너 삭제
docker compose ps	서비스 상태 확인
docker compose logs	전체 서비스 로그 확인
docker compose logs -f 서비스명	특정 서비스 로그 실시간 확인
docker compose stop	컨테이너 중지 (삭제 안 함)
docker compose start	중지된 컨테이너 재시작
docker compose restart	서비스 재시작
[12-2] up 명령 실행
powershell
📋 복사
PS C:\my-dev-workspace> docker compose up -d

[+] Running 3/3
 ✔ Network my-dev-workspace_app-network  Created
 ✔ Container multi-redis                 Started
 ✔ Container multi-web                   Started
[12-3] ps 명령 실행
powershell
📋 복사
PS C:\my-dev-workspace> docker compose ps

NAME           IMAGE          SERVICE   STATUS    PORTS
multi-redis    redis:latest   redis     running   0.0.0.0:6379->6379/tcp
multi-web      my-nginx:1.0   web       running   0.0.0.0:8080->80/tcp
[12-4] logs 명령 실행
powershell
📋 복사
PS C:\my-dev-workspace> docker compose logs

multi-web    | /docker-entrypoint.sh: Configuration complete; ready for start up
multi-redis  | * Ready to accept connections tcp

# 특정 서비스 로그만 확인
PS C:\my-dev-workspace> docker compose logs web
multi-web    | 172.18.0.1 - - [01/Jan/2025:00:00:00 +0000] "GET / HTTP/1.1" 200 615

# 실시간 로그 확인
PS C:\my-dev-workspace> docker compose logs -f web
(Ctrl+C로 종료)
[12-5] stop / start 명령 실행
powershell
📋 복사
# 중지 (컨테이너 유지)
PS C:\my-dev-workspace> docker compose stop

[+] Stopping 2/2
 ✔ Container multi-web    Stopped
 ✔ Container multi-redis  Stopped

# 상태 확인 (exited 상태)
PS C:\my-dev-workspace> docker compose ps

NAME           IMAGE          SERVICE   STATUS    PORTS
multi-redis    redis:latest   redis     exited
multi-web      my-nginx:1.0   web       exited

# 재시작
PS C:\my-dev-workspace> docker compose start

[+] Running 2/2
 ✔ Container multi-redis  Started
 ✔ Container multi-web    Started
[12-6] down 명령 실행
powershell
📋 복사
PS C:\my-dev-workspace> docker compose down

[+] Running 3/3
 ✔ Container multi-web                   Removed
 ✔ Container multi-redis                 Removed
 ✔ Network my-dev-workspace_app-network  Removed
[12-7] stop vs down 비교
구분	compose stop	compose down
컨테이너	중지 (유지)	중지 + 삭제
네트워크	유지	삭제
볼륨	유지	유지 (--volumes 옵션 시 삭제)
재시작	compose start	compose up
[섹션 13] 환경 변수 활용
[13-1] 환경 변수 사용 목적
목적	설명
보안	비밀번호·API 키를 코드에서 분리
유연성	환경(개발/운영)별 설정 분리
재사용성	동일 이미지를 다양한 설정으로 실행
[13-2] .env 파일 작성
code
📋 복사
# .env
APP_ENV=development
APP_PORT=8080
NGINX_VERSION=1.0
REDIS_HOST=redis
REDIS_PORT=6379
[13-3] docker-compose.yml에 환경 변수 적용
yaml
📋 복사
version: "3.8"

services:
  web:
    image: my-nginx:${NGINX_VERSION}
    container_name: env-web
    ports:
      - "${APP_PORT}:80"
    environment:
      - APP_ENV=${APP_ENV}
    networks:
      - app-network
    restart: unless-stopped

  redis:
    image: redis:latest
    container_name: env-redis
    environment:
      - REDIS_PORT=${REDIS_PORT}
    networks:
      - app-network
    restart: unless-stopped

networks:
  app-network:
    driver: bridge
[13-4] 환경 변수 적용 확인
powershell
📋 복사
# .env 파일 자동 로드 후 실행
PS C:\my-dev-workspace> docker compose up -d

[+] Running 3/3
 ✔ Network my-dev-workspace_app-network  Created
 ✔ Container env-redis                   Started
 ✔ Container env-web                     Started

# 환경 변수 적용 확인
PS C:\my-dev-workspace> docker compose config

services:
  web:
    image: my-nginx:1.0        ← ${NGINX_VERSION} → 1.0 치환 ✅
    ports:
      - "8080:80"              ← ${APP_PORT} → 8080 치환 ✅
    environment:
      APP_ENV: development     ← ${APP_ENV} → development 치환 ✅
  redis:
    image: redis:latest
    environment:
      REDIS_PORT: "6379"       ← ${REDIS_PORT} → 6379 치환 ✅
[13-5] 컨테이너 내부 환경 변수 확인
powershell
📋 복사
PS C:\my-dev-workspace> docker exec env-web env | findstr APP_ENV
APP_ENV=development  ✅
[13-6] .gitignore에 .env 등록
code
📋 복사
# .gitignore
.env
*.log
⚠️ 주의: .env 파일에는 비밀번호·API 키 등 민감 정보가 포함될 수 있으므로
반드시 .gitignore에 등록하여 GitHub에 올라가지 않도록 합니다.

[섹션 14] GitHub SSH 키 설정
[14-1] SSH 키 방식 vs HTTPS 방식 비교
구분	HTTPS	SSH
인증	아이디/비밀번호 또는 토큰	공개키/개인키 쌍
편의성	매번 인증 필요	최초 1회 설정 후 자동 인증
보안	토큰 관리 필요	개인키 로컬 보관
권장	일회성 작업	지속적 개발 환경
[14-2] SSH 키 생성
powershell
📋 복사
PS C:\> ssh-keygen -t ed25519 -C "[이메일@example.com]"

Generating public/private ed25519 key pair.
Enter file in which to save the key (C:\Users\[사용자명]/.ssh/id_ed25519):
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in C:\Users\[사용자명]/.ssh/id_ed25519
Your public key has been saved in C:\Users\[사용자명]/.ssh/id_ed25519.pub
The key fingerprint is:
SHA256:xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx [이메일@example.com]
[14-3] 생성된 키 확인
powershell
📋 복사
PS C:\> ls ~/.ssh

    디렉터리: C:\Users\[사용자명]\.ssh

Mode    LastWriteTime    Length  Name
----    -------------    ------  ----
-a---   2025-01-01       411     id_ed25519        ← 개인키 (절대 공유 금지 🔒)
-a---   2025-01-01       105     id_ed25519.pub    ← 공개키 (GitHub에 등록)
[14-4] 공개키 내용 확인
powershell
📋 복사
PS C:\> Get-Content ~/.ssh/id_ed25519.pub

ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAA[공개키내용] [이메일@example.com]
[14-5] SSH Agent 등록
powershell
📋 복사
# SSH Agent 서비스 시작
PS C:\> Start-Service ssh-agent

# 개인키 등록
PS C:\> ssh-add ~/.ssh/id_ed25519
Identity added: C:\Users\[사용자명]\.ssh\id_ed25519

# 등록 확인
PS C:\> ssh-add -l
256 SHA256:xxxxxxxxxxxx [이메일@example.com] (ED25519)
[14-6] GitHub에 공개키 등록
code
📋 복사
1. GitHub 접속 → 우측 상단 프로필 클릭
2. Settings → SSH and GPG keys
3. [New SSH key] 클릭
4. Title: "개발 워크스테이션"
5. Key: id_ed25519.pub 내용 붙여넣기
6. [Add SSH key] 클릭

[GitHub SSH 키 등록 스크린샷 첨부]
→ screenshots/section14-ssh-github.png
[14-7] SSH 연결 테스트
powershell
📋 복사
PS C:\> ssh -T git@github.com

Hi [사용자명]! You've successfully authenticated,
but GitHub does not provide shell access.
[14-8] 원격 저장소 URL을 SSH로 변경
powershell
📋 복사
# 현재 원격 URL 확인
PS C:\> git remote -v
origin  https://github.com/[사용자명]/my-dev-workspace.git (fetch)
origin  https://github.com/[사용자명]/my-dev-workspace.git (push)

# SSH URL로 변경
PS C:\> git remote set-url origin git@github.com:[사용자명]/my-dev-workspace.git

[14-9] SSH 방식으로 푸시 확인
powershell
📋 복사
PS C:\my-dev-workspace> git push origin main

Enumerating objects: 10, done.
Counting objects: 100% (10/10), done.
Delta compression using up to 8 threads
Compressing objects: 100% (7/7), done.
Writing objects: 100% (10/10), 2.34 KiB | 2.34 MiB/s, done.
Total 10 (delta 1), reused 0 (delta 0), pack-reused 0
To git@github.com:[사용자명]/my-dev-workspace.git
   xxxxxxx..yyyyyyy  main -> main
[14-10] SSH 설정 완료 확인 요약
단계	명령	결과
키 생성	ssh-keygen -t ed25519	✅ 공개키/개인키 생성
Agent 등록	ssh-add ~/.ssh/id_ed25519	✅ 개인키 등록
GitHub 등록	GitHub Settings → SSH keys	✅ 공개키 등록
연결 테스트	ssh -T git@github.com	✅ 인증 성공
URL 변경	git remote set-url origin	✅ SSH URL 적용
푸시 확인	git push origin main	✅ 푸시 성공
📁 최종 프로젝트 구조
code
📋 복사
my-dev-workspace/
├── .env                    ← 환경 변수 (gitignore 처리)
├── .gitignore              ← Git 제외 파일 목록
├── Dockerfile              ← 커스텀 NGINX 이미지 정의
├── README.md               ← 프로젝트 문서
├── docker-compose.yml      ← Compose 멀티 서비스 정의
├── src/
│   └── index.html          ← 커스텀 웹 페이지
├── logs/
│   └── test_backup.txt     ← 섹션1 실습 파일
└── screenshots/
    ├── section7-browser.png    ← 브라우저 접속 증거
    ├── section9-github.png     ← GitHub 연동 증거
    └── section14-ssh-github.png ← SSH 키 등록 증거
🔄 전체 워크플로우 요약
code
📋 복사
[1] CLI 조작
    └─▶ 디렉토리/파일 생성·이동·삭제·권한 관리

[2] Docker 기초
    └─▶ 이미지 pull → 컨테이너 run → ps/logs/stats → stop/rm

[3] 커스텀 이미지
    └─▶ Dockerfile 작성 → docker build → docker run

[4] 포트 매핑
    └─▶ -p 8080:80 → curl/브라우저 접속 확인

[5] 볼륨 영속성
    └─▶ volume create → 컨테이너 연결 → 삭제 후 재연결 → 데이터 유지 확인

[6] Git/GitHub
    └─▶ config 설정 → init → add/commit → remote → push

[7] Docker Compose
    └─▶ 단일 서비스 → 멀티 서비스 → 운영 명령 (up/down/ps/logs)

[8] 환경 변수
    └─▶ .env 작성 → Compose에서 참조 → .gitignore 등록

[9] SSH 인증
    └─▶ 키 생성 → Agent 등록 → GitHub 등록 → 연결 테스트 → SSH 푸시
💡 배운 점 및 회고
잘 된 점
Docker 볼륨을 통해 데이터 영속성의 중요성을 직접 실습으로 확인
Docker Compose로 멀티 컨테이너 환경을 YAML 한 파일로 관리하는 편리함 체험
SSH 키 기반 인증으로 GitHub 연동 시 보안성과 편의성 동시 확보
.env 파일로 설정과 코드를 분리하는 실무적 습관 형성

어려웠던 점 & 해결 방법
어려웠던 점	해결 방법
Windows에서 SSH Agent 서비스 시작 오류	Start-Service ssh-agent 관리자 권한으로 실행
Compose 네트워크 통신 확인	docker network inspect로 IP 확인 후 ping 테스트
.env 변수가 Compose에 적용 안 됨	.env 파일 위치를 docker-compose.yml과 동일 경로에 배치
giy 
향후 개선 방향
Dockerfile 멀티 스테이지 빌드 적용
GitHub Actions CI/CD 파이프라인 연동
Docker Compose 헬스체크(healthcheck) 설정 추가
환경별(dev/prod) Compose 파일 분리 (docker-compose.override.yml)

📎 참고 자료
자료	URL
Docker 공식 문서	https://docs.docker.com
Docker Compose 공식 문서	https://docs.docker.com/compose
Git 공식 문서	https://git-scm.com/doc
GitHub SSH 설정 가이드	https://docs.github.com/en/authentication/connecting-to-github-with-ssh

