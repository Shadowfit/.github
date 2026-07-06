<div align="center">

# 🏋️ ShadowFit

**카메라로 찍은 내 운동 자세를, 정답 동작과 실시간으로 비교해주는 홈트레이닝 앱**

사용자가 스마트폰 카메라로 스쿼트 동작을 촬영하면, AI 서버가 자세를 추출해 기준(레퍼런스) 동작과 시계열로 비교(DTW)하고, 그 결과를 실시간 피드백(음성 안내 포함)으로 되돌려줍니다.
React Native 앱 ↔ Spring Boot 백엔드 ↔ FastAPI AI 서버가 gRPC로 연동되는 구조입니다.

</div>

---

## 📖 사용자 여정

```mermaid
flowchart LR
    A["가입 · 로그인"] --> B["온보딩<br/>페르소나 선택"]
    B --> C["메인 대시보드<br/>주간요약 · 달력"]
    C --> D["운동 시작<br/>기준 동작 선택"]
    D --> E["실시간 분석<br/>싱크로율 · 음성 피드백"]
    E --> F["운동 종료"]
    F --> G["세션 리포트 확인"]
    G --> H["일지 작성"]
    H --> C
```

1. **가입 & 온보딩** — 이메일로 회원가입 후, 온보딩에서 페르소나(헬린이·헬창·다이어트·재활)를 선택합니다. 이후 피드백 톤과 싱크로율 기준이 이 페르소나에 맞춰집니다.
2. **메인 대시보드** — 이번 주 운동 요약(총 운동시간·칼로리·요일별 그래프)과 달력으로 최근 운동 현황을 확인합니다.
3. **운동 시작** — 운동(현재는 스쿼트)을 선택하면, 등록된 기준 동작(YouTube에서 사전 추출된 관절 좌표)을 기준으로 세션이 시작됩니다.
4. **실시간 분석** — 카메라로 촬영한 프레임이 AI 서버로 전송되고, 관절 좌표 추출 → 기준 동작과 DTW 비교로 실시간 싱크로율이 계산됩니다. 자세가 기준을 벗어나면 등록된 피드백 멘트가 음성으로 안내됩니다.
5. **운동 종료** — 종료 버튼을 누르면 세션이 마감되고, AI가 집계한 최종 통계(총 반복 횟수·평균 싱크로율·칼로리)가 반영됩니다.
6. **결과 확인** — 세션 리포트에서 가장 자세가 흐트러졌던 구간과 이유, 직전 세션 대비 변화를 확인합니다.
7. **기록 남기기** — 오늘의 운동에 메모와 기분을 남기고, 다시 대시보드로 돌아가 흐름이 반복됩니다.

---

## ✨ 핵심 기능

| 기능 | 설명 |
| :--- | :--- |
| 🏃 **실시간 자세 분석** | MediaPipe로 관절 좌표를 추출하고, 기준 동작과 DTW(Dynamic Time Warping)로 시계열 비교해 싱크로율 산출 |
| 🎥 **기준 동작 매칭** | YouTube URL에서 기준 포즈를 사전 추출해 운동별 기준 데이터로 저장 |
| 🔊 **음성 피드백 안내** | 서버는 운동별 피드백 멘트·사용자 TTS 설정(속도/on-off)을 관리하고, 실제 음성 합성·재생은 클라이언트 device TTS(`expo-speech`)가 담당 |
| 🧑‍🤝‍🧑 **페르소나별 피드백 톤** | 헬린이 · 헬창 · 다이어트 · 재활 4가지 페르소나에 따라 다른 톤의 피드백 템플릿 제공 |
| 📅 **운동 기록 & 리포트** | 달력 기반 운동 일지, 세션별 리포트(취약 구간 분석, 이전 세션 대비 변화) 제공 |

> 🚧 **로드맵**: 적응형 난이도 자동 조절(성공/실패에 따른 레벨 승강), 스쿼트 외 운동(데드리프트·턱걸이) 확장은 설계 단계이며 아직 구현 전입니다.

**대표 API**

