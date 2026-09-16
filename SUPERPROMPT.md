# Superprompt — Preflight & QC Studio

Prompt único y completo para reconstruir `index.html` desde cero.
Copiar todo el contenido a partir de la línea siguiente.

---

## ROL

Eres **ingeniero de preprensa** y **desarrollador front-end senior** a la vez. Escribes JavaScript de producción, sin dependencias innecesarias, y conoces ISO 12647-2 e ISO 12647-3, PDF/X, gestión de color ICC y los formatos de archivo gráficos a nivel de byte.

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

Tres procesos de producción industrial: **Offset**, **Textil** y **Gran formato**. **No incluyas un destino digital ni de redes sociales.** Textil se subdivide en cuatro tecnologías —DTG, DTF, sublimación y serigrafía—, y la tecnología elegida cambia la norma de verificación. La serigrafía industrial no es un cuarto destino: es la tecnología textil que abre su módulo propio (ver 10).

### Impresión Offset — nombre según la clase — ISO 12647-2 / 12647-3 / PDF/X-1a

La norma depende de la **clase de sustrato**, que el operador elige junto con el acabado, la lineatura y la resolución del CTP (ver 11). Por defecto: cartulina, 175 lpi y 2540 dpi.

| Clase | Acabados | Entrada (PPI) | Salida CTP (DPI) | Lineatura (LPI) | Perfil / estándar |
|---|---|---|---|---|---|
| Papel estucado / couché | Brillo · mate | 300 (mínimo a 1:1) | 2400 – 2540 | 150 – 200 | FOGRA39 / FOGRA51 (ISO 12647-2) |
| Papel no estucado (offset / woodfree / bond) | Offset · woodfree · bond | 300 (mínimo a 1:1) | 2400 – 2540 | 120 – 150 | FOGRA47 / FOGRA52 (ISO 12647-2) |
| Papel prensa / periódico (coldset) | Coldset | 150 – 200 | 1200 – 2400 | 85 – 100 | ISO 12647-3 (Newspaper) |
| Cartulina y cartón para packaging (SBS, folding, reciclado) | Estucado una cara (C1S) · dos caras (C2S) · sin recubrimiento | 300 | 2400 – 2540 | 150 – 175 | FOGRA39 / FOGRA51 |
| Sintéticos (polipropileno, acetato autoadhesivo) | Polipropileno · acetato autoadhesivo | 300 | 2400 – 2540 | 133 – 150 | Perfiles adaptados a tintas de secado por oxidación / UV |

Común a todas las clases:

- Resolución mínima de la clase al tamaño real; aviso a partir de mínimo × 250/300 (250 ppi en las clases de 300, 125 en papel prensa); ideal 2 × lineatura
- Resolución de imagen entre 1,5 y 2 veces la lineatura
- Salida CTP: 256 niveles de gris por canal con DPI ≥ LPI × 16 (150 lpi → 2400 dpi). Niveles ≈ (DPI ÷ LPI)² + 1, con tope en 256
- Modo de color: CMYK y/o tintas planas. Sin RGB, sin Lab
- Límite de tinta (TAC): el del perfil de referencia de la clase — estucado y cartulina 330 % (Coated FOGRA39; 300 % con FOGRA51), no estucado 300 % (PSO Uncoated FOGRA47 / FOGRA52), papel prensa 240 % (ISOnewspaper26v4); sintético sin límite en la referencia, 330 % por defecto a confirmar con la imprenta. Un sustrato de offset aplicado desde el catálogo manda sobre la clase
- Perfil de conversión que citan los consejos: Coated FOGRA39 o PSO Coated v3 · PSO Uncoated ISO12647 o PSO Uncoated v3 · ISOnewspaper26v4 · el adaptado a la tinta en sintéticos
- Formato: PDF/X-1a:2001 (ISO 15930-1), fuentes incrustadas o a curvas, transparencias aplanadas, página única
- Geometría: sangrado de 3–5 mm por lado (BleedBox ≥ TrimBox + 3 mm); textos dentro de la zona de seguridad de 3–5 mm
- Negros: texto y códigos de barras C0 M0 Y0 K100; masas C60 M40 Y40 K100
- Tipografía ≥ 6 pt; grosor de línea ≥ 0,25 pt
- JPEG: resolución incrustada en Photoshop / 8BIM o en JFIF con unidades. El RIP prioriza 8BIM > JFIF y, sin ellos, colapsa la imagen a 72 ppi
- Peso máximo orientativo: 300 MB

### Personalización Textil (Estampados) — DTG / DTF / Sublimación / Serigrafía

Norma común para DTG, sublimación y serigrafía:

- Lienzo base 25 × 30 cm sobre prenda
- Resolución mínima 150 ppi al tamaño real (≥ 1476 × 1771 px); 300 ppi para degradados; aviso bajo 120 ppi
- Modo de color: CMYK o tintas planas (Pantone+ Solid Coated)
- Perfil: ICC calibrado del taller (p. ej. Epson_F170_Opaque)
- Formato: vector puro (AI/EPS/PDF) o ráster de alta densidad (TIFF/PNG-32 con canal alfa)
- Sublimación: poliéster ≥ 80 % blanco. DTG: algodón perchado + pretratamiento + base blanca TiO₂ en prendas oscuras. Serigrafía: positivos con líneas ≥ 0,5 pt y texto ≥ 6 pt
- Peso máximo orientativo: 150 MB

Norma propia de **DTF** (Direct to Film), que difiere en color, formato y línea mínima:

- Resolución 300 ppp a tamaño real 1:1; mínimo aceptable 200; aviso entre 200 y 300. Crear el documento a 300 ppp desde el inicio: pasar de 72 a 300 no añade información
- Sin área base fija: no se exige el lienzo de 25 × 30 cm
- Modo de color **RGB sRGB IEC61966-2.1** con el perfil incrustado. El RIP convierte a CMYK + blanco con perfiles por tinta, cabezal (Epson i3200 / XP600) y número de pasadas. CMYK es aviso, no error
- Fondo 100 % transparente: PNG-24 o TIFF con transparencia, o PDF vectorial PDF/X-4:2008. **JPG rechazado**: no admite transparencia
- Bordes sólidos sin halo blanco: nada de sombras difuminadas ni semitransparencias alrededor del contorno (medir la franja de alfa parcial)
- Texto ≥ 6 pt; líneas ≥ **0,5 mm**; tipografías convertidas a curvas

### Impresión en Gran Formato — Gigantografía / DOOH / PDF/X-1a

