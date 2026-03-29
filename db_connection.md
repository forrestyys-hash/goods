# 🗄️ 데이터베이스 연결 계획서 (Supabase)

> **연계 문서**: `development_plan.md`
> **작성일**: 2026-03-29
> **기반 기술**: Supabase (PostgreSQL + Auth + Storage + Realtime)

---

## 1. Supabase 연결 구성도

```
┌──────────────────────────────────────────────────────┐
│                    Frontend (HTML/JS)                 │
│                                                      │
│   메인페이지  상품목록  상품상세  장바구니  마이페이지   │
│      │          │         │        │         │        │
│      └──────────┴─────────┴────────┴─────────┘        │
│                         │                             │
│              supabase-js Client SDK                   │
└─────────────────────────┬────────────────────────────┘
                          │  REST API / Realtime
                          ▼
┌─────────────────────────────────────────────────────┐
│                   Supabase Backend                   │
│                                                      │
│  ┌───────────┐  ┌──────────┐  ┌───────────────────┐  │
│  │ PostgreSQL│  │   Auth   │  │     Storage       │  │
│  │           │  │          │  │                   │  │
│  │ products  │  │ 이메일    │  │ 상품 이미지        │  │
│  │ cart      │  │ 소셜로그인 │  │ 배너 이미지        │  │
│  │ orders    │  │ JWT 토큰  │  │ 프로필 이미지      │  │
│  │ users     │  │          │  │                   │  │
│  └───────────┘  └──────────┘  └───────────────────┘  │
└─────────────────────────────────────────────────────┘
```

---

## 2. 테이블 스키마 설계

### 📦 `products` — 상품 정보

| 컬럼 | 타입 | 제약조건 | 설명 |
|:---|:---|:---|:---|
| `id` | `UUID` | PK, DEFAULT uuid_generate_v4() | 고유 식별자 |
| `title` | `TEXT` | NOT NULL | 상품명 |
| `description` | `TEXT` | — | 상세 설명 |
| `price` | `NUMERIC(10,2)` | NOT NULL, CHECK > 0 | 가격 |
| `genre` | `TEXT` | — | 장르 (Fantasy, Romance 등) |
| `image_url` | `TEXT` | — | 메인 이미지 URL |
| `stock` | `INTEGER` | DEFAULT 0 | 재고 수량 |
| `is_limited` | `BOOLEAN` | DEFAULT false | 한정판 여부 |
| `badge` | `TEXT` | — | 뱃지 (New, Rare, Best 등) |
| `created_at` | `TIMESTAMPTZ` | DEFAULT now() | 등록일 |
| `updated_at` | `TIMESTAMPTZ` | DEFAULT now() | 수정일 |

---

### 🛒 `cart` — 장바구니

| 컬럼 | 타입 | 제약조건 | 설명 |
|:---|:---|:---|:---|
| `id` | `BIGINT` | PK, GENERATED ALWAYS | 고유 ID |
| `user_id` | `UUID` | FK → auth.users(id), NOT NULL | 사용자 ID |
| `product_id` | `UUID` | FK → products(id), NOT NULL | 상품 ID |
| `quantity` | `INTEGER` | NOT NULL, CHECK > 0 | 수량 |
| `created_at` | `TIMESTAMPTZ` | DEFAULT now() | 담은 시각 |

---

### 📋 `orders` — 주문 내역

| 컬럼 | 타입 | 제약조건 | 설명 |
|:---|:---|:---|:---|
| `id` | `UUID` | PK, DEFAULT uuid_generate_v4() | 주문 ID |
| `user_id` | `UUID` | FK → auth.users(id) | 주문자 |
| `total_amount` | `NUMERIC(10,2)` | NOT NULL | 총 결제 금액 |
| `status` | `TEXT` | DEFAULT 'pending' | 상태 (pending/paid/shipped/delivered) |
| `shipping_address` | `JSONB` | — | 배송지 정보 |
| `created_at` | `TIMESTAMPTZ` | DEFAULT now() | 주문일 |

---

### 📋 `order_items` — 주문 상품 목록

| 컬럼 | 타입 | 제약조건 | 설명 |
|:---|:---|:---|:---|
| `id` | `BIGINT` | PK, GENERATED ALWAYS | 고유 ID |
| `order_id` | `UUID` | FK → orders(id) | 주문 ID |
| `product_id` | `UUID` | FK → products(id) | 상품 ID |
| `quantity` | `INTEGER` | NOT NULL | 수량 |
| `unit_price` | `NUMERIC(10,2)` | NOT NULL | 주문 시점 단가 |

