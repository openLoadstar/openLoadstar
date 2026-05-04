> 🌐 **[English](README.md)** | **한국어**

# openLoadstar

> **AI 에이전트와 함께 일하는 개발자를 위한, 코드와 메타데이터의 길잡이 시스템**

openLoadstar는 AI가 프로젝트의 의도와 진행 상황을 정확히 이해하고 작업할 수 있도록, **WayPoint** 라는 작업 단위 기반의 메타데이터 체계를 제공합니다. SPEC 문서, CLI 도구, 웹 UI, MCP 서버까지 4개 컴포넌트로 구성되며, 어떤 환경의 AI 클라이언트(Claude Code · Cursor · Claude Desktop 등)에서도 동일한 작업 맥락을 복원할 수 있습니다.

---

## 🤔 왜 openLoadstar 인가

AI 에이전트와 협업하는 개발자라면 한 번쯤 겪는 문제들입니다.

- **세션이 끝나면 컨텍스트가 사라진다** — 다음 대화에서 같은 설명을 또 해야 한다.
- **코드와 문서가 따로 논다** — 현재 무엇을 작업 중인지, 어디서 멈췄는지 메타데이터로 남지 않는다.
- **AI가 코드베이스를 광역 탐색하면서 토큰을 낭비한다** — 작업 범위 제한이 없다.
- **"무엇을 왜 하는가"가 코드 어디에도 없다** — 의도(intent)는 PR 설명이나 Slack에 흩어져 있다.

openLoadstar는 이를 **파일 기반 메타데이터(WayPoint·Map·Decision)** 로 풀어냅니다. AI는 세션 시작 시 진입점 파일 하나만 읽으면 현재 작업 맥락을 복원하고, CODE_MAP에 정의된 범위 내에서만 코드를 탐색하며, TECH_SPEC 체크박스로 진행도를 추적합니다.

### 설계 원칙: Tolerable Consistency (허용 범위 내 일관성)

> 코드와 메타데이터의 **완전 일관성을 목표로 하지 않습니다.**
> 매 코드 수정마다 모든 메타를 갱신하는 비용은 그 효과를 초과합니다. openLoadstar는 "드리프트를 알고 있는 상태"를 목표로, SYNCED_AT과 변경 로그를 통해 누적 드리프트를 모니터링하고 주기적으로 조정합니다.

> 💡 **드리프트(drift)** — 코드와 메타데이터(`.loadstar/`) 사이의 불일치 상태. 코드는 바뀌었는데 WayPoint의 TECH_SPEC이나 CODE_MAP은 갱신되지 않은 경우 등이 해당합니다. 완전 제거가 아니라 **인지·관리** 가 목표입니다.

---

## 🧭 핵심 개념 & 용어

### 5대 요소

| 요소 | 약어 | 역할 |
|:---|:---|:---|
| **Map** | `M://` | WayPoint 묶음을 위한 인덱스 (계층 경로) |
| **WayPoint** | `W://` | **모든 작업의 실행 단위** (의도·TECH_SPEC·CODE_MAP·진행도) |
| **Link** | `L://` | 요소 간 논리적 관계 (참조·선후·검증) |
| **SavePoint** | `S://` | 물리적 좌표 (Git 커밋·파일·라인) |
| **Decision** | — | OPEN_QUESTIONS의 결정 기록 (ADR 패턴) |

### 용어 풀이

| 용어 | 의미 |
|:---|:---|
| **드리프트(drift)** | 코드와 메타데이터(`.loadstar/`) 간 불일치. 완전 제거가 아닌 **인지·관리** 가 목표 |
| **TECH_SPEC** | WayPoint 안의 작업 항목 체크박스. `[ ]` 미완료 / `[x] YYYY-MM-DD` 완료 |
| **CODE_MAP** | WayPoint가 코드를 수정할 때 AI의 탐색 범위(scope)를 디렉토리 단위로 한정 — 광역 탐색 비용 절감 |
| **STATUS** | `S_IDL`(대기) / `S_PRG`(진행) / `S_STB`(안정) / `S_ERR`(오류) / `S_REV`(검토 필요) |
| **TODO 상태** | `PENDING` / `ACTIVE` / `BLOCKED` / `COMPLETED` / `FAILED` |
| **SYNCED_AT** | 해당 요소가 실제 코드와 마지막으로 일치한 시각. 30일 초과 시 CODE_MAP 재확인 권장 |
| **OPEN_QUESTIONS** | 작업 착수 전 사람의 결정이 필요한 미결 질문. AI는 미해결 항목이 있으면 사람에게 확인 |
| **`.loadstar/`** | 메타데이터 루트 디렉토리 (Map·WayPoint·Decision 등) |
| **`.clionly/`** | `.loadstar/.clionly/` — CLI 전담 영역. **AI도 사람도 직접 접근 금지** (LOG·TODO_LIST·스냅샷) |
| **LOADSTAR_INIT.md** | AI 세션 진입점 파일. 매 세션 시작 시 이 파일을 읽어 프로젝트 컨텍스트 복원 |