La resolución exigida depende del **sustrato**, el **uso**, la **distancia de visión** y la **escala de trabajo** (ver 12).

- Resolución **efectiva** a tamaño final (1:1) según sustrato y uso. A escala 1:N la resolución del documento se divide por N; a 1:10, el documento va a 300 – 720 ppp
- Modo de color **CMYK obligatorio**; la única excepción son las pantallas DOOH, en RGB
- Perfil: Coated FOGRA39 / ISO Coated v2 en soportes estucados o no porosos; Uncoated FOGRA29 es para papeles offset y da aviso en un sustrato no poroso
- TAC ≤ 320 % en estucados y no porosos, 300 % en papel offset; no conforme por encima del límite + 20
- Formato: PDF/X-1a:2001 como estándar certificado; TIFF o EPS sin compresión. JPG no conforme; otra variante PDF/X, aviso; PDF sin declaración, no conforme
- Sangrado de 3 – 5 mm por borde, a tamaño final
- Zona de seguridad amplia frente a marcos, ojales y doblados; con vainas o canales, 7 cm libres de texto
- Peso máximo orientativo: 2 GB

---

## 2 · LECTURA DE ARCHIVOS

El formato se identifica **por firma binaria, nunca por la extensión**. La extensión sólo desempata RAW.

### Parsers de cabecera escritos a mano

**JPEG** — recorrido marcador a marcador: SOF (número de componentes ⇒ CMYK real frente a RGB), profundidad, progresivo; APP0 JFIF (densidad y unidades); APP1 EXIF (XResolution, YResolution, ResolutionUnit); APP13 «Photoshop 3.0» con el recurso 8BIM 1005 (ResolutionInfo); los tres contenedores de resolución se guardan **por separado** para saber qué leerá el RIP; APP2 ICC_PROFILE **concatenando todos los segmentos**; APP14 Adobe (transform).

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

Hasta 25 según el archivo: sangrado declarado, sangrado medido, demasías, página única, fuentes incrustadas, transparencias aplanadas, cuerpo mínimo de texto, tintas planas, sobreimpresión, cobertura de tinta, negros neutros frente a compuestos, gama CMYK, máscaras de línea, troquel detectado, canal alfa, halo de recorte, área de estampado, densidad para degradados y naturaleza del arte. Offset añade clase de sustrato, lineatura, relación PPI/LPI, resolución CTP y metadatos de resolución JPEG (ver 11). Gran formato sustituye las suyas por las del preflight de gigantografía (ver 12).

**No conviertas el índice de nitidez en una comprobación.** Promedia el gradiente de toda la imagen reducida: un arte plano con bordes duros sale «blando» y un remuestreo no deja huella tras la reducción. Muéstralo como dato neutro en el análisis de píxel, sin color de alarma, y deja la «creación desde cero» como verificación manual.

### Advertencias destacadas

Dos mensajes en bloque rojo, aparte del checklist:

> **ADVERTENCIA DE PREPRENSA:** Resolución efectiva insuficiente (X ppi). Riesgo de pixelación en planchas de trama de N lpi (la lineatura configurada en offset; «trama fina» en los demás procesos).

> **ERROR DE MODO DE COLOR:** El archivo está en RGB: provocará conversión destructiva en el RIP.

En PDF, la advertencia de resolución usa la peor imagen colocada dentro del arte.

### Verificación manual del operador

Puntos por proceso, con casilla y registro en la ficha técnica. Offset (8): fuentes en curvas, transparencias aplanadas, troquel en tinta plana y sobreimpresión, marcas de corte y registro, negros de texto en 100 % K, sangrado verificado, zona de seguridad, OutputIntent declarado. Textil: la lista se **filtra por tecnología** —fondo transparente limpio, base blanca TiO₂, poliéster ≥ 80 %, pretratamiento, positivos de serigrafía, colores Pantone confirmados, prueba de lavado, área de estampado— y DTF añade cinco: documento creado a 300 ppp sin remuestrear desde 72, RGB sRGB IEC61966-2.1 con el perfil incrustado, perfil ICC del RIP según tinta, cabezal y pasadas, contorno sin sombras suaves ni halo blanco, y líneas ≥ 0,5 mm con texto ≥ 6 pt y tipografías a curvas. Gran formato: 18 puntos **filtrados por sustrato** con un tercer elemento, igual que en textil —sustrato, uso y distancia confirmados; escala declarada; gramaje ≥ 450 g/m² y vainas de 7 cm (frontlit y PE); estructura frente al efecto vela (frontlit); polietileno sin PVC (PE); tipos grandes y ubicación (mesh); tipo de vinilo y tecnología de impresión (vinilo); material del panel y modo de impresión (rígido); textos lejos de marcos y ojales, tipografías, límite de tinta y prueba de color (todos los impresos); píxeles exactos y pixel pitch (DOOH)—.

Offset suma un **checklist de producción** de 17 puntos en su módulo, aparte de estos ocho (ver 11).

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
- **Tintas planas** — identificación de tintas directas y especiales (ver abajo)

Añade un **histograma** de distribución de cobertura con la línea del umbral, y una lectura de área sobre el límite, máximo, medio y origen del dato (medido o estimado).

### Modo «Tintas planas»

Cuarto botón del control de cobertura. Oculta el límite y el histograma, que no aplican, y muestra la lista de tintas y su mapa.

**Lista, leída del archivo.** PDF: nombres de `/Separation` decodificando todas las secuencias `#xx`, y colorantes de `/DeviceN` sin los de proceso, `/None` ni `/All`. TIFF: `InkNames` sin los de proceso. PSD: sólo se informa de los canales que superan los del modo de color, o del duotono o multicanal. Formatos sin tintas planas (JPEG, PNG, WebP…): aviso de que todo irá en cuatricromía.

**Clasificación por nombre:** registro (`All`) · proceso como plana (Cyan, Magenta, Yellow, Black y sus nombres en español; aviso) · troquel o corte (misma expresión que el detector de troquel) · barniz o acabado · blanco · metálica (gold, silver, foil… o Pantone 871–877, 8xxx y 10xxx) · fluorescente (Pantone 801–807, fluor, neon) · Pantone con su guía (C, U, CP, UP, TCX, TPX, TPG, M) · otra guía (HKS, Toyo, DIC, Focoltone, Trumatch, RAL) · especial sin guía.

**Avisos:** la misma tinta con dos grafías (clave normalizada sin «PANTONE», espacios ni signos, con CV/CVC → C); la misma referencia en guías distintas; Pantone CP/UP como tinta directa; en offset, guía C sobre no estucado o prensa y guía U sobre estucado o cartulina recubierta. Con avisos, la comprobación de offset «Tintas planas declaradas» pasa a observación.

