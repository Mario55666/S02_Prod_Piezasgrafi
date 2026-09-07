# Superprompt — Preflight & QC Studio

Prompt único y completo para reconstruir `index.html` desde cero.
Copiar todo el contenido a partir de la línea siguiente.

---

## ROL

Eres **ingeniero de preprensa** y **desarrollador front-end senior** a la vez. Escribes JavaScript de producción, sin dependencias innecesarias, y conoces ISO 12647-2, PDF/X, gestión de color ICC y los formatos de archivo gráficos a nivel de byte.

No eres un generador de maquetas. Todo lo que la aplicación afirme sobre un archivo tiene que estar **medido en el archivo**, no supuesto. Cuando algo no se pueda medir, la aplicación debe decirlo con esas palabras en la interfaz.

## ENCARGO

Construye **una aplicación web de un solo archivo** (`index.html`) para control de calidad de preprensa industrial, de uso didáctico en un instituto de diseño. HTML, CSS y JavaScript en el mismo archivo. Se abre con doble clic, sin servidor ni instalación. Ningún archivo del usuario sale del navegador.

**Entrega el archivo completo. Sin truncar, sin `// resto del código aquí`, sin marcadores de posición.**

## IDENTIDAD INSTITUCIONAL

- Instituto de Educación Superior Pública «Diseño y Comunicación»
- Docente: Mg. Mario Quiroz
- Curso: Unidad didáctica de Producción y publicación de piezas gráficas · 2026 · Semana 1
- Logotipo institucional embebido como **PNG en base64** (no vectorizado): versión blanca en cabecera y pie sobre fondo oscuro, versión a color en la ficha técnica impresa sobre papel blanco.
- Idioma de toda la interfaz: **español**, con acentuación correcta.

## RESTRICCIONES TÉCNICAS

Cuatro librerías desde CDN, nada más:

```
https://cdn.tailwindcss.com
https://cdnjs.cloudflare.com/ajax/libs/pdf.js/3.4.120/pdf.min.js
https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js
https://cdnjs.cloudflare.com/ajax/libs/animejs/3.2.2/anime.min.js
```

Todo lo demás se escribe a mano: parsers de cabecera, descompresores, motor de reglas, medición de tinta y extracción de contornos. JavaScript vanilla, `"use strict"`, sin módulos ES, sin bundler.

Si Tailwind no carga, la página debe seguir siendo usable con una hoja de respaldo mínima y un aviso visible. Detectar con `window.tailwind`.

---

## 1 · DESTINOS DE PRODUCCIÓN

Exactamente dos. **No incluyas un destino digital ni de redes sociales.**

### Impresión Offset (Empaques) — ISO 12647-2 / PDF/X-1a

- Resolución mínima 300 ppi al tamaño real de reproducción; ideal 350 ppi para trama de 175 lpi; aviso entre 250 y 300
- Modo de color: CMYK y/o tintas planas. Sin RGB, sin Lab
- Perfil / OutputIntent: ISO Coated v2 · FOGRA39
- Límite de tinta (TAC): 300 % – 330 %
- Formato: PDF/X-1a:2001 (ISO 15930-1), fuentes incrustadas, transparencias aplanadas, página única
- Geometría: BleedBox ≥ TrimBox + 3 mm por lado; zona de seguridad 3–5 mm interior
- Negros: texto y códigos de barras C0 M0 Y0 K100; masas C60 M40 Y40 K100
- Tipografía ≥ 6 pt; grosor de línea ≥ 0,25 pt
- Peso máximo orientativo: 300 MB

### Personalización Textil (Estampados) — DTG / Sublimación / Serigrafía

- Lienzo base 25 × 30 cm sobre prenda
- Resolución mínima 150 ppi al tamaño real (≥ 1476 × 1771 px); 300 ppi para degradados; aviso bajo 120 ppi
- Modo de color: CMYK o tintas planas (Pantone+ Solid Coated)
- Perfil: ICC calibrado del taller (p. ej. Epson_F170_Opaque)
- Formato: vector puro (AI/EPS/PDF) o ráster de alta densidad (TIFF/PNG-32 con canal alfa)
- Sublimación: poliéster ≥ 80 % blanco. DTG: algodón perchado + pretratamiento + base blanca TiO₂ en prendas oscuras. Serigrafía: positivos con líneas ≥ 0,5 pt y texto ≥ 6 pt
- Peso máximo orientativo: 150 MB

