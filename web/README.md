# LAMP 웹서버

PHP 대시보드, MySQL 스키마, REST API가 포함될 디렉토리입니다.

## 디렉토리 구조 (예정)

```
web/
├── php/
│   ├── config.php              # DB 연결 설정
│   ├── index.php               # 메인 대시보드
│   ├── api/
│   │   ├── posture.php         # 자세 기록 API
│   │   ├── status.php          # 현재 상태 API
│   │   ├── stats.php           # 통계 API
│   │   ├── settings.php        # 설정 API
│   │   ├── login.php           # 로그인 API
│   │   └── register.php        # 회원가입 API
│   ├── login.php               # 로그인 페이지
│   ├── register.php            # 회원가입 페이지
│   ├── statistics.php          # 통계 페이지
│   └── settings.php            # 설정 페이지
├── css/
│   └── style.css
├── js/
│   └── dashboard.js
├── sql/
│   └── schema.sql
└── assets/
```

## LAMP 서버 설치

```bash
sudo apt update
sudo apt install apache2 mysql-server php php-mysql libapache2-mod-php
```
