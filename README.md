# ai-domain-check-list

AI 도구의 **공식 도메인**과 **사칭으로 확인된 도메인**을 모아둔 공개 데이터베이스입니다.

[AI Domain Check 크롬 확장프로그램](https://github.com/ai-domain-check/ai-domain-check)이 이 데이터를 가져와, 결제·로그인 직전에 사이트가 진짜인지 한눈에 보여줍니다.

| 배지 | 의미 |
|---|---|
| 🟢 공식 | 이 저장소 `whitelist.json`에 등록된 도메인 |
| 🟡 미확인 | 어디에도 등록되지 않은 일반 도메인 |
| 🔴 사칭 의심 | 이 저장소 `blacklist.json`에 등록된 도메인 |

---

## 도와주세요 — 한 번 클릭이면 됩니다

### 사칭 사이트를 발견하셨나요?

[**사칭 도메인 신고하기 →**](../../issues/new?template=domain-report.yml)

폼에 도메인·증거(스크린샷)만 적어주시면 됩니다. 메인테이너가 검토 후 `blacklist.json`에 추가하면 모든 사용자가 보호받습니다.

### 우리 사이트가 공식인데 미확인으로 떠요

[**화이트리스트 등록 요청하기 →**](../../issues/new?template=whitelist-request.yml)

공식 도메인이라는 증거(공식 announcement, Wikipedia 등)를 두 개 이상 첨부해주세요.

---

## 데이터는 이렇게 생겼습니다

두 개의 JSON 파일이 전부입니다.

```json
// whitelist.json — 공식 도메인
{
  "domain": "chatgpt.com",
  "publisher": "OpenAI",
  "allowSubdomains": true,
  "evidence": ["https://openai.com/index/chatgpt/"]
}

// blacklist.json — 사칭 도메인
{
  "domain": "sites.google.com",
  "paths": ["/view/claudversion09"],
  "reasonCode": "phishing",
  "impersonates": "claude.ai",
  "evidence": ["..."]
}
```

확장프로그램은 다음 URL에서 데이터를 6시간마다 자동으로 가져옵니다.

- `https://raw.githubusercontent.com/ai-domain-check/ai-domain-check-list/main/whitelist.json`
- `https://raw.githubusercontent.com/ai-domain-check/ai-domain-check-list/main/blacklist.json`

<details>
<summary><strong>스키마 자세히 보기 (개발자·기여자용)</strong></summary>

### whitelist 엔트리

| 필드 | 필수 | 설명 |
|---|---|---|
| `domain` | ✓ | 소문자 호스트네임. 예: `openai.com` |
| `publisher` | ✓ | 운영 주체. 예: `OpenAI` |
| `category` |  | `llm` / `image` / `audio` / `video` / `code` / `agent` / `other` |
| `allowSubdomains` |  | `true`면 `*.domain` 전부 허용. 기본 `false` |
| `paths` |  | UGC 호스팅에서 특정 경로만 신뢰할 때. 예: `["/features/copilot"]` |
| `evidence` | ✓ | 공식 도메인 증명 URL 1개 이상 |
| `addedAt` | ✓ | `YYYY-MM-DD` |
| `addedBy` |  | GitHub 핸들 |

### blacklist 엔트리

| 필드 | 필수 | 설명 |
|---|---|---|
| `domain` | ✓ | 사칭 도메인 |
| `reasonCode` | ✓ | `typosquat` / `clone` / `phishing` / `malware` / `other` |
| `impersonates` |  | 사칭 대상 공식 도메인 |
| `allowSubdomains` |  | 같은 캠페인이 서브도메인 다발이면 `true` |
| `paths` |  | UGC 호스팅 위 사칭일 때 (`sites.google.com/view/xxx` 등) |
| `evidence` | ✓ | 증거 URL 1개 이상 |
| `addedAt` | ✓ | `YYYY-MM-DD` |
| `addedBy` |  | GitHub 핸들 |

### `paths`가 필요한 경우

도메인 자체가 사용자 콘텐츠 호스팅 플랫폼(GitHub, Notion, Google Sites 등)일 때입니다. 도메인 전체에 녹색/빨간색을 칠하면 정상 사용자 페이지까지 잘못 분류됩니다. `paths`로 운영 주체가 제어하는 특정 경로만 좁혀주세요.

</details>

---

## 운영 원칙

- 모든 변경은 PR로 진행하며 머지 전 메인테이너 검토를 거칩니다
- 화이트리스트 추가는 객관적·공개 검증 가능한 증거가 필수입니다
- 분기별로 도메인이 여전히 유효한지 재검증합니다

자세한 기여 흐름·메인테이너 가이드는 [`CONTRIBUTING.md`](./CONTRIBUTING.md)를 참고해주세요.

---

## 라이선스

추후 정식 공개 시점에 추가됩니다 (현재 후보: CC0 또는 CC BY 4.0).