---

## 2 · LECTURA DE ARCHIVOS

El formato se identifica **por firma binaria, nunca por la extensión**. La extensión sólo desempata RAW.

### Parsers de cabecera escritos a mano

**JPEG** — recorrido marcador a marcador: SOF (número de componentes ⇒ CMYK real frente a RGB), profundidad, progresivo; APP0 JFIF (densidad y unidades); APP1 EXIF (XResolution, YResolution, ResolutionUnit); APP2 ICC_PROFILE **concatenando todos los segmentos**; APP14 Adobe (transform).

**PNG** — chunk a chunk: IHDR, pHYs (píxeles por metro → ppi), iCCP (nombre del perfil), sRGB, gAMA, tRNS. Canal alfa por colorType 4/6 o tRNS.

**WebP** — RIFF: VP8X (banderas de alfa, ICC y animación, dimensiones de 24 bits), VP8, VP8L, ALPH, ICCP.

**BMP** — cabecera DIB: dimensiones, bits por píxel, compresión, píxeles por metro.

**GIF** — descriptor lógico de pantalla y bits de paleta.

**PSD** — cabecera 8BPS de 26 bytes: canales, dimensiones, profundidad y modo de color (0 mapa de bits, 1 grises, 2 indexado, 3 RGB, 4 CMYK, 7 multicanal, 8 duotono, 9 Lab).

**SVG** — `viewBox`, atributos `width`/`height`, presencia de `<text>` e imágenes incrustadas.

**ICC** — cabecera completa: clase de dispositivo, espacio, PCS, versión, fecha, plataforma, CMM, intento por defecto; tabla de etiquetas; descripción legible desde `desc` (ICC v2), `mluc` (v4) y `text`.

### TIFF — lector completo

Recorre el IFD y extrae: dimensiones, BitsPerSample, SamplesPerPixel, PhotometricInterpretation, Compression, PlanarConfiguration, Predictor, RowsPerStrip, StripOffsets, StripByteCounts, ExtraSamples, SampleFormat, ColorMap, InkSet, NumberOfInks, InkNames, XResolution, YResolution, ResolutionUnit, Software, DateTime, ICC incrustado (tag 34675), XMP y recursos de Photoshop. Detecta BigTIFF y lo declara no soportado.

Implementa los descompresores: **sin compresión, LZW (variante TIFF con incremento anticipado de código a 511/1023/2047), PackBits y Deflate**. Aplica el predictor horizontal cuando corresponda.

Decodifica **franja a franja muestreando hacia un lienzo reducido**, para que un TIFF de 200 megapíxeles no agote la memoria. Convierte según la interpretación fotométrica: CMYK separado, RGB, escala de grises directa e invertida, y paleta indexada.

Cuando el TIFF es CMYK, **guarda los canales de tinta reales**. Ésa es la diferencia entre medir la cobertura y estimarla.

Si la estructura no es decodificable (mosaicos, planar separado, 32 bits, JPEG interno), lee todos los metadatos igualmente y explica en pantalla qué falta y por qué.

### PDF — con PDF.js más escaneo binario directo

De PDF.js: número de páginas, metadatos, viewport, contenido de texto con transformaciones, lista de operadores y render.

Del escaneo binario del documento (lo que PDF.js no expone): TrimBox, BleedBox, MediaBox, CropBox, ArtBox, `GTS_PDFXVersion`, OutputIntent y su condición, `/FontFile`, `/BaseFont`, `/Separation` (tintas planas), DeviceRGB, DeviceCMYK, DeviceN, Lab, ICCBased, transparencia viva y SMask, sobreimpresión, contenido opcional, JavaScript, cifrado y recuento de imágenes. Si el documento usa `/ObjStm`, adviértelo: la lectura puede ser parcial.

**Resolución efectiva de cada imagen colocada.** Recorre la lista de operadores llevando la matriz de transformación (`save`, `restore`, `transform`) y, en cada `paintImageXObject`, `paintImageMaskXObject` y `paintInlineImageXObject`, calcula el tamaño de colocación como la norma de las columnas de la CTM. La resolución efectiva es `píxeles × 72 ÷ puntos de colocación`. Es el mismo cálculo del Preflight de Acrobat. Distingue máscaras de 1 bit, que exigen 600–1200 ppi, de las imágenes de tono continuo.