**Color equivalente, calculado con el propio visor.** Del escaneo binario se lee el espacio alternativo (`DeviceCMYK`, `DeviceRGB`, `DeviceGray`, `Lab`, o `ICCBased` resolviendo su `/N`) y la función de tono: tipo 2 (`C0`, `C1`, `N`) o tipo 4 sin `/Filter`, tomando un factor `mul` por componente. Con esos valores se genera en memoria un PDF mínimo con cada tinta al 25, 50, 75 y 100 %, se rasteriza con PDF.js (`intent: 'print'` y guarda de 6 s) y se leen los píxeles. Así el equivalente coincide con la conversión que produjo el render del arte.

**Mapa estimado.** Cada píxel se compara con la curva de tonos blanco → 25 → 50 → 75 → 100 % de cada tinta (distancia RGB al segmento más cercano, tolerancia 26). Los casi blancos no identifican nada y se descartan. Las zonas que coinciden conservan su color y el resto pasa a gris, con el porcentaje estimado de área por tinta. Registro y proceso como plana sólo se pintan cuando se aíslan. Cada tinta con equivalente tiene un botón **Aislar**. Dos nombres con el mismo equivalente ocupan los mismos píxeles: el segundo no se pinta y se muestra como «mismo color que «…»». La cobertura y esas equivalencias de la vista completa se conservan al aislar, para el panel y el JSON.

**Caché.** La lista y los equivalentes dependen del archivo **y** del objeto de metadatos: durante la carga el archivo nuevo convive un instante con los metadatos del anterior, y una caché que sólo mire el nombre del archivo guardaría las tintas del archivo previo.

**Candidatas a tinta directa.** Si no hay tintas de color declaradas: colores dominantes con saturación > 0,82, brillo > 190 y al menos un 2 % del área con color.

La ficha técnica lista las tintas con su clase y el JSON exporta nombre, espacio, clase, guía, nota, aviso, equivalente RGB, cobertura estimada y candidatas.

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

### Sustratos — 16 entradas

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
| DTG sobre algodón | perfil calibrado del equipo | 260 % | — | ráster |
| Sublimación sobre poliéster | perfil del papel transfer | 240 % | — | ráster |
| Serigrafía textil | Pantone+ Solid Coated | — | 55 | ráster |
| Lona Frontlit (PVC opaca) | Coated FOGRA39 / ISO Coated v2 | 320 % | — | X-1a |
| Lona Mesh (microperforada) | Coated FOGRA39 / ISO Coated v2 | 320 % | — | X-1a |
| Lona ecológica PE (sin PVC) | Coated FOGRA39 / ISO Coated v2 | 320 % | — | X-1a |
| Vinilo adhesivo (corte, opaco, microperforado) | Coated FOGRA39 / ISO Coated v2 | 320 % | — | X-1a |
| Soporte rígido (Dibond / PVC espumado) | Coated FOGRA39 / ISO Coated v2 | 320 % | — | X-1a |

**Al seleccionar un sustrato, el motor adopta su límite de tinta y su perfil esperado**, y el mapa de cobertura se recalcula. Un botón devuelve los valores de la norma. La ficha técnica nunca debe declarar un sustrato que no esté vigente en las reglas.

El catálogo muestra por defecto **sólo los sustratos del proceso activo**, con un botón «Ver todos».

Los sustratos de offset llevan la clase del preflight de offset (`off`): cartulina estucada, tinta limitada y cartón compacto → cartulina; FOGRA51, GRACoL y Japan Color → estucado; papel offset no estucado → no estucado; papel prensa → prensa; flexo, sin clase. Los de gran formato llevan su sustrato de gigantografía (`giga`). Aplicar uno cambia la clase o el sustrato del módulo; elegir en el módulo una clase distinta retira el sustrato de offset aplicado, y elegir un sustrato de gran formato aplica su entrada del catálogo. Al abrir Gran formato, el sustrato del módulo se declara también en el catálogo.

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

### Papeles comerciales — 8 (referencia)

Tabla de resolución de entrada (PPI), salida digital (DPI), salida impresa (LPI) y modo mapa de bits. Una columna final califica el archivo cargado: Óptimo, Apto, Al límite o Insuficiente, con el ancho máximo reproducible. Se muestra en Offset › Referencia por papel comercial, sin la fila de serigrafía, con una nota que la contrasta con la clasificación por sustrato: el preflight verifica con la clasificación.

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

### Gigantografía por tamaño — 6 bandas (referencia)

La exigencia baja al crecer la pieza porque crece la distancia de observación. La banda se selecciona por el lado mayor del tamaño final declarado, pero **sólo como referencia**: el preflight califica con el sustrato y la distancia de visión (ver 12).

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

## 10 · SERIGRAFÍA INDUSTRIAL

Módulo situado en **Procesos de producción › Textil › Serigrafía industrial**, no un destino aparte: abarca textil, rígidos y PCB. Configurador arriba, parámetros calculados debajo, y diez pestañas.

**Conflicto de lineatura que debe mostrarse, no esconderse:** la tabla general de resolución por sustrato da 60 · 70 · 85 LPI para serigrafía, mientras la regla de la malla (`hilos ÷ 2`) da 38–65 LPI en las mallas habituales. Prevalece la regla de la malla, que es la que se comprueba, y la nota lo explica.

### Configurador

Técnica (cuatricromía, proceso simulado, tintas planas, color indexado, PCB) · soporte (textil de algodón, textil sintético, rígido, PCB) · fondo claro u oscuro · malla (34, 43, 49, 55, 61, 77, 90, 100, 110, 120, 140 hilos/cm) · colores previstos · método de leyenda PCB · efecto 3D. La técnica PCB y el soporte PCB van siempre juntos. Al cambiar técnica, soporte o efecto se propone la malla recomendada.

### Parámetros por técnica

| Técnica | Malla (hilos/cm) | Lineatura | Pantallas | Ángulos | Punto |
|---|---|---|---|---|---|
| Cuatricromía | 90 – 120 | hilos ÷ 2 · algodón 45–55 · sintético o rígido 55–65 | 4 claro · 5 oscuro (base blanca) | Y 0° · C 15° · K 45° · M 75°, o genérico 22,5° / 23° | redondo o elíptico |
| Proceso simulado | 90 – 120 | 38 – 55 LPI | 6 – 10 (dominantes + base blanca) | genérico 22,5° / 23° | semitono con tintas directas, en RGB |
| Tintas planas | textil 34–43 (plastisol 43) · rígido 90–120 | no aplica | una por tinta (+ base blanca) | no aplica | sólido Pantone (PMS) |
| Color indexado | 90 – 120 (detalle fino) | no aplica | 6 – 10 colores sólidos | sin ángulos: sin muaré | cuadrado FM uniforme |
| PCB | según método | — | capa de leyenda en resina epoxi | — | — |

