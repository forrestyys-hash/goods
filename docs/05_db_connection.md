# 🔌 05. DB 연결 및 API 가이드

> **버전**: v1.1 | **작성일**: 2026-03-29
> **관련 문서**: `04_db_development_plan.md`

---

## 1. 초기 설정

### CDN 방식
```html
<script src="https://cdn.jsdelivr.net/npm/@supabase/supabase-js@2"></script>
<script>
  const supabase = window.supabase.createClient('YOUR_URL', 'YOUR_ANON_KEY')
</script>
```

---

## 2. 페이지별 API 연동

### 메인 — 상품 조회
```javascript
async function getNewProducts(limit = 8) {
  const { data } = await supabase
    .from('products').select('id, title, price, image_url, badge, genre')
    .order('created_at', { ascending: false }).limit(limit)
  return data
}
```

### 상품 목록 — 필터 + 정렬 + 페이징
```javascript
async function getProductList({ genre, sortBy, page = 1, perPage = 12 }) {
  let query = supabase.from('products').select('*', { count: 'exact' })
  if (genre) query = query.eq('genre', genre)
  if (sortBy === 'price_asc') query = query.order('price', { ascending: true })
  else if (sortBy === 'price_desc') query = query.order('price', { ascending: false })
  else query = query.order('created_at', { ascending: false })
  const from = (page - 1) * perPage
  query = query.range(from, from + perPage - 1)
  const { data, count } = await query
  return { products: data, total: count, totalPages: Math.ceil(count / perPage) }
}
```

### 장바구니 — CRUD
```javascript
// 조회 (JOIN)
async function getCart() {
  const { data } = await supabase.from('cart')
    .select('id, quantity, products:product_id(id, title, price, image_url, genre, stock)')
    .order('created_at', { ascending: false })
  return data
}

// 담기 (중복 시 수량 증가)
async function addToCart(productId, qty = 1) {
  const { data: existing } = await supabase.from('cart')
    .select('id, quantity').eq('product_id', productId).maybeSingle()
  if (existing) {
    return supabase.from('cart').update({ quantity: existing.quantity + qty }).eq('id', existing.id)
  }
  return supabase.from('cart').insert({ product_id: productId, quantity: qty })
}

// 수량 변경
async function updateCartQty(cartId, quantity) {
  if (quantity <= 0) return supabase.from('cart').delete().eq('id', cartId)
  return supabase.from('cart').update({ quantity }).eq('id', cartId)
}

// 삭제
async function removeFromCart(cartId) {
  return supabase.from('cart').delete().eq('id', cartId)
}
```

### 주문 생성
```javascript
async function createOrder(cartItems, shippingAddress) {
  const totalAmount = cartItems.reduce((s, i) => s + i.products.price * i.quantity, 0)
  const { data: order } = await supabase.from('orders')
    .insert({ total_amount: totalAmount, shipping_address: shippingAddress })
    .select().single()
  const items = cartItems.map(i => ({
    order_id: order.id, product_id: i.products.id,
    quantity: i.quantity, unit_price: i.products.price
  }))
  await supabase.from('order_items').insert(items)
  await supabase.from('cart').delete().neq('id', 0)
  return order
}
```

---

## 3. Auth 연동
```javascript
// 회원가입 / 로그인 / 로그아웃
const signUp = (email, pw) => supabase.auth.signUp({ email, password: pw })
const signIn = (email, pw) => supabase.auth.signInWithPassword({ email, password: pw })
const signOut = () => supabase.auth.signOut()

// 인증 상태 감지
supabase.auth.onAuthStateChange((event, session) => {
  session ? updateUIForLoggedIn(session.user) : updateUIForAnonymous()
})
```

---

## 4. 에러 처리 패턴
```javascript
async function safeQuery(queryFn) {
  try {
    const { data, error } = await queryFn()
    if (error) { console.error('[DB]', error.message); return null }
    return data
  } catch (e) { console.error('[Network]', e); return null }
}
// 사용: const products = await safeQuery(() => supabase.from('products').select('*'))
```

---

## 변경 이력

| 날짜 | 내용 |
|:---|:---|
| 2026-03-29 | v1.0 초안 → v1.1 구조화 개선, 페이지별 API 분리 |