**Contornos vectoriales.** Recorre `constructPath` aplicando la CTM y aplanando `moveTo`, `lineTo`, `curveTo`, `curveTo2`, `curveTo3`, `rectangle` y `closePath`. Conserva sólo los trazados y elige como **troquel** el de mayor área que no coincida con el TrimBox.

**Capa de texto real.** De `getTextContent`, guarda para cada bloque su caja en coordenadas de página y su cuerpo en puntos, derivado de la matriz.

---

## 3 · MOTOR DE REGLAS

### Checklist obligatorio de seis campos

1. Dimensiones finales (px / mm)
2. Resolución efectiva (ppi)
3. Modo de color (CMYK / RGB / tinta plana)
4. Perfil ICC / OutputIntent
5. Formato de exportación
6. Peso del archivo

Cada comprobación devuelve: clave, etiqueta, estado (`pass` / `fail` / `warn` / `info`), valor leído del archivo, valor exigido por la norma, consejo accionable y clave de glosario.

**El consejo tiene que ser accionable.** No «resolución insuficiente», sino «tamaño máximo reproducible con calidad: 24,8 × 16,5 cm». No «convierte a CMYK», sino «convierte con ISO Coated v2 (FOGRA39), colorimétrico relativo + compensación de punto negro».

### Comprobaciones por destino

Hasta 25 según el archivo: sangrado declarado, sangrado medido, demasías, página única, fuentes incrustadas, transparencias aplanadas, cuerpo mínimo de texto, tintas planas, sobreimpresión, cobertura de tinta, negros neutros frente a compuestos, gama CMYK, máscaras de línea, troquel detectado, canal alfa, halo de recorte, área de estampado, densidad para degradados, definición de borde y naturaleza del arte.

### Advertencias destacadas

Dos mensajes en bloque rojo, aparte del checklist:

> **ADVERTENCIA DE PREPRENSA:** Resolución efectiva insuficiente (X ppi). Riesgo de pixelación en planchas de trama fina (175 lpi).

> **ERROR DE MODO DE COLOR:** El archivo está en RGB: provocará conversión destructiva en el RIP.

En PDF, la advertencia de resolución usa la peor imagen colocada dentro del arte.

### Verificación manual del operador

Ocho puntos por destino, con casilla y registro en la ficha técnica. Offset: fuentes en curvas, transparencias aplanadas, troquel en tinta plana y sobreimpresión, marcas de corte y registro, negros de texto en 100 % K, sangrado verificado, zona de seguridad, OutputIntent declarado. Textil: fondo transparente limpio, base blanca TiO₂, poliéster ≥ 80 %, pretratamiento, positivos de serigrafía, colores Pantone confirmados, prueba de lavado, área de estampado verificada.

### Veredicto

`No apto` con número de bloqueos · `Apto con reservas` con número de observaciones · `Apto para producción`.

---

## 4 · COBERTURA DE TINTA

Reproduce la lectura de **Previsualización de salida → Cobertura de tinta** de Acrobat.

Construye un mapa de canales de tinta de toda la imagen. Si el archivo trae canales reales (TIFF CMYK), úsalos. Si no, separa con este modelo GCR ligero y **declara en la interfaz que es una estimación**:

```
c0 = 1 - R/255,  m0 = 1 - G/255,  y0 = 1 - B/255
kMin = min(c0, m0, y0)
k    = kMin^1.6 × 0.95        // negro esqueleto no lineal
ucr  = k × 0.6                // remoción bajo color
c = clamp(c0 - ucr), m = clamp(m0 - ucr), y = clamp(y0 - ucr)
TAC = (c + m + y + k) × 100
```

**Umbral regulable de 160 a 400 %**, con deslizador, campo numérico y botón «Norma» que vuelve al límite del sustrato activo. Tres modos:

- **Sobre el límite** — arte desaturado en gris y resalte macizo rojo donde se supera el umbral, ámbar en los 25 puntos previos
- **Densidad** — rampa continua azul → verde → amarillo → rojo sobre el total de cobertura
- **Separaciones** — las cuatro planchas en cuadrícula 2×2, cada una entintada con su color de proceso y rotulada

Añade un **histograma** de distribución de cobertura con la línea del umbral, y una lectura de área sobre el límite, máximo, medio y origen del dato (medido o estimado).

---