### 주소 체계

```
M://root/cli                 →  .loadstar/MAP/root.cli.md
W://root/cli/cmd_show        →  .loadstar/WAYPOINT/root.cli.cmd_show.md
```

자세한 SPEC은 **[openLoadstar/spec](https://github.com/openLoadstar/spec)** 참조.

---

## 📦 4개 컴포넌트

| 컴포넌트 | 역할 | 스택 | 저장소 |
|:---|:---|:---|:---|
| **spec** | LOADSTAR 방법론 명세 (SPEC 문서) | Markdown | [openLoadstar/spec](https://github.com/openLoadstar/spec) |
| **cli** | WayPoint·Map·TODO 관리 CLI 도구 | Go + cobra | [openLoadstar/cli](https://github.com/openLoadstar/cli) |
| **ui** | Explorer 웹 UI (시각화·편집) | Spring Boot 3 + React 19 | [openLoadstar/ui](https://github.com/openLoadstar/ui) |
| **mcp** | MCP 서버 (Claude Desktop·Cursor 등 외부 AI 클라이언트 연동) | Python 3.10+ | [openLoadstar/mcp](https://github.com/openLoadstar/mcp) |

### 어떤 조합으로 쓰면 되는가

| 사용 시나리오 | 필요한 컴포넌트 |
|:---|:---|
| Claude Code 같은 IDE 통합 환경에서만 쓴다 | **cli** (필수) |
| 시각화·검색·편집을 웹에서 하고 싶다 | cli + **ui** |
| Claude Desktop / Cursor 등 외부 AI 클라이언트에서 쓴다 | cli + **mcp** |
| 모두 통합 운용 | cli + ui + mcp |

> **spec** 은 모든 시나리오에서 참조 문서로 사용됩니다. 직접 설치하지는 않고, 필요할 때 SPEC 저장소를 보면 됩니다.

---

## 🛠️ 설치 가이드

> 모든 컴포넌트의 자세한 빌드·설치 방법은 각 저장소 README를 따라가세요. 여기서는 **전체 설치 흐름**만 안내합니다.

### Step 1. CLI 설치 (필수)

```bash
git clone https://github.com/openLoadstar/cli.git
cd cli
go build -o bin/loadstar.exe .
```

`bin/loadstar.exe` 를 PATH에 추가하거나, 절대경로로 호출합니다.

### Step 2. UI 설치 (권장)

CLI만으로도 모든 기능을 쓸 수 있지만, 웹 UI를 함께 띄우면 WayPoint·Map·TODO 시각화, 검색, 그래프 탐색, 편집을 훨씬 효율적으로 수행할 수 있습니다. 다수의 WayPoint를 다루는 프로젝트라면 거의 필수에 가깝습니다.

```bash
git clone https://github.com/openLoadstar/ui.git
cd ui
# Backend (Spring Boot)
cd backend && mvn spring-boot:run
# Frontend (Vite)
cd frontend && npm install && npm run build
```

기본 URL: `http://localhost:8080`

### Step 3. MCP 서버 설치 (선택)

Claude Desktop·Cursor 등 외부 AI 클라이언트에서 LOADSTAR 도구를 호출하려면:

```bash
git clone https://github.com/openLoadstar/mcp.git
cd mcp
pip install -e .
# 또는 uv sync
```

Claude Desktop의 경우 `claude_desktop_config.json` 에 다음과 같이 등록:

```json
{
  "mcpServers": {
    "loadstar": {
      "command": "python",
      "args": ["-m", "loadstar_mcp"],
      "env": {
        "LOADSTAR_CLI_PATH": "C:/path/to/loadstar.exe",
        "LOADSTAR_SPEC_PATH": "C:/path/to/loadstar_SPEC"
      }
    }
  }
}
```

자세한 설정은 [openLoadstar/mcp](https://github.com/openLoadstar/mcp) README 참조.

---

## 🤖 AI 세션 진입 프롬프트 (필수)

> ⚠️ **AI와 첫 작업을 시작하는 세션에서는 반드시 SPEC을 먼저 로딩해야 합니다.** AI가 LOADSTAR의 요소 포맷·주소 체계·운영 규약을 이해하지 못한 상태에서 메타데이터를 건드리면 정합성이 깨집니다. SPEC은 `openLoadstar/spec` 저장소의 마크다운 파일들이며, 매 세션 시작 시 한 번만 로딩하면 그 세션 동안 유지됩니다.

아래는 실제 운용 중인 **세션 진입 프롬프트(`/loadstar-enter`)** 의 예시입니다. Claude Code의 슬래시 커맨드, Cursor의 룰, 또는 일반 채팅 첫 메시지로 그대로 사용할 수 있습니다.

````markdown
LOADSTAR SPEC 기준으로 이 프로젝트의 현재 상태를 검토하고 오늘 할 작업을 식별해줘.

## 진입 순서 (엄격 준수)

1. **SPEC 파악** — `<SPEC_PATH>/` 에서 01, 02, 03, 05, 07, 08 을 읽어
   요소 포맷·주소 체계·AI 행동 강령을 이해한다 (필요한 것만 lazy load).
2. **프로젝트 진입점** — `.loadstar/LOADSTAR_INIT.md` 를 읽어
   AI 참고사항·진행 컨텍스트를 확보한다.
3. **WayPoint 상태 스냅샷** — 다음을 실행:
   - `loadstar show`             # 전체 WP 목록 + STATUS
   - `loadstar todo list`        # PENDING / ACTIVE / BLOCKED
   - `loadstar validate`         # 깨진 참조 검출
   - `loadstar question`         # 미해결 OPEN_QUESTIONS
4. **ACTIVE/S_PRG WP 심층 확인** — 진행 중 WP만 `.loadstar/WAYPOINT/*.md` 를
   Read 하여 TECH_SPEC 체크박스 상태 확인.

## 규칙 (엄수)

- **소스코드 열람 금지** — 메타데이터만으로 판단. 구현 착수 결정 후 필요 시에만 Read.
- **항목 없이 코드 수정 금지** — TECH_SPEC에 없으면 먼저 WP에 `- [ ] 작업내용` 추가.
- **STATUS 전환** — 작업 시작 시 `S_IDL → S_PRG`, 완료 시 전체 `[x]` 후 `S_STB`.
- **SYNCED_AT 30일 초과 WP** — 발견 시 경고 표시. CODE_MAP scope 유효성 확인 권고.
- **OPEN_QUESTIONS 미해결 항목** — 발견 시 사용자에게 먼저 확인 요청.
- **`.clionly/` 직접 접근 금지** — TODO·LOG는 CLI로만 조회.

## 출력 형식

### ACTIVE WayPoints
- W://address — SUMMARY (TECH_SPEC: 완료 N / 전체 M)

### ⚠️ 경고
- SYNCED_AT 30일 초과 / 깨진 참조 / 미해결 OPEN_QUESTIONS

### 💡 오늘 착수 권장 (P1 → P3)
1. [P1] W://address — 이유
````

> 📌 `<SPEC_PATH>` 부분에 본인 환경의 spec 저장소 경로(예: `C:/work/openLoadstar/spec` 또는 `~/oss/spec`)를 채워 넣으세요. **MCP 서버를 사용하는 환경이라면** `loadstar_get_spec` 도구가 자동으로 SPEC을 반환하므로 경로 지정이 불필요합니다.

### 프로젝트 경로 알리기

진입 프롬프트 실행 후 AI가 SPEC을 로드하면, 다음 단계로 **본인의 프로젝트 경로** 를 알려줍니다. AI는 그 디렉토리의 `.loadstar/` 메타데이터를 읽어 현재 작업 컨텍스트를 복원합니다.

```
프로젝트 경로: C:/work/my-project
```

또는 단일 메시지로 합쳐서:

```
/loadstar-enter

프로젝트 경로: ~/work/my-project
```

### Claude Code 사용자를 위한 팁

Claude Code 환경에서는 **사용자 메모리(auto memory)** 가 세션 간에 보존됩니다.

- 프로젝트 경로·SPEC 경로·자주 쓰는 진입 절차를 한 번 알려주면 자동으로 메모리 파일에 저장되어 **다음 세션부터는 별도 입력 없이 자동 복원** 됩니다.
- 진입 프롬프트(`/loadstar-enter`) 자체를 슬래시 커맨드 또는 CLAUDE.md에 고정해 두면, 매 세션 시작 시 한 줄로 호출만 하면 됩니다.
- 프로젝트별 `CLAUDE.md` 에 LOADSTAR 작업 규칙을 적어 두면, 사용자 메모리와 함께 세션마다 자동 주입되어 AI가 일관된 흐름으로 작업합니다.

> Cursor 등 다른 환경에서도 비슷하게 — `.cursorrules` 또는 시스템 프롬프트에 진입 절차를 고정하면 동일한 효과를 얻을 수 있습니다.

---

이후 작업 세션에서는 위 진입 프롬프트가 한 번 실행되면, AI가 컨텍스트를 복원한 상태로 일반 작업 요청("X 기능 추가해줘", "이 버그 분석해줘")을 받아 처리합니다.

---

## 🚀 빠른 시작

### A. 신규 프로젝트에서 시작하기

새 프로젝트 디렉토리에서:

```bash
loadstar init
```

`.loadstar/` 디렉토리와 진입점 파일(`LOADSTAR_INIT.md`)이 생성됩니다. 이제 첫 WayPoint를 만들고 작업을 정의합니다 — AI에게 다음과 같이 요청하면 됩니다.

#### 💬 AI 프롬프트 예시 — 신규 프로젝트

```
이 프로젝트의 첫 LOADSTAR 구조를 만들어줘.

- 프로젝트 개요: [내용]
- 주요 기능: [목록]
- 첫 Map: M://root
- 첫 WayPoint: W://root/initial_setup

W://root/initial_setup 의 TECH_SPEC에 다음 초기 셋업 항목을 등록해줘:
- [ ] 디렉토리 구조 정의
- [ ] 의존성 설치 스크립트 작성
- [ ] 첫 단위 테스트 케이스 추가
```

### B. 기존 프로젝트에 적용하기

이미 코드가 있는 프로젝트에 LOADSTAR 메타데이터를 도입할 때:

```bash
cd <existing-project>
loadstar init
```

진입점 파일이 생성되면, AI에게 코드베이스 분석을 요청하여 초기 Map/WayPoint 구조를 추출합니다.

#### 💬 AI 프롬프트 예시 — 기존 프로젝트

```
이 코드베이스를 LOADSTAR 메타데이터로 정리하고 싶어.

1. CLAUDE.md 에 LOADSTAR 규칙 섹션을 추가해 (spec/01.MASTER_GUIDE.md 참조)
2. 주요 모듈을 분석해서 Map 계층을 만들어줘 — 예: M://root/backend, M://root/frontend
3. 각 모듈의 핵심 책임을 WayPoint로 정의해. CODE_MAP.scope 에 해당 디렉토리를 명시해.
4. 현재 진행 중인 작업이 있으면 해당 WayPoint의 TECH_SPEC 에 [ ] 항목으로 등록해.

작업 후 `loadstar show` 와 `loadstar validate` 로 깨진 참조가 없는지 확인해줘.
```

---

## ⚠️ 사용 시 유의점

### 절대 규칙
- **`.loadstar/.clionly/` 직접 접근 금지** — CLI 전담 영역. AI 에이전트도 직접 읽기·쓰기 금지. 직접 접근 시 LOG와 실제 메타 상태 간 정합성이 영구적으로 깨집니다.

### 권장 규칙
- **항목 없이 코드 수정 금지** — 코드 수정 전 대상 WayPoint의 TECH_SPEC에 `- [ ] 작업 내용` 을 먼저 등록합니다. 빠른 버그 수정의 경우 사후 등록도 허용됩니다.
- **STATUS 전환 시점**:
  - 작업 시작 시 `S_IDL → S_PRG`
  - 모든 TECH_SPEC 항목이 `[x]` 가 되면 `S_PRG → S_STB`
- **SYNCED_AT 30일 초과 WP** — CODE_MAP의 scope 가 여전히 유효한지 확인한 후 작업합니다.
- **OPEN_QUESTIONS 미해결 항목** — AI는 작업 착수 전 사람에게 결정을 확인합니다.

### 현실적인 운영 원칙
- **메타데이터를 100% 일관되게 유지하려 애쓰지 말 것** — Tolerable Consistency. 드리프트는 자연스러운 현상이며, `loadstar validate` 와 주기적 조정 세션으로 관리합니다.
- **AI 세션 시작 시 `LOADSTAR_INIT.md` 자동 로드** — CLAUDE.md, `.cursorrules`, System Prompt 등에 진입 절차를 고정해 매 세션 컨텍스트를 자동 복원합니다.

---

## 📚 더 알아보기

- 📖 **SPEC 문서**: [openLoadstar/spec](https://github.com/openLoadstar/spec) — 요소 포맷, 주소 체계, AI 행동 강령
- 🛠️ **CLI 명령 레퍼런스**: [openLoadstar/cli](https://github.com/openLoadstar/cli)
- 🖥️ **UI 사용 가이드**: [openLoadstar/ui](https://github.com/openLoadstar/ui)
- 🔌 **MCP 도구 명세**: [openLoadstar/mcp](https://github.com/openLoadstar/mcp)

---

## 📮 Contact & Support

- 🐛 **버그 리포트**: [GitHub Issues](https://github.com/openLoadstar/openLoadstar/issues)
- 💬 **질문·아이디어**: [GitHub Discussions](https://github.com/openLoadstar/openLoadstar/discussions)
- 🤝 **기여 안내**: [CONTRIBUTING.ko.md](./CONTRIBUTING.ko.md)
- 🔒 **보안 취약점**: [SECURITY.ko.md](./SECURITY.ko.md) — 비공개 신고 절차
- 👤 **메인테이너**: [@aeolusk](https://github.com/aeolusk)

---

## 📄 License

[Apache License 2.0](./LICENSE)
