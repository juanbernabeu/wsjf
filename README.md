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
- **KR principal** — opcional pero recomendado; obliga a vincular la iniciativa a un resultado concreto. Suma eng-months en los reportes.
- **KR secundario** — opcional. Para cuando la iniciativa también beneficia a otro KR además del principal; aparece en el reporte de impactos secundarios sin contar eng-months (evita doble conteo).
- **Categoría** — `Run`, `Change evolutivo`, `Change estratégico` o `Auditoría`. Clasifica el tipo de trabajo (independiente del score) para los reportes de inversión/balance por categoría. Ver [Categorías](#categoría-de-la-iniciativa).

### Las 4 dimensiones (escala 1 → 5)

| Dim | Nombre | Descripción |
|-----|--------|-------------|
| **I** | Impacto en OKR | ¿Cuánto mueve la aguja en los KRs de empresa este Q? |
| **U** | Urgencia / Costo de demora | ¿Qué perdemos si lo hacemos el Q que viene? |
| **R** | Reducción de riesgo | ¿Qué riesgo técnico u operacional elimina? |
| **E** | Esfuerzo *(divisor)* | ¿Cuánto trabajo requiere? Más alto = peor score. Nunca se transforma por escala. |

Cada dimensión tiene:
- **Slider** interactivo (1 a 5)
- **Rúbrica** — 5 etiquetas clicables con descripción para eliminar ambigüedad en el debate

El **peso** con el que I/U/R entran a la fórmula depende de la escala activa del stack (lineal o Fibonacci) — ver [Escala del score](#escala-del-score--lineal-vs-fibonacci).

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
- Fórmula desglosada: `(I + U + R) ÷ E`, con I/U/R ya convertidos según la escala activa.
- **Veredictos automáticos** — los cortes se reescalan según la escala del stack (ver tabla abajo).

### Escala del score — Lineal vs Fibonacci

I/U/R se puntúan siempre 1 a 5 en el slider, pero el **peso** de cada nivel al calcular el score depende de la escala activa. E nunca se transforma, en ninguna escala.

| Escala | Peso de niveles 1‑5 | Numerador máximo |
|--------|----------------------|-------------------|
| **Lineal** (v1, legacy) | 1, 2, 3, 4, 5 | 15 |
| **Fibonacci** (v2, default) | 1, 2, 3, **5**, **8** | 24 |

Fibonacci separa más los niveles altos: la distancia entre 4 y 5 pasa a ser el doble que entre 1 y 2, para que una iniciativa realmente crítica se despegue del resto en vez de mezclarse con las que son solo "importantes".

**Veredictos automáticos** (los umbrales v2 son los v1 × 1.6, mismo numerador máximo):

| Veredicto | Lineal (v1) | Fibonacci (v2) |
|-----------|-------------|-----------------|
| 🟢 COMMIT | > 4.0 | > 6.4 |
| 🟡 CONSIDERAR | 2.5 – 4.0 | 4.0 – 6.4 |
| 🔵 PARKING | 1.5 – 2.5 | 2.4 – 4.0 |
| 🔴 STOP | ≤ 1.5 | ≤ 2.4 |

**Reglas clave:**

- La escala es una propiedad de **todo el stack**, nunca de una iniciativa individual — un stack no mezcla las dos escalas.
- Un stack nuevo, o recién vaciado con "Limpiar todo", arranca siempre en Fibonacci.
- Un link o CSV viejo (escala lineal) siempre carga y muestra los mismos números — abrirlo no lo migra ni lo mezcla con la escala que esté usando el resto del equipo.
- La migración v1 → v2 (botón/badge "Escala: Lineal (1‑5) — migrar a Fibonacci") es de **un solo sentido**: recalcula todo el stack y reordena el ranking; no hay vuelta atrás.
- Importar un CSV con otra escala al stack actual bloquea la opción de "agregar" (solo permite "reemplazar" o migrar primero) para no mezclar escalas.

---

### Categoría de la iniciativa

Clasifica el *tipo* de trabajo, independiente del score — para ver en qué se está yendo la capacidad del equipo, no para priorizar entre sí.

| Categoría | Qué es |
|-----------|--------|
| **Run** | Mantenimiento operativo, guardia, soporte. |
| **Change evolutivo** | Mejora incremental sobre algo que ya existe. |
| **Change estratégico** | Iniciativa transformacional, capacidad nueva alineada a un OKR grande. |
| **Auditoría** | Cumplimiento regulatorio, hallazgos de auditoría interna o externa. |

Alimenta los reportes de "Inversión por categoría" y "Balance por categoría".

---

### Stack rank

- Lista ordenada automáticamente de **mayor a menor score WSJF**.
- Muestra ranking, score, dimensiones, camiseta, eng-months, categoría y veredicto por iniciativa.
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
Equipo | Ranking | Iniciativa | KR principal | KR secundario | Categoria | WSJF Score | I | U | R | E | Camiseta | Eng-months | Decision | Escala
```

Para importar en Google Sheets: Archivo → Importar → seleccionar el CSV → separador: Coma.

### Importar CSV

Carga un archivo CSV con el mismo formato del export para recuperar un stack guardado o compartir entre sesiones. Detecta el formato por los headers (compatible con exports viejos, sin categoría/KR secundario/escala).

- **Reemplazar** el stack actual, o **agregar** las iniciativas importadas al stack existente (a elección al momento de importar).
- Autocompletado del nombre de equipo desde el CSV si el campo está vacío.
- Validación de columnas y valores antes de cargar.
- Si el CSV usa una escala distinta (lineal vs Fibonacci) a la del stack actual, **agregar** se bloquea — hay que reemplazar el stack o migrarlo primero (ver [Escala del score](#escala-del-score--lineal-vs-fibonacci)).

### Persistencia local

Todo el stack y el nombre de equipo se guardan automáticamente en **localStorage** del browser. Al reabrir la herramienta se restaura el estado anterior sin necesidad de reimportar.

---

### Reportes (tab dedicado)

Siete vistas que se calculan en vivo sobre el stack cargado. Útiles al final de la sesión de scoring para validar el plan antes de cerrarlo.

| Reporte                                  | Para qué sirve                                                                                                                                  |
|------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------|
| **Inversión por OKR/KR**                 | Barras horizontales con eng-months por KR. Detecta divergencia entre la narrativa de prioridades y dónde está realmente la capacidad.           |
| **Capacidad acumulada por ranking**      | Barra apilada con un segmento por iniciativa, coloreada por veredicto. Línea vertical marca la capacidad: lo que queda a la derecha no entra.   |
| **Distribución de scores por dimensión** | 4 histogramas (I, U, R, E) con la cuenta de iniciativas por valor 1-5 (por nivel crudo, no por peso de la escala). Detecta inflación de calibración (regla: >25% en valor 5 = recalibrar).  |
| **Inversión por categoría**              | Gráfico de torta + barras con eng-months por categoría (Run, Change evolutivo, Change estratégico, Auditoría).                                  |
| **Balance por OKR/KR**                   | Tabla con # iniciativas, eng-months, % de capacidad, score promedio y conteo por veredicto. Vista ejecutiva para revisar con stakeholders.      |
| **🔗 Impactos secundarios por KR**       | Iniciativas que benefician a un KR distinto del principal (campo KR secundario), sin contar eng-months. Muestra el multi-impacto.               |
| **Balance por categoría**                | La misma vista ejecutiva que "Balance por OKR/KR" pero agrupada por categoría en vez de por KR.                                                 |

Sin dependencias externas — todos los charts son SVG/CSS nativo.

---

### Guía y FAQ integrada

Vista con documentación interna que cubre:

- Qué es WSJF y cuándo usarlo
- Escala Fibonacci: qué es, por qué existe, cómo migrar y qué pasa con links/CSV de otra escala
- Qué significan las categorías y para qué sirve el KR secundario
- Cómo calcular eng-months paso a paso
- Dudas frecuentes por dimensión
- Cómo organizar la sesión de scoring (quién define qué, cuánto tarda, cada cuánto hacerlo)
- Qué hay en cada reporte del tab "Reportes"

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