## 5 · TROQUEL, SANGRADO Y DEMASÍAS

En la superposición de geometría, sobre un PDF, dibuja:

- Todos los contornos vectoriales trazados, en índigo tenue
- El **troquel** detectado, en cian grueso y rotulado
- TrimBox en azul, BleedBox en rojo discontinuo, zona de seguridad en ámbar
- El **borde real de la tinta**, medido sobre el render buscando el primer píxel no blanco por cada lado

Y calcula dos cosas distintas que suelen confundirse:

- **Sangrado medido en el arte** — cuánta tinta hay realmente más allá del TrimBox, lado por lado. Puede ser insuficiente aunque el BleedBox esté correctamente declarado: el error clásico es declarar la caja sin extender el fondo.
- **Demasías** — arte que rebasa el BleedBox, que en el pliego de montaje invade la pieza contigua y en el troquelado se recorta sin control.

Señala la demasía con **trama diagonal magenta sobre fondo oscuro**, nunca con un relleno translúcido: un rojo semitransparente desaparece sobre arte rojo.

---

## 6 · VISOR 3D DE ORDEN DE APILACIÓN

Descompone el arte en las capas en que se aplica y apílalas en el orden de impresión:

```
sustrato → base blanca TiO₂ → masas de color → imagen ráster → vectores → texto → troquel
```

Cada capa se **deriva del archivo real**: las masas de color por cuantización de baja frecuencia, los vectores por detección de bordes Sobel, el detalle fino por diferencia de frecuencias, la base blanca desde el canal alfa, el troquel desde las cajas y los contornos. En PDF, la capa de texto son las **cajas de texto reales**, con los bloques por debajo del cuerpo mínimo pintados en rojo.

Cada capa hereda las incidencias del preflight que le corresponden: la etiqueta flotante muestra el número de errores y la lista lateral marca error, aviso o correcto. Al seleccionar una capa se aísla y se muestran sus incidencias con el mismo formato del checklist.

Escena Three.js: suelo, retícula, luz ambiental y foco con sombras, grupo contenedor. Planos horizontales con textura de lienzo y material básico (sin iluminar) para que el color se lea fiel. Órbita con arrastre, zoom con rueda, separación regulable, vistas isométrica y compuesta, giro automático de 40 s con anime.js, modo contornos que sustituye las texturas por sus mapas de bordes, y captura PNG.

---

## 7 · CATÁLOGOS DE REFERENCIA

### Sustratos — 13 entradas

Cada una con perfil ICC de destino, archivo del perfil, límite de tinta, lineatura, ganancia de punto, preajuste PDF recomendado, destino asociado y una nota de producción.

| Sustrato | Perfil ICC | TAC | LPI | Preset |
|---|---|---|---|---|
| Cartulina estucada — empaque | ISO Coated v2 (ECI) / FOGRA39 | 330 % | 175 | X-1a |
| Cartulina estucada — tinta limitada | ISOcoated_v2_300_eci | 300 % | 175 | X-1a |
| Cartón compacto / microcorrugado | APTEC PC10 CardBoard 2023 | 300 % | 150 | X-1a |
| Estucado moderno con OBA | PSOcoated_v3 (FOGRA51) | 300 % | 175 | X-4 |
| Estucado GRACoL | Coated GRACoL 2006 | 320 % | 175 | X-1a |
| Estucado Japan Color | Japan Color 2001 Coated | 320 % | 175 | X-1a |
| Papel offset no estucado | PSO Uncoated / Euroscale Uncoated | 300 % | 133 | X-1a |
| Papel prensa (coldset) | ISOnewspaper 26v4 | 240 % | 100 | X-1a |
| Flexo sobre corrugado | perfil del proveedor | 240 % | 85 | X-4 |
| DTG sobre algodón | perfil calibrado del equipo | 260 % | — | ráster |
| Sublimación sobre poliéster | perfil del papel transfer | 240 % | — | ráster |
| Serigrafía textil | Pantone+ Solid Coated | — | 55 | ráster |
| Gran formato / inkjet | perfil del RIP + sustrato | 280 % | — | X-4 |

**Al seleccionar un sustrato, el motor adopta su límite de tinta y su perfil esperado**, y el mapa de cobertura se recalcula. Un botón devuelve los valores de la norma. La ficha técnica nunca debe declarar un sustrato que no esté vigente en las reglas.

### Preajustes PDF — 4

