# 기여 가이드

크게 두 가지 흐름이 있습니다.

1. **사칭 의심 도메인 신고** — 누구나 (개발자 아니어도) 가능
2. **공식 도메인 등록 요청** — 누구나 가능, 검증 더 엄격

대부분 시간은 메인테이너가 검토·머지하는 데 들어갑니다. 기여자는 폼만 채우면 됩니다.

---

## 1. 사칭 의심 도메인 신고

### 가장 빠른 방법

크롬 확장프로그램의 popup에서 노란색·빨간색 배지를 보고 **신고** 버튼을 누르면 도메인이 자동으로 채워진 폼이 새 탭에 열립니다.

### 직접 폼 열기

[**사칭 도메인 신고 폼 →**](../../issues/new?template=domain-report.yml)

### 폼에 적을 내용

| 필드 | 예시 |
|---|---|
| 도메인 또는 URL | `https://sites.google.com/view/claudversion09` |
| 사칭 대상 | `Claude (claude.ai)` |
| 발견 경로 | `검색 결과의 광고(Sponsored)` |
| 증거 | 스크린샷 (본문에 드래그) + 추가 설명 |

**개인정보(계정·결제정보)는 본문에 절대 적지 마세요.** 공개 이슈로 모두에게 노출됩니다.

### 제출 후 어떻게 되나요?

폼을 제출하면:

1. 워크플로가 자동으로 PR을 만들어 `blacklist.json`에 추가 제안
2. 메인테이너가 PR을 검토 (도메인·증거 확인)
3. 머지되면 6시간 안에 모든 확장프로그램 사용자에게 반영 (popup 새로고침·페이지 reload 시 즉시)

---

## 2. 공식 도메인 등록 요청

확장프로그램이 "미확인(노란색)"으로 표시하는 사이트가 정말 공식이라면 등록을 요청해주세요.

### 가장 빠른 방법

popup의 미확인 상태에서 **"화이트리스트 등록 요청"** 링크 클릭.

### 직접 폼 열기

[**화이트리스트 등록 요청 폼 →**](../../issues/new?template=whitelist-request.yml)

### 폼에 적을 내용

| 필드 | 예시 |
|---|---|
| 도메인 또는 URL | `https://example.ai` |
| 운영 주체 | `Example Corp` |
| 카테고리 | `llm` / `image` / 등 |
| 증거 | 공식 announcement, Wikipedia, 인증된 SNS 등 **두 개 이상** |

### 신고보다 엄격한 이유

화이트리스트는 사용자에게 "공식" 보증을 주는 데이터입니다. 잘못 추가되면 사용자가 그 도메인을 신뢰하게 되므로, **두 개 이상의 객관적·공개 검증 가능한 증거**가 필수입니다.

거부되는 패턴:
- 운영 주체가 불명확
- 증거가 비공개 자료뿐
- 사용자 콘텐츠 호스팅 플랫폼 자체(notion.site, vercel.app 등)를 추가하려는 시도

---

## 메인테이너 가이드

<details>
<summary><strong>1회 설정</strong></summary>

### Actions 권한 활성화

조직 Settings → Actions → General:
- **Workflow permissions** = `Read and write permissions`
- ✅ `Allow GitHub Actions to create and approve pull requests`

### 라벨 생성

Actions 탭 → **Setup Labels** → **Run workflow** 클릭.

워크플로가 운영에 필요한 라벨 2개(`domain-report`, `whitelist-request`)를 만들고 GitHub 기본 라벨들을 정리합니다.

</details>

<details>
<summary><strong>이슈 → PR 자동화 어떻게 동작하나</strong></summary>

이슈가 `domain-report` 또는 `whitelist-request` 라벨로 생성되면 `.github/workflows/issue-to-*-pr.yml`이 자동 실행됩니다.

**워크플로가 하는 일:**

1. 이슈 본문(폼 출력)에서 필드 추출
2. URL이면 hostname과 pathname 자동 분리
3. `blacklist.json` 또는 `whitelist.json`에 entry 추가
4. 새 브랜치에 커밋 후 PR 생성 (제목 prefix `[blacklist]` 또는 `[whitelist]`)

**메인테이너가 하는 일:** PR을 열어 검토하고 머지(또는 거부).

</details>

<details>
<summary><strong>PR 검토 기준</strong></summary>

### blacklist PR

- [ ] domain·paths가 정확한지
- [ ] `reasonCode`가 적절한지 (기본 `phishing`, 필요 시 `typosquat` 등으로 수정)
- [ ] `impersonates`가 화이트리스트의 공식 도메인과 일치하는지
- [ ] evidence가 검토 가능한지

### whitelist PR (더 엄격)

- [ ] **소유권 검증** — Wikipedia·공식 announcement·whois 등 두 가지 이상 출처로 교차 검증
- [ ] `publisher` 정확한 회사명
- [ ] `allowSubdomains` 결정 — 회사가 그 도메인의 모든 서브도메인을 운영하는가
- [ ] `paths` 필요한가 — UGC 호스팅 위라면 반드시 좁힐 것

</details>

<details>
<summary><strong>이슈 close 흐름</strong></summary>

- **처리 완료** — PR 머지 시 `Closes #N`이 원 이슈를 자동으로 닫음
- **사칭 아님 / 공식 미확인** — 이슈에 사유 코멘트 후 close
- **중복** — GitHub의 "Close as duplicate of #X" 옵션 사용 (close 버튼 드롭다운)

별도 라벨 없이 이슈의 open/closed 상태와 코멘트만으로 추적합니다.

</details>

<details>
<summary><strong>UGC 호스팅 플랫폼 처리</strong></summary>

도메인이 사용자 콘텐츠 호스팅 플랫폼인 경우, hostname 단위로 화이트리스트·블랙리스트에 넣으면 정상 사용자 페이지까지 잘못 분류됩니다. 반드시 `paths`로 좁히세요.

대표적인 UGC 플랫폼:
- 코드: `github.com`, `gitlab.com`, `bitbucket.org`, `codeberg.org`
- 모델·데이터: `huggingface.co`, `replicate.com`, `kaggle.com`
- 문서: `notion.so`, `medium.com`, `substack.com`
- 페이지: `sites.google.com`, `*.vercel.app`, `*.notion.site`

판단 기준: **"이 도메인 안에 누구든 임의 경로를 만들 수 있는가?"** Yes면 `paths` 필수.

예시:

```json
{
  "domain": "github.com",
  "publisher": "GitHub Copilot",
  "paths": ["/features/copilot", "/settings/copilot"]
}
```

이러면 `github.com/features/copilot`은 녹색이지만, `github.com/fake-org/scam-repo`는 미확인으로 떨어집니다.

</details>
