# RoFolder GitHub Pages 완벽 가이드

## 📌 개요
RoFolder를 GitHub Pages로 완전히 배포하고 호스팅하는 전체 과정입니다.

---

## 🎯 전체 단계 요약

```
1️⃣ GitHub 저장소 생성
2️⃣ 로컬 저장소 연결
3️⃣ 환경 변수 설정
4️⃣ GitHub Secrets 설정
5️⃣ 자동 배포 확인
6️⃣ 호스팅 사이트 접속
7️⃣ (선택) 커스텀 도메인 설정
```

---

## 📋 사전 요구사항

- ✅ Git 설치됨
- ✅ GitHub 계정 있음
- ✅ 로컬 프로젝트 준비됨 (이미 `git init` 완료)
- ✅ `node_modules` 설치됨

---

## 🚀 1단계: GitHub 저장소 생성

### 1-1) GitHub에서 저장소 생성

1. https://github.com/new 방문
2. **Repository name**: `RoFolder` (또는 원하는 이름)
3. **Description**: `로폴더 사이트` (선택)
4. **Visibility**: `Public` 선택 (Pages 배포를 위해 필수)
5. **Add .gitignore**: 스킵 (이미 있음)
6. **Add a license**: `MIT License` 선택 권장
7. **Create repository** 클릭

### 1-2) 저장소 URL 복사
- GitHub 저장소 페이지에서 `Code` 버튼 클릭
- HTTPS URL 복사: `https://github.com/YOUR_USERNAME/RoFolder.git`

---

## 🔗 2단계: 로컬 저장소 연결

### 2-1) 원격 저장소 추가

```bash
cd c:\Users\oxoxo\Documents\rofolder-site

# 원격 저장소 추가 (YOUR_USERNAME 교체)
git remote add origin https://github.com/YOUR_USERNAME/RoFolder.git

# 또는 이미 추가했다면:
git remote set-url origin https://github.com/YOUR_USERNAME/RoFolder.git
```

### 2-2) 브랜치 이름 변경 및 푸시

```bash
# 메인 브랜치로 이름 변경
git branch -M main

# GitHub에 푸시
git push -u origin main
```

✅ 완료! GitHub에서 파일들을 확인할 수 있습니다.

---

## 🔐 3단계: 환경 변수 설정

### 3-1) 로컬 환경 변수 설정 (.env)

이미 생성된 `.env` 파일에서 다음을 확인/수정하세요:

```env
# Discord Webhook URLs
VITE_WEBHOOK_URL=https://discord.com/api/webhooks/YOUR_ID/YOUR_TOKEN
VITE_ADMIN_WEBHOOK_URL=https://discord.com/api/webhooks/YOUR_ID/YOUR_TOKEN

# Contact Information
VITE_CONTACT_EMAIL=your-email@example.com

# Discord Links
VITE_DISCORD_COMMUNITY_URL=https://discord.gg/YOUR_INVITE
VITE_DISCORD_FORUM_URL=https://discord.com/channels/YOUR_SERVER_ID/YOUR_FORUM_CHANNEL_ID
VITE_TWITTER_URL=https://twitter.com/your_handle

# Admin Password
VITE_ADMIN_PASSWORD=RoFolder2026
```

⚠️ **중요**: `.env` 파일은 GitHub에 업로드되지 않습니다 (`.gitignore`에 있음)

### 3-2) GitHub에서 환경 변수 설정 (Secrets)

GitHub Actions 빌드 시 환경 변수가 필요하므로 GitHub Secrets에 설정해야 합니다.

**GitHub 저장소 → Settings → Secrets and variables → Actions**

다음 시크릿 추가:

| 이름 | 값 |
|------|-----|
| `VITE_WEBHOOK_URL` | Discord Webhook URL |
| `VITE_ADMIN_WEBHOOK_URL` | Admin Webhook URL |
| `VITE_CONTACT_EMAIL` | 연락처 이메일 |
| `VITE_DISCORD_COMMUNITY_URL` | 디스코드 커뮤니티 링크 |
| `VITE_DISCORD_FORUM_URL` | 디스코드 포럼 채널 링크 |
| `VITE_TWITTER_URL` | 트위터 계정 링크 |
| `VITE_ADMIN_PASSWORD` | 관리자 비밀번호 |

