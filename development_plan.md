# 🛍️ Neon Noir Boutique — 개발 기획서

> **프로젝트명**: Neon Noir Boutique (The Electric Gallery)
> **작성일**: 2026-03-29
> **상태**: Phase 1 진행 중

---

## 1. 프로젝트 개요

| 항목 | 내용 |
|:---|:---|
| **컨셉** | "The Electric Gallery" — 제품을 전시하는 프리미엄 갤러리형 쇼핑몰 |
| **타겟** | 프리미엄 굿즈·컬렉터블 상품을 구매하는 2030 세대 |
| **핵심 가치** | 심플함 × 강렬한 임팩트 × 고급스러운 UX |
| **디바이스** | Desktop 우선 (반응형 확장 예정) |
| **Stitch 프로젝트 ID** | `13077107173303992371` |

---

## 2. 화면 구성 (총 5개)

Stitch에서 설계 완료된 화면 목록입니다.

| # | 화면명 | Screen ID | 설명 | 상태 |
|:---:|:---|:---|:---|:---:|
| 1 | **Electric Gallery Home** 🆕 | `b120ba26...` | 다크모드 프리미엄 메인 (히어로+큐레이션) | ✅ 완료 |
| 2 | **Main Home Page** | `321f4876...` | 라이트모드 기본 메인 페이지 | ✅ 완료 |
| 3 | **Product Listing Page** | `1113edd1...` | 카테고리별 상품 목록 그리드 | ✅ 완료 |
| 4 | **Product Detail Page** | `8201efb9...` | 개별 상품 상세 정보 | ✅ 완료 |
| 5 | **Shopping Cart Page** | `c1ad03c6...` | 장바구니 + 주문 요약 | ✅ 완료 |

### 추가 예정 화면
| # | 화면명 | 설명 | 상태 |
|:---:|:---|:---|:---:|
| 6 | 회원가입 / 로그인 | Supabase Auth 연동 | ⬜ 미착수 |
| 7 | 마이페이지 | 주문내역, 회원정보 | ⬜ 미착수 |
| 8 | 검색 결과 | 필터링 + 정렬 기능 | ⬜ 미착수 |
| 9 | 결제 / 체크아웃 | 주문 확정 프로세스 | ⬜ 미착수 |

---

## 3. 디자인 시스템

### 🎨 컬러 팔레트

```
┌─────────────────────────────────────────────────┐
│  🌑 Dark Theme (Electric Gallery)               │
│                                                 │
│  Background   ██████  #131315  (Deep Charcoal)  │
│  Primary      ██████  #BC13FE  (Neon Purple)    │
│  Secondary    ██████  #00D2FF  (Electric Blue)  │
│  Tertiary     ██████  #7000FF  (Deep Violet)    │
│  On-Surface   ██████  #E5E1E4  (Soft White)     │
│                                                 │
│  🌕 Light Theme (Illustrated Boutique)          │
│                                                 │
│  Background   ██████  #FEF3FF  (Soft Lavender)  │
│  Primary      ██████  #751FE7  (Vivid Purple)   │
│  Secondary    ██████  #A33800  (Warm Orange)     │
│  Tertiary     ██████  #006384  (Ocean Blue)     │
│  On-Surface   ██████  #3B264B  (Dark Plum)      │
└─────────────────────────────────────────────────┘
```

### 🔤 타이포그래피

| 용도 | 폰트 | 비고 |
|:---|:---|:---|
| **Headlines** | Epilogue / Plus Jakarta Sans | 대담하고 권위있는 느낌, letter-spacing: -0.02em |
| **Body** | Manrope | 깔끔한 가독성, 기술적 세련미 |
| **Labels** | Manrope | ALL-CAPS, letter-spacing: 0.1em |

### 📐 디자인 원칙

1. **No-Line Rule** — 1px 선 사용 금지. 배경색 변화와 여백으로 구분
2. **Tonal Layering** — 4단계 표면 레이어로 깊이감 표현
3. **Glassmorphism** — 네비게이션, 모달에 반투명 블러 효과
4. **Neon Glow** — CTA 버튼에 네온 그라디언트 + 글로우 섀도

---

## 4. 기술 스택

| 분류 | 기술 | 비고 |
|:---|:---|:---|
| **Structure** | HTML5 | 시맨틱 마크업 |
| **Styling** | Tailwind CSS (CDN) | Stitch 생성 코드 호환 |
| **Logic** | Vanilla JavaScript | 경량 인터랙션 |
| **Icons** | Material Symbols Outlined | Google Fonts 연동 |
| **Fonts** | Epilogue, Manrope, Plus Jakarta Sans | Google Fonts |
| **Database** | Supabase (PostgreSQL) | Phase 2에서 연동 |
| **Auth** | Supabase Auth | 이메일/소셜 로그인 |
| **Storage** | Supabase Storage | 상품 이미지 관리 |
| **Hosting** | 미정 (Vercel / Netlify) | 정적 배포 우선 |

---

## 5. 개발 로드맵

```
Phase 1 (현재)          Phase 2                 Phase 3
UI/UX 설계              DB 연동                  기능 고도화
━━━━━━━━━━━━           ━━━━━━━━━━━━            ━━━━━━━━━━━━
[x] 메인 페이지         [ ] DB 스키마 설계        [ ] 결제 연동
[x] 상품 목록           [ ] Supabase 프로젝트     [ ] 주문 관리
[x] 상품 상세           [ ] 상품 CRUD API         [ ] 리뷰 시스템
[x] 장바구니            [ ] 장바구니 실시간 연동    [ ] 검색/필터
[x] 다크모드 메인       [ ] 회원 인증 (Auth)       [ ] 관리자 대시보드
[ ] 로그인/회원가입     [ ] 이미지 Storage         [ ] SEO 최적화
[ ] 마이페이지          [ ] RLS 보안 정책          [ ] 성능 최적화
```

---

## 6. 핵심 기능 명세

### 🏠 메인 페이지
- 풀스크린 히어로 배너 (대담한 타이포 + 고해상도 이미지)
- 글래스모피즘 상단 내비게이션 (투명 블러)
- 큐레이션 상품 그리드 (hover 시 네온 글로우)
- 뉴스레터 구독 영역

### 📦 상품 목록
- 장르별 필터 칩 (Fantasy, Romance, Action 등)
- 카드형 상품 그리드 (호버 애니메이션)
- 무한 스크롤 또는 페이지네이션

### 🔍 상품 상세
- 대형 상품 이미지 + 갤러리
- 가격, 재고, 한정판 여부 표시
- "장바구니 담기" CTA 버튼 (네온 그라디언트)

### 🛒 장바구니
- 수량 증감 컨트롤
- 실시간 합계 계산
- 배송비 안내 프로그레스 바
- 추천 상품 섹션

---

## 7. 참고 파일

| 파일 | 경로 | 설명 |
|:---|:---|:---|
| DB 연결 계획서 | `db_connection.md` | Supabase 스키마 설계 및 연동 코드 |
| 최신 화면 HTML | `latest_screen.html` | Shopping Cart 페이지 HTML 코드 |
