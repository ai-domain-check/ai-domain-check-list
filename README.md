# ai-domain-check-list

[`ai-domain-check/ai-domain-check`](https://github.com/ai-domain-check/ai-domain-check) 크롬 확장프로그램이 참조하는 공개 도메인 데이터베이스입니다. 어떤 도메인이 AI 도구의 공식 사이트인지(화이트리스트), 어떤 도메인이 사칭으로 확인되었는지(블랙리스트)를 사람이 검토 가능한 JSON 형식으로 관리합니다.

확장프로그램은 이 저장소의 `whitelist.json`과 `blacklist.json`을 다음 URL에서 가져와 `chrome.storage.local`에 캐싱합니다.

- `https://raw.githubusercontent.com/ai-domain-check/ai-domain-check-list/main/whitelist.json`
- `https://raw.githubusercontent.com/ai-domain-check/ai-domain-check-list/main/blacklist.json`

저장소가 코드와 분리되어 있어 데이터 추가·수정이 PR 하나로 처리되며, 확장프로그램 재배포가 필요하지 않습니다.

## 파일 구조

```
ai-domain-check-list/
├── whitelist.json            # 공식 도메인 (정식 운영 시 생성)
├── blacklist.json            # 사칭 확인 도메인 (정식 운영 시 생성)
├── whitelist.example.json    # 스키마 예시
├── blacklist.example.json    # 스키마 예시
├── CONTRIBUTING.md           # PR 가이드, 출처 증명 요구사항
└── README.md
```

## 화이트리스트 스키마

각 엔트리는 다음 필드를 갖습니다.

| 필드 | 필수 | 설명 |
| --- | --- | --- |
| `domain` | ✓ | 호스트네임. 소문자, 프로토콜 없이. 예: `openai.com` |
| `publisher` | ✓ | 운영 주체. 예: `OpenAI` |
| `category` |  | `llm` / `image` / `audio` / `video` / `code` / `agent` / `other` |
| `allowSubdomains` |  | `true`이면 모든 서브도메인 허용. 기본값 `false` (완전 일치) |
| `paths` |  | 경로 prefix 배열. 지정 시 hostname 매칭 후 URL pathname이 이 중 하나로 시작해야 official. 사용자 콘텐츠 호스팅 플랫폼(아래 참고)에서 특정 페이지만 신뢰하고 싶을 때 사용. 미지정/빈 배열이면 hostname-only 매칭. |
| `evidence` | ✓ | 공식 도메인 증명 URL 1개 이상 (공식 발표·DNS 조회 결과 등) |
| `addedAt` | ✓ | 추가 날짜 `YYYY-MM-DD` |
| `addedBy` |  | 메인테이너 GitHub 핸들 |

### `paths`를 언제 써야 하나 — 사용자 콘텐츠 플랫폼 처리

도메인이 임의의 사용자 콘텐츠를 호스팅하는 플랫폼(예: GitHub, GitLab, Hugging Face, Notion, Replicate)이라면 hostname-only 매칭이 위험합니다. 누군가 사칭 레포·페이지·모델을 만들면 우리 확장프로그램이 잘못된 "공식" 보증을 줄 수 있기 때문입니다.

예: `github.com`을 단순 hostname-only로 허용하면 `github.com/fake-openai/whisper-trojan`도 녹색 배지가 떠 사용자가 사칭 레포를 공식으로 오인할 수 있습니다.

이런 도메인은 `paths`를 명시해 **특정 경로만** 공식으로 인정합니다.

```json
{
  "domain": "github.com",
  "publisher": "GitHub Copilot",
  "paths": ["/features/copilot", "/settings/copilot"],
  ...
}
```

이러면 `github.com/features/copilot`은 녹색이지만 `github.com/anyone/repo`는 미확인으로 떨어집니다.

**경로 매칭 규칙:**
- pathname이 `paths`의 어느 한 prefix와 정확히 일치하거나, prefix + `/`로 시작하면 매칭
- `/features/copilot`은 매칭, `/features/copilothelp`는 매칭 안 됨
- `/features/copilot/setup`은 매칭됨 (하위 경로)
- 대소문자 무시

## 블랙리스트 스키마

| 필드 | 필수 | 설명 |
| --- | --- | --- |
| `domain` | ✓ | 사칭 도메인 |
| `reasonCode` | ✓ | `typosquat` / `clone` / `phishing` / `malware` / `other` |
| `impersonates` |  | 사칭 대상 화이트리스트 도메인 |
| `evidence` | ✓ | 사칭 증거 URL (스크린샷, 신고 내역, 분석 글 등) |
| `addedAt` | ✓ | 추가 날짜 |
| `addedBy` |  | 메인테이너 GitHub 핸들 |

## 기여

도메인 추가·수정·신고 절차는 [`CONTRIBUTING.md`](./CONTRIBUTING.md)를 참고해주세요.

## 운영 원칙

- 모든 변경은 PR로 진행하며 메인테이너 2인 이상 승인 후 머지합니다.
- main 브랜치 보호, signed commits, 메인테이너 2FA 의무를 적용합니다.
- 분기별로 화이트리스트의 도메인이 여전히 공식인지 재검증합니다(공식 사이트가 폐쇄·이전되는 경우가 있음).

## 라이선스

추후 정식 공개 시점에 추가될 예정입니다(현재 후보: CC0 또는 CC BY 4.0).