PDF/X-1a:2001 (ISO 15930-1), PDF/X-4:2010 (ISO 15930-7), PDF/X-3:2003 (ISO 15930-3) y ráster de alta densidad. Cada uno con sus ajustes de exportación detallados en ventana emergente.

### Formatos de imagen — 10

Tabla con descripción, uso principal, profundidad de color, compresión y alcance real en la aplicación. La fila del archivo cargado se resalta.

| Formato | Descripción | Uso principal | Profundidad | Compresión |
|---|---|---|---|---|
| JPEG | Compresión con pérdida para imágenes fotográficas | Fotografía digital, diseño gráfico, web | 8 bits por canal | Con pérdida |
| PNG | Compresión sin pérdida, áreas transparentes o fondos planos | Gráficos de alta calidad, web, iconos, logotipos | 8 bits por canal | Sin pérdida |
| GIF | Compresión con pérdida limitada, principalmente animaciones | Gráficos animados, diseño web | 8 bits por canal | Con pérdida limitada |
| TIFF | Sin pérdida, para impresión e intercambio de archivos | Publicación, impresión, diseño gráfico | 8, 16 o 32 bits por canal | Sin pérdida |
| RAW | Sin procesar, todos los datos de la cámara | Fotografía profesional, edición avanzada | Según el sensor | Sin pérdida |
| BMP | Mapa de bits de Windows | Diseño gráfico, impresión | 1, 4, 8, 16, 24, 32 bits por píxel | Sin pérdida |
| PSD | Adobe Photoshop, admite capas | Edición de imágenes, diseño, publicidad | 8, 16 o 32 bits por canal | Sin pérdida |
| SVG | Vectorial para gráficos escalables | Gráficos escalables, diseño web | N/A | Sin pérdida |
| HEIF | Alta eficiencia para imágenes de alta calidad | Fotografía digital, móviles y web | Según la imagen | Con o sin pérdida |
| WebP | Formato de Google con y sin pérdida | Fotografía digital, web, carga rápida | 8 bits por canal | Con o sin pérdida |

### Resolución por sustrato — 8

Tabla de resolución de entrada (PPI), salida digital (DPI), salida impresa (LPI) y modo mapa de bits. Una columna final califica el archivo cargado: Óptimo, Apto, Al límite o Insuficiente, con el ancho máximo reproducible.

| Sustrato | LPI | PPI | DPI | Mapa de bits / B&N |
|---|---|---|---|---|
| Periódico (newsprint) | 75 – 85 | 112 · 120 · 185 · 200 | 1200 | 600 – 1200 |
| Bond (uncoated) | 85 – 110 | 200 – 220 | 1200 – 2400 | 600 – 1200 |
| Folcote (coated) | 133 | 220 – 266 | 2400 | 600 – 1200 |
| Opalina (uncoated) | 133 | 220 – 266 | 1200 | 600 – 1200 |
| Kimberly (uncoated) | 133 | 220 – 266 | 1200 | 600 – 1200 |
| Couché (coated) | 150 – 175 | 266 · 300 · 350 | 2400 – 3600 | 600 – 1200 |
| Fotográfico (coated) | 150 – 175 | 266 · 300 · 350 | 2400 – 3600 | 600 – 1200 |
| Serigrafía | 60 · 70 · 85 | 200 – 300 | 600 | 600 – 1200 |

En PDF, la calificación usa la **peor imagen colocada dentro del arte**, no el documento.

### Gigantografía — 6 bandas

La exigencia baja al crecer la pieza porque crece la distancia de observación. La banda se selecciona automáticamente por el lado mayor del tamaño declarado.

| Tamaño aproximado | Resolución orientativa a tamaño final |
|---|---:|
| Hasta 1 × 1 m | 100 – 150 PPI |
| Hasta 3 × 3 m | 72 – 100 PPI |
| Hasta 5 × 5 m | 50 – 72 PPI |
| Hasta 10 × 10 m | 30 – 50 PPI |
| Más de 10 × 10 m | 20 – 40 PPI |
| Vallas / billboards de gran escala | 15 – 30 PPI |

La fila de vallas queda como referencia y no se auto-selecciona: depende de la distancia, no sólo del tamaño.

---

## 8 · PERFILES ICC DEL TALLER

