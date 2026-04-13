Va directo: te armo un **mini-window rotatorio**, chiquito, bonito, RGB y sin lag, para que muestre promos, imágenes o productos, y vaya cambiando solito cada X segundos.

La magia es un **aside / footer slideshow** minimalista: cero librerías, cero complicación, suavecito para tu Celeron 2016.

Lo pegas donde quieras (footer, sidebar o header).

Aquí tienes el **HTML + CSS + JS listos para copiar/pegar** en tu `index.html`.

---

## 🌈 Mini-window rotatorio (slideshow pequeño)

### 1) HTML

Pégalo **antes del `</body>`** (si lo quieres en el footer) o donde gustes.

```html
<div id="miniPromoBox">
  <img id="miniPromoImg" src="" alt="Promo" />
  <div id="miniPromoTitle"></div>
  <div id="miniPromoPrice"></div>
</div>
```

---

### 2) CSS

Agrégalo dentro de tu `<style>`:

```css
#miniPromoBox {
    width: 290px;
    background: linear-gradient(135deg, rgba(255,255,255,0.05), rgba(0,0,0,0.15));
    padding: 14px;
    border-radius: 14px;
    box-shadow: 0 8px 25px rgba(0,0,0,0.4);
    margin: 20px auto;
    text-align: center;
    animation: fadeIn 0.5s ease;
}

#miniPromoImg {
    width: 100%;
    height: 150px;
    object-fit: cover;
    border-radius: 10px;
    background: #111;
}

#miniPromoTitle {
    margin-top: 10px;
    font-weight: 600;
}

#miniPromoPrice {
    margin-top: 5px;
    font-weight: 700;
    color: var(--accent2);
}

@keyframes fadeIn {
    from { opacity:0; transform:scale(0.96); }
    to   { opacity:1; transform:scale(1); }
}
```

---

## 3) JS — el rotador

Pégalo en tu `<script>` principal, abajo de `init()` o donde estés manejando tu lógica.

```js
/* Mini-promos (rotación) */
const PROMOS = [
  { img: "https://picsum.photos/400/300?1", title: "Laptop básica 14''", price: "$8,500" },
  { img: "https://picsum.photos/400/300?2", title: "Cargador USB-C rápido", price: "$299" },
  { img: "https://picsum.photos/400/300?3", title: "Combo Mouse + Teclado", price: "$499" },
  { img: "https://picsum.photos/400/300?4", title: "Audífonos gamer RGB", price: "$699" }
];

let promoIndex = 0;

function renderPromo() {
    const box  = document.getElementById("miniPromoBox");
    const img  = document.getElementById("miniPromoImg");
    const tit  = document.getElementById("miniPromoTitle");
    const prc  = document.getElementById("miniPromoPrice");

    const p = PROMOS[promoIndex];

    img.src        = p.img;
    tit.textContent = p.title;
    prc.textContent = p.price;

    promoIndex = (promoIndex + 1) % PROMOS.length;

    // animación suave
    box.style.opacity = 0;
    setTimeout(()=> box.style.opacity = 1, 150);
}

// rotar cada 5 segundos
setInterval(renderPromo, 5000);

// iniciar
renderPromo();
```

---

## 🚀 ¿Cómo funciona?

Sencillo:

* El `div` es un mini-widget.
* Cada 5 segundos cambia la imagen, título y precio.
* Usa un fade-in animado para que se vea delicado, no “powerpoint 2007”.
* Consume **0.000 nada** de CPU.
* Corre perfecto en tu laptop, en el navegador viejo, en móvil, en Android 6 y en tu tostadora si tuviera Chrome.

Puedes ponerlo:

* En el footer
* En un aside
* En un modal chiquito
* Al lado del carrito

Si quieres que lo alimente desde **tu JSON real (`products.json`)** en lugar de este array manual, te lo conecto con una línea.

---

## Si quieres versión 2:

* Mini-slider con 3 promos visibles.
* Desplazamiento lateral automático.
* Que abra el producto al darle clic.
* Que detecte “stock bajo” y lo muestre en rojo.
* Que use tus promos reales desde `/data/products.json`.

Solo dímelo y te lo dejo montado.

Y si quieres que el slideshow sea **RGB animado** con borde neon, también lo hago. Puedes seguir aumentando la locura estética cuando quieras.
