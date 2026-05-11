# 기여 가이드

이 저장소는 AI 도구 사이트의 신뢰도 판정 데이터를 사람이 검토 가능한 JSON 형식으로 관리합니다. 잘못된 항목 하나가 사용자 결제 안전과 직결되므로, 모든 변경은 PR + 메인테이너 2인 승인을 거칩니다.

## 변경 유형

1. **공식 도메인 추가** — `whitelist.json`에 새 엔트리 추가
2. **사칭 도메인 추가** — `blacklist.json`에 새 엔트리 추가
3. **도메인 정보 수정** — publisher 변경, 서브도메인 정책 조정 등
4. **만료된 항목 제거** — 폐쇄·이전된 공식 사이트 정리

## 공식 도메인 PR 절차

1. 이 저장소를 fork합니다.
2. `whitelist.json`에 엔트리를 추가합니다. 스키마는 `README.md`와 `whitelist.example.json`을 참고해주세요.
3. **출처 증명을 1개 이상 첨부합니다.** 다음 중 어느 하나여야 합니다.
   - 운영 주체의 공식 사이트에서 해당 도메인을 자기 도메인으로 명시한 페이지 URL
   - 공식 블로그·언론 발표
   - DNS WHOIS 결과(운영 주체 명의 확인 가능한 경우)
   - 공식 SNS 계정에서 해당 도메인을 안내한 게시물
4. PR 제목은 `[whitelist] add example.com (OpenAI)` 형식으로 작성해주세요.
5. PR 본문에 다음을 포함해주세요.
   - 어떤 AI 도구의 어떤 운영 주체인지
   - 출처 증명 URL과 그 신뢰성 근거
   - 서브도메인 허용 여부와 그 이유

## 사칭 도메인 PR 절차

1. 가능하면 먼저 [Issue]로 신고하여 메인테이너가 검증할 수 있도록 해주세요. PR 직접 제출도 가능합니다.
2. `blacklist.json`에 엔트리를 추가합니다.
3. **사칭 증거를 첨부합니다.** 다음 중 어느 하나여야 합니다.
   - 페이지 스크린샷(공식 브랜딩 도용 확인 가능)
   - 결제 페이지가 공식과 다른 경로/주소로 이동하는 증거
   - 공식 운영 주체의 사칭 신고 게시물
   - 보안 분석 글·언론 보도
4. PR 본문에 어떤 공식 도메인을 사칭하는지 명시해주세요.

## 메인테이너 검토 기준

- 출처 증명이 1개 이상 있고 공개적으로 검증 가능한가
- `domain` 값이 소문자·프로토콜 없는 호스트네임 형식인가
- 이미 등록된 항목과 중복되지 않는가
- 화이트리스트와 블랙리스트에 동시에 들어가 있지 않은가
- **사용자 콘텐츠를 호스팅하는 플랫폼이면 `paths` 필드가 적절히 지정되었는가** (아래 참고)

### 사용자 콘텐츠 호스팅 플랫폼은 반드시 `paths`로 좁힐 것

다음과 같이 임의의 사용자 콘텐츠가 같은 도메인 안에 호스팅되는 경우, hostname-only 매칭은 사칭 레포·페이지에 잘못된 "공식" 보증을 줄 위험이 있습니다.

- 코드 호스팅: `github.com`, `gitlab.com`, `bitbucket.org`, `codeberg.org`, `sourceforge.net`
- AI 모델·데이터 허브: `huggingface.co`, `replicate.com`, `kaggle.com`
- 문서·페이지 호스팅: `notion.so`, `medium.com`, `substack.com`
- 기타: 도메인 안에서 사용자가 새 경로를 임의로 만들 수 있는 모든 플랫폼

이런 도메인은 `paths`에 **공식 운영 주체가 제어하는 특정 경로만** 명시해주세요. 예시:

```json
{
  "domain": "github.com",
  "publisher": "GitHub Copilot",
  "paths": ["/features/copilot", "/settings/copilot"],
  ...
}
```

이러면 `github.com/features/copilot`은 녹색이지만, 누군가 만든 `github.com/fake-org/scam-repo`는 미확인으로 떨어집니다.

판단 기준: "이 도메인 안에 누군가가 임의 경로를 만들 수 있는가?" Yes면 `paths` 필수.

