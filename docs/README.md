# 📚 Neon Noir Boutique — 기획 문서 목차

> **최종 수정일**: 2026-03-29
> **프로젝트**: Neon Noir Boutique (The Electric Gallery)

---

## 문서 구조

```
C:\홈페이지\
│
├── 📂 docs/                              ← 기획 문서 (현재 위치)
│   ├── README.md                         ← 이 파일 (문서 목차)
│   ├── 01_development_plan.md            ← 종합 개발 기획서
│   ├── 02_design_system.md               ← 디자인 시스템 명세서
│   ├── 03_page_specification.md          ← 화면별 상세 기능 명세
│   ├── 04_db_development_plan.md         ← DB 개발 기획서 (신규)
│   ├── 05_db_connection.md               ← DB 연결 및 API 가이드
│   └── 06_file_structure.md              ← 프로젝트 파일 구조 가이드
│
├── 📂 src/                               ← 소스 코드 (Phase 2~)
│   ├── index.html                        ← 메인 진입점 (예정)
│   ├── css/                              ← 스타일시트
│   ├── js/                               ← JavaScript
│   └── assets/                           ← 이미지/아이콘
│
├── development_plan.md                   ← 기존 기획서 (원본 보존)
├── db_connection.md                      ← 기존 DB 계획서 (원본 보존)
└── latest_screen.html                    ← Stitch 생성 HTML
```

---

## 문서별 역할

| # | 문서 | 역할 | 대상 |
|:---:|:---|:---|:---|
| 01 | **개발 기획서** | 프로젝트 전체 개요, 로드맵, 기술 스택 | 전체 팀 |
| 02 | **디자인 시스템** | 컬러, 타이포, 컴포넌트 규칙 | 프론트엔드 |
| 03 | **화면별 명세** | 각 페이지의 기능·인터랙션 상세 | 프론트엔드 |
| 04 | **DB 개발 기획서** | 데이터 모델링, ERD, 마이그레이션 전략 | 백엔드/DB |
| 05 | **DB 연결 가이드** | API 코드, RLS 정책, 연동 예시 | 프론트+백엔드 |
| 06 | **파일 구조 가이드** | 디렉토리 컨벤션, 네이밍 규칙 | 전체 팀 |

---

## 읽는 순서

```
01 개발 기획서  →  02 디자인 시스템  →  03 화면별 명세
                                          │
                                          ▼
               06 파일 구조  ←  05 DB 연결  ←  04 DB 기획서
```
