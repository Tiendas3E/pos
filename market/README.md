Va, aquí te va **tu JSON de precios**, limpio, profesional y listo para engancharlo a tu marketplace, exactamente como lo pediría un SaaS serio.

Sin rodeos, sin adornos: **copias esto, lo pegas en `/data/pricing.json` y ya tienes tu lista de precios lista para cargar con `fetch()`.**

```json
{
  "plans": [
    {
      "id": "basic",
      "name": "Plan Básico",
      "price_mxn": 500,
      "billing": "mensual",
      "features": [
        "Catálogo principal",
        "Carrito con WhatsApp",
        "Subdominio empresa.vercel.app",
        "Actualizaciones menores",
        "Soporte básico"
      ]
    },
    {
      "id": "pro",
      "name": "Plan Profesional",
      "price_mxn": 1400,
      "billing": "mensual",
      "features": [
        "Todo del Plan Básico",
        "Dashboard de métricas",
        "Chat interno vendedor-cliente",
        "Reportes PDF",
        "Branding personalizado",
        "Soporte prioridad"
      ]
    },
    {
      "id": "enterprise",
      "name": "Plan Empresa",
      "price_mxn": 3200,
      "billing": "mensual",
      "features": [
        "Todo del Plan Profesional",
        "Multiusuario",
        "Roles y permisos",
        "Reportes avanzados",
        "Integraciones extra",
        "Soporte diario",
        "Custom features"
      ]
    }
  ]
}
```

### Cómo cargarlo en tu HTML sin trabar el sitio

Este fragmento hace que tu página:

• cargue instantáneo
• no se congele
• no quede esperando eternamente
• y muestre los planes cuando ya estén listos

```js
async function cargarPrecios() {
  try {
    const precios = await fetch('./data/pricing.json')
      .then(res => res.json());

    renderPricing(precios.plans);
  } catch (e) {
    console.warn("No se pudieron cargar los precios:", e);
    renderPricing([]); // fallback sin romper nada
  }
}

function renderPricing(planes) {
  const cont = document.getElementById("pricingBox");

  if (!planes.length) {
    cont.innerHTML = "<p>No disponible temporalmente.</p>";
    return;
  }

  cont.innerHTML = planes.map(p => `
    <div class="plan">
      <h2>${p.name}</h2>
      <p><strong>$${p.price_mxn} MXN / ${p.billing}</strong></p>
      <ul>
        ${p.features.map(f => `<li>${f}</li>`).join("")}
      </ul>
    </div>
  `).join("");
}

cargarPrecios();
```

Listo.
Tienes un **módulo profesional de precios**, con estructura para vender como SaaS y con JSON escalable.

Si quieres, te hago también:

• el JSON de productos,
• el de vendedores,
• el del carrito,
• y la estructura completa para tu marketplace modular.

Dime cuál sigue y lo armamos con la misma calidad.