Zona de arrastre para `.icc` / `.icm`, con carga múltiple. Lee y muestra clase de dispositivo, espacio, PCS, versión, fecha de creación, intento por defecto, fabricante, modelo, plataforma, CMM, presencia de tablas A2B/B2A y lista de etiquetas.

Los perfiles de **salida CMYK se marcan como destino automáticamente** y pasan a validar el campo «Perfil ICC» del checklist. La biblioteca persiste en `localStorage` guardando **sólo los metadatos**, nunca el archivo, y toda lectura y escritura va dentro de `try/catch`.

**Cuidado con el emparejamiento de nombres:** no uses tokens genéricos como `ICC` o `COATED` en la lista de perfiles esperados. Harían coincidir casi cualquier descripción y producirían aprobados falsos. Usa nombres específicos y deja que el operador marque su propio perfil.

---

## 9 · PREAJUSTES .joboptions DE ADOBE

Un `.joboptions` es un diccionario PostScript `<< /Clave valor >>` en texto plano o UTF-16. Auditarlo **antes** de exportar evita descubrir el problema con el PDF ya generado.

Extrae los pares clave/valor, resolviendo booleanos, números, nombres `/Name`, cadenas `( )` con paréntesis en octal, arreglos `[ ]` y la descripción multilingüe.

Audita contra la norma del destino y el sustrato activos: compatibilidad (PDF/X-1a exige 1.3), transparencia permitida, estrategia de conversión de color, perfil de la intención de salida, verificación PDF/X, generar sólo si cumple, desplazamiento BleedBox/TrimBox, tolerar falta de TrimBox, incrustación de fuentes, resolución de color, grises y mapa de bits, umbral de submuestreo, sobreimpresión y funciones de transferencia.

Muestra el resultado con el mismo formato de fila del checklist, y los ajustes leídos en un desplegable.

---

## 10 · INTERFAZ

**Estética:** modo oscuro, paleta zinc/slate, bordes rectos sin redondeos, jerarquía tipográfica clara, densidad alta pero legible. Nada de degradados decorativos ni tarjetas flotantes.

**Portada:** título a gran escala con la palabra «Studio» en degradado, fondo de retícula con máscara radial, halo suave y grano SVG. Debajo, cuatro indicadores en vivo: destino activo, archivo, sustrato y dictamen.

**Barra fija** con logotipo, anclas de sección, botón de **Glosario**, selector de destino y píldora de veredicto con color según el estado.

**Cuatro secciones:** Preflight · Catálogo de perfiles · Formatos y resolución · Visor 3D de capas.

**Panel de preflight en tres columnas:**
1. Destino de producción · tamaño de reproducción con cálculo de ppi en vivo · control de flujo con IA · emisión del informe
2. Previsualización con superposiciones · dashboard de metadatos · análisis forense de píxel
3. Alertas · checklist obligatorio · especificaciones del destino · verificación manual · norma aplicada

**Zona de carga:** arrastrar, pegar con `Ctrl+V` o seleccionar.

**Cálculo de resolución efectiva:**

```
PPI = píxeles de ancho ÷ (ancho físico en cm ÷ 2,54)
```

Con selector de unidad (cm, pulgadas, mm), bloqueo de proporción, tamaño sugerido según el archivo y el destino, e indicador con barra de color.

**Control de flujo con IA:** casilla «¿Este diseño contiene gráficos generados con Inteligencia Artificial?» que despliega dos listas —limitación nativa detectada y acción correctiva aplicada— más notas del operador. Se integra en la ficha técnica como trazabilidad.

**Ayuda contextual:** botón de interrogación junto a cada concepto, que abre una ficha explicativa. Y un botón de **Glosario** que abre las 14 fichas completas con buscador.

**Gráfico instructivo** para el panel de especificaciones de offset: un SVG con cuatro cuadrantes —anatomía del pliego con MediaBox, BleedBox, TrimBox, seguridad y troquel con solapas; barras de carga de tinta comparando negro rico de 240 % contra negro de registro de 400 % con la línea del límite; roseta de trama de 175 lpi con la regla ppi ≈ 2 × lpi; y el negro de texto en 100 % K frente al compuesto con registro desviado— y siete llamadas numeradas explicadas.

---

## 11 · SALIDAS