**추가 방법:**
1. **New repository secret** 클릭
2. Name에 위 항목 입력
3. Secret 값 붙여넣기
4. **Add secret** 클릭

---

## 🤖 4단계: GitHub Actions 자동 배포 설정

### 4-1) 자동 배포 워크플로우 확인

프로젝트에 이미 `.github/workflows/deploy.yml`이 있는지 확인:

```bash
ls .github/workflows/
```

### 4-2) 워크플로우 수정 (환경 변수 주입)

`.github/workflows/deploy.yml` 파일을 다음과 같이 수정하세요:

```yaml
name: Deploy to GitHub Pages

on:
  push:
    branches:
      - main

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '18'
          cache: 'npm'

      - name: Install dependencies
        run: npm install

      - name: Build
        run: npm run build
        env:
          VITE_WEBHOOK_URL: ${{ secrets.VITE_WEBHOOK_URL }}
          VITE_ADMIN_WEBHOOK_URL: ${{ secrets.VITE_ADMIN_WEBHOOK_URL }}
          VITE_CONTACT_EMAIL: ${{ secrets.VITE_CONTACT_EMAIL }}
          VITE_DISCORD_COMMUNITY_URL: ${{ secrets.VITE_DISCORD_COMMUNITY_URL }}
          VITE_DISCORD_FORUM_URL: ${{ secrets.VITE_DISCORD_FORUM_URL }}
          VITE_TWITTER_URL: ${{ secrets.VITE_TWITTER_URL }}
          VITE_ADMIN_PASSWORD: ${{ secrets.VITE_ADMIN_PASSWORD }}

      - name: Deploy to GitHub Pages
        uses: peaceiris/actions-gh-pages@v4
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_dir: ./dist
```

### 4-3) GitHub Pages 브랜치 설정

**GitHub 저장소 → Settings → Pages**

- **Build and deployment** → **Source**
- 드롭다운에서 **GitHub Actions** 선택
- 저장

✅ 설정 완료! 이제 `main` 브랜치로 푸시하면 자동으로 빌드/배포됩니다.

---

## 🌐 5단계: 자동 배포 확인

### 5-1) GitHub Actions 실행 확인

```bash
# 로컬에서 변경사항 푸시
git add .
git commit -m "Configure: Setup GitHub Actions deployment"
git push origin main
```

### 5-2) 배포 진행 상황 확인

1. **GitHub 저장소** 방문
2. **Actions** 탭 클릭
3. 최신 워크플로우 실행 확인
   - 🟡 실행 중 (노란색)
   - 🟢 성공 (초록색)
   - 🔴 실패 (빨간색)

### 5-3) 실패 시 디버깅

실패한 워크플로우 클릭 후 **Logs** 확인

일반적인 문제:
- `npm install` 실패 → `npm audit` 으로 의존성 확인
- 빌드 실패 → 로컬에서 `npm run build` 테스트
- 환경 변수 누락 → GitHub Secrets 다시 확인

---

## 🚀 6단계: 호스팅 사이트 접속

### 6-1) 배포 URL 확인

**GitHub 저장소 → Settings → Pages**

배포 URL 확인:
```
https://YOUR_USERNAME.github.io/RoFolder
```

### 6-2) 사이트 접속

배포 URL를 브라우저에 입력하여 사이트 확인

✅ **성공 신호:**
- ✓ 배너 표시됨
- ✓ 서버 목록 표시됨 (비어있음 - 새로 추가하면 표시)
- ✓ 관리 기능 있음
- ✓ Discord 링크 작동함

---

## 🌍 7단계: 커스텀 도메인 설정 (선택)

### 7-1) 도메인 DNS 설정

커스텀 도메인 (예: `rofolder.com`)을 사용하려면:

DNS 레코드 추가:
```
Type: A
Name: @
Value: 185.199.108.153
       185.199.109.153
       185.199.110.153
       185.199.111.153
```

