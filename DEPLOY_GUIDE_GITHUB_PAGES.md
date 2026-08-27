# 작은편리 웹사이트 배포 가이드 (GitHub Pages + stopy.io)

Azure for Students의 무료 크레딧/기간이 끝나서, 카드 등록 없이 **GitHub Pages**로
배포하는 방법으로 정리했습니다. 완전 무료이고, 이미 GitHub에 올려두신
`jakeunpyeolli-site` 저장소를 그대로 사용합니다.

---

## 1) 저장소를 Public으로 전환

GitHub Pages는 무료 플랜(Free)에서는 **Public 저장소**에서만 사용할 수 있습니다.
지금 저장소가 Private으로 되어 있으니 전환이 필요합니다. (회사 소개 웹사이트라
어차피 공개되는 내용이라 문제 없습니다. 저장소 안에 비밀번호나 API 키 같은 건
없으니 안심하셔도 됩니다.)

1. 저장소 페이지 상단 **Settings** 클릭
2. 왼쪽 메뉴 맨 아래 **General** 탭에서 스크롤을 끝까지 내려 **Danger Zone** 찾기
3. **Change repository visibility** → **Change visibility** → **Make public** 선택
4. 저장소 이름(`jinhxxon/jakeunpyeolli-site`)을 입력해 확인

---

## 2) GitHub Pages 켜기

1. 같은 **Settings** 화면 왼쪽 메뉴에서 **Pages** 클릭
2. **Build and deployment** 항목에서
   - Source: **Deploy from a branch**
   - Branch: **main**, 폴더는 **/ (root)** 선택
3. **Save** 클릭

1~2분 정도 지나면 페이지 상단에 초록색 안내와 함께
`https://jinhxxon.github.io/jakeunpyeolli-site/` 같은 주소가 나타납니다.
먼저 이 주소로 들어가서 사이트가 정상적으로 보이는지 확인하세요.

---

## 3) stopy.io 커스텀 도메인 연결

### 3-1. 도메인 등록기관에서 DNS 레코드 추가
stopy.io를 구매한 등록기관(가비아, 후이즈 등)의 DNS 관리 화면으로 이동해서
아래 레코드를 추가합니다.

**루트 도메인(stopy.io)용 — A 레코드 4개 추가**

| 유형 | 호스트 | 값 |
|---|---|---|
| A | @ (또는 공백) | 185.199.108.153 |
| A | @ (또는 공백) | 185.199.109.153 |
| A | @ (또는 공백) | 185.199.110.153 |
| A | @ (또는 공백) | 185.199.111.153 |

**www 서브도메인용 — CNAME 레코드 1개 추가**

| 유형 | 호스트 | 값 |
|---|---|---|
| CNAME | www | jinhxxon.github.io |

(등록기관마다 "호스트/이름" 칸 표기가 `@`, 공백, 또는 도메인 그대로일 수 있습니다.
루트 도메인을 의미하는 값을 넣으면 됩니다.)

### 3-2. GitHub Pages에 커스텀 도메인 등록
1. 저장소 **Settings → Pages** 화면으로 다시 이동
2. **Custom domain** 입력란에 `stopy.io` 입력 후 **Save**
   - 이 과정에서 저장소 루트에 `CNAME`이라는 파일이 자동으로 하나 생깁니다. 지우지 마세요.
3. DNS가 전파되면 (보통 몇 분~몇 시간, 최대 24시간) 같은 화면에
   **"DNS check successful"** 표시가 뜨고, **Enforce HTTPS** 체크박스가 활성화됩니다.
   체크해서 HTTPS를 강제 적용하세요.

이렇게 하면 `https://stopy.io`와 `https://www.stopy.io` 모두 사이트로 연결됩니다.

---

## 4) 이후 콘텐츠 수정 시

파일을 수정한 뒤 GitHub 저장소 화면에서 "Upload files"로 다시 올리거나, 이미
로컬에 git으로 클론해두셨다면 `git push`만 하면 자동으로 재배포됩니다.
(별도 빌드 과정 없이 몇 초~1분 내 반영됩니다.)

---

## 참고
- 이 방법은 완전 무료이며 카드 등록이 필요 없습니다.
- DNS 반영에는 시간이 걸릴 수 있습니다. 등록기관 화면에서 레코드를 저장한 직후
  바로 안 되더라도 정상이니 몇 시간 뒤 다시 확인해보세요.
- 앞서 드린 Azure 배포 가이드(`DEPLOY_GUIDE.md`)는 참고용으로 남겨두지만,
  지금은 이 GitHub Pages 방법을 따라가시면 됩니다.
