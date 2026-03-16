# 시스템 설계

## 1. 시스템 블록도

AI 엔진은 Windows 호스트에서 웹캠과 함께 실행하고, LAMP 서버는 Linux VM에서 구동한다. AI 엔진이 REST API로 VM의 서버에 데이터를 전송하는 구조이다.

```mermaid
graph TB
    subgraph HOST["Windows 호스트"]
        CAM[웹캠] --> MP[MediaPipe Pose<br/>관절 추출]
        MP --> CLS[자세 판정 엔진<br/>4분류]
        CLS --> SOUND[소리 알림<br/>경고음 / TTS]
        CLS --> API_OUT[REST API 전송]
    end

    subgraph VM["Linux VM (Ubuntu)"]
        APACHE[Apache + PHP<br/>REST API + 대시보드]
        MYSQL[(MySQL<br/>자세 기록 DB)]
        APACHE --> MYSQL
    end

    subgraph CLIENT["클라이언트"]
        WEB[웹 대시보드]
        APP[Flutter 앱 + 푸시 알림]
    end

    API_OUT -- "HTTP POST" --> APACHE
    APACHE --> WEB
    APACHE --> APP
```

---

## 2. 데이터 흐름도

```mermaid
flowchart LR
    A["사용자"] --> B["웹캠 영상"]
    B --> C["MediaPipe<br/>관절 추출"]
    C --> D["각도 계산"]
    D --> E{"자세 판정"}
    E -- "바른 자세" --> F["정상 기록"]
    E -- "나쁜 자세<br/>(3초 이상)" --> G["경고음 + 푸시 알림"]
    F --> H["PHP API → MySQL 저장"]
    G --> H
    H --> I["웹 대시보드"]
    H --> J["Flutter 앱"]
```

---

## 3. 자세 판정 플로우차트

```mermaid
flowchart TD
    START["프레임 입력"] --> POSE["MediaPipe Pose<br/>관절 좌표 추출"]
    POSE --> NECK{"거북목?"}
    NECK -- "예" --> TURTLE["거북목 판정"]
    NECK -- "아니오" --> SHOULDER{"좌우 비대칭?"}
    SHOULDER -- "예" --> ASYM["비대칭 판정"]
    SHOULDER -- "아니오" --> LEAN{"앞으로 기울임?"}
    LEAN -- "예" --> FORWARD["기울임 판정"]
    LEAN -- "아니오" --> GOOD["바른 자세"]
    TURTLE --> HOLD{"3초 이상 유지?"}
    ASYM --> HOLD
    FORWARD --> HOLD
    GOOD --> HOLD
    HOLD -- "예" --> CONFIRM["자세 확정 + 알림 + API 전송"]
    HOLD -- "아니오" --> START
    CONFIRM --> START
```

---

## 4. 시퀀스 다이어그램

```mermaid
sequenceDiagram
    participant AI as AI 엔진 (Windows)
    participant SPK as 스피커
    participant PHP as LAMP 서버 (VM)
    participant DB as MySQL
    participant WEB as 웹 대시보드
    participant APP as Flutter 앱

    loop 매 프레임
        AI->>AI: 웹캠 → 관절 추출 → 자세 판정
    end
    
    alt 나쁜 자세 (3초 이상)
        AI->>SPK: 경고음 / TTS 재생
        AI->>PHP: POST /api/posture
        PHP->>DB: INSERT 자세 기록
        PHP->>APP: FCM 푸시 알림
    else 바른 자세
        AI->>PHP: POST /api/posture
        PHP->>DB: INSERT 자세 기록
    end
    
    WEB->>PHP: 대시보드 데이터 요청
    PHP->>WEB: JSON 응답
    APP->>PHP: 통계 데이터 요청
    PHP->>APP: JSON 응답
```

---

## 5. 데이터베이스 ERD

```mermaid
erDiagram
    USERS ||--o{ POSTURE_LOGS : "기록"
    USERS ||--|| SETTINGS : "설정"
    USERS ||--o{ DAILY_STATS : "통계"

    USERS {
        int user_id PK
        varchar username
        varchar password
        varchar name
        varchar fcm_token
        datetime created_at
    }

    POSTURE_LOGS {
        int log_id PK
        int user_id FK
        enum status
        float neck_angle
        float shoulder_diff
        float lean_angle
        datetime recorded_at
    }

    SETTINGS {
        int setting_id PK
        int user_id FK
        boolean sound_on
        boolean push_on
        varchar sound_type
        int neck_threshold
        int shoulder_threshold
        int lean_threshold
        int hold_seconds
    }

    DAILY_STATS {
        int stat_id PK
        int user_id FK
        date stat_date
        int total_minutes
        int good_minutes
        int turtle_minutes
        int asym_minutes
        int lean_minutes
        float good_ratio
    }
```