또는 `CNAME` 레코드:
```
Type: CNAME
Name: www
Value: YOUR_USERNAME.github.io
```

### 7-2) GitHub에서 커스텀 도메인 등록

1. **GitHub 저장소 → Settings → Pages**
2. **Custom domain** 입력 (예: `rofolder.com`)
3. **Save**
4. DNS 전파 대기 (최대 24시간)

### 7-3) HTTPS 강제 적용

자동으로 HTTPS가 적용되며, "Enforce HTTPS" 옵션도 활성화됩니다.

---

## 📝 일반적인 배포 워크플로우

새 기능 추가 후 배포:

```bash
# 1. 코드 수정
# ... 파일 수정 ...

# 2. 로컬에서 테스트
npm run build
npm run preview

# 3. Git에 커밋
git add .
git commit -m "Feature: Add new server registration"

# 4. GitHub에 푸시 (자동 배포 시작)
git push origin main

# 5. GitHub Actions에서 배포 확인
# https://github.com/YOUR_USERNAME/RoFolder/actions
```

🎉 몇 분 뒤 사이트에서 변경사항 확인 가능!

---

## 📊 배포 아키텍처

```
┌────────────────────────────┐
│   Local Development        │
│  (npm run dev)             │
└──────────┬─────────────────┘
           │
           │ git commit & git push
           │
           ▼
┌────────────────────────────┐
│  GitHub Repository         │
│  (main branch)             │
└──────────┬─────────────────┘
           │
           │ (자동 트리거)
           │
           ▼
┌────────────────────────────┐
│  GitHub Actions            │
│  ✓ Checkout               │
│  ✓ Setup Node             │
│  ✓ npm install            │
│  ✓ npm run build          │
│  ✓ Deploy to gh-pages     │
└──────────┬─────────────────┘
           │
           ▼
┌────────────────────────────┐
│  GitHub Pages              │
│  (gh-pages branch)         │
└──────────┬─────────────────┘
           │
           ▼
┌────────────────────────────┐
│  🌐 Live Website           │
│  https://user.github.io/   │
└────────────────────────────┘
```

---

## 🛡️ 보안 체크리스트

배포 전 확인 사항:

- [ ] `.env` 파일이 `.gitignore`에 있음
- [ ] `.env.example`에는 가짜 값만 있음
- [ ] 모든 시크릿이 GitHub Secrets에 설정됨
- [ ] 로컬 `.env`에는 실제 값이 있음
- [ ] `dist/` 폴더가 `.gitignore`에 있음 (gh-pages가 자동 관리)
- [ ] Discord Webhook URL이 로그에 노출되지 않음
- [ ] 관리자 비밀번호가 하드코딩되지 않음
- [ ] HTTPS 강제 활성화됨

### 현재 구현된 보안 조치

✅ **환경 변수**: 민감한 정보를 코드에서 분리
✅ **GitHub Secrets**: 빌드 시에만 주입
✅ **XSS 방지**: `escapeHtml()` 함수로 모든 입력 검증
✅ **Rate Limiting**: 관리자 비밀번호 5회 실패 시 30초 잠금
✅ **금칙어 필터**: 부적절한 콘텐츠 자동 차단
✅ **HTTPS**: GitHub Pages 기본 제공

---

## 🚀 배포 후 관리

### 환경 변수 수정 필요할 때

```bash
# 1. 로컬 .env 수정
VITE_WEBHOOK_URL=new_value

# 2. GitHub Secrets도 수정
# GitHub 저장소 → Settings → Secrets → 해당 시크릿 수정

# 3. 다시 푸시하면 자동 배포
git add .
git commit -m "Update: Change environment variables"
git push origin main
```

### 의존성 업데이트

```bash
# npm 의존성 확인
npm outdated

# 의존성 업데이트
npm update

# 보안 취약점 확인
npm audit
npm audit fix
```

---

## 💡 팁 & 트러블슈팅

### 배포가 안 될 때

1. **GitHub Actions 로그 확인**
   ```
   Repository → Actions → 실패한 워크플로우 → 상세 로그
   ```

2. **로컬 빌드 테스트**
   ```bash
   npm run build
   npm run preview
   ```

