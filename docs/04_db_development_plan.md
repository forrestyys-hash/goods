# 🗃️ 04. DB 개발 기획서

> **버전**: v1.0
> **작성일**: 2026-03-29
> **BaaS**: Supabase (PostgreSQL 15+)
> **관련 문서**: `05_db_connection.md` (연동 코드)

---

## 1. 데이터 모델 개요

이 쇼핑몰의 핵심 데이터 흐름은 다음과 같습니다:

```
[사용자] → 회원가입/로그인 (Auth)
    │
    ├── 상품 조회 ←── [products]
    │
    ├── 장바구니 담기 ──→ [cart]
    │
    ├── 주문 생성 ──→ [orders] + [order_items]
    │
    └── 리뷰 작성 ──→ [reviews] (Phase 3)
```

---

## 2. ERD (Entity Relationship Diagram)

```
┌─────────────────┐
│   auth.users    │          Supabase 내장 인증 테이블
│─────────────────│
│ id (PK, UUID)   │──────────────────────────────────┐
│ email           │                                   │
│ created_at      │                                   │
└─────────────────┘                                   │
        │                                             │
        │ 1:N                                         │ 1:N
        ▼                                             ▼
┌─────────────────┐                          ┌─────────────────┐
│     cart        │                          │    orders       │
│─────────────────│                          │─────────────────│
│ id (PK)         │                          │ id (PK, UUID)   │
│ user_id (FK)  ──┤                          │ user_id (FK)  ──┤
│ product_id (FK)─┤──┐                      │ total_amount    │
│ quantity        │  │                       │ status          │
│ created_at      │  │                       │ shipping_addr   │
└─────────────────┘  │                       │ created_at      │
                     │                       └────────┬────────┘
                     │                                │
                     │                                │ 1:N
                     │                                ▼
                     │                       ┌─────────────────┐
                     │                       │  order_items    │
┌─────────────────┐  │                       │─────────────────│
│    products     │  │                       │ id (PK)         │
│─────────────────│  │                       │ order_id (FK) ──┤
│ id (PK, UUID)   │◄─┘                      │ product_id (FK)─┤──┐
│ title           │                          │ quantity        │  │
│ description     │                          │ unit_price      │  │
│ price           │◄─────────────────────────┘                │  │
│ genre           │◄──────────────────────────────────────────┘
│ image_url       │
│ stock           │
│ is_limited      │
│ badge           │
│ created_at      │
│ updated_at      │
└─────────────────┘
```

---

## 3. 테이블 상세 설계

### 3-1. `products` (상품)

```sql
CREATE TABLE public.products (
  id          UUID DEFAULT uuid_generate_v4() PRIMARY KEY,
  title       TEXT NOT NULL,
  description TEXT,
  price       NUMERIC(10,2) NOT NULL CHECK (price > 0),
  genre       TEXT,
  image_url   TEXT,
  stock       INTEGER DEFAULT 0 CHECK (stock >= 0),
  is_limited  BOOLEAN DEFAULT false,
  badge       TEXT,                -- 'New', 'Rare', 'Best' 등
  created_at  TIMESTAMPTZ DEFAULT now(),
  updated_at  TIMESTAMPTZ DEFAULT now()
);

-- 인덱스
CREATE INDEX idx_products_genre ON public.products(genre);
CREATE INDEX idx_products_created ON public.products(created_at DESC);
```

### 3-2. `cart` (장바구니)

```sql
CREATE TABLE public.cart (
  id          BIGINT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
  user_id     UUID NOT NULL REFERENCES auth.users(id) ON DELETE CASCADE,
  product_id  UUID NOT NULL REFERENCES public.products(id) ON DELETE CASCADE,
  quantity    INTEGER NOT NULL CHECK (quantity > 0),
  created_at  TIMESTAMPTZ DEFAULT now(),

  UNIQUE(user_id, product_id)  -- 같은 상품 중복 방지 (수량으로 관리)
);

-- 인덱스
CREATE INDEX idx_cart_user ON public.cart(user_id);
```

### 3-3. `orders` (주문)

```sql
CREATE TABLE public.orders (
  id               UUID DEFAULT uuid_generate_v4() PRIMARY KEY,
  user_id          UUID NOT NULL REFERENCES auth.users(id),
  total_amount     NUMERIC(10,2) NOT NULL CHECK (total_amount >= 0),
  status           TEXT DEFAULT 'pending'
                     CHECK (status IN ('pending','paid','shipped','delivered','cancelled')),
  shipping_address JSONB,          -- { name, address, city, zipcode, phone }
  created_at       TIMESTAMPTZ DEFAULT now()
);

-- 인덱스
CREATE INDEX idx_orders_user ON public.orders(user_id);
CREATE INDEX idx_orders_status ON public.orders(status);
```

### 3-4. `order_items` (주문 상품)

```sql
CREATE TABLE public.order_items (
  id          BIGINT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
  order_id    UUID NOT NULL REFERENCES public.orders(id) ON DELETE CASCADE,
  product_id  UUID NOT NULL REFERENCES public.products(id),
  quantity    INTEGER NOT NULL CHECK (quantity > 0),
  unit_price  NUMERIC(10,2) NOT NULL CHECK (unit_price >= 0)
);

-- 인덱스
CREATE INDEX idx_order_items_order ON public.order_items(order_id);
```