Métodos PCB: manual / fotográfica (emulsión, insolación UV, revelado por agua) · foto-imagen líquida (resina epoxi fotosensible, alta densidad y trazos finos) · impresión directa de leyenda (inyección, sin malla ni fotolito). **En PCB, LPI significa *Liquid Photo-Imageable*:** adviértelo en la interfaz, porque en el resto de la herramienta LPI es lineatura.

Durómetro: tramadas 80 (alternativa 70/90/70) · efecto 3D 60 · tintas planas 70. Emulsión: tramadas y rígidos fotopolímero / SBQ-UV · tintas planas textiles Diazo / doble curado · efecto 3D alta densidad. Las técnicas tramadas mandan sobre el efecto 3D, y la combinación se avisa.

### Verificación del arte — hasta 12 comprobaciones

1. **Malla y técnica** — fuera de rango en tramada con malla abierta: error, porque los puntos caen entre hilos.
2. **Efecto 3D sobre trama** — aviso: el relieve pide malla baja y rasqueta 60; la trama, malla alta y rasqueta 80.
3. **Lineatura según malla** — `hilos ÷ 2` contra el rango de la técnica y el soporte, proponiendo la malla o la lineatura correctas.
4. **Resolución** ≥ 300 ppi al tamaño final; aviso dentro del 10 %; en PDF, la peor imagen colocada.
5. **Modo de color** — CMYK para cuatricromía; RGB para simulado e indexado; separaciones Pantone en tintas planas.
6. **Tintas y pantallas** — colores dominantes por cuantización a 16 niveles por canal, ignorando lo que ocupe menos del 0,5 % del área y agrupando tonos vecinos. Si los dominantes cubren menos del 90 %, es tono continuo: error en tintas planas. En PDF mandan las `/Separation` declaradas.
7. **Base blanca** sobre fondo oscuro — derivable del canal alfa; sin alfa, aviso de rectángulo blanco.
8. **Texto** — ≥ 6 pt y convertido a contornos.
9. **Grosor de trazo** ≥ 0,5 pt, con detección de trazos de grosor 0.
10. **Detalle frente a malla** — el trazo debe abarcar al menos dos pasos de malla (`10 ÷ hilos` mm). Márcalo siempre como **orientativo**.
11. **Registro** — presencia del color de registro `/Separation /All`.
12. **Método de leyenda PCB** — aviso si se elige el fotográfico con trazos finos.

### Pestañas

Verificación del arte · Cuatricromía (calculadora malla → lineatura con rangos por soporte, roseta de ángulos con `mix-blend-mode: multiply`, trama frente a malla a 0° y 22,5°, pantallas por fondo) · Proceso simulado · Tintas planas · Color indexado (AM frente a FM) · PCB · Arte y fotolito (vectorización, registro, reventado, opacidad del positivo, ganancia del plotter) · Mallas (tabla, color de malla y refracción, simulador de detalle con hilos a escala real) · Grabado de pantalla (cuatro fases, tipos de emulsión, efecto de la exposición) · Rasquetas (durómetros con depósito relativo).

Doce verificaciones manuales del proceso de grabado. Bloque 8 opcional en la ficha técnica, activado por defecto al elegir la tecnología textil de serigrafía hasta que el usuario lo toque.

---

## 11 · PREFLIGHT DE OFFSET POR SUSTRATO

Módulo en **Procesos de producción › Offset**, bajo la tabla de especificaciones. Clasifica el soporte y **normaliza la salida**.

### Configurador

Tipo de sustrato (las cinco clases de la sección 1) · acabado, dependiente de la clase · lineatura (número, con `datalist` del rango) · salida CTP (1200, 2400 o 2540 dpi). Al cambiar de clase se proponen su lineatura y su CTP: estucado 175 / 2540, no estucado 150 / 2540, prensa 100 / 2400, cartulina 175 / 2540, sintético 150 / 2540. La misma elección está en la tarjeta de offset del panel 1.

Debajo, tres tarjetas —**Entrada (PPI)**, **Lineatura (LPI)** y **Salida CTP (DPI)**— con su definición, el valor de la clase y la lectura del archivo.

### Comprobaciones que se suman a las de offset

1. **Clase de sustrato y acabado** — informativa, con la nota de salida de la clase y, en no estucados, el ajuste de curva previo.
2. **Lineatura de trama** — aviso fuera del rango de la clase. En soportes absorbentes explica que una trama cerrada une los puntos en la fibra y cierra las sombras.
3. **Relación PPI / LPI** — aviso por debajo de 1,5 ×, con la resolución o la lineatura que lo corrigen. Conforme entre 1,5 y 2 ×. Por encima de 2 ×, conforme con nota de que la trama no aprovecha el detalle, sin sugerir bajar del mínimo de la clase.
4. **Resolución de salida CTP** — aviso fuera del rango de la clase. Si no alcanza DPI ≥ LPI × 16, informativa con los niveles resultantes: es un parámetro del RIP, no un defecto del arte, y no debe empeorar el dictamen.
5. **Metadatos de resolución (JPEG)** — el RIP lee 8BIM; si falta, JFIF con unidades; si falta, 72 ppi. Aviso cuando no hay 8BIM ni JFIF con unidades, con el tamaño físico que resultaría a 72 ppi, y aviso cuando 8BIM y JFIF discrepan.

El **perfil esperado** cambia con la clase: `FOGRA39`, `ISO COATED V2`, `FOGRA51`, `PSO COATED V3` en estucados y cartulinas; `FOGRA47`, `PSO UNCOATED`, `FOGRA52`, `UNCOATED V3` en no estucados; `NEWSPAPER`, `IFRA26`, `12647-3` en papel prensa. Se suman los tokens del sustrato de catálogo aplicado y los perfiles marcados en la biblioteca. En sintéticos no hay tokens: sin coincidencia, el resultado es informativo.

### Pestañas

