# Flutter 모바일 앱

LAMP 서버의 REST API를 호출하여 자세 상태를 확인하고 통계를 조회하는 Flutter 앱입니다.

## 디렉토리 구조 (예정)

```
app/
└── lib/
    ├── main.dart
    ├── screens/
    │   ├── login_screen.dart
    │   ├── home_screen.dart
    │   ├── stats_screen.dart
    │   └── settings_screen.dart
    ├── services/
    │   ├── api_service.dart
    │   └── push_service.dart
    └── models/
        ├── posture.dart
        └── user.dart
```

## 필수 패키지

```yaml
dependencies:
  http: ^1.1.0
  fl_chart: ^0.65.0
  firebase_messaging: ^14.7.0
  shared_preferences: ^2.2.0
```
