> 🌐 **[English](SECURITY.md)** | **한국어**

# 보안 정책 (Security Policy)

openLoadstar는 보안 취약점 보고를 환영합니다. 발견하신 취약점이 다른 사용자에게 영향을 주기 전에 패치할 수 있도록 협조해 주시면 감사하겠습니다.

---

## 🔢 지원 버전 (Supported Versions)

openLoadstar는 현재 초기 개발 단계(0.x)이며, **항상 최신 main 브랜치**가 보안 패치 대상입니다.

| 컴포넌트 | 지원 버전 |
|:---|:---|
| spec | main (최신) |
| cli | main (최신) |
| ui | main (최신) |
| mcp | main (최신) |
| openLoadstar (overview) | main (최신) |

> 정식 1.0 릴리스 이후에는 마이너 버전별 보안 지원 정책을 수립하여 이 표를 갱신할 예정입니다.

---

## 🚨 취약점 신고 방법 (Reporting a Vulnerability)

> ⚠️ **공개 이슈(GitHub Issues)에 보안 취약점을 올리지 마세요.** 패치 배포 전 공개되면 악용될 위험이 있습니다.

### 1순위: GitHub Security Advisories (권장)

각 저장소의 **Security** 탭에서 비공개로 신고할 수 있습니다. GitHub이 메인테이너에게만 노출되는 비공개 채널을 자동으로 만들어 주며, 향후 CVE 발급까지 지원합니다.

- 메타: https://github.com/openLoadstar/openLoadstar/security/advisories/new
- spec: https://github.com/openLoadstar/spec/security/advisories/new
- cli: https://github.com/openLoadstar/cli/security/advisories/new
- ui: https://github.com/openLoadstar/ui/security/advisories/new
- mcp: https://github.com/openLoadstar/mcp/security/advisories/new

### 2순위: 메인테이너 직접 연락

GitHub Security Advisories를 사용하기 어려운 경우 메인테이너의 GitHub 프로필을 통해 연락해 주세요.

- 👤 [@aeolusk](https://github.com/aeolusk)

### 신고에 포함하면 좋은 정보

- 영향 받는 컴포넌트와 버전(또는 커밋 해시)
- 취약점의 종류와 영향 범위 (예: RCE, 정보 유출, DoS 등)
- 재현 절차 (가능하면 최소 PoC)
- 확인된 실제 영향 (어떤 데이터/시스템이 위협받는지)
- 가능한 완화 방법 (있는 경우)

---

## ⏱️ 응답 일정 (Response Timeline)

다음 일정은 **최선의 노력 기준** 이며, 사안의 심각도와 복잡성에 따라 조정될 수 있습니다.

| 단계 | 목표 시간 |
|:---|:---|
| 신고 접수 회신 | 48시간 이내 |
| 초기 확인·평가 결과 공유 | 7일 이내 |
| 패치 또는 완화 방안 배포 | 90일 이내 |

---

## 📢 공개 정책 (Disclosure Policy)

openLoadstar는 **조정 공개(coordinated disclosure)** 원칙을 따릅니다.

1. 신고자와 메인테이너가 합의된 일정 내에서 비공개로 패치를 준비합니다.
2. 패치 배포 후 **CVE 발급 및 보안 권고문(advisory)** 을 GitHub Security Advisories를 통해 공개합니다.
3. 공개 시 신고자는 원하는 경우 credit에 명시됩니다 (실명·핸들·익명 선택 가능).
4. 사용자에게 업그레이드를 권고할 충분한 시간이 지난 후 (보통 패치 배포 후 30일) 상세 기술 정보를 공개할 수 있습니다.

---

## 🛡️ 범위 (Scope)

이 정책은 다음 저장소의 **공식 코드** 에 적용됩니다.

- `openLoadstar/spec`
- `openLoadstar/cli`
- `openLoadstar/ui`
- `openLoadstar/mcp`
- `openLoadstar/openLoadstar` (overview)

**범위 외**:
- 사용자가 자신의 프로젝트에 작성한 `.loadstar/` 메타데이터 내용
- 사용자가 fork·수정한 파생 코드 (해당 fork의 메인테이너에게 신고)
- 외부 의존성 (예: Spring Boot, React, Cobra 등) — 해당 프로젝트의 보안 채널로 신고

---

## 🙏 감사

보안 연구자·기여자의 책임 있는 신고는 openLoadstar 사용자 모두를 보호합니다. 시간을 내어 신고해 주시는 모든 분께 진심으로 감사드립니다.