---

## 3. 테이블 관계도 (ERD)

```
┌─────────────┐       ┌─────────────┐
│  auth.users │       │  products   │
│─────────────│       │─────────────│
│ id (PK)     │       │ id (PK)     │
│ email       │       │ title       │
│ ...         │       │ price       │
└──────┬──────┘       │ genre       │
       │              │ stock       │
       │              └──────┬──────┘
       │                     │
       │  ┌──────────────┐   │
       ├──│    cart       │───┤
       │  │──────────────│   │
       │  │ user_id (FK) │   │
       │  │ product_id(FK│   │
       │  │ quantity     │   │
       │  └──────────────┘   │
       │                     │
       │  ┌──────────────┐   │
       └──│   orders     │   │
          │──────────────│   │
          │ user_id (FK) │   │
          │ total_amount │   │
          │ status       │   │
          └──────┬───────┘   │
                 │           │
          ┌──────┴───────┐   │
          │ order_items  │───┘
          │──────────────│
          │ order_id(FK) │
          │ product_id(FK│
          │ quantity     │
          │ unit_price   │
          └──────────────┘
```

---

## 4. 보안 정책 (RLS)

| 테이블 | SELECT | INSERT | UPDATE | DELETE |
|:---|:---|:---|:---|:---|
| `products` | 🌐 전체 공개 | 🔒 관리자만 | 🔒 관리자만 | 🔒 관리자만 |
| `cart` | 🔑 본인만 | 🔑 인증 사용자 | 🔑 본인만 | 🔑 본인만 |
| `orders` | 🔑 본인만 | 🔑 인증 사용자 | 🔒 관리자만 | 🔒 관리자만 |
| `order_items` | 🔑 본인 주문만 | 🔑 인증 사용자 | ❌ 불가 | ❌ 불가 |

> 🌐 = 누구나 | 🔑 = 인증된 본인만 | 🔒 = 관리자만 | ❌ = 차단

---

## 5. 프론트엔드 연동 코드 (예시)

### 초기 설정
```javascript
import { createClient } from '@supabase/supabase-js'

const supabase = createClient(
  'YOUR_SUPABASE_URL',
  'YOUR_SUPABASE_ANON_KEY'
)
```

### 상품 목록 조회 (메인/리스트 페이지)
```javascript
// 전체 상품
const { data: products } = await supabase
  .from('products')
  .select('*')
  .order('created_at', { ascending: false })

// 장르별 필터링
const { data: fantasy } = await supabase
  .from('products')
  .select('*')
  .eq('genre', 'Fantasy')
```

### 장바구니 담기
```javascript
const { error } = await supabase
  .from('cart')
  .insert({
    user_id: currentUser.id,
    product_id: selectedProduct.id,
    quantity: 1
  })
```

### 장바구니 조회 (상품 정보 JOIN)
```javascript
const { data: cartItems } = await supabase
  .from('cart')
  .select(`
    id,
    quantity,
    products ( id, title, price, image_url )
  `)
  .eq('user_id', currentUser.id)
```

### 주문 생성
```javascript
// 1. 주문 레코드 생성
const { data: order } = await supabase
  .from('orders')
  .insert({ user_id: currentUser.id, total_amount: totalPrice })
  .select()
  .single()

// 2. 주문 상품 일괄 삽입
const orderItems = cartItems.map(item => ({
  order_id: order.id,
  product_id: item.product_id,
  quantity: item.quantity,
  unit_price: item.products.price
}))
await supabase.from('order_items').insert(orderItems)

// 3. 장바구니 비우기
await supabase.from('cart').delete().eq('user_id', currentUser.id)
```

---

## 6. 연동 작업 체크리스트

```
[ ] Supabase 프로젝트 생성
[ ] 환경변수(URL, ANON_KEY) 설정
[ ] products 테이블 생성 (Migration)
[ ] cart 테이블 생성
[ ] orders / order_items 테이블 생성
[ ] RLS 정책 설정
[ ] 샘플 상품 데이터 삽입
[ ] Storage 버킷 생성 (product-images)
[ ] Auth 설정 (이메일 가입 활성화)
[ ] 프론트엔드 supabase-js 초기화
[ ] 상품 목록 동적 렌더링 연결
[ ] 장바구니 CRUD 연결
[ ] 주문 프로세스 연결
```

---

> **다음 단계**: 이 문서를 기반으로 Supabase 프로젝트를 생성하고, Migration을 통해 테이블을 구축합니다.
