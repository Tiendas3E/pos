# cajero







con todo el cariño nerd.


Hice **dos cosas** :

1. Te aviso: algunos archivos que subiste antes vencieron y no puedo leerlos aquí. Si quieres que integre tu repo exacto (`cajero-main.zip` / otros), vuelve a subirlos o dame acceso al repo privado. Mientras tanto, **voy a darte una versión completa y lista para pegar** que funcionará en tu repositorio tal cual (solo crea la carpeta `/data/` y pega los JSON).

2. Te dejo **un `index.html` estilo marketplace RGB “mamalón”** (todo en un archivo para copiar/pegar), más 2 JSON de ejemplo (`products.json` y `pricing.json`) y la **lógica** que pediste: `fetch` con timeout y fallback, carga progresiva (UI aparece inmediatamente), carrito, puntos (1%), incentivo cajero (+1 por ticket), link a WhatsApp con el carrito, ticket imprimible y almacenamiento en `localStorage`. También te doy la **cotización** sugerida (one-time + mensual) y proyecciones rápidas.

---

## 1) Archivos que pega directo

### `/data/products.json` (ejemplo — reemplázalo por el tuyo)

```json
{
  "products": [
    { "id":"p001", "name":"Cargador USB-C", "price":299, "stock":12, "img": "" },
    { "id":"p002", "name":"Laptop Básica 14\"", "price":8500, "stock":3, "img": "" },
    { "id":"p003", "name":"Mouse Óptico", "price":149, "stock":25, "img": "" },
    { "id":"p004", "name":"Cable USB-A", "price":59, "stock":50, "img": "" }
  ]
}
```

### `/data/pricing.json` (repito por conveniencia)

```json
{
  "plans": [
    { "id": "basic", "name": "Plan Básico", "price_mxn": 500, "billing": "mensual", "features": ["Catálogo","Carrito WhatsApp","Subdominio","Soporte básico"] },
    { "id": "pro", "name": "Plan Profesional", "price_mxn": 1400, "billing": "mensual", "features": ["Todo Básico","Dashboard","Chat","Soporte prioridad"] },
    { "id": "enterprise", "name": "Plan Empresa", "price_mxn": 3200, "billing": "mensual", "features": ["Todo Pro","Multiusuario","Reportes","Integraciones"] }
  ]
}
```

---

## 2) `index.html` — marketplace RGB + lógica (copia todo en `index.html`)

Guarda esto y ábrelo; crea `/data/` con los JSON de arriba. Está pensado para funcionar en Vercel / GitHub Pages sin backend.
(El archivo es auto-contenido: HTML + CSS + JS. Cambia `telefonoVendedor` por tu número para probar WhatsApp.)

