# 시스템 설계

## 시스템 블록도

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