3. **캐시 삭제 후 재시도**
   ```bash
   git clean -fdx
   npm install
   npm run build
   ```

### 사이트가 안 보일 때

1. **Settings → Pages 확인**
   - Source가 "GitHub Actions"로 설정되어 있는지
   - 배포 URL이 맞는지

2. **캐시 삭제**
   - 브라우저 캐시 삭제 (Ctrl+Shift+Delete)
   - 시크릿 창에서 확인

3. **DNS 전파 확인** (커스텀 도메인의 경우)
   ```bash
   nslookup yourdomain.com
   ```

### 환경 변수가 안 적용될 때

1. **GitHub Secrets 다시 확인**
   - 오타 없는지
   - 값이 올바른지

2. **워크플로우에서 env 추가 확인**
   ```yaml
   env:
     VITE_WEBHOOK_URL: ${{ secrets.VITE_WEBHOOK_URL }}
   ```

3. **빌드 로그에서 변수 확인**
   ```
   Run npm run build
   (로그에서 변수가 정상 주입되었는지 확인)
   ```

---

## 📞 자주 묻는 질문 (FAQ)

### Q: 이미 GitHub 저장소를 만들었는데 로컬과 연결하려면?

```bash
git remote set-url origin https://github.com/YOUR_USERNAME/RoFolder.git
git push -u origin main
```

### Q: 환경 변수를 수정했는데 배포 후에도 반영이 안 됩니다

1. GitHub Secrets을 수정했는지 확인
2. 새로운 코드를 푸시하여 GitHub Actions 재실행
3. 브라우저 캐시 삭제 (Ctrl+Shift+Delete)

### Q: 커스텀 도메인 없이 GitHub Pages에서만 호스팅할 수 있나요?

네! 커스텀 도메인은 선택사항입니다:
```
https://YOUR_USERNAME.github.io/RoFolder
```
이 링크로 바로 접속 가능합니다.

### Q: GitHub Pages 배포 후 일반 도메인(예: rofolder.kro.kr)을 연결할 수 있나요?

네, 가능합니다! DNS 레코드를 설정하면 됩니다:
```
A 레코드:
185.199.108.153
185.199.109.153
185.199.110.153
185.199.111.153
```

### Q: 로컬에서만 테스트하고 싶습니다

```bash
npm run dev       # 개발 서버 시작 (http://localhost:5173)
npm run build     # 프로덕션 빌드
npm run preview   # 빌드 결과 미리보기
```

### Q: GitHub에 푸시하기 전에 로컬에서 다 테스트하려면?

```bash
# 1. 모든 변경사항이 git에 추가되었는지 확인
git status

# 2. 빌드 테스트
npm run build

# 3. 빌드 결과 확인
npm run preview

# 4. 문제없으면 푸시
git add .
git commit -m "Your message"
git push origin main
```

---

## ✨ 배포 완료 체크리스트

마지막 확인 사항:

- [ ] 로컬 `.env` 파일에 실제 값 입력됨
- [ ] `.env.example`에는 템플릿만 있음
- [ ] GitHub Secrets에 모든 환경 변수 추가됨
- [ ] `.github/workflows/deploy.yml`에 환경 변수 주입 설정됨
- [ ] GitHub 저장소 Settings → Pages에서 Source가 "GitHub Actions"로 설정됨
- [ ] `main` 브랜치로 푸시 완료
- [ ] GitHub Actions에서 빌드 성공 확인
- [ ] 배포 URL에서 사이트 접속 확인
- [ ] 모든 기능 작동 확인 (배너, 서버 목록, 관리 기능 등)

---

## 🎉 축하합니다!

RoFolder가 성공적으로 배포되었습니다! 이제:

✅ 언제 어디서나 사이트 접속 가능
✅ 자동 배포로 쉬운 업데이트
✅ 보안이 강화된 환경 변수 관리
✅ 수백만 명이 접속할 수 있는 GitHub Pages 인프라

새로운 기능을 추가할 때마다 `git push`하면 자동으로 배포됩니다! 🚀

행운을 빕니다!