```html
<!doctype html>
<html lang="es">
<head>
<meta charset="utf-8" />
<meta name="viewport" content="width=device-width,initial-scale=1" />
<title>Market V0.0.10 — OS Shop</title>
<link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;600;700&display=swap" rel="stylesheet">
<style>
  :root{
    --bg:#071025; --card:#0f1726; --glass: rgba(255,255,255,0.04);
    --accent1:#8b5cf6; --accent2:#06b6d4; --accent3:#f97316;
    color-scheme: dark;
    font-family: Inter, system-ui, Arial;
  }
  *{box-sizing:border-box}
  body{margin:0;background:linear-gradient(180deg,#03041a 0%, #071025 60%); color:#e6eef8; -webkit-font-smoothing:antialiased}
  header{display:flex;align-items:center;justify-content:space-between;padding:18px 24px}
  .brand{display:flex;align-items:center;gap:12px}
  .logo{width:44px;height:44px;border-radius:10px;background:linear-gradient(135deg,var(--accent1),var(--accent2));box-shadow:0 8px 30px rgba(107,70,193,0.25);display:flex;align-items:center;justify-content:center;font-weight:700}
  h1{margin:0;font-size:18px}
  .container{display:grid;grid-template-columns:1fr 360px;gap:18px;padding:18px}
  /* Productos */
  .grid{display:grid;grid-template-columns:repeat(auto-fill,minmax(220px,1fr));gap:14px}
  .card{background:linear-gradient(180deg,rgba(255,255,255,0.02),rgba(0,0,0,0.08));border-radius:12px;padding:12px;box-shadow:0 10px 30px rgba(2,6,23,0.6);border:1px solid rgba(255,255,255,0.03)}
  .thumb{height:120px;border-radius:8px;background:linear-gradient(45deg,#111 0%, #20202a 100%);display:flex;align-items:center;justify-content:center;color:#9aa3b2;font-size:13px}
  .product-name{font-weight:600;margin-top:8px}
  .price{font-weight:700;color:var(--accent2);margin-top:6px}
  .btn{cursor:pointer;padding:8px 12px;border-radius:10px;border:none;background:linear-gradient(90deg,var(--accent1),var(--accent2));color:white;font-weight:600}
  .muted{color:rgba(230,238,248,0.6);font-size:13px}
  /* Carrito */
  .aside{position:sticky;top:18px}
  .cart-head{display:flex;justify-content:space-between;align-items:center;gap:8px}
  .cart-items{max-height:420px;overflow:auto;margin-top:12px;display:flex;flex-direction:column;gap:8px}
  .item-row{display:flex;justify-content:space-between;gap:8px;background:var(--glass);padding:8px;border-radius:8px}
  .total{margin-top:12px;font-weight:800;font-size:18px;color:var(--accent3)}
  .small{font-size:13px;color:rgba(230,238,248,0.6)}
  .skeleton{height:120px;border-radius:8px;background:linear-gradient(90deg, rgba(255,255,255,0.02), rgba(255,255,255,0.04), rgba(255,255,255,0.02)); animation: shine 1.2s linear infinite}
  @keyframes shine{0%{background-position:-200px 0}100%{background-position:200px 0}}
  footer{padding:18px;text-align:center;color:rgba(230,238,248,0.5)}
  .badge{background:rgba(255,255,255,0.04);padding:6px 8px;border-radius:8px;font-weight:600;color:#bcd}
  .controls{display:flex;gap:8px;align-items:center}
  input,select{padding:8px;border-radius:8px;border:1px solid rgba(255,255,255,0.04);background:transparent;color:inherit}
  .compact{font-size:13px;padding:6px 8px}
  @media(max-width:900px){.container{grid-template-columns:1fr;}.aside{position:static}}
</style>
</head>
<body>
<header>
  <div class="brand">
    <div class="logo">OS</div>
    <div>
      <h1>Bio-us Marketplace</h1>
      <div class="muted">Tu marketplace modular — demo</div>
    </div>
  </div>
  <div class="controls">
    <div id="statusBadge" class="badge">Cargando...</div>
    <select id="filterVendor" class="compact"><option value="">Todos</option></select>
    <button id="refreshBtn" class="compact">Refrescar</button>
  </div>
</header>

<div class="container">
  <main>
    <div style="display:flex;align-items:center;justify-content:space-between;margin-bottom:12px">
      <div class="muted" id="sourceLabel">Cargando catálogo...</div>
      <div class="muted">Planes: <span id="planLabel">—</span></div>
    </div>

    <div id="productsGrid" class="grid">
      <!-- cards o skeletons -->
    </div>
  </main>

  <aside class="aside">
    <div class="card">
      <div class="cart-head">
        <div>
          <div class="muted">Carrito</div>
          <div class="small" id="cartCount">0 items</div>
        </div>
        <div><button id="clearCart" class="compact">Limpiar</button></div>
      </div>

      <div id="cartItems" class="cart-items" aria-live="polite"></div>

      <div class="total" id="totalLine">Total: $0.00</div>
      <div class="small" id="pointsLine">Puntos cliente: 0</div>

      <div style="margin-top:10px;display:flex;gap:8px">
        <input id="clienteId" placeholder="Email o phone (opcional)" style="flex:1"/>
      </div>

      <div style="margin-top:10px;display:flex;gap:8px">
        <button id="closeTicket" class="btn">Cerrar ticket</button>
        <button id="waBtn" class="btn" style="background:linear-gradient(90deg,#34c759,#06b6d4)">WhatsApp</button>
      </div>

      <div style="margin-top:10px" class="small">Tickets guardados localmente. Para puntos reales conecta Firebase/Supabase.</div>
    </div>

    <div style="height:12px"></div>
    <div class="card">
      <div class="muted">Demo Admin</div>
      <div style="margin-top:8px;">
        <button id="openAdmin" class="compact">Panel Admin</button>
      </div>
    </div>
  </aside>
</div>

<footer>Diseño RGB · OS Shop · <span id="buildInfo">v0.0.10 demo</span></footer>

<script>
/* ---------------- util: fetch con timeout ---------------- */
function fetchWithTimeout(url, ms = 2500) {
  return Promise.race([
    fetch(url).then(r => { if(!r.ok) throw 'fetch-fail'; return r.json(); }),
    new Promise((_, rej) => setTimeout(() => rej('timeout'), ms))
  ]);
}

/* ---------------- estado ---------------- */
let PRODUCTS = [];
let VENDORS = [];
let CART = [];
const telefonoVendedor = '5215577000000'; // cambia por tu número real si quieres probar

/* ---------------- helpers (celdas de lógica) ---------------- */
// suma total (digit-by-digit mental: JS calcula floats, mostramos con toFixed)
function calcTotal(cart) {
  return cart.reduce((acc, it) => acc + (Number(it.price) * Number(it.qty)), 0);
}
function calcClientPoints(total) {
  const p = total * 0.01; // 1%
  return Number(p.toFixed(2));
}
function calcCajeroIncentive() { return 1; } // por ticket
function cartToWhatsAppText(cart) {
  const lines = cart.map(i => `${i.qty}x ${i.name} - $${(i.qty*i.price).toFixed(2)}`);
  const total = calcTotal(cart).toFixed(2);
  return encodeURIComponent(`Pedido:\n${lines.join('\\n')}\nTotal: $${total}`);
}

/* ---------------- UI render ---------------- */
function renderSkeletons() {
  const grid = document.getElementById('productsGrid');
  grid.innerHTML = Array.from({length:8}).map(()=>`<div class="card"><div class="thumb skeleton"></div><div style="height:10px"></div><div class="skeleton" style="height:14px"></div></div>`).join('');
}
function renderProducts(list) {
  const grid = document.getElementById('productsGrid');
  if (!list.length) {
    grid.innerHTML = `<div class="card"><div class="muted">No hay productos</div></div>`;
    return;
  }
  grid.innerHTML = list.map(p => `
    <div class="card">
      <div class="thumb">${p.img ? `<img src="${p.img}" style="max-width:100%;border-radius:6px"/>` : '<div style="text-align:center">IMG</div>'}</div>
      <div class="product-name">${p.name}</div>
      <div class="price">$${Number(p.price).toFixed(2)}</div>
      <div style="margin-top:8px;display:flex;gap:8px;align-items:center">
        <input class="qty" data-id="${p.id}" type="number" min="1" value="1" style="width:70px;padding:6px;border-radius:8px"/>
        <button class="btn addBtn" data-id="${p.id}">Agregar</button>
      </div>
      <div class="small" style="margin-top:8px">Stock: ${p.stock}</div>
    </div>
  `).join('');

  // listeners
  document.querySelectorAll('.addBtn').forEach(b => b.addEventListener('click', (e) => {
    const id = e.target.dataset.id;
    const input = document.querySelector(`.qty[data-id="${id}"]`);
    const qty = Math.max(1, Number(input.value || 1));
    const p = PRODUCTS.find(x=>x.id===id);
    addToCart({ id: p.id, name: p.name, price: Number(p.price), qty });
  }));
}

/* ---------------- cart actions ---------------- */
function addToCart(item) {
  // si ya existe, suma qty
  const idx = CART.findIndex(i => i.id === item.id);
  if (idx >= 0) CART[idx].qty += item.qty;
  else CART.push(item);
  renderCart();
  saveLocal('os_cart', CART);
}
function renderCart() {
  const container = document.getElementById('cartItems');
  const total = calcTotal(CART);
  document.getElementById('totalLine').textContent = 'Total: $' + total.toFixed(2);
  document.getElementById('pointsLine').textContent = 'Puntos cliente: ' + calcClientPoints(total).toFixed(2);
  document.getElementById('cartCount').textContent = CART.length + ' items';

  if (!CART.length) { container.innerHTML = '<div class="small muted">Carrito vacío</div>'; return; }
  container.innerHTML = CART.map((it, i) => `<div class="item-row">
    <div style="flex:1"><strong>${it.name}</strong><div class="small">x${it.qty} · $${it.price}</div></div>
    <div style="text-align:right">$${(it.qty*it.price).toFixed(2)}<br><button data-idx="${i}" class="compact removeBtn">X</button></div>
  </div>`).join('');
  container.querySelectorAll('.removeBtn').forEach(b => b.addEventListener('click', e => {
    const i = Number(e.target.dataset.idx);
    CART.splice(i,1); renderCart(); saveLocal('os_cart',CART);
  }));
}
function clearCart() { CART = []; renderCart(); saveLocal('os_cart', CART); }

/* ---------------- storage helpers ---------------- */
function saveLocal(k,v){ localStorage.setItem(k, JSON.stringify(v)); }
function loadLocal(k, fallback){ try{ const s = localStorage.getItem(k); return s ? JSON.parse(s) : fallback; }catch(e){ return fallback; } }

/* ---------------- load data (fetch con timeout + fallback a localStorage) ---------------- */
async function loadAll() {
  renderSkeletons();
  document.getElementById('statusBadge').textContent = 'Cargando productos...';
  try {
    const data = await fetchWithTimeout('./data/products.json', 2500);
    PRODUCTS = data.products || [];
    localStorage.setItem('os_products', JSON.stringify(PRODUCTS));
    document.getElementById('sourceLabel').textContent = 'data/products.json (fetch)';
  } catch (e) {
    const stored = loadLocal('os_products', []);
    if (stored.length) {
      PRODUCTS = stored;
      document.getElementById('sourceLabel').textContent = 'localStorage (fallback)';
    } else {
      PRODUCTS = [];
      document.getElementById('sourceLabel').textContent = 'sin datos';
    }
  }
  // carga pricing
  try {
    const pD = await fetchWithTimeout('./data/pricing.json', 2500);
    const pro = pD.plans?.find(x=>x.id==='pro');
    document.getElementById('planLabel').textContent = pro ? `${pro.name} $${pro.price_mxn}/mes` : '—';
  } catch(e){
    document.getElementById('planLabel').textContent = '—';
  }

  // render
  renderProducts(PRODUCTS);
  document.getElementById('statusBadge').textContent = 'Listo';
  // restore cart
  CART = loadLocal('os_cart', []);
  renderCart();
}

/* ---------------- ticket flow: cerrar y guardar ---------------- */
function createTicketObject(clienteId=null) {
  const total = Number(calcTotal(CART).toFixed(2));
  const puntos = calcClientPoints(total);
  const incentivo = calcCajeroIncentive();
  return {
    id: 'tk_' + Date.now(),
    cliente: clienteId,
    items: CART,
    total,
    puntos_generados: puntos,
    incentivo_cajero: incentivo,
    createdAt: new Date().toISOString()
  };
}

function closeTicket() {
  if (!CART.length) return alert('Carrito vacío');
  const cliente = document.getElementById('clienteId').value.trim() || null;
  const ticket = createTicketObject(cliente);
  const tickets = loadLocal('os_tickets', []);
  tickets.unshift(ticket);
  saveLocal('os_tickets', tickets);

  // incentivos cajero (guardamos en os_cajeros.local)
  const cajeroKey = 'cajero_default';
  const cajeros = loadLocal('os_cajeros', {});
  cajeros[cajeroKey] = cajeros[cajeroKey] || { name: 'Cajero', incentivo: 0 };
  cajeros[cajeroKey].incentivo = (cajeros[cajeroKey].incentivo || 0) + ticket.incentivo_cajero;
  saveLocal('os_cajeros', cajeros);

  // puntos cliente
  if (cliente) {
    const clients = loadLocal('os_clients', {});
    clients[cliente] = clients[cliente] || { id: cliente, puntos: 0 };
    clients[cliente].puntos = Number((clients[cliente].puntos + ticket.puntos_generados).toFixed(2));
    saveLocal('os_clients', clients);
  }

  // abrir ticket en nueva ventana para imprimir
  const w = window.open('','_blank','width=600,height=800');
  w.document.write(buildTicketHTML(ticket));
  w.document.close();
  // limpiar carrito
  clearCart();
  document.getElementById('statusBadge').textContent = `Ticket ${ticket.id} generado`;
}

/* ---------------- ticket HTML simple ---------------- */
function buildTicketHTML(t) {
  const rows = t.items.map(i=>`<tr><td>${i.name}</td><td>${i.qty}</td><td>$${i.price}</td><td>$${(i.qty*i.price).toFixed(2)}</td></tr>`).join('');
  return `
  <html><head><meta charset="utf-8"><title>Ticket ${t.id}</title>
  <style>body{font-family:Arial;padding:16px;color:#111}table{width:100%;border-collapse:collapse}td,th{padding:6px;border-bottom:1px solid #ddd}</style>
  </head><body>
  <h2>OS TIENDA</h2><p>ID: ${t.id}<br>Fecha: ${t.createdAt}</p>
  <table><thead><tr><th>Producto</th><th>Cant</th><th>PU</th><th>Sub</th></tr></thead><tbody>${rows}</tbody></table>
  <h3>Total: $${t.total.toFixed(2)}</h3><p>Puntos: ${t.puntos_generados.toFixed(2)}</p><p>Gracias por comprar</p>
  <script>setTimeout(()=>window.print(),400);</script></body></html>`;
}

/* ---------------- WhatsApp link ---------------- */
function sendWhatsApp() {
  if (!CART.length) return alert('Carrito vacío');
  const txt = cartToWhatsAppText(CART);
  const url = `https://wa.me/${telefonoVendedor}?text=${txt}`;
  window.open(url, '_blank');
}