| Method | Endpoint | 설명 |
| :--- | :--- | :--- |
| `POST` | `/exercises/sessions` | 운동 세션 시작 (DB 생성 후 202 즉시 응답, gRPC 송신은 비동기) |
| `PATCH` | `/sessions/{sessionId}/end` | 세션 종료 (단일 엔드포인트, 커밋 후 AI에 비동기 통보) |
| `POST` | `/exercises/{exerciseId}/reference` | YouTube 기준 동작 좌표 추출 요청 |
| `GET` `PATCH` | `/preferences/tts` | TTS 사용 여부·속도 조회/변경 |
| `GET` | `/exercises/{exerciseId}/feedback-templates` | 운동별 피드백 멘트 조회 |
| `GET` | `/reports/calendar` | 달력 기반 월별 운동 기록 |
| `GET` | `/reports/weekly-summary` | 주간 활동 요약 |
| `POST` | `/reports/daily-logs` | 운동 일지 작성 |
| `GET` | `/reports/session/{sessionId}` | 세션별 상세 리포트(취약 구간·이전 세션 비교) |
| `PATCH` | `/admin/exercises/{exerciseId}/thresholds` | 페르소나별 싱크로율 임계값 조정 (관리자) |

전체 스펙은 백엔드 저장소의 Swagger(`/swagger-ui`) 참고.

**📊 리포트 기능 상세**

- **세션 리포트** (`GET /reports/session/{id}`)
  - *취약 구간 탐지*: 포즈 데이터를 3프레임 단위 슬라이딩 윈도우로 순회해 평균 싱크로율이 가장 낮은 구간을 찾습니다. 프레임 하나만 보면 일시적 노이즈에 흔들릴 수 있어 구간 단위로 판단하고, 해당 구간에서 가장 자주 나온 피드백 메시지를 이유로 함께 보여줍니다.
  - *이전 세션 대비 비교*: 같은 운동 종목의 가장 최근 완료 세션과 비교해 싱크로율·운동시간·칼로리 변화량을 계산합니다.
- **주간 요약** (`GET /reports/weekly-summary`) — 이번 주(월~일) 전체 세션을 집계해 총 운동시간·총 칼로리·요일별 그래프, 오늘 진행한 운동 목록을 제공합니다.
- **달력** (`GET /reports/calendar`) — 월 단위로 운동한 날짜와 날짜별 평균 싱크로율을 표시하고, 이번 달 총 운동일수·전체 평균 싱크로율·연속 운동일수(최근 100일 데이터 기준)를 함께 보여줍니다.
- **운동 일지** (`POST /reports/daily-logs`) — 날짜별 메모와 기분(mood)을 기록하며, 이미 작성된 날짜면 덮어씁니다(upsert).

---

## 🧩 아키텍처

```mermaid
graph LR
    FE["📱 App"]
    AI["🤖 AI Server"]
    BE["⚙️ Spring Boot"]
    DB[("🗄️ MySQL")]

    FE -- "프레임 스트리밍" --> AI
    AI -- "분석 콜백" --> BE
    FE -- "세션 시작/중단" --> BE
    BE --> DB
```

프론트는 카메라 프레임을 AI 서버에 직접 스트리밍하고, AI 서버는 gRPC 콜백으로 결과를 Spring에 전달합니다. 세션 시작/중단만 프론트→Spring→AI로 한 단계 거칩니다.

## 🔁 세션 라이프사이클 시퀀스

### ▶️ 세션 시작 & 실시간 분석