**Ficha Técnica en PDF** por `window.print()` con hoja de estilo A4. Siete bloques: identificación del trabajo (OT, cliente, operador, fecha, destino, sustrato y perfil), metadatos del archivo, checklist obligatorio, especificaciones del destino, verificación manual, declaración de flujo con IA y norma aplicada. Con logotipo institucional **a color**, línea de docente y curso, aviso técnico y casillas de firma para operador, producción y cliente.

**Diagnóstico JSON** descargable con metadatos completos, análisis de píxel, los tres checklists, sustrato, formato identificado, perfiles del taller, calificación por sustrato y banda de gigantografía.

**Captura PNG** de la vista actual del visor 3D.

---

## 12 · DETALLES DE IMPLEMENTACIÓN QUE EVITAN FALLOS REALES

Éstos no son opcionales. Cada uno corresponde a un fallo que aparece si se omite.

1. **PDF.js con `intent: 'print'`.** El render con `intent: 'display'` usa `requestAnimationFrame` y **no avanza nunca si la pestaña está en segundo plano**, dejando el preflight colgado. Con `intent: 'print'` es determinista. Añade además una guarda de 20 s que cancele la tarea y continúe sin análisis de píxel.

2. **Tolerancia de 0,5 ppi** en la comprobación de resolución. Un archivo exactamente a 300 ppi da 299,92 por el redondeo del tamaño físico introducido a mano, y marcaría aviso sin motivo.

3. **Lienzo 3D con medida provisional.** Si el contenedor arranca sin ancho (pestaña oculta, contenedor colapsado, layout sin resolver), el lienzo se queda en 0 px para siempre. Arranca con una medida de respaldo y corrígela en el primer fotograma.

4. **SVG con tipo MIME explícito.** El navegador sólo rasteriza un SVG si el blob declara `image/svg+xml`. Un `File` con tipo vacío falla silenciosamente.

5. **`preserveDrawingBuffer: true`** en el renderer de Three.js, o `toDataURL` devuelve un lienzo vacío.

6. **Escapa el NUL como ` `** en las expresiones regulares. Un byte nulo literal es válido en JavaScript pero convierte el archivo en binario para las herramientas.

7. **Trama, no transparencia,** para señalar zonas sobre el arte. Un relleno translúcido desaparece sobre arte del mismo color.

8. **`localStorage` siempre dentro de `try/catch`.** Puede lanzar en ventanas privadas o con almacenamiento bloqueado.

9. **Guardas en todos los bucles de parsing.** Un contador máximo de iteraciones evita que un archivo malformado congele la pestaña.

10. **Sin código muerto.** Si una función se sustituye por otra versión, elimina la anterior.

---

## 13 · HONESTIDAD TÉCNICA

La aplicación debe declarar sus límites **en la propia interfaz**, no sólo en la documentación:

- La cobertura de tinta y el modo de color derivados de píxel son heurísticos, calculados sin motor de gestión de color. No sustituyen una separación con perfil ICC ni la verificación en RIP. Sólo con canales de tinta reales la medición es directa, y la interfaz debe distinguir un caso del otro con las palabras «medido» y «estimado».
- En PDF 1.5+ con flujos de objetos comprimidos, la lectura de cajas, fuentes y separaciones puede ser parcial. Avísalo cuando se detecte `/ObjStm`.
- La descomposición en capas del visor 3D es una reconstrucción de diagnóstico por análisis de imagen, no la separación interna del documento. Dilo bajo el visor.
- El troquel se identifica por geometría: hay que confirmar que esté en su propia tinta plana y marcado como no imprimible.
- Enumera los casos no soportados con la acción correctiva: AI y EPS exportar a PDF; RAW revelar a TIFF; PSD aplanar y exportar; TIFF en mosaicos o de 32 bits sólo metadatos; HEIF según el navegador.

Prefiere un `warn` honesto a un `pass` cómodo. Nunca declares conforme algo que no se ha podido comprobar.

---

## 14 · CALIDAD DEL CÓDIGO

- Español en toda la interfaz, con acentuación correcta. Comentarios en español, explicando **por qué**, no qué.
- Organiza el archivo en bloques numerados con cabecera de comentario.
- Captura de errores en todo parser: un archivo malformado nunca debe romper la aplicación, sólo producir un aviso claro.
- Los mensajes de error dicen qué pasó y qué hacer.
- Comprueba que el JavaScript resultante pasa un análisis de sintaxis antes de entregar.

## ENTREGA

El archivo `index.html` completo, funcional al abrirlo con doble clic.