/* ---------------- admin demo ---------------- */
function openAdmin() {
  const tickets = loadLocal('os_tickets', []);
  const clients = loadLocal('os_clients', {});
  const cajeros = loadLocal('os_cajeros', {});
  const html = `
    <html><head><meta charset="utf-8"><title>Admin</title></head><body style="font-family:Arial;padding:16px">
    <h3>Admin demo</h3>
    <h4>Tickets (${tickets.length})</h4>
    <pre>${JSON.stringify(tickets.slice(0,20), null, 2)}</pre>
    <h4>Clientes</h4><pre>${JSON.stringify(clients, null, 2)}</pre>
    <h4>Cajeros</h4><pre>${JSON.stringify(cajeros, null,2)}</pre>
    </body></html>`;
  const w = window.open('','_blank','width=900,height=700'); w.document.write(html); w.document.close();
}

/* ---------------- eventos ---------------- */
document.getElementById('refreshBtn').addEventListener('click', loadAll);
document.getElementById('clearCart').addEventListener('click', ()=>{ if(confirm('Limpiar carrito?')) clearCart(); });
document.getElementById('closeTicket').addEventListener('click', closeTicket);
document.getElementById('waBtn').addEventListener('click', sendWhatsApp);
document.getElementById('openAdmin').addEventListener('click', openAdmin);