- **Verificación del arte** — dictamen, todas las comprobaciones y la casilla para incluir el bloque en la ficha técnica.
- **Sustratos y acabados** — tabla de las cinco clases con la calificación del archivo (y cuántas veces cubre la lineatura más alta del rango), y fichas con características, acabados, salida, preprensa, ganancia de punto y uso habitual. Botón «Verificar con este».
- **Normalización de la salida** — cuatro cifras para la configuración (imagen 1,5–2 ×, CTP para 256 niveles, niveles a la resolución elegida, lineatura máxima con 256 niveles), una tabla que responde por clase si su CTP sostiene 256 niveles en todo su rango de lineatura, y la prioridad de metadatos con la lectura del JPEG cargado.
- **Checklist de producción** — 17 puntos en cuatro fases. **1 · Preimpresión**: modo de color, resolución, metadatos JPEG, sangrado y seguridad, tipografías, trapping. **2 · RIP y planchas**: CTP a 2400/2540 dpi, lineatura por porosidad, curva de linealización, prueba de contrato bajo D50. **3 · Reología**: temperatura, arrancado en frío, velo y flyeo. **4 · Tirada**: emulsificación hasta ~25 % de agua, densidad de los sólidos a lo ancho del pliego, TVI, registro. Los puntos ligados a una comprobación medida muestran su estado. Incluye una calculadora de temperatura → viscosidad (aproximación lineal de 5–6 % por °C: +5 °C ≈ −25 a −30 %; en negativo, riesgo de arrancado) y notas de emulsificación y de TVI de la clase.
- **Referencia por papel comercial** — la tabla de la sección 7.

### Subordinación de todo el panel a la clase

Ningún texto del camino de offset puede dar por hecho FOGRA39, 175 lpi, 330 % o empaque. Al cambiar clase, acabado, lineatura o CTP se re-evalúa y se repinta:

- `TARGETS.offset.name`, `tag` y `desc` son **getters**, para que ningún título diga «Empaques» cuando no lo es y la tarjeta, la pestaña, el título de especificaciones, la norma aplicada, las alertas, el catálogo, la portada, la ficha y el JSON no puedan quedar desfasados:

  | Clase | `name` | `tag` |
  |---|---|---|
  | Estucado | Impresión Offset (Comercial y editorial · Estucado) | ISO 12647-2 · FOGRA39 / FOGRA51 · PDF/X-1a |
  | No estucado | Impresión Offset (Editorial y papelería · No estucado) | ISO 12647-2 · FOGRA47 / FOGRA52 · PDF/X-1a |
  | Papel prensa | Impresión Offset Coldset (Periódicos · Papel prensa) | ISO 12647-3 · ISOnewspaper26v4 · PDF/X-1a |
  | Cartulina | Impresión Offset (Empaques · Cartulina y cartón) | ISO 12647-2 · FOGRA39 / FOGRA51 · PDF/X-1a |
  | Sintético | Impresión Offset (Sintéticos · Polipropileno y acetato) | ISO 12647-2 · perfil según la tinta · PDF/X-1a |

  `desc` = «Offset litográfico para {ámbito de la clase} sobre {clase} · {acabado}, trama de {lpi} lpi y CTP a {dpi} dpi», con el ámbito tomado del uso habitual de la clase (sintéticos no tienen). Como el nombre ya contiene la clase, el título de especificaciones no la repite y la ficha sólo añade el acabado. El gráfico instructivo se rotula como «Ejemplo de empaque en cartulina a 175 lpi», porque es un ejemplo fijo, y la portada habla de impresión offset, estampado textil y gran formato.
- `offSyncTac()` fija `tacMax` desde la clase, salvo con un sustrato de offset del catálogo aplicado, y reajusta el deslizador del mapa TAC sólo si el límite cambia. Se llama al cambiar de clase, al refrescar el módulo, al arrancar y al final de `applySubstrate`, para que retirar un sustrato del catálogo devuelva el límite de la clase.
- En la comprobación de resolución, el sufijo es «clase …», no «banda …», y la ayuda es la de PPI · LPI · DPI.
- Los consejos de modo de color (PDF e imagen) y de TAC citan el perfil de conversión de la clase; el de cuerpo mínimo, la lineatura configurada; la norma de sangrado es 3 – 5 mm; la expectativa del TAC indica si el límite viene de la clase o del catálogo.
- Verificación manual: el ítem de OutputIntent lleva `{perfil}` y se resuelve con el perfil de la clase; el troquel se filtra fuera de papel prensa; se añaden la curva tonal previa (no estucado), la ganancia ISO 12647-3 (prensa) y la tinta de oxidación o UV (sintético). Los ítems nuevos van al final para no cambiar los índices ya marcados.
- Norma aplicada: TAC con su origen y perfil con el de conversión. En el preajuste `.joboptions` de offset, perfil esperado, etiqueta y resolución mínima de imagen salen de la clase.
- **La tabla de especificaciones de la pestaña se genera desde la clase** (`offSpecs()`), no desde una constante: dos tablas con los mismos conceptos y valores distintos son una contradicción esperando a ocurrir.
- **El gráfico instructivo también depende de la clase:** título, subtítulo con norma, perfil, TAC, lineatura y CTP; línea del límite de tinta en su porcentaje; etiqueta del negro rico de 240 % que dice «correcto», «justo en el límite» o «supera el límite» según el TAC; sección de trama con la lineatura, su rango y la resolución; cuerpo mínimo de texto; pie con norma y perfil. El interior del TrimBox cambia: troquel con solapas, mancha de la página o mancha sin troquel en papel prensa. La roseta es esquemática y la interfaz lo dice.
- **Escala del gráfico:** las barras de carga de tinta arrancan en el 0 % y la línea del límite se sitúa con la misma escala. Si las barras empiezan en la marca del 100 %, un negro rico de 240 % se dibuja cruzando un límite de 330 % y el gráfico enseña lo contrario de lo que explica.

### Reglas que no son iguales en todas las clases

Un destino con clases no puede aplicar la misma regla a todas: `OFF_REGLA` recoge las que cambian.

- **Páginas:** un archivo por diseño en cartulina y sintéticos (no conforme si hay más); en estucado, no estucado y papel prensa un documento paginado es lo normal y sólo se informa, recordando acordar la imposición.
- **Cuerpo mínimo de texto:** 6 pt, y 7 pt en papel prensa, donde la alta ganancia tonal cierra el filo. El consejo cita el mínimo y la lineatura vigentes.
- **Troquel:** obligatorio en cartulina y sintéticos, opcional en estucado y no estucado —el contorno dominante se informa sin exigirlo— e inexistente en papel prensa, donde la comprobación no se emite.

### Coherencia del catálogo con el destino