```mermaid
sequenceDiagram
    participant FE as 📱 App
    participant AI as 🤖 FastAPI AI
    participant BE as ⚙️ Spring Boot
    participant DB as 🗄️ MySQL

    FE->>BE: POST /exercises/sessions
    BE->>DB: Session(status=IN_PROGRESS) 생성
    BE-->>FE: 202 Accepted (sessionId)
    BE->>AI: (@Async) gRPC StartAnalysis(reference_poses)
    AI->>AI: SessionState 생성 (in-memory)

    loop 카메라 프레임마다
        FE->>AI: POST /pose (base64 frame)
        AI->>AI: MediaPipe 관절 추출 + DTW 싱크로율 계산
        alt rep 1회 완성
            AI->>BE: gRPC SavePoseDataBatch
            BE->>DB: pose_data 저장
        end
    end

    rect rgba(150,150,150,0.15)
    Note over AI,DB: 설계됨 · 미연동 (ai-server에 세트 경계 감지·전송 로직 없음)
    opt 세트 경계 / 세션 종료 시점
        AI->>BE: gRPC ReportFeedbackBatch(set_no, is_final, events)
        BE->>DB: feedback_log 저장 (uniqueKey + INSERT IGNORE, 멱등)
    end
    end
```

프레임마다 AI가 rep 완성 여부를 판단해 완성된 rep만 Spring에 배치로 콜백합니다. TTS 발화 이벤트 배치(`ReportFeedbackBatch`)는 Spring 계약은 완료됐지만 AI 서버 쪽 세트 경계 감지·전송 로직은 아직 구현 전입니다.

### ⏹️ 세션 종료 & 동시성 처리

```mermaid
sequenceDiagram
    participant FE as 📱 App
    participant AI as 🤖 FastAPI AI
    participant BE as ⚙️ Spring Boot
    participant DB as 🗄️ MySQL
    participant T as ⏰ TimeoutScheduler

    FE->>BE: PATCH /sessions/{id}/end
    BE->>DB: endTime 기록 (commit)
    BE-->>FE: 200 OK
    BE->>AI: (afterCommit) gRPC StopAnalysis
    AI->>AI: SessionState 제거 + 누적 통계 계산

    par 오래 걸리는 세션은 동시에
        T->>DB: status=FAILED 시도 (낙관적 락 체크)
    and
        AI->>BE: gRPC CompleteAnalysis (실패 시 최대 3회 재시도)
        BE->>DB: @Version 낙관적 락으로 갱신 시도
    end
    alt 스케줄러와 충돌
        BE->>BE: 최대 3회 재시도, 콜백 결과 우선
    end
    BE->>DB: status=COMPLETED (first-write-wins, 멱등)
```

세션 종료 콜백이 지연되면 `SessionTimeoutScheduler`가 만료 세션을 `FAILED` 처리 시도하지만, AI의 `CompleteAnalysis` 콜백과 동시에 충돌하면 `@Version` 낙관적 락 재시도 후 콜백 결과를 우선합니다(first-write-wins, 멱등).

## 🔀 데이터 플로우

```mermaid
flowchart TD
    YT["YouTube 기준 영상"] -->|"youtube_url"| Extract["🤖 AI: 관절 좌표 추출"]
    Extract -->|"jointCoordinates 시계열"| RefDB[("exercise_reference")]

    Cam["📱 카메라 프레임 (base64)"] -->|"POST /pose"| MP["🤖 MediaPipe 추론"]
    RefDB -.->|"세션 시작 시 조회"| DTW
    MP --> DTW["DTW 비교 + syncRate 계산"]
    DTW -->|"rep 완성 시 배치"| PoseDB[("pose_data<br/>jointCoordinates · syncRate · feedbackMessage")]
    DTW -.->|"설계됨 · 미연동"| FeedbackDB[("session_feedback_log")]

    PoseDB -->|"세션 종료 시 집계"| SessionDB[("session<br/>totalReps · avgSyncRate · calories")]

    PoseDB -->|"3프레임 슬라이딩 윈도우"| Weak["취약 구간 탐지"]
    SessionDB -->|"직전 세션과 비교"| Weak
    Weak --> Report["세션 리포트"]
    SessionDB -->|"주/월 단위 집계"| Weekly["주간요약 · 달력"]
    DailyLogDB[("daily_log<br/>memo · mood")] --> Weekly
```

카메라 프레임은 AI 서버 내부에서 관절 좌표로 변환된 뒤 rep 단위로만 Spring에 저장되고, 세션 리포트·주간요약·달력은 모두 이 `pose_data`/`session` 테이블에서 파생됩니다.

