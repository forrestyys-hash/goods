# 🎨 02. 디자인 시스템 명세서

> **시스템명**: Neon Noir Boutique / The Illustrated Boutique
> **버전**: v1.0
> **Stitch Asset ID**: `4170187018c64a5e8d4c33d52db215b1` (Dark) / `76201b6c1764471aa7ccdf384eaeb17b` (Light)

---

## 1. 테마 구성

이 프로젝트는 **두 가지 테마**를 운용합니다.

| 항목 | 🌑 Dark (Electric Gallery) | 🌕 Light (Illustrated Boutique) |
|:---|:---|:---|
| **컨셉** | 네온 노아르 갤러리 | 웹툰 스타일 부티크 |
| **느낌** | 미래지향적, 고급, 몰입감 | 밝고 생동감, 에디토리얼 |
| **사용 화면** | Electric Gallery Home | 기타 모든 페이지 |

---

## 2. 컬러 팔레트

### 🌑 Dark Theme

| 토큰 | 색상 | Hex | 용도 |
|:---|:---:|:---|:---|
| `background` | ⬛ | `#131315` | 페이지 배경 |
| `surface` | ⬛ | `#131315` | 기본 표면 |
| `surface-container-low` | ⬛ | `#1C1B1E` | 섹션 레이어 |
| `surface-container` | ⬛ | `#201F22` | 카드 컨테이너 |
| `surface-container-high` | ⬛ | `#2A2A2C` | 인터랙티브 요소 |
| `surface-container-highest` | ⬛ | `#353437` | 팝오버/포커스 |
| `primary` | 🟣 | `#EBB2FF` | 기본 텍스트 강조 |
| `primary-container` | 🟣 | `#BC13FE` | CTA 버튼 배경 |
| `secondary` | 🔵 | `#A5E7FF` | 보조 액션 텍스트 |
| `secondary-container` | 🔵 | `#00D2FF` | 액션 버튼/칩 |
| `tertiary-container` | 🟣 | `#8950FF` | 보조 강조 |
| `on-surface` | ⬜ | `#E5E1E4` | 본문 텍스트 |
| `on-surface-variant` | 🔘 | `#D4C0D7` | 보조 텍스트 |
| `outline` | 🔘 | `#9D8BA0` | 비활성 라벨 |
| `error` | 🔴 | `#FFB4AB` | 에러 메시지 |

### 🌕 Light Theme

| 토큰 | 색상 | Hex | 용도 |
|:---|:---:|:---|:---|
| `background` | 🟪 | `#FEF3FF` | 페이지 배경 |
| `surface-container` | 🟪 | `#F6E1FF` | 섹션 배경 |
| `surface-container-lowest` | ⬜ | `#FFFFFF` | 카드 배경 |
| `primary` | 🟣 | `#751FE7` | CTA, 브랜드 |
| `secondary` | 🟠 | `#A33800` | 보조 액션 |
| `tertiary` | 🔵 | `#006384` | 링크/정보 |
| `on-surface` | ⬛ | `#3B264B` | 본문 텍스트 |
| `on-surface-variant` | 🔘 | `#6A527A` | 보조 텍스트 |

---

## 3. 타이포그래피

### 폰트 패밀리

| 용도 | Dark Theme | Light Theme | 스타일 |
|:---|:---|:---|:---|
| **Headlines** | Epilogue | Plus Jakarta Sans | Bold~ExtraBold, -0.02em |
| **Body** | Manrope | Manrope | Regular~Medium |
| **Labels** | Manrope (ALL-CAPS) | Plus Jakarta Sans | Medium, +0.1em |

### 사이즈 스케일 (참고)

| 토큰 | 크기 | 용도 |
|:---|:---|:---|
| `display-lg` | 3rem+ | 히어로 헤드라인 |
| `headline-lg` | 2rem | 섹션 타이틀 |
| `title-lg` | 1.25rem | 상품명, 카드 제목 |
| `body-md` | 1rem | 본문 설명 |
| `body-sm` | 0.875rem | 부가 설명 |
| `label-md` | 0.875rem | 메타 정보, 칩 |
| `label-sm` | 0.75rem | 뱃지, 태그 |

---

## 4. 디자인 원칙

### 원칙 1: No-Line Rule ❌📏
> 1px 선(border)으로 구역을 나누지 않는다

- ✅ 배경색 변화 (Tonal Shift)로 구분
- ✅ 여백 (Negative Space)으로 구분
- ✅ 그림자 (Ambient Glow)로 구분
- ❌ `border: 1px solid` 사용 금지

### 원칙 2: Tonal Layering 📐
> 4단계 표면 레이어로 깊이감 표현

```
Layer 0  surface               ← 무한 배경
Layer 1  surface-container-low ← 섹션 영역
Layer 2  surface-container     ← 카드/컨테이너
Layer 3  surface-container-high← 팝오버/포커스
```

### 원칙 3: Glassmorphism 🪟
> 내비게이션, 모달에 프로스트 글래스 효과

```css
.glass {
  background: rgba(32, 31, 34, 0.6);   /* surface-container 60% */
  backdrop-filter: blur(24px);
  -webkit-backdrop-filter: blur(24px);
}
```

### 원칙 4: Neon Glow ✨
> CTA 버튼과 주요 요소에 네온 글로우 그림자

```css
.neon-primary {
  background: linear-gradient(135deg, #BC13FE, #EBB2FF);
  box-shadow: 0 8px 32px rgba(188, 19, 254, 0.15);
}
.neon-secondary {
  box-shadow: 0 8px 32px rgba(0, 210, 255, 0.15);
}
```

---

## 5. 컴포넌트 가이드

### 버튼

| 타입 | 스타일 | 용도 |
|:---|:---|:---|
| **Primary** | 네온 그라디언트 + 글로우 + rounded-full | 결제, CTA |
| **Secondary** | 글래스모피즘 + Ghost Border | 보조 액션 |
| **Tertiary** | 텍스트만 (secondary 컬러) | 링크형 액션 |
| **Danger** | error 컬러 텍스트 | 삭제, 취소 |

### 카드

- 배경: `surface-container-lowest` (Light) / `surface-container` (Dark)
- 모서리: `border-radius: 1rem` (rounded-2xl)
- 호버: `translateY(-8px)` + 네온 글로우
- 선/구분선: **사용 금지** → 여백으로 대체

### 입력 필드

- 배경: `surface-container-highest` (Light) / `surface-container-lowest` (Dark)
- 포커스: `secondary` 컬러 1px 네온 펄스 테두리
- 라벨: `label-sm`, 플로팅 스타일

---

## 변경 이력

| 날짜 | 내용 |
|:---|:---|
| 2026-03-29 | Stitch 디자인 시스템 2종 기반 초안 작성 |