- Ningún sustrato del catálogo puede pertenecer a un proceso que no se verifica: la flexografía sobre corrugado no es offset y no debe aparecer en su catálogo.
- El preajuste PDF de un sustrato de offset no puede proponer PDF/X-4 mientras el destino exige PDF/X-1a.
- Las notas de los sustratos no pueden contradecir las reglas del destino: si la nota del papel prensa dice «nada de negros ricos» y «cuerpo mínimo 7 pt», o se alinean los textos o se cambia la regla, pero no pueden convivir.
- Aplicar un sustrato de offset adopta también su lineatura cuando cabe en el rango de la clase.

### Conflictos que la interfaz muestra

- La tabla asigna 150–200 lpi a 2400–2540 dpi, pero con DPI ≥ LPI × 16 esas resoluciones dan 256 niveles sólo hasta 158 lpi: 175 lpi piden 2800 dpi y 200 lpi, 3200. En papel prensa, 1200 dpi sostienen 75 lpi.
- La tabla de papeles comerciales difiere de la clasificación: periódico 75–85 lpi, 112–200 ppi y 1200 dpi frente a 85–100 lpi, 150–200 ppi y 1200–2400 dpi; bond 85–110 lpi y 200–220 ppi frente a 120–150 lpi y 300 ppi; couché 150–175 lpi y 2400–3600 dpi frente a 150–200 lpi y 2400–2540 dpi. Prevalece la clasificación.

---

## 12 · PREFLIGHT DE GIGANTOGRAFÍA

Módulo en **Procesos de producción › Gran formato**, bajo la tabla de parámetros transversales.

### Sustratos y resolución por uso

| Sustrato | Uso · distancia | Entrada 1:1 | Entrada 1:10 | Salida | Efectiva |
|---|---|---|---|---|---|
| Lona Frontlit (PVC opaca) | Mupi / marquesina · 1–3 m | 150 ppp | 300 ppp (hasta 720) | 300 – 720+ DPI | 100 – 150 ppp |
| | Valla de carretera · 10–16 m | 72 – 100 ppp | 300 ppp (hasta 720) | 300 – 720+ DPI | 5 – 15 ppp |
| Lona Mesh (microperforada) | Fachada / andamio / cerramiento · 5–50 m | 72 ppp (más se desperdicia) | 300 ppp | 300 – 600 DPI | 5 – 25 ppp |
| Lona ecológica PE | Los usos de la frontlit, **asumidos** | | | | |
| Vinilo adhesivo | Escaparate / vehículo · 0,5–2 m | 150 – 300 ppp | no especificada | 720 – 1440 DPI | 100 – 300 ppp |
| Soporte rígido (Dibond, PVC espumado) | Panel interior / stand · 0,5–1,5 m | 150 – 300 ppp | no especificada | 600 – 1200 DPI (UV cama plana) | 150 – 300 ppp |
| | Mural exterior grande · 1,5–3 m (emparejado por distancia) | 72 – 100 ppp | no especificada | 600 – 1200 DPI | 38 – 76 ppp |
| Pantalla LED (DOOH) | Píxeles exactos · RGB a 72 ppi | — | no aplica | pixel pitch 2,5 – 16 mm | 25,4 ÷ pitch |

Cada sustrato impreso lleva su ficha: composición, rendimiento gráfico, gramaje (≥ 450 g/m² en frontlit), confección (vainas de 7 cm en frontlit y PE), limitación física (efecto vela), uso ideal.

**Calificación:** Óptimo si la efectiva alcanza la entrada recomendada · Apto si alcanza la efectiva mínima · Al límite hasta un 10 % por debajo (aviso) · Insuficiente (no conforme).

**Tabla de distancia de visión** (referencia; para una distancia no tabulada se toma la fila inferior, la más exigente): 0,25–0,50 m → 150–305 ppp · 1,00–1,20 m → 64–76 ppp · 3 m → 25 ppp · 5 m → 15 ppp · 10–16 m o más → 5–8 ppp.

### Configurador

Sustrato · uso (propone su distancia) · distancia de visión · escala del documento (1:1, 2, 5, 10, 20) · confección de bordes (vainas de 7 cm, ojales / dobladillo / bastidor, sin confección). En DOOH, ancho y alto de la pantalla en píxeles y pixel pitch. La elección de sustrato también está en la tarjeta del panel 1.

**Tamaño final autocompletado:** PDF → TrimBox × N; imagen con ppi → píxeles ÷ ppi × N; sin ppi → píxeles ÷ entrada recomendada; DOOH → píxeles × pitch. El panel 2 siempre es el tamaño final.

Debajo, tres tarjetas —**Resolución de entrada**, **de salida** y **efectiva**— con la definición de cada una y la lectura del archivo.

### Comprobaciones

El gran formato delega los seis campos obligatorios y todas sus específicas en este módulo:

- **Dimensiones** — PDF: TrimBox × N frente al tamaño final declarado. DOOH: píxeles exactos de la pantalla (un PDF no es conforme).
- **Resolución efectiva** — calificación anterior, con la resolución del documento a 1:N. En mesh, nota cuando supera 72 ppp a 1:1. En DOOH, informativa con los ppp de emisión.
- **Color, perfil y formato** — según la sección 1; en DOOH, RGB y formato del reproductor como información.
- **Sustrato y uso**, **distancia de visión** (aviso si queda fuera del rango del uso), **escala 1:10** (conforme con ≥ 300 ppp en el documento), **banda por tamaño** (referencia).
- **Sangrado** — BleedBox − TrimBox × N ≥ 3 mm a tamaño final; en imagen suelta, los píxeles de 3–5 mm.
- **Zona de seguridad por confección** — distancia mínima de las cajas de texto vivo al TrimBox, × N. Con vainas, aviso por debajo de 70 mm; sin vainas, informativa. Siempre **orientativa**.
- **Tipografías**, **texto más pequeño a tamaño final** (pt × N y cm de cuerpo, con nota de tipos grandes en mesh), **transparencias vivas** (aviso) y **TAC**.

### Pestañas

Verificación del arte · Sustratos (tabla con calificación del archivo y fichas) · Resolución y distancia (las tres definiciones, la tabla de distancia y la de bandas por tamaño) · Escala de trabajo (calculadora: ancho y alto finales, escala, ppp del documento → documento en mm, píxeles, efectiva a tamaño real y calificación con el sustrato; ejemplo de la valla de 8 × 3 m a 1:10 = 800 × 300 mm a 300 ppp → 30 ppp; en DOOH, de píxeles y pitch a metros).