---

<div align="center">

# 🛠️ 기술 스택

## 📱 Frontend

| 역할 | 종류 |
| :--- | :--- |
| **Framework & Runtime** | ![React Native](https://img.shields.io/badge/REACT_NATIVE_0.81-61DAFB?style=flat-square&logo=react&logoColor=black) ![React](https://img.shields.io/badge/REACT_19-61DAFB?style=flat-square&logo=react&logoColor=black) ![Expo](https://img.shields.io/badge/EXPO_SDK_54-000020?style=flat-square&logo=expo&logoColor=white) ![Expo Router](https://img.shields.io/badge/EXPO_ROUTER_6-000020?style=flat-square&logo=expo&logoColor=white) |
| **Language** | ![TypeScript](https://img.shields.io/badge/TYPESCRIPT_5.9-3178C6?style=flat-square&logo=typescript&logoColor=white) |
| **State & Networking** | ![Zustand](https://img.shields.io/badge/ZUSTAND-443E38?style=flat-square&logo=react&logoColor=white) ![TanStack Query](https://img.shields.io/badge/TANSTACK_QUERY-FF4154?style=flat-square&logo=reactquery&logoColor=white) ![Axios](https://img.shields.io/badge/AXIOS-5A29E4?style=flat-square&logo=axios&logoColor=white) |
| **Storage** | ![AsyncStorage](https://img.shields.io/badge/ASYNC_STORAGE-000000?style=flat-square&logo=react&logoColor=white) ![SecureStore](https://img.shields.io/badge/SECURE_STORE-000020?style=flat-square&logo=expo&logoColor=white) |
| **UI & Interaction** | ![React Navigation](https://img.shields.io/badge/REACT_NAVIGATION-000000?style=flat-square&logo=react&logoColor=white) ![Reanimated](https://img.shields.io/badge/REANIMATED-FFCC00?style=flat-square&logo=react&logoColor=black) ![Gesture Handler](https://img.shields.io/badge/GESTURE_HANDLER-000000?style=flat-square&logo=react&logoColor=white) ![SVG](https://img.shields.io/badge/SVG-FFB13B?style=flat-square&logo=svg&logoColor=black) ![Chart Kit](https://img.shields.io/badge/CHART_KIT-34A853?style=flat-square) ![Calendars](https://img.shields.io/badge/CALENDARS-4285F4?style=flat-square) |
| **Device & Media** | ![Expo Camera](https://img.shields.io/badge/EXPO_CAMERA-000020?style=flat-square&logo=expo&logoColor=white) ![Image Picker](https://img.shields.io/badge/IMAGE_PICKER-000020?style=flat-square&logo=expo&logoColor=white) ![Media Library](https://img.shields.io/badge/MEDIA_LIBRARY-000020?style=flat-square&logo=expo&logoColor=white) ![Expo AV](https://img.shields.io/badge/EXPO_AV-000020?style=flat-square&logo=expo&logoColor=white) ![Expo Speech](https://img.shields.io/badge/EXPO_SPEECH-000020?style=flat-square&logo=expo&logoColor=white) ![Expo Haptics](https://img.shields.io/badge/EXPO_HAPTICS-000020?style=flat-square&logo=expo&logoColor=white) |

---

## ⚙️ Backend

| 역할 | 종류 |
| :--- | :--- |
| **Language** | ![Java](https://img.shields.io/badge/JAVA_21-ED8B00?style=flat-square&logo=openjdk&logoColor=white) |
| **Framework** | ![Spring Boot](https://img.shields.io/badge/SPRING_BOOT_3.4-6DB33F?style=flat-square&logo=springboot&logoColor=white) ![Spring WebFlux](https://img.shields.io/badge/WEBFLUX-6DB33F?style=flat-square&logo=spring&logoColor=white) |
| **Database / ORM** | ![MySQL](https://img.shields.io/badge/MYSQL_8-4479A1?style=flat-square&logo=mysql&logoColor=white) ![JPA](https://img.shields.io/badge/JPA-59666C?style=flat-square&logo=hibernate&logoColor=white) ![H2](https://img.shields.io/badge/H2_DATABASE-09476B?style=flat-square&logo=h2&logoColor=white) |
| **Security** | ![JWT](https://img.shields.io/badge/JWT-000000?style=flat-square&logo=jsonwebtokens&logoColor=white) ![Spring Security](https://img.shields.io/badge/SPRING_SECURITY-6DB33F?style=flat-square&logo=springsecurity&logoColor=white) |
| **API Docs** | ![Swagger](https://img.shields.io/badge/SWAGGER-85EA2D?style=flat-square&logo=swagger&logoColor=black) |
| **Service 간 통신** | ![gRPC](https://img.shields.io/badge/GRPC-244C5A?style=flat-square&logo=grpc&logoColor=white) ![Protocol Buffers](https://img.shields.io/badge/PROTOBUF-4285F4?style=flat-square&logo=google&logoColor=white) |
| **Utilities** | ![Lombok](https://img.shields.io/badge/LOMBOK-BC4521?style=flat-square&logo=java&logoColor=white) ![ModelMapper](https://img.shields.io/badge/MODELMAPPER-FF6F00?style=flat-square&logo=java&logoColor=white) |

---

## 🤖 AI Server

| 역할 | 종류 |
| :--- | :--- |
| **Language** | ![Python](https://img.shields.io/badge/PYTHON-3776AB?style=flat-square&logo=python&logoColor=white) |
| **Framework** | ![FastAPI](https://img.shields.io/badge/FASTAPI-009688?style=flat-square&logo=fastapi&logoColor=white) ![Uvicorn](https://img.shields.io/badge/UVICORN-4051B5?style=flat-square&logo=uvicorn&logoColor=white) |
| **AI / CV** | ![MediaPipe](https://img.shields.io/badge/MEDIAPIPE-FF6F00?style=flat-square&logo=google&logoColor=white) ![OpenCV](https://img.shields.io/badge/OPENCV-5C3EE8?style=flat-square&logo=opencv&logoColor=white) ![PyTorch](https://img.shields.io/badge/PYTORCH-EE4C2C?style=flat-square&logo=pytorch&logoColor=white) |
| **Motion Analysis** | ![DTW](https://img.shields.io/badge/DTW-DYNAMIC_TIME_WARPING-blue?style=flat-square) |
| **Validation** | ![Pydantic](https://img.shields.io/badge/PYDANTIC-E92063?style=flat-square&logo=pydantic&logoColor=white) |
| **Service 간 통신** | ![gRPC](https://img.shields.io/badge/GRPC-244C5A?style=flat-square&logo=grpc&logoColor=white) ![Protocol Buffers](https://img.shields.io/badge/PROTOBUF-4285F4?style=flat-square&logo=google&logoColor=white) |

---

## 🐳 Infra & 배포

| 역할 | 종류 |
| :--- | :--- |
| **Container** | ![Docker](https://img.shields.io/badge/DOCKER-2496ED?style=flat-square&logo=docker&logoColor=white) ![Docker Compose](https://img.shields.io/badge/DOCKER_COMPOSE-2496ED?style=flat-square&logo=docker&logoColor=white) |
| **Web Server** | ![Nginx](https://img.shields.io/badge/NGINX-009639?style=flat-square&logo=nginx&logoColor=white) |
| **Cloud** | ![AWS](https://img.shields.io/badge/AWS-232F3E?style=flat-square&logo=amazonaws&logoColor=white) |
| **CI/CD** | ![GitHub Actions](https://img.shields.io/badge/GITHUB_ACTIONS-2088FF?style=flat-square&logo=githubactions&logoColor=white) |
| **Service 간 프로토콜** | ![REST API](https://img.shields.io/badge/REST_API-005571?style=flat-square) ![gRPC](https://img.shields.io/badge/GRPC-244C5A?style=flat-square&logo=grpc&logoColor=white) |

</div>
