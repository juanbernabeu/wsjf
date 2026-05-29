# WSJF Calculator

Calculadora de priorización **Weighted Shortest Job First (WSJF)** para equipos de tecnología en entornos ágiles. Diseñada para sesiones de PI Planning y scoring trimestral de iniciativas.

> **Sin instalación — abrí `index.html` en el browser.**

---

## ¿Qué es WSJF?

WSJF es el framework de priorización de SAFe (Scaled Agile). La premisa: entre dos trabajos de igual valor, hacé primero el más corto. Así liberás capacidad antes, entregás valor más rápido y reducís el riesgo de entrega parcial.

```
Score WSJF = ( I + U + R ) ÷ E
```

---

## Funcionalidades

### Formulario de scoring

- **Nombre de equipo** persistente — se mantiene entre iniciativas y se incluye en el export.
- **Nombre de la iniciativa** — campo obligatorio (la herramienta valida antes de agregar).
- **OKR / KR asociado** — opcional pero recomendado; obliga a vincular la iniciativa a un resultado concreto.

### Las 4 dimensiones (escala 1 → 5)

| Dim | Nombre | Descripción |
|-----|--------|-------------|
| **I** | Impacto en OKR | ¿Cuánto mueve la aguja en los KRs de empresa este Q? |
| **U** | Urgencia / Costo de demora | ¿Qué perdemos si lo hacemos el Q que viene? |
| **R** | Reducción de riesgo | ¿Qué riesgo técnico u operacional elimina? |
| **E** | Esfuerzo *(divisor)* | ¿Cuánto trabajo requiere? Más alto = peor score. |

Cada dimensión tiene:
- **Slider** interactivo (1 a 5)
- **Rúbrica** — 5 etiquetas clicables con descripción para eliminar ambigüedad en el debate

### Selector de camiseta (T-shirt sizing)

Mapeo automático talla → E + autofill del campo eng-months con el mínimo de la banda:

| Talla | E | Eng-months | Autofill |
|-------|---|------------|----------|
| XS    | 1 | < 1        | 0.5      |
| S     | 2 | 1 – 3      | 1        |
| M     | 3 | 3 – 6      | 3        |
| L     | 4 | 6 – 12     | 6        |
| XL    | 5 | > 12 ⚠     | 12       |

> Tocar el talle sobrescribe el campo eng-months con el mínimo del rango. Si querés un valor más fino dentro de la banda (ej. M=4.5), editás el número manualmente después de elegir el talle o usás la calculadora de eng-months.

### Calculadora de eng-months

Fórmula incorporada: `devs × semanas ÷ 4 × factor histórico`

- Calcula el esfuerzo ajustado por la velocidad real del equipo.
- **Factor histórico** = promedio de (tiempo real ÷ tiempo estimado) de las últimas 4–5 iniciativas. Si no hay historial: 1.5× por defecto.

### Score en tiempo real

- Muestra el WSJF actualizado al instante con cada cambio de slider.
- Fórmula desglosada: `(I + U + R) ÷ E`
- **Veredictos automáticos:**

| Rango | Veredicto |
|-------|-----------|
| > 4.0 | 🟢 COMMIT |
| > 2.5 | 🟡 CONSIDERAR |
| > 1.5 | 🔵 PARKING |
| ≤ 1.5 | 🔴 STOP |

---

### Stack rank

- Lista ordenada automáticamente de **mayor a menor score WSJF**.
- Muestra ranking, score, dimensiones, camiseta, eng-months y veredicto por iniciativa.
- **Edición inline** — modal para modificar nombre, KR y dimensiones de cualquier iniciativa ya agregada.
- **Eliminación** individual por iniciativa.
- **Limpiar todo** con confirmación.

### Agrupar por OKR/KR

Toggle "Lista / Por OKR/KR" arriba de la lista. En modo agrupado:

