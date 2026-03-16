# AI / 영상처리

웹캠 기반 MediaPipe Pose 자세 감지 엔진 코드가 포함될 디렉토리입니다.

## 디렉토리 구조 (예정)

```
ai/
├── posture_detector.py         # 메인 자세 감지 엔진
├── posture_classifier.py       # 자세 판정 알고리즘
├── alert_manager.py            # 소리 알림 (경고음 / TTS)
├── api_sender.py               # REST API 전송 모듈
└── config.json                 # 임계값 설정 파일
```

## 필수 패키지

```bash
pip install opencv-python mediapipe numpy pyttsx3 playsound requests
```
