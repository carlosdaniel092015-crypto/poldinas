# Poldinas 🪙

App para repartir la cuenta cuando el equipo va a consumir las poldinas (multas). Los montos se muestran en pesos dominicanos (RD$).

Esta versión corre en **Vercel** y usa la **IA de visión de OpenAI** para leer las fotos de factura.

## Qué hace

- **Personas**: agregas a cada integrante. Sus poldinas se calculan solas según las multas que le registres.
- **Registrar poldina**: cada multa queda con persona, fecha, hora, motivo (Taza sucia o fuera de lugar, Luz encendida, Aire encendido, Dejó el carnet, u Otro) y descripción opcional.
- **Factura**: subes una **foto** o un **PDF** y la app la lee automáticamente, o **pegas el texto**, o agregas los items a mano. Cada item se marca como Común (lo pagan las multas) o Individual (lo paga la persona).
- **Cálculo**: las multas cubren el consumo común; si se pasa, la diferencia se divide entre TODAS las personas por igual. Los extras los paga cada quien. Detecta subtotal/total y agrega los impuestos como cargo común para que el total cuadre.
- **Exporta a PDF y Excel**. Guardado automático en el navegador, con historial de eventos.

---

## Estructura

```
poldinas-vercel/
├── api/
│   └── index.py       # App Flask + cálculo + lectura de factura + PDF + Excel
├── requirements.txt
├── vercel.json
└── README.md
```

---

## Paso 1: subir a GitHub y a Vercel

1. Sube esta carpeta (`poldinas-vercel`) a un repositorio de GitHub.
2. En https://vercel.com → **Add New… → Project** → importa el repositorio.
3. Vercel detecta Python solo (por `vercel.json`). Clic en **Deploy**.

## Paso 2: configurar la clave de OpenAI (para leer fotos)

⚠️ **Nunca escribas la clave dentro del código ni la compartas en chats.** Solo va en las variables de entorno de Vercel.

1. Entra a https://platform.openai.com/api-keys y crea una API key nueva.
2. En tu proyecto de Vercel: **Settings → Environment Variables**.
3. Agrega:
   - **Name:** `OPENAI_API_KEY`
   - **Value:** tu clave (empieza con `sk-...`)
4. (Opcional) Para elegir otro modelo, agrega `OPENAI_MODEL` (por defecto usa `gpt-4o-mini`, que es barato y con visión).
5. Ve a **Deployments → Redeploy** para que tome la variable.

Sin esta clave, la app funciona igual con "Pegar texto", PDF con texto real y agregar a mano; solo la lectura de fotos queda desactivada (te avisa).

---

## Notas

- **Costo:** leer una foto con OpenAI cuesta centavos (según su tarifa por uso). Tú controlas el gasto desde tu cuenta de OpenAI (puedes ponerle un límite mensual).
- **Seguridad de la clave:** si alguna vez expones tu clave por error, ve a platform.openai.com/api-keys, elimínala y crea una nueva.
- **Guardado:** los eventos se guardan en el navegador del dispositivo donde los creas (no requiere base de datos).
- Aunque la IA es buena leyendo, siempre puedes revisar y corregir lo leído (cantidad, precio, tipo, persona) antes de confirmar.

---

## Probar en tu computador (opcional)

```bash
cd poldinas-vercel
pip install -r requirements.txt
export OPENAI_API_KEY=tu_clave   # en Windows: set OPENAI_API_KEY=tu_clave
python api/index.py
# abre http://localhost:5000
```