### Conflictos que la interfaz muestra

- Tres referencias que no dicen lo mismo: la tabla de distancia (lo que el ojo aprecia), la regla por sustrato (lo que se recomienda producir) y la tabla por tamaño. Califica la del sustrato.
- «720 dpi (o 300 ppp)» a 1:10 no equivalen: dan 72 y 30 ppp a tamaño real, y son resolución de entrada del documento, no DPI de salida.

---

## 13 · INTERFAZ

**Estética:** modo oscuro, paleta zinc/slate, bordes rectos sin redondeos, jerarquía tipográfica clara, densidad alta pero legible. Nada de degradados decorativos ni tarjetas flotantes.

**Portada:** título a gran escala con la palabra «Studio» en degradado, fondo de retícula con máscara radial, halo suave y grano SVG. Debajo, cuatro indicadores en vivo: destino activo, archivo, sustrato y dictamen.

**Barra fija** con logotipo, anclas de sección, botón de **Glosario**, selector de destino y píldora de veredicto con color según el estado.

**Cinco secciones:** Preflight · Procesos de producción · Catálogo de perfiles · Formatos de archivo · Visor 3D de capas.

**Procesos de producción, en pestañas** para que el estudiante no mezcle las propiedades técnicas de un proceso con las de otro:

- Pestañas **Offset · Textil · Gran formato** con roles ARIA `tablist` / `tab` / `tabpanel` y navegación con flechas, Inicio y Fin. Paneles ocultos con `hidden`, reforzado con `display: none !important` para que ninguna utilidad de Tailwind los muestre.
- **La pestaña activa es la norma que verifica el preflight.** Selector de la cabecera, tarjetas del panel 1 y pestañas están sincronizados en ambos sentidos; cambiar de pestaña re-evalúa el archivo cargado.
- Cada pestaña abre con su **tabla de especificaciones técnicas**, agrupada por bloques (resolución, color, formato, geometría, tipografía y trazo…). Cada fila lleva un distintivo de **cómo se comprueba**: *se mide* en el archivo, *se estima*, *verificación manual* o *referencia*, con leyenda. No marques como medido lo que sólo se estima.
- **Offset:** tabla de especificaciones (resolución y trama, color, formato, geometría con trapping, negros, prensa y tirada), acceso al gráfico instructivo y el preflight de offset por sustrato (ver 11).
- **Textil:** subpestañas DTG · DTF · Sublimación · Serigrafía industrial, cada una con su tabla. DTF lleva una nota que separa su línea mínima (0,5 mm) de la de serigrafía (0,5 pt). Serigrafía contiene el módulo completo (ver 10) y la nota del conflicto de lineatura con la calificación del archivo.
- **Gran formato:** tabla de parámetros transversales (resolución de entrada, salida y efectiva, escala 1:1 y 1:10; modo de color y perfiles; TAC; formato de entrega; sangrado, seguridad y vainas; tecnologías de impresión) y el preflight de gigantografía (ver 12). Números con separador de miles de punto escrito a mano: `toLocaleString('es')` no agrupa las cifras de cuatro dígitos.

El título de especificaciones, la portada, la verificación manual, la norma aplicada, la ficha técnica y el JSON muestran el proceso **y su subdivisión**: la tecnología textil, la clase de sustrato offset o el sustrato de gran formato. El botón del gráfico instructivo sólo aparece en offset.

**Panel de preflight en tres columnas:**
1. Proceso de producción, con botones de tecnología textil, clase de sustrato offset o sustrato de gran formato bajo la tarjeta activa · tamaño de reproducción con cálculo de ppi en vivo · control de flujo con IA · emisión del informe
2. Previsualización con superposiciones · dashboard de metadatos · análisis forense de píxel
3. Alertas · checklist obligatorio · especificaciones del destino · verificación manual · norma aplicada

**Zona de carga:** arrastrar, pegar con `Ctrl+V` o seleccionar.

**Cálculo de resolución efectiva:**

```
PPI = píxeles de ancho ÷ (ancho físico en cm ÷ 2,54)
```

Con selector de unidad (cm, pulgadas, mm), bloqueo de proporción, tamaño sugerido según el archivo y el destino, e indicador con barra de color.

**Control de flujo con IA:** casilla «¿Este diseño contiene gráficos generados con Inteligencia Artificial?» que despliega dos listas —limitación nativa detectada y acción correctiva aplicada— más notas del operador. Se integra en la ficha técnica como trazabilidad.

**Ayuda contextual:** botón de interrogación junto a cada concepto, que abre una ficha explicativa. Y un botón de **Glosario** que abre las 30 fichas completas con buscador (incluidas DTF, gigantografía, resoluciones y sustratos de gran formato, sustratos de offset, PPI · LPI · DPI y control en prensa).

**Gráfico instructivo** para el panel de especificaciones de offset: un SVG con cuatro cuadrantes —anatomía del pliego con MediaBox, BleedBox, TrimBox, seguridad y troquel con solapas; barras de carga de tinta comparando negro rico de 240 % contra negro de registro de 400 % con la línea del límite; roseta de trama de 175 lpi con la regla ppi ≈ 2 × lpi; y el negro de texto en 100 % K frente al compuesto con registro desviado— y siete llamadas numeradas explicadas.

---

## 14 · SALIDAS

**Ficha Técnica en PDF** por `window.print()` con hoja de estilo A4. Siete bloques: identificación del trabajo (OT, cliente, operador, fecha, destino con su subdivisión, sustrato y perfil), metadatos del archivo, checklist obligatorio, especificaciones del destino, verificación manual, declaración de flujo con IA y norma aplicada. Después, bloques opcionales numerados a continuación: verificación de serigrafía industrial; en gran formato, el preflight de gigantografía (sustrato, uso, distancia, escala, confección, las tres resoluciones, la del archivo, la fila de distancia y la banda por tamaño); en offset, la clasificación del sustrato y la normalización de la salida con el estado de los 17 puntos del checklist de producción. Con logotipo institucional **a color**, línea de docente y curso, aviso técnico y casillas de firma para operador, producción y cliente.

**Diagnóstico JSON** descargable con metadatos completos, análisis de píxel, los tres checklists, sustrato, formato identificado, perfiles del taller, calificación por papel comercial, verificación de serigrafía industrial, preflight de gigantografía y clasificación offset con su checklist de producción.

**Captura PNG** de la vista actual del visor 3D.

---

## 15 · DETALLES DE IMPLEMENTACIÓN QUE EVITAN FALLOS REALES