**blacklist 쪽에도 같은 규칙이 적용됩니다.** UGC 플랫폼 위의 사칭 페이지(예: `sites.google.com/view/fake-claude`)는 hostname 전체를 blacklist에 넣으면 정상 사용자 페이지까지 빨간 배지가 떠버립니다. 반드시 `paths`로 좁혀주세요.

```json
{
  "domain": "sites.google.com",
  "paths": ["/view/some-fake-claude"],
  "reasonCode": "phishing",
  "impersonates": "claude.ai",
  ...
}
```

승인은 메인테이너 2인 이상이 필요하며, 두 명이 같은 출처에 의존하지 않도록 노력합니다.

## 신고만 하고 싶을 때

도메인을 직접 PR로 올리기 부담스러우면 **[사칭 도메인 신고 Issue 템플릿](../../issues/new?template=domain-report.yml)** 을 사용해주세요. 폼 형식이라 입력 누락 없이 신고할 수 있습니다.

폼이 받는 정보:
- 신고할 도메인 또는 URL
- 사칭 대상 공식 AI 도구
- 어떻게 발견했는지 (광고/검색/메일 등)
- 사칭 증거 (스크린샷·외부 링크)
- 추가 정보 (선택)

AI Domain Check 크롬 확장프로그램의 popup에서 "신고" 버튼을 누르면 이 템플릿으로 자동 연결되며, 현재 보고 있는 URL이 도메인 필드에 미리 채워집니다.

메인테이너가 검증 후 blacklist PR로 옮깁니다 — 단, **이 PR 생성은 GitHub Actions가 자동으로** 합니다 (`.github/workflows/issue-to-blacklist-pr.yml`). 메인테이너 역할은 자동 생성된 PR을 검토·머지하는 것뿐입니다.

### 메인테이너 1회 설정

**1. 라벨 생성** — `Issues` 탭 → `Labels` → New label

| 이름 | 색상 | 설명 |
|---|---|---|
| `domain-report` | `#ef4444` | 사칭 의심 도메인 신고 / Suspicious domain impersonation report |
| `needs-triage` | `#f59e0b` | 메인테이너 초기 검토 대기 / Awaiting maintainer triage |
| `blacklist-pr` | `#7c3aed` | 자동 생성된 blacklist 추가 PR / Auto-drafted blacklist PR |
| `needs-review` | `#3b82f6` | 메인테이너 검토 필요 / Awaiting maintainer review |

GitHub은 존재하지 않는 라벨을 silently drop하므로 미리 만들어두세요.

**2. Actions가 PR을 만들 수 있게 권한 허용** — Settings → Actions → General:

- "Workflow permissions" 섹션에서 **Read and write permissions** 선택
- **Allow GitHub Actions to create and approve pull requests** 체크

이 설정 안 하면 워크플로가 PR 생성 단계에서 권한 오류로 실패합니다.

### 자동 PR 워크플로 동작 방식

이슈가 `domain-report` 라벨로 생성되면:

1. 워크플로가 이슈 본문(폼 출력)을 파싱해 `domain`, `paths`, `impersonates` 추출
2. URL이면 hostname과 pathname을 자동 분리 (UGC 플랫폼 대응)
3. `reasonCode`는 기본 `phishing`으로 설정 (메인테이너가 PR에서 조정 가능)
4. blacklist.json에 entry 추가하고 새 브랜치에 커밋
5. PR 생성: 제목·본문에 원 이슈 링크 + 체크리스트 자동 포함
6. PR에 `blacklist-pr` + `needs-review` 라벨 자동 부여

메인테이너는 PR을 열어 4가지만 확인하면 됩니다:
- domain/paths가 정확한지
- reasonCode가 phishing이 맞는지 (아니면 typosquat 등으로 변경)
- impersonates 매칭이 맞는지
- evidence가 충분한지 (스크린샷 추가 등)

문제없으면 머지. 다음 fetch 사이클(최대 6시간, popup 새로고침/페이지 reload 시 즉시)에 모든 사용자에게 반영됩니다.

## 거부되는 PR 패턴

- 출처 증명이 없거나 검증 불가능한 출처(폐쇄된 사이트, 비공개 자료)인 경우
- 단순한 의심·추측만으로 블랙리스트 추가 요청한 경우
- 화이트리스트 도메인이 명백하지 않은데 등록 요청한 경우(예: 중복 운영 주체, 도메인 소유주 불명)
- 합법 호스팅 플랫폼 자체(`vercel.app`, `notion.site` 등)를 등록하려는 경우

[Issue]: ../../issues
