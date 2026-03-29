# 📁 06. 프로젝트 파일 구조 가이드

> **버전**: v1.0 | **작성일**: 2026-03-29

---

## 1. 현재 디렉토리 구조

```
C:\홈페이지\
│
├── 📂 docs/                          ← 기획 문서 (구조화됨)
│   ├── README.md                     ← 문서 목차 및 안내
│   ├── 01_development_plan.md        ← 종합 개발 기획서
│   ├── 02_design_system.md           ← 디자인 시스템 명세서
│   ├── 03_page_specification.md      ← 화면별 기능 명세
│   ├── 04_db_development_plan.md     ← DB 개발 기획서
│   ├── 05_db_connection.md           ← DB 연결/API 가이드
│   └── 06_file_structure.md          ← 이 파일
│
├── development_plan.md               ← 기존 기획서 (원본 보존)
├── db_connection.md                  ← 기존 DB 계획 (원본 보존)
└── latest_screen.html                ← Stitch 생성 HTML
```

---

## 2. 목표 디렉토리 구조 (Phase 2~3)

```
C:\홈페이지\
│
├── 📂 docs/                          ← 기획 문서
│
├── 📂 src/                           ← 소스 코드
│   ├── index.html                    ← 메인 진입점
│   ├── product-list.html             ← 상품 목록
│   ├── product-detail.html           ← 상품 상세
│   ├── cart.html                     ← 장바구니
│   ├── login.html                    ← 로그인/회원가입
│   │
│   ├── 📂 css/
│   │   ├── globals.css               ← 전역 스타일, CSS 변수
│   │   ├── components.css            ← 공통 컴포넌트
│   │   └── pages/                    ← 페이지별 스타일
│   │
│   ├── 📂 js/
│   │   ├── supabase.js               ← Supabase 초기화
│   │   ├── auth.js                   ← 인증 관련
│   │   ├── products.js               ← 상품 API
│   │   ├── cart.js                    ← 장바구니 API
│   │   ├── orders.js                 ← 주문 API
│   │   └── utils.js                  ← 유틸리티 함수
│   │
│   └── 📂 assets/
│       ├── images/                   ← 로컬 이미지
│       ├── icons/                    ← 커스텀 아이콘
│       └── fonts/                    ← 로컬 폰트 (선택)
│
├── 📂 stitch-exports/                ← Stitch 원본 HTML 보관
│   ├── electric-gallery-home.html
│   ├── main-home-page.html
│   ├── product-listing.html
│   ├── product-detail.html
│   └── shopping-cart.html
│
└── .env                              ← 환경변수 (Git 제외)
```

---

## 3. 네이밍 규칙

| 대상 | 규칙 | 예시 |
|:---|:---|:---|
| HTML 파일 | kebab-case | `product-detail.html` |
| CSS 파일 | kebab-case | `globals.css` |
| JS 파일 | camelCase | `supabase.js`, `cart.js` |
| CSS 클래스 | BEM 또는 Tailwind | `.card__title`, `text-primary` |
| JS 함수 | camelCase 동사+명사 | `getProducts()`, `addToCart()` |
| JS 상수 | UPPER_SNAKE | `SUPABASE_URL` |
| 이미지 | kebab-case | `hero-banner-dark.webp` |
| 문서 | 번호_snake_case | `01_development_plan.md` |

---

## 4. 유지보수 메모

- **기존 파일은 삭제하지 않습니다** — `development_plan.md`, `db_connection.md`는 원본으로 보존
- **docs/ 폴더가 최신 문서** — 기획 변경 시 docs/ 내 파일을 수정
- **Stitch HTML은 stitch-exports/에 보관** — 원본 보존, src/에서 리팩토링 사용

---

## 변경 이력

| 날짜 | 내용 |
|:---|:---|
| 2026-03-29 | 프로젝트 구조, 네이밍 규칙, 유지보수 가이드 작성 |