/* ---------------- init ---------------- */
(async function init(){ await loadAll(); })();
</script>
</body>
</html>
```

---

## 3) Cómo pegarlo en tu repo (rápido)

1. Crea carpeta `data/` en tu repo. Pon `products.json` y `pricing.json`.
2. Reemplaza `index.html` en tu carpeta `market` o `venta` por este `index.html`.
3. Sube todo a Vercel/GitHub Pages. Funciona desde laptops viejas (tu Celeron 2016 con 4GB) porque la UI carga primero y los datos son pequeños.

---

## 4) Cotización (one-time + mensual de mantenimiento / despliegue / SaaS)

**A. Desarrollo e integración (one-time)** — código, tests básicos, deploy en Vercel, integrar JSON y admin demo:

* **Paquete Básico** (entrega: marketplace + POS demo + admin demo): **6,000 MXN**.

* **Paquete Pro** (todo lo anterior + chat realtime (Supabase), subdominios por empresa + setup simple para múltiples empresas, branding): **18,000 MXN**.

* **Paquete Enterprise** (Pro + integraciones reales de facturación / facturas PDF automáticas / reportes, soporte 30 días, personalizaciones): **45,000 MXN**.

(Estos precios son ejemplos razonables para una implementación profesional en México. Si quieres, hago presupuesto exacto según tu repo cuando lo subas.)

**B. Mantenimiento / Hosting (mensual)**

* Hosting básico + backups + soporte: **500 MXN / mes**.
* Hosting + soporte prioritario + actualizaciones menores: **1,400 MXN / mes**.
* Soporte full (incluye cambios, nuevos módulos): **3,500 MXN / mes**.

**C. Modelo SaaS (si lo RENTAS por empresa)**

* Recomiendo plan PRO: **1,400 MXN / mes por empresa**.

  * Si consigues **10 empresas**: 10 × 1,400 = 14,000 MXN mensuales.
  * Si consigues **30 empresas**: 30 × 1,400 = 42,000 MXN mensuales.

Cálculos paso a paso:

* 1,400 × 10 = 14,000.
* 1,400 × 30 = 42,000.

Esa renta te pone en modo “no volver a trabajar para nadie” si escalas clientes y mantienes churn bajo.

---

## 5) Siguientes pasos (elige 1–2 y lo hago)

* [ ] Integro exactamente tu repo (sube el `.zip` o da acceso al repo privado).
* [ ] Conecto un Firebase / Supabase demo para que los puntos y tickets vivan en la nube.
* [ ] Agrego chat realtime (Supabase Realtime) y panel por empresa + subdominios.
* [ ] Armo `admin.html` con filtros bonitos, export CSV y descarga de tickets en lote.
* [ ] Ajusto el diseño RGB a tu estilo exacto (colores, tipografías, logos).

Puedes decir “haz X” y lo hago — o súbeme el repo/zip y me pongo a integrar. Si quieres que integre ahora, **sube el `cajero-main.zip` o dale acceso al repo privado** (o vuelve a subir los archivos que expiraron), y yo me encargo.

---

Listo, te di la versión funcional, los JSON, la lógica de fórmulas (celdas) y una cotización clara.
Dime qué parte quieres que haga ahora mismo (integro repo, enlazo Supabase, o te dejo todo listo para deploy).

Vamos a dejar ese market **mamalón** y que tu laptop Celeron y la pantalla medio rota se conviertan en una obra de arte productiva. 🔧✨