Éstos no son opcionales. Cada uno corresponde a un fallo que aparece si se omite.

1. **PDF.js con `intent: 'print'`.** El render con `intent: 'display'` usa `requestAnimationFrame` y **no avanza nunca si la pestaña está en segundo plano**, dejando el preflight colgado. Con `intent: 'print'` es determinista. Añade además una guarda de 20 s que cancele la tarea y continúe sin análisis de píxel.

2. **Tolerancia de 0,5 ppi** en la comprobación de resolución. Un archivo exactamente a 300 ppi da 299,92 por el redondeo del tamaño físico introducido a mano, y marcaría aviso sin motivo.

3. **Lienzo 3D con medida provisional.** Si el contenedor arranca sin ancho (pestaña oculta, contenedor colapsado, layout sin resolver), el lienzo se queda en 0 px para siempre. Arranca con una medida de respaldo y corrígela en el primer fotograma.

4. **SVG con tipo MIME explícito.** El navegador sólo rasteriza un SVG si el blob declara `image/svg+xml`. Un `File` con tipo vacío falla silenciosamente.

5. **`preserveDrawingBuffer: true`** en el renderer de Three.js, o `toDataURL` devuelve un lienzo vacío.

6. **Escapa el NUL como `\u0000`** en las expresiones regulares. Un byte nulo literal es válido en JavaScript pero convierte el archivo en binario para las herramientas.

7. **Trama, no transparencia,** para señalar zonas sobre el arte. Un relleno translúcido desaparece sobre arte del mismo color.

8. **`localStorage` siempre dentro de `try/catch`.** Puede lanzar en ventanas privadas o con almacenamiento bloqueado.

9. **Guardas en todos los bucles de parsing.** Un contador máximo de iteraciones evita que un archivo malformado congele la pestaña.

10. **Sin código muerto.** Si una función se sustituye por otra versión, elimina la anterior.

11. **Grosor de trazo con la matriz vigente.** Captura `setLineWidth` en la lista de operadores, guárdalo y recupéralo con `save` / `restore`, y escálalo por √|det(CTM)|. Un grosor 0 es la línea más fina del dispositivo: se marca como error, no como «muy fino».

12. **Deslizadores que sobreviven al arrastre.** En las pestañas interactivas, el evento `input` actualiza sólo la salida del control. Re-renderizar el panel durante el arrastre destruye el deslizador bajo el cursor.

13. **Configuradores construidos una sola vez.** En los módulos de offset y gigantografía, el render sincroniza los valores de los controles sin reemplazarlos, no toca el campo que tiene el foco y sólo rehace las opciones dependientes (acabados, usos) cuando cambia la clase o el sustrato. Los campos numéricos evalúan en `change`, no en cada pulsación, porque cada evaluación rehace el análisis de píxel.

14. **Sincronización sin doble evaluación.** `applySubstrate(id, quiet)` acepta un modo silencioso para que un módulo declare su sustrato en el catálogo sin lanzar otra evaluación. Volver a elegir el sustrato o la clase activos no debe reiniciar el uso, la distancia ni la lineatura configurados.

15. **Recurso 8BIM 1005.** Cada bloque es `8BIM` + id (uint16) + nombre Pascal con relleno a longitud par + tamaño (uint32) + datos con relleno a par. En ResolutionInfo, `hRes` y `vRes` son 16.16 de coma fija y **siempre en ppi**; la unidad que acompaña es sólo de visualización. Si llega antes que JFIF, JFIF no debe sobrescribirlo.

---

## 16 · HONESTIDAD TÉCNICA

La aplicación debe declarar sus límites **en la propia interfaz**, no sólo en la documentación:

- La cobertura de tinta y el modo de color derivados de píxel son heurísticos, calculados sin motor de gestión de color. No sustituyen una separación con perfil ICC ni la verificación en RIP. Sólo con canales de tinta reales la medición es directa, y la interfaz debe distinguir un caso del otro con las palabras «medido» y «estimado».
- En PDF 1.5+ con flujos de objetos comprimidos, la lectura de cajas, fuentes y separaciones puede ser parcial. Avísalo cuando se detecte `/ObjStm`.
- La descomposición en capas del visor 3D es una reconstrucción de diagnóstico por análisis de imagen, no la separación interna del documento. Dilo bajo el visor.
- El troquel se identifica por geometría: hay que confirmar que esté en su propia tinta plana y marcado como no imprimible.
- Enumera los casos no soportados con la acción correctiva: AI y EPS exportar a PDF; RAW revelar a TIFF; PSD aplanar y exportar; TIFF en mosaicos o de 32 bits sólo metadatos; HEIF según el navegador.
- Cuando dos referencias técnicas no coinciden, muestra ambas y di cuál prevalece y por qué, en lugar de elegir una en silencio.
- En gran formato, la zona de seguridad se mide sobre las cajas de texto vivo: no ve los elementos gráficos y es orientativa. La lona PE usa los valores de la frontlit y la interfaz dice que son asumidos. En DOOH no se verifica el formato del reproductor.
- En offset, curvas de linealización, prueba de contrato, reología, emulsificación, densidad, TVI y registro no se miden en el archivo: son puntos del checklist, no comprobaciones.
- No inventes valores que la referencia no da: sin densidades objetivo por soporte, sin uso habitual para sintéticos, sin resolución a 1:10 para vinilos y rígidos.
- El mapa de tintas planas es una estimación por color: no ve la tinta en sobreimpresión ni bajo otros colores y puede marcar colores de proceso iguales. Dilo bajo la lista. Sin función de tono legible, la tinta se lista pero no se sitúa, y la interfaz explica por qué.
- En serigrafía, el conteo de tintas y colores dominantes es una estimación del render; el criterio de dos pasos de malla es orientativo; las marcas de registro se infieren por el uso del color de registro, no por su geometría.

Prefiere un `warn` honesto a un `pass` cómodo. Nunca declares conforme algo que no se ha podido comprobar.

---

## 17 · CALIDAD DEL CÓDIGO

- Español en toda la interfaz, con acentuación correcta. Comentarios en español, explicando **por qué**, no qué.
- Organiza el archivo en bloques numerados con cabecera de comentario.
- Captura de errores en todo parser: un archivo malformado nunca debe romper la aplicación, sólo producir un aviso claro.
- Los mensajes de error dicen qué pasó y qué hacer.
- Comprueba que el JavaScript resultante pasa un análisis de sintaxis antes de entregar.

## ENTREGA

El archivo `index.html` completo, funcional al abrirlo con doble clic.
