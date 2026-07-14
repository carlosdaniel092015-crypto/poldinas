# Poldinas 🪙

App para repartir la cuenta cuando el equipo va a consumir las poldinas (multas).

**Qué hace**
- Registras quién tiene poldinas (multas) y quién no.
- Cargas la factura del consumo común (pizzas, etc.).
- Calcula automáticamente: las multas cubren el consumo común y, si **se pasan**, el exceso se reparte por partes iguales entre quienes **no tienen** multa.
- Los extras (bebidas, cosas individuales) los paga cada quien.
- Muestra cuánto debe pagar cada persona y **exporta a PDF y a Excel**.
- Los datos se guardan solos en tu navegador y puedes guardar/cargar eventos anteriores.

Cada poldina vale $1.000 por defecto (lo puedes cambiar arriba en "Valor poldina").

---

## Estructura

```
poldinas-app/
├── api/
│   └── index.py       # App Flask: interfaz + cálculo + PDF + Excel
├── requirements.txt   # Flask, fpdf2, openpyxl
├── vercel.json        # Enruta todo a la app Flask
└── README.md
```

---

## Subir a Vercel

### Opción A — desde GitHub (recomendada)
1. Crea un repositorio en GitHub y sube esta carpeta (`poldinas-app`).
2. Entra a https://vercel.com → **Add New… → Project**.
3. Importa el repositorio. Vercel detecta Python y lee `vercel.json` y `requirements.txt`.
4. Clic en **Deploy**. Listo: te da una URL pública.

### Opción B — con la CLI
```bash
npm i -g vercel
cd poldinas-app
vercel        # sigue las preguntas (deploy de prueba)
vercel --prod # cuando quieras publicarlo
```

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

- **Guardado:** los eventos se guardan en el navegador (localStorage), así que quedan en el dispositivo donde los creas. No necesitas base de datos ni configurar nada.
- **Compartir entre varios equipos/dispositivos:** si más adelante quieres que todos vean los mismos datos, se puede conectar Vercel KV o Vercel Postgres. Avísame y te agrego esa parte.
- **Cálculo:** lo hace el servidor en Python (`/api/calc`); si el servidor no responde, la interfaz calcula igual como respaldo.
