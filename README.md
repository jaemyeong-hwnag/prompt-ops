# prompt-ops

# 🧠 PromptOps – CLI + HTTP API 기반 AI 개발 자동화 도구

> Claude / OpenAI 기반의 AI Task 처리 플랫폼  
> Web UI 없이 CLI 또는 HTTP API를 통해 사용하며, 모든 핵심 기능은 공통 core 모듈에서 처리됨.  
> 향후 모듈/서비스 분리까지 고려한 멀티 모듈 아키텍처 기반 설계.

---

## 📦 프로젝트 구조

```
promptops/
├── promptops-core/     # 🧠 핵심 비즈니스 로직 (PRD, Task 생성/실행/AI 연동)
├── backend/             # 🌐 REST API 서버 (Spring Boot)
├── cli/                 # 💻 CLI 도구 (Clikt 기반)
├── mcp-server/          # 🛰 IDE 연동용 MCP 서버 (선택)
├── .taskmaster/         # 🗂 PRD 및 Task 결과 저장소 (파일 기반)
└── README.md
```

---

## 🧱 각 모듈 책임

| 모듈 | 설명 |
|------|------|
| `promptops-core` | 모든 핵심 기능 구현 위치. 다른 모듈은 이 모듈에 의존 |
| `backend` | REST API 서버. HTTP 요청 처리 후 core 로직 위임 |
| `cli` | Clikt 기반 명령어 실행. core 또는 backend API 호출 |
| `mcp-server` | IDE(MCP) 연동용 서버. JSON-RPC 처리. 선택 구성 |

---

## 🔄 모듈 의존 관계

```
[cli]          [backend]          [mcp-server]
   \              |                  //
    \             |                 //
     \       [promptops-core]      //
          ↑ 공통 핵심 기능 모듈
```

- 모든 기능은 `promptops-core`에서 구현됨
- CLI/API/MCP는 인터페이스 역할만 수행 (Port/Adapter 아키텍처)

---

## 🧠 주요 기능

| 기능 | CLI | HTTP API |
|------|-----|----------|
| PRD 업로드 | ✅ `promptops add-prd ./prd.txt` | ✅ `POST /api/prd/upload` |
| Git PRD 연동 | ✅ `--repo` 플래그 사용 | ✅ `POST /api/prd/from-git` |
| Task 목록 보기 | ✅ `list-tasks` | ✅ `GET /api/prd/{id}/tasks` |
| Task 실행 | ✅ `run-task` | ✅ `POST /api/prd/{id}/tasks/{taskId}/execute` |
| 리서치 요청 | ✅ `research` | ✅ `POST /api/prd/{id}/tasks/{taskId}/research` |
| Task 확장 | ✅ `expand-task` | ✅ `POST /api/prd/{id}/tasks/{taskId}/expand` |

---

## 🔐 AI 연동 방식

- 지원 모델: `Claude 3`, `GPT-4`, `GPT-3.5`
- 사용자가 직접 API Key, 모델, provider 입력

```bash
promptops run-task --prd 1 --task 2 \
  --provider claude \
  --model claude-3-opus-20240229 \
  --apiKey sk-xxx
```

- 또는 설정 파일 저장:
  `~/.taskmaster/config.json`

```json
{
  "provider": "claude",
  "model": "claude-3-opus-20240229",
  "apiKey": "sk-xxx"
}
```

---

## 🧪 실행 방식

### 서버 실행 (백엔드)

```bash
cd backend
./gradlew bootRun
```

또는 Docker:

```bash
docker run -p 8080:8080 \
  -e AI_PROVIDER=claude \
  -e AI_MODEL=claude-3-opus-20240229 \
  -e AI_API_KEY=sk-xxx \
  promptops-backend
```

### CLI 실행

```bash
cd cli
./gradlew run --args="add-prd ./prd.txt"
```

또는 Fat JAR 실행:

```bash
java -jar cli/build/libs/promptops-cli.jar run-task --prd 1 --task 2
```

---

## 📁 .taskmaster 저장 구조 예시

```
.taskmaster/
├── prd-1/
│   ├── prd.md
│   ├── tasks.json
│   ├── task-1.md
│   ├── task-2.md
```

---

## 🔄 향후 확장 가능성

- ✅ DB 기반 저장소로 전환
- ✅ GitHub 연동 및 자동 PR 생성
- ✅ Web UI 모듈 추가 가능 (frontend 별도)
- ✅ 각 모듈 독립 서비스화 (task-api-service, task-cli 등)

---

## 📌 모듈 이름 검토 결과

| 기존 이름 | 변경 여부 | 변경 이름 | 사유 |
|-----------|------------|-------------|------|
| `service` | ✅ 변경 | `promptops-core` | "service"는 추상적, core 역할을 명확히 반영 |

---

## ✅ 아키텍처 적합성 검토 요약

| 항목 | 상태 | 비고 |
|------|-------|------|
| SRP, 역할 분리 | ✅ | 각 모듈 단일 책임 |
| 재사용성 | ✅ | core 로직을 모든 모듈에서 재사용 |
| 테스트 용이성 | ✅ | core 단위 테스트 가능 |
| 모듈 분리 대비 | ✅ | 향후 서비스 단위 분리 용이 |
| 클린 아키텍처 정합성 | ✅ | Adapter-Driven 구조 충실 |

---

## 📘 참고

- 원 프로젝트: [claude-promptops](https://github.com/eyaltoledano/claude-promptops)
- 작성자: 당신의 GitHub