---

## 4. RLS (Row Level Security) 정책

### 4-1. products — 누구나 조회, 관리자만 수정

```sql
ALTER TABLE public.products ENABLE ROW LEVEL SECURITY;

-- 누구나 조회 가능
CREATE POLICY "products_select_all"
  ON public.products FOR SELECT
  USING (true);

-- 관리자만 INSERT/UPDATE/DELETE (서비스 키 또는 Edge Function 처리)
```

### 4-2. cart — 본인 데이터만 접근

```sql
ALTER TABLE public.cart ENABLE ROW LEVEL SECURITY;

CREATE POLICY "cart_select_own"
  ON public.cart FOR SELECT
  USING (auth.uid() = user_id);

CREATE POLICY "cart_insert_own"
  ON public.cart FOR INSERT
  WITH CHECK (auth.uid() = user_id);

CREATE POLICY "cart_update_own"
  ON public.cart FOR UPDATE
  USING (auth.uid() = user_id);

CREATE POLICY "cart_delete_own"
  ON public.cart FOR DELETE
  USING (auth.uid() = user_id);
```

### 4-3. orders — 본인 주문만 조회

```sql
ALTER TABLE public.orders ENABLE ROW LEVEL SECURITY;

CREATE POLICY "orders_select_own"
  ON public.orders FOR SELECT
  USING (auth.uid() = user_id);

CREATE POLICY "orders_insert_own"
  ON public.orders FOR INSERT
  WITH CHECK (auth.uid() = user_id);
```

### 4-4. order_items — 본인 주문 상품만 조회

```sql
ALTER TABLE public.order_items ENABLE ROW LEVEL SECURITY;

CREATE POLICY "order_items_select_own"
  ON public.order_items FOR SELECT
  USING (
    order_id IN (
      SELECT id FROM public.orders WHERE user_id = auth.uid()
    )
  );
```

---

## 5. Storage 버킷 설계

| 버킷명 | 공개 여부 | 용도 | 파일 형식 |
|:---|:---:|:---|:---|
| `product-images` | ✅ Public | 상품 대표/갤러리 이미지 | JPG, PNG, WebP |
| `banner-images` | ✅ Public | 히어로 배너, 프로모션 | JPG, PNG, WebP |
| `user-avatars` | 🔒 Private | 회원 프로필 이미지 | JPG, PNG |

---

## 6. 마이그레이션 전략

Supabase Migration을 통해 순차적으로 테이블을 생성합니다.

| 순서 | Migration 이름 | 대상 | 비고 |
|:---:|:---|:---|:---|
| 1 | `create_products_table` | products | 핵심 테이블 |
| 2 | `create_cart_table` | cart | FK: users, products |
| 3 | `create_orders_tables` | orders + order_items | FK: users, products |
| 4 | `setup_rls_policies` | 전체 | 보안 정책 |
| 5 | `seed_sample_data` | products | 테스트용 샘플 5~10개 |

### 샘플 데이터 (Migration 5번용)

```sql
INSERT INTO public.products (title, description, price, genre, is_limited, badge, stock) VALUES
  ('Shadow Monarch Acrylic Standee', 'Limited Edition - Solo Leveling Series', 24.99, 'Fantasy', true, 'Rare', 50),
  ('The Villainess Reverses the Hourglass Vol.1', 'Hardcover Collector''s Edition', 35.00, 'Romance', false, NULL, 120),
  ('Abyssal Dragon Ceramic Mug', 'Matte black with embossed dragon logo', 18.00, 'Fantasy', false, 'New', 200),
  ('Hero''s Crest Enamel Pin Set', '5-piece colorful pin collection', 12.50, 'Action', false, NULL, 300),
  ('Floating Island Lithograph', 'Metallic paper art print, panoramic fantasy cityscape', 45.00, 'Fantasy', true, 'Rare', 30),
  ('Mana Weaver Oversized Hoodie', 'Purple hoodie with minimalist cat line-art', 55.00, 'Slice of Life', false, NULL, 80);
```

---

## 7. DB 작업 체크리스트

```
Phase 2 작업 순서:
━━━━━━━━━━━━━━━━━
[ ] 1. Supabase 프로젝트 생성
[ ] 2. 환경변수 확보 (URL, ANON_KEY)
[ ] 3. Migration #1: products 테이블 생성
[ ] 4. Migration #2: cart 테이블 생성
[ ] 5. Migration #3: orders + order_items 생성
[ ] 6. Migration #4: RLS 정책 설정
[ ] 7. Migration #5: 샘플 데이터 삽입
[ ] 8. Storage 버킷 생성 (product-images)
[ ] 9. Auth 설정 (이메일 가입 활성화)
[ ] 10. Security Advisor 점검
```

---

## 변경 이력

| 날짜 | 내용 |
|:---|:---|
| 2026-03-29 | DDL, RLS, Storage, Migration 전략 포함 초안 작성 |
