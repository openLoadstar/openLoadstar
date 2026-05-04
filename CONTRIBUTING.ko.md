> 🌐 **[English](CONTRIBUTING.md)** | **한국어**

# openLoadstar 기여 가이드

openLoadstar에 기여를 고려해 주셔서 감사합니다. 이 문서는 효율적인 협업을 위한 일반적인 절차와 컨벤션을 안내합니다.

---

## 🧭 시작하기 전에

openLoadstar는 5개의 저장소로 구성됩니다. 기여하려는 내용에 맞는 저장소를 먼저 선택하세요.

| 저장소 | 어떤 기여를 받는가 |
|:---|:---|
| [spec](https://github.com/openLoadstar/spec) | LOADSTAR 방법론·요소 포맷·주소 체계 등 명세 변경 |
| [cli](https://github.com/openLoadstar/cli) | Go 기반 CLI 도구 신기능·버그 수정 |
| [ui](https://github.com/openLoadstar/ui) | Spring Boot + React 웹 UI 개선 |
| [mcp](https://github.com/openLoadstar/mcp) | Python MCP 서버, 외부 AI 클라이언트 연동 |
| [openLoadstar](https://github.com/openLoadstar/openLoadstar) | 통합 README·문서·생태계 안내 |

> 어디에 올릴지 모르겠다면 [openLoadstar Discussions](https://github.com/openLoadstar/openLoadstar/discussions)에서 먼저 질문해 주세요.

---

## 🤝 기여 방법

### 🐛 버그 리포트

해당 저장소의 **Issues** 에 다음을 포함해 등록해 주세요.

- 버그 발생 환경 (OS, 컴포넌트 버전 또는 커밋 해시)
- 재현 절차 (가능하면 최소 예시)
- 기대 동작과 실제 동작
- 관련 로그·에러 메시지

### 💡 기능 제안

작은 개선은 Issue로, **큰 변경(API 추가·SPEC 변경 등)은 Discussions에서 먼저 토론** 해 주세요. 합의 후 PR을 보내면 머지 가능성이 높습니다.

### 🔧 코드 기여

1. 저장소를 **포크** 합니다.
2. main에서 **토픽 브랜치** 를 만듭니다 (예: `feat/show-recent-flag`, `fix/validate-broken-ref`).
3. 변경 내용에 맞는 **테스트** 를 추가하거나 갱신합니다.
4. 로컬에서 **테스트 통과** 를 확인합니다.
5. **PR을 생성** 하고 변경 의도·테스트 방법·관련 Issue를 본문에 명시합니다.
6. CI 통과 + 메인테이너 리뷰 후 머지됩니다.

> ⚠️ main 브랜치에 직접 PR을 보내지 마세요. fork → topic branch 흐름이 표준입니다.

---

## 🛠️ 개발 환경 설정

각 컴포넌트의 빌드 방법과 의존성은 해당 저장소의 README를 참조하세요.

| 컴포넌트 | 필요 도구 |
|:---|:---|
| spec | (마크다운 편집기만 필요) |
| cli | Go 1.21+ |
| ui | JDK 17+, Node 18+, Maven, npm |
| mcp | Python 3.10+, pip 또는 uv |

공통적으로 필요한 도구:
- Git
- 본인의 GitHub 계정과 SSH 키 또는 PAT

---

## 📝 커밋 메시지 컨벤션

**[Conventional Commits](https://www.conventionalcommits.org/)** 형식을 권장합니다.

```
<type>(<scope>): <subject>

<body>
```

### 자주 쓰는 type

| type | 의미 |
|:---|:---|
| `feat` | 신규 기능 |
| `fix` | 버그 수정 |
| `docs` | 문서 변경 (README, 주석) |
| `refactor` | 리팩터링 (기능 변화 없음) |
| `test` | 테스트 추가·수정 |
| `chore` | 빌드·설정·잡일 |

### 예시

```
feat(cli): add --recent flag to show command
fix(ui): prevent crash when WayPoint has no CHILDREN
docs: clarify CODE_MAP scope rule in spec
chore: bump golangci-lint to 1.55
```

- subject는 50자 이내, 한글·영문 모두 허용
- body는 "왜" 변경했는지를 설명 (코드는 "무엇"을 보여줌)
- BREAKING CHANGE는 footer에 명시

---

## 🎨 코드 스타일

각 컴포넌트의 자동 포매터·린터를 따릅니다. PR 전 로컬에서 실행하여 경고 0개를 유지해 주세요.

| 컴포넌트 | 도구 |
|:---|:---|
| cli (Go) | `gofmt`, `go vet`, `golangci-lint` |
| ui backend (Java) | `mvn spotless:apply` (있는 경우), `mvn verify` |
| ui frontend (TS/React) | `npm run lint`, `npm run format` (Prettier) |
| mcp (Python) | `ruff`, `black` |
| 마크다운 | 한 줄당 권장 80자, 표는 `:---:` 정렬 일관성 유지 |

---

## ✅ 테스트 규칙

- **새 기능은 테스트 동반** — 단위 테스트 또는 통합 테스트 중 적합한 것
- **버그 수정은 회귀 테스트와 함께** — 같은 버그가 다시 들어오지 않도록
- 기존 테스트가 깨지면 PR 머지 불가 — CI에서 자동 차단됨

---

## 🔄 PR 체크리스트

PR을 보내기 전에 확인해 주세요.

- [ ] 토픽 브랜치에서 작업했다 (main 직접 수정 ❌)
- [ ] 커밋 메시지가 Conventional Commits 형식을 따른다
- [ ] 새 기능에 테스트를 추가했다
- [ ] 기존 테스트가 모두 통과한다
- [ ] 린터/포매터 경고 0개
- [ ] PR 본문에 변경 의도와 테스트 방법을 적었다
- [ ] 관련 Issue 번호를 명시했다 (해당 시)
- [ ] LOADSTAR 메타데이터(`.loadstar/`)도 함께 갱신했다 (코드 변경 시)

---

## 📜 LOADSTAR 메타데이터 갱신

openLoadstar 자체 컴포넌트에 코드를 기여할 때는 **변경 사항을 메타데이터에도 반영** 해야 합니다.

1. 변경 대상 WayPoint를 찾습니다 (`loadstar show`)
2. TECH_SPEC에 `- [ ] 작업 내용` 항목을 추가합니다 (이미 있으면 사용)
3. 코드 작업 후 `- [x] YYYY-MM-DD 작업 내용` 으로 체크합니다
4. WP의 모든 항목이 완료되었으면 STATUS를 `S_PRG → S_STB` 로 변경합니다
5. 커밋 시 메타 변경도 함께 포함합니다

자세한 절차는 [openLoadstar README의 사용 시 유의점](./README.ko.md#-사용-시-유의점) 섹션을 참조하세요.

---

## ⚖️ 라이선스 동의

openLoadstar에 기여한 모든 코드는 [Apache License 2.0](./LICENSE) 으로 배포됩니다. PR을 제출함으로써 본인 작업물을 이 라이선스로 배포하는 데 동의하는 것으로 간주됩니다.

> 📌 별도의 CLA(Contributor License Agreement) 서명은 요구하지 않으며, 향후 정책이 변경될 경우 사전에 안내합니다.

---

## 🌟 행동 강령

openLoadstar 커뮤니티는 모든 기여자가 존중받고 안전하게 참여할 수 있는 환경을 지향합니다. 차별·괴롭힘·공격적 언어 등은 허용되지 않습니다. 문제가 발생하면 메인테이너에게 알려 주세요.

---

## 🙏 감사

크고 작은 모든 기여(코드·문서·이슈·아이디어)에 감사드립니다. openLoadstar는 커뮤니티 기여로 성장합니다.