- Iniciativas agrupadas por el OKR/KR cargado, ordenadas por el score máximo de cada grupo.
- Cada cabecera muestra cuántas iniciativas tiene, eng-months totales y score promedio.
- Las iniciativas sin OKR/KR caen en un grupo aparte al final.
- Útil para responder *"¿qué tenemos planeado para este KR?"* en una reunión.

### Tracker de capacidad del equipo

- Inputs: cantidad de devs, semanas del Q, % overhead.
- Cálculo automático: `devs × semanas ÷ 4 × (1 - overhead/100)`
- Barra de progreso que muestra qué % de la capacidad consumen las iniciativas comprometidas.
- Indicadores de alerta: amarillo al superar el 80%, rojo al superar el 100%.

### Estadísticas del stack

- Total de iniciativas
- Cantidad en COMMIT
- Cantidad en PARKING
- Score promedio del stack

---

### Exportar CSV

Descarga un archivo `wsjf-<equipo>.csv` compatible con **Google Sheets** y Excel.

Columnas exportadas:
```
Equipo | Ranking | Iniciativa | OKR / KR asociado | WSJF Score | I | U | R | E | Camiseta | Eng-months | Decisión
```

Para importar en Google Sheets: Archivo → Importar → seleccionar el CSV → separador: Coma.

### Importar CSV

Carga un archivo CSV con el mismo formato del export para recuperar un stack guardado o compartir entre sesiones.

- **Reemplazar** el stack actual, o **agregar** las iniciativas importadas al stack existente (a elección al momento de importar).
- Autocompletado del nombre de equipo desde el CSV si el campo está vacío.
- Validación de columnas y valores antes de cargar.

### Persistencia local

Todo el stack y el nombre de equipo se guardan automáticamente en **localStorage** del browser. Al reabrir la herramienta se restaura el estado anterior sin necesidad de reimportar.

---

### Reportes (tab dedicado)

Cuatro vistas que se calculan en vivo sobre el stack cargado. Útiles al final de la sesión de scoring para validar el plan antes de cerrarlo.

| Reporte                                  | Para qué sirve                                                                                                                                  |
|------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------|
| **Inversión por OKR/KR**                 | Barras horizontales con eng-months por KR. Detecta divergencia entre la narrativa de prioridades y dónde está realmente la capacidad.           |
| **Capacidad acumulada por ranking**      | Barra apilada con un segmento por iniciativa, coloreada por veredicto. Línea vertical marca la capacidad: lo que queda a la derecha no entra.   |
| **Distribución de scores por dimensión** | 4 histogramas (I, U, R, E) con la cuenta de iniciativas por valor 1-5. Detecta inflación de calibración (regla: >25% en valor 5 = recalibrar).  |
| **Balance por OKR/KR**                   | Tabla con # iniciativas, eng-months, % de capacidad, score promedio y conteo por veredicto. Vista ejecutiva para revisar con stakeholders.      |

Sin dependencias externas — todos los charts son SVG/CSS nativo.

---

### Guía y FAQ integrada

Vista con documentación interna que cubre:
- Qué es WSJF y cuándo usarlo
- Cómo calcular eng-months paso a paso
- Dudas frecuentes por dimensión
- Cómo organizar la sesión de scoring (quién define qué, cuánto tarda, cada cuánto hacerlo)

---

## Uso recomendado

1. **Preparación** (antes de la sesión): definir nombre + criterio de done de cada iniciativa.
2. **Sesión de scoring** (~2 h si está bien preparada): PM + Tech Lead puntúan cada dimensión con la rúbrica como árbitro.
3. **Exportar** el stack rank al finalizar → importar en Google Sheets para compartir.
4. **Mid-Q**: si aparece una urgencia, scorear la nueva iniciativa y comparar contra el último COMMIT del stack.

---

## Stack técnico

- HTML5 + CSS3 + JavaScript vanilla — sin dependencias ni bundler.
- [Lucide Icons](https://lucide.dev/) vía CDN.
- [Google Fonts](https://fonts.google.com/) — Plus Jakarta Sans + JetBrains Mono.
- `localStorage` para persistencia.

---

## Licencia

MIT
