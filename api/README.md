# Poldinas 🪙

App para repartir la cuenta cuando el equipo va a consumir las poldinas (multas). Los montos se muestran en pesos dominicanos (RD$).

## Qué hace

- **Personas**: agregas a cada integrante. La cantidad de poldinas de cada uno se calcula sola, a partir de las multas que le registres (no la escribes a mano).
- **Registrar poldina**: cada multa queda guardada con persona, fecha, hora, motivo (Taza sucia o fuera de lugar, Luz encendida, Aire encendido, Dejó el carnet, u Otro) y una descripción libre opcional.
- **Factura**:
  - Si subes un **PDF con texto real** (no una foto guardada como PDF, sino uno generado digitalmente por el sistema de facturación), la app lo lee automáticamente **sin ningún costo ni configuración** — usa una librería de Python (`pdfplumber`) que extrae el texto que ya existe en el archivo, sin inteligencia artificial.
  - Si subes una **foto** (o un PDF que en realidad es una imagen escaneada), la app intenta leerla con un **OCR local** (`RapidOCR`) — también gratis y sin API key, corre dentro del mismo servidor de Python. Funciona mejor con fotos derechas, bien iluminadas y sin mucha inclinación; con fotos muy torcidas o borrosas puede no reconocer nada, y en ese caso te avisa.
  - Si además configuras una `ANTHROPIC_API_KEY` (opcional, de pago), la app la usa como respaldo cuando el OCR local no reconoce nada útil — más precisa para fotos difíciles. *Ver más abajo cómo activarla.*
  - Sin ninguna configuración, siempre puedes **pegar el texto de la factura** (copiado o escrito a mano) y un lector de texto (sin IA, gratis, siempre disponible) reconoce líneas como `2 Pizza mediana 24000` o `Gaseosa 3000`.
  - Los items leídos quedan en una zona de revisión donde ajustas cantidad, precio, tipo (Común/Individual) y a quién asignar antes de confirmarlos.
  - También puedes agregar items a mano en cualquier momento.
- **Cálculo automático**: las multas cubren el consumo común. Si el consumo se pasa de lo que cubren las multas, el exceso se reparte por partes iguales entre quienes no tienen multa (o entre todos, si nadie está libre de multa). Los items individuales los paga cada quien.
- **Aviso de items sin asignar**: si dejas una bebida sin decir quién la consumió, la app te avisa en vez de perderla del total.
- **Exportar a PDF y Excel** con el resumen, el reparto por persona, la factura completa y el historial de multas.
- **Guardado**: los datos quedan guardados solos en tu navegador. Puedes guardar distintos eventos y volver a cargarlos.

---

## Estructura

```
poldinas-app/
├── api/
│   └── index.py       # App Flask: interfaz + cálculo + lectura de factura + PDF + Excel
├── requirements.txt   # Flask, fpdf2, openpyxl, requests
├── vercel.json        # Enruta todo a la app Flask
└── README.md
```

---

## Subir a Vercel

### Desde GitHub (recomendada)
1. Sube esta carpeta (`poldinas-app`) a un repositorio en GitHub.
2. Entra a https://vercel.com → **Add New… → Project**.
3. Importa el repositorio. Vercel detecta Python solo (lee `vercel.json` y `requirements.txt`).
4. Clic en **Deploy**.

### Con la CLI
```bash
npm i -g vercel
cd poldinas-app
vercel        # deploy de prueba
vercel --prod # publicar
```

---

## Si el OCR local (fotos) no funciona al desplegar

La lectura de fotos usa una librería llamada `rapidocr-onnxruntime`, que se instala con `pip` sin necesitar ningún programa de sistema — a diferencia de Tesseract, que sí lo necesita y por eso no es compatible con Vercel. Aun así, no pude probar esta instalación en un despliegue real de Vercel antes de entregártela.

Si al subir una foto te aparece el error *"El lector automático (OCR) no está disponible en este servidor todavía"*, probablemente algo falló al instalar `rapidocr-onnxruntime` o `PyMuPDF` durante el build de Vercel. En ese caso:

1. Entra a tu proyecto en Vercel → **Deployments** → abre el despliegue más reciente → pestaña **Building** (o **Logs**).
2. Busca si hay un error relacionado con `rapidocr`, `onnxruntime` o `fitz`/`PyMuPDF`.
3. Cópiame el error exacto y lo ajustamos — puede ser simplemente que el nombre del paquete cambió de versión, o que se necesite un ajuste menor.

Mientras tanto, "Pegar texto" y "Agregar a mano" siguen funcionando siempre, sin depender de esto.

## Activar la lectura automática de facturas (opcional)

Sin configurar nada, la app funciona igual usando "Pegar texto" o agregando items a mano.

Si quieres que **suba una foto y la lea sola**, necesitas una API key de Anthropic:

1. Crea una cuenta en https://console.anthropic.com y genera una API key.
2. En tu proyecto de Vercel: **Settings → Environment Variables**.
3. Agrega una variable:
   - **Name:** `ANTHROPIC_API_KEY`
   - **Value:** tu clave (empieza con `sk-ant-...`)
4. Vuelve a desplegar el proyecto (Vercel → **Deployments → Redeploy**) para que tome la variable.

Nota: esto usa la API de pago de Anthropic — cada foto leída tiene un costo pequeño según el uso. Si no configuras la key, la app simplemente muestra un aviso y sigue funcionando con el método de pegar texto o manual.

---

## Probar en tu computador (opcional)

```bash
cd poldinas-app
pip install -r requirements.txt
python api/index.py
# abre http://localhost:5000
```

---

## Notas

- **Guardado:** los eventos se guardan en el navegador (localStorage) del dispositivo donde los creas. No requiere base de datos.
- **Compartir entre varios equipos/dispositivos:** si más adelante quieres que todos vean los mismos datos desde cualquier lugar, se puede conectar Vercel KV o Vercel Postgres. Avísame y lo agrego.
- **Cálculo:** lo hace el servidor en Python (`/api/calc`); si el servidor no responde, la interfaz calcula igual como respaldo.
- **Tamaño de archivo:** las fotos de factura muy pesadas pueden fallar por límites de tamaño en Vercel. Si pasa, prueba con una foto más liviana o usa "Pegar texto".
