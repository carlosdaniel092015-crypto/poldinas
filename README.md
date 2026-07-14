# Poldinas 🪙

App para repartir la cuenta cuando el equipo va a consumir las poldinas (multas). Los montos se muestran en pesos dominicanos (RD$).

Esta versión está preparada para **Render.com**, que sí permite instalar las librerías necesarias para que la **lectura de fotos (OCR) funcione gratis**, sin necesitar ninguna API de pago.

## Qué hace

- **Personas**: agregas a cada integrante. La cantidad de poldinas de cada uno se calcula sola, a partir de las multas que le registres.
- **Registrar poldina**: cada multa queda guardada con persona, fecha, hora, motivo (Taza sucia o fuera de lugar, Luz encendida, Aire encendido, Dejó el carnet, u Otro) y una descripción libre opcional.
- **Factura**: subes una **foto** o un **PDF** y la app la lee automáticamente (gratis), separando lo común (comida para compartir) de lo individual (bebidas). También puedes **pegar el texto** o agregar los items a mano. Los items leídos quedan en una zona de revisión donde ajustas cantidad, precio, tipo y a quién asignar antes de confirmarlos.
- **Cálculo**: las multas cubren el consumo común. Si el consumo se pasa, la diferencia se divide entre TODAS las personas por igual. Los items individuales los paga cada quien. Detecta subtotal/total de la factura y agrega los impuestos como cargo común para que el total cuadre.
- **Exportar a PDF y Excel** con el resumen, el reparto por persona, la factura y el historial de multas.
- **Guardado** automático en el navegador, con historial de eventos.

---

## Estructura

```
poldinas-render/
├── app.py             # App Flask: interfaz + cálculo + lectura de factura (PDF/foto/texto) + PDF + Excel
├── requirements.txt   # Dependencias de Python
├── Dockerfile         # Instala las librerías de sistema que el OCR necesita
└── README.md
```

---

## Subir a Render (paso a paso)

### 1. Sube el proyecto a GitHub
Sube esta carpeta (`poldinas-render`) a un repositorio en GitHub, igual que hiciste antes.

### 2. Crea el servicio en Render
1. Entra a https://render.com y crea una cuenta (puedes entrar con tu cuenta de GitHub).
2. Clic en **New +** → **Web Service**.
3. Conecta tu repositorio de GitHub y selecciónalo.
4. Render debería detectar el **Dockerfile** automáticamente. Verifica que:
   - **Language / Runtime:** Docker
   - **Plan:** Free
5. Clic en **Create Web Service**.
6. Render construye la imagen (esto tarda varios minutos la primera vez porque instala el OCR y sus modelos). Cuando termine, te da una URL tipo `poldinas.onrender.com`.

Cada vez que subas cambios a GitHub, Render vuelve a desplegar solo.

---

## Cosas importantes que debes saber

- **El plan gratuito "se duerme":** si nadie usa la app por ~15 minutos, se apaga para ahorrar recursos. La siguiente vez que alguien entre, tarda entre 30 y 60 segundos en "despertar" (después va normal). Para un uso de equipo interno esto no suele ser problema.
- **La lectura de fotos ahora es gratis y funciona**, pero el OCR es menos preciso con fotos torcidas, borrosas o con poca luz. Para el mejor resultado: toma la foto derecha, de cerca, con buena luz. Si lee algo mal, siempre puedes corregirlo en la pantalla de revisión antes de confirmar, o usar "Pegar texto".
- **Siempre puedes revisar** lo que el OCR leyó antes de agregarlo: cantidad, precio, tipo (Común/Individual) y a quién se asigna cada bebida (eso el recibo no lo dice, lo pones tú).

---

## (Opcional) IA como respaldo para fotos difíciles

Si quieres máxima precisión con fotos complicadas, puedes activar además la lectura por IA (de pago). La app la usa automáticamente solo cuando el OCR gratis no logra leer nada útil.

1. Crea una API key en https://console.anthropic.com.
2. En Render: tu servicio → **Environment** → **Add Environment Variable**.
   - **Key:** `ANTHROPIC_API_KEY`
   - **Value:** tu clave (empieza con `sk-ant-...`)
3. Guarda; Render redepliega solo.

Si no configuras esto, no pasa nada: el OCR gratis sigue siendo el que lee las fotos.

---

## Probar en tu computador (opcional)

```bash
cd poldinas-render
pip install -r requirements.txt
python app.py
# abre http://localhost:5000
```
(En tu computador, para que el OCR funcione necesitarías tener las librerías del sistema; en Render eso lo resuelve el Dockerfile automáticamente.)
