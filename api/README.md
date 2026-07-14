# Poldinas 🪙

App para repartir la cuenta cuando el equipo va a consumir las poldinas (multas). Los montos se muestran en pesos dominicanos (RD$).

## Qué hace

- **Personas**: agregas a cada integrante. La cantidad de poldinas de cada uno se calcula sola, a partir de las multas que le registres (no la escribes a mano).
- **Registrar poldina**: cada multa queda guardada con persona, fecha, hora, motivo (Taza sucia o fuera de lugar, Luz encendida, Aire encendido, Dejó el carnet, u Otro) y una descripción libre opcional.
- **Factura**:
  - Puedes **subir una foto o PDF** de la factura y la app intenta leerla automáticamente, separando lo que es para compartir (pizzas, comida grande) de lo individual (bebidas, postres personales). *Esto requiere configurar una `ANTHROPIC_API_KEY` en Vercel — ver más abajo.*
  - Sin esa configuración, puedes **pegar el texto de la factura** (copiado o escrito a mano) y un lector de texto (sin IA, gratis, siempre disponible) reconoce líneas como `2 Pizza mediana 24000` o `Gaseosa 3000`.
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
