# 1-
코디세이 1차 미션
## 🔧 트러블슈팅

### 1. Docker Permission Denied 오류

- **문제**: `docker ps` 실행 시 Permission denied 발생
- **원인 가설**: 사용자가 docker 그룹에 미포함되어 소켓 접근 불가
- **확인**: `groups` 명령어로 docker 그룹 없음 확인
- **해결**: `sudo usermod -aG docker $USER` 후 `newgrp docker` 실행

---

### 2. .gitignore 로그 파일 차단 오류

- **문제**: `git add logs/*.log` 시 ignored by .gitignore 오류
- **원인 가설**: .gitignore의 `*.log` 규칙이 전체 차단
- **확인**: `cat .gitignore` 로 60번째 줄 `*.log` 발견
- **해결**: `# *.log` 주석 처리 후 정상 추가 완료