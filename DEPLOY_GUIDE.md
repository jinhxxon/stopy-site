# 작은편리 웹사이트 배포 가이드 (Azure Static Web Apps + stopy.io)

이 문서는 `jakeunpyeolli-site` 폴더(index.html, styles.css, assets/)를
Azure 학생 계정으로 배포하고, stopy.io 도메인을 연결하는 과정을 정리한 것입니다.

정적 파일만 있는 사이트이므로 **Azure Static Web Apps**의 무료 등급(Free plan)을
사용합니다. 빌드 과정이 없어서 배포 설정이 매우 간단합니다.

---

## 0. 준비물

- Azure for Students 계정 (이미 보유)
- GitHub 계정 (권장 배포 방식에 필요, 없으면 방법 B 사용)
- stopy.io 도메인 관리자 페이지(DNS 레코드 추가 권한)

---

## 방법 A. GitHub 연동 배포 (추천)

가장 간단하고, 이후 파일을 수정해서 GitHub에 푸시만 하면 자동으로 재배포됩니다.

### 1) GitHub에 저장소 만들기
1. GitHub에서 새 저장소를 만듭니다 (예: `jakeunpyeolli-site`, Public 또는 Private 상관없음).
2. 받은 `jakeunpyeolli-site` 폴더 안의 파일들(`index.html`, `styles.css`, `assets/`)을
   저장소 루트에 올리고 커밋/푸시합니다.

```bash
git init
git add .
git commit -m "작은편리 웹사이트 초기 배포"
git branch -M main
git remote add origin https://github.com/사용자명/jakeunpyeolli-site.git
git push -u origin main
```

### 2) Azure Portal에서 Static Web App 생성
1. [portal.azure.com](https://portal.azure.com) 접속 → 학생 구독으로 로그인.
2. 상단 검색창에 **Static Web Apps** 입력 → **만들기(Create)**.
3. 기본 설정:
   - **구독(Subscription)**: Azure for Students
   - **리소스 그룹**: 새로 만들기 (예: `jakeunpyeolli-rg`)
   - **이름**: `jakeunpyeolli-site`
   - **플랜 종류**: **Free**
   - **지역**: 가까운 지역 선택 (예: East Asia)
4. **배포 세부 정보(Deployment details)**:
   - 원본: **GitHub** 선택 후 계정 연동, 방금 만든 저장소/브랜치(main) 선택
5. **빌드 세부 정보(Build Details)**:
   - **빌드 프리셋**: `Custom`
   - **앱 위치(App location)**: `/`
   - **API 위치(Api location)**: 비워둠
   - **출력 위치(Output location)**: 비워둠 (빌드 과정이 없으므로)
6. **검토 + 만들기 → 만들기** 클릭.

몇 분 안에 GitHub Actions가 자동으로 실행되며 배포됩니다. 완료되면 Azure가 제공하는
`https://xxxxx.azurestaticapps.net` 주소로 사이트가 열립니다. 먼저 이 주소로
정상 동작하는지 확인하세요.

---

## 방법 B. GitHub 없이 CLI로 직접 배포

1. Node.js가 설치되어 있어야 합니다.
2. SWA CLI 설치:
   ```bash
   npm install -g @azure/static-web-apps-cli
   ```
3. Azure CLI로 로그인 후 리소스 생성 (Azure CLI 설치 필요):
   ```bash
   az login
   az staticwebapp create \
     --name jakeunpyeolli-site \
     --resource-group jakeunpyeolli-rg \
     --location "eastasia" \
     --sku Free
   ```
4. 배포 토큰 확인 후 폴더 배포:
   ```bash
   az staticwebapp secrets list --name jakeunpyeolli-site --query "properties.apiKey" -o tsv
   swa deploy ./jakeunpyeolli-site --deployment-token <위에서 나온 토큰> --env production
   ```

---

## 3) stopy.io 커스텀 도메인 연결

Static Web App이 정상 배포된 후 진행합니다.

1. Azure Portal → 만든 Static Web App 리소스 → 왼쪽 메뉴 **사용자 지정 도메인(Custom domains)**
   → **추가(Add)**.
2. 도메인 종류를 선택합니다. 두 가지를 모두 연결하는 걸 권장합니다.

### www.stopy.io 연결 (쉬움)
1. "사용자 지정 도메인 추가"에서 `www.stopy.io` 입력.
2. Azure가 안내하는 **CNAME** 레코드 값을 복사.
3. 도메인 등록기관(도메인을 구매한 곳)의 DNS 관리 화면으로 이동해 아래 레코드 추가:
   - 유형: `CNAME`
   - 호스트/이름: `www`
   - 값: Azure가 알려준 `xxxxx.azurestaticapps.net`
4. Azure로 돌아와 **검증(Validate) → 추가** 완료. 보통 몇 분~1시간 내 반영됩니다.

### stopy.io (루트/apex 도메인) 연결
루트 도메인은 DNS 규칙상 CNAME을 쓸 수 없어서 아래 두 방법 중 하나를 씁니다.

- **옵션 1 (가장 간단): 루트 → www 리다이렉트**
  대부분의 도메인 등록기관(가비아, 후이즈, Cloudflare, GoDaddy 등)이 "도메인 포워딩" 기능을
  제공합니다. `stopy.io` → `https://www.stopy.io` 로 포워딩 설정만 하면 됩니다.

- **옵션 2: Azure DNS로 네임서버 이전 후 ALIAS 레코드 사용**
  등록기관에서 도메인의 네임서버를 Azure DNS로 변경하고, Azure DNS 영역에서
  루트 도메인에 대해 **ALIAS 레코드**(Static Web App 리소스를 가리킴)를 만듭니다.
  더 정교하지만 설정이 한 단계 더 필요합니다. (필요하시면 이 부분도 이어서 도와드릴 수 있습니다.)

3. 연결 후 Azure가 자동으로 무료 관리형 TLS 인증서를 발급해 `https://stopy.io`,
   `https://www.stopy.io` 모두 HTTPS로 접속됩니다 (보통 몇 분~수 시간 소요).

---

## 4) 이후 콘텐츠 수정 시

- 방법 A(GitHub 연동)를 썼다면, 파일을 수정해서 GitHub 저장소에 `git push`만 하면
  자동으로 재배포됩니다.
- 방법 B(CLI)를 썼다면, 수정 후 `swa deploy` 명령을 다시 실행하면 됩니다.

---

## 참고
- Azure Static Web Apps Free 플랜은 100GB 대역폭/월, 커스텀 도메인 2개, 무료 SSL을
  포함하고 있어 이런 소개 페이지 용도로는 비용이 들지 않습니다.
- 도메인 등록기관 화면 캡처를 보여주시면 어떤 레코드를 어디에 넣어야 하는지
  구체적으로 같이 봐드릴 수 있습니다.
