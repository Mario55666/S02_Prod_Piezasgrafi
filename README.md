# Preflight & QC Studio

**Control de calidad de preprensa industrial · herramienta didáctica**

Instituto de Educación Superior Pública «Diseño y Comunicación»
Docente: Mg. Mario Quiroz — Unidad didáctica de Producción y publicación de piezas gráficas · 2026 · Semana 1

---

## Qué es

Una aplicación web de **un solo archivo** que verifica arte final antes de producción. Analiza imágenes y documentos PDF contra la norma del destino elegido, mide lo que se puede medir y declara con claridad lo que no.

Todo el procesamiento ocurre en el navegador del estudiante. **Ningún archivo se transmite a un servidor.**

## Cómo se usa

1. Abre `index.html` con doble clic. No requiere instalación ni servidor.
2. Elige el proceso de producción: **Offset**, **Textil** —y dentro, la tecnología: DTG, DTF, sublimación o serigrafía— o **Gran formato**. Cada uno verifica con su propia norma.
3. Arrastra el archivo a la zona de carga, pégalo con `Ctrl+V` o selecciónalo.
4. Introduce el **tamaño físico de reproducción** en el panel 2. Sin ese dato no existe resolución efectiva.
5. Ajusta el sustrato: en **offset**, la clase de sustrato, el acabado, la lineatura y la resolución del CTP; en **gran formato**, el sustrato, el uso, la distancia de visión y la escala de trabajo. Opcional: aplica un sustrato del catálogo para que el límite de tinta y el perfil esperado se ajusten a él.
6. Revisa el dictamen, completa las verificaciones manuales y genera la **Ficha Técnica**.

La primera carga necesita conexión para traer las librerías desde CDN. Una vez cargada la página, el análisis funciona sin conexión.

---

## Destinos de producción

| Destino | Norma | Criterio |
|---|---|---|
| **Impresión Offset** (el nombre sigue a la clase de sustrato) | ISO 12647-2 / 12647-3 · PDF/X-1a:2001 | Según la clase de sustrato: entrada 300 ppi (150–200 en papel prensa) y 1,5–2 × lineatura, lineatura de 85 a 200 lpi según la porosidad, CTP de 1200 a 2540 dpi, perfil FOGRA39/51, FOGRA47/52 o ISO 12647-3; TAC del perfil de la clase (330 %, 300 % o 240 %); CMYK, sangrado 3–5 mm, texto ≥ 6 pt |
| **Personalización Textil (Estampados)** | DTG · DTF · Sublimación · Serigrafía | Norma según la tecnología: DTG y sublimación a 150 ppi en área 25 × 30 cm con canal alfa y CMYK o tintas planas; DTF a 300 ppp (mínimo 200) en RGB sRGB con fondo transparente; serigrafía con su propio módulo |
| **Impresión en Gran Formato** | Gigantografía · DOOH · PDF/X-1a:2001 | Resolución efectiva según sustrato, uso, distancia de visión y escala de trabajo; CMYK con Coated FOGRA39 / ISO Coated v2 (RGB sólo en pantallas DOOH), TAC 320 %, PDF/X-1a o TIFF sin compresión, sangrado 3–5 mm y 7 cm libres de texto en vainas |

---

## Qué verifica

### Checklist obligatorio — 6 campos

Dimensiones finales · Resolución efectiva · Modo de color · Perfil ICC · Formato de exportación · Peso del archivo.

### Especificaciones del destino

Hasta 25 comprobaciones adicionales según el destino y el tipo de archivo: sangrado medido, demasías, página única, fuentes incrustadas, transparencias, cuerpo mínimo de texto, tintas planas, sobreimpresión, cobertura de tinta, negros neutros frente a compuestos, gama CMYK, canal alfa, halo de recorte, máscaras de línea, relación PPI/LPI, resolución del CTP, metadatos de resolución JPEG, distancia de visión, escala de trabajo, zona de seguridad por confección y más.

### Verificación manual del operador

Puntos por proceso —y en textil, por tecnología— que la máquina no puede comprobar sola: troquel en sobreimpresión, pretratamiento DTG, poliéster ≥ 80 % blanco, perfil sRGB incrustado en DTF, gramaje y vainas de la lona, pixel pitch en DOOH, prueba de lavado, marcas de registro, etc. En gran formato la lista se filtra por sustrato. Quedan registrados en la ficha técnica.

En offset se suma un **checklist de producción** de 17 puntos en cuatro fases, desde la preimpresión hasta la tirada.

---

## Lectura de archivos

### Formatos reconocidos por firma binaria

No se confía en la extensión del archivo.

| Formato | Alcance |
|---|---|
| **PDF** | Cajas de página, PDF/X, OutputIntent, fuentes, tintas planas, transparencia, texto vivo, imágenes colocadas, contornos vectoriales |
| **TIFF** | IFD completo, LZW · PackBits · Deflate · sin comprimir, ICC incrustado, tintas declaradas, **TAC medido sobre canales reales** |
| **JPEG** | Marcadores SOF, resolución en Photoshop/8BIM, JFIF y EXIF por separado, ICC multi-segmento, APP14 Adobe |
| **PNG** | IHDR, pHYs, iCCP, sRGB, gAMA, tRNS, canal alfa |
| **WebP** | RIFF/VP8X, alfa, ICC |
| **GIF · BMP · SVG** | Cabecera y análisis de píxel completo |
| **PSD** | Cabecera 8BPS: modo de color, canales, profundidad |
| **RAW · HEIF** | Identificación con indicación de qué hacer antes de producción |

### Perfiles ICC del taller

Arrastra tus `.icc` / `.icm` al catálogo. Lee clase de dispositivo, espacio, PCS, versión, fecha, intento por defecto, fabricante y tablas A2B/B2A. Los perfiles de salida CMYK se marcan como destino automáticamente y pasan a validar el campo «Perfil ICC». La biblioteca persiste en el navegador.

### Preajustes `.joboptions` de Adobe

Carga el preajuste de exportación y lo audita **antes** de que el PDF se genere: compatibilidad, estrategia de conversión de color, intención de salida, incrustación de fuentes, submuestreo, sangrado, sobreimpresión y funciones de transferencia.

---

## Herramientas de inspección

### Mapa de cobertura de tinta

Lectura equivalente a *Previsualización de salida → Cobertura de tinta* de Acrobat, con **umbral regulable de 160 a 400 %** y tres modos:

- **Sobre el límite** — arte desaturado y resalte macizo donde se supera el umbral
- **Densidad** — rampa continua sobre el total de cobertura
- **Separaciones** — las cuatro planchas en cuadrícula
- **Tintas planas** — identifica las tintas directas y especiales

Incluye histograma de distribución y botón «Norma» para volver al límite del sustrato activo.

### Tintas planas: tinta directa y especial

El botón **Tintas planas** del control de cobertura lee del archivo las tintas declaradas —espacios `/Separation` y `/DeviceN` del PDF, `InkNames` del TIFF— y las clasifica:

| Clase | Qué es |
|---|---|
| Pantone | Tinta directa de la guía, con su sufijo: C (estucado), U (no estucado), CP/UP (cuatricromía), TCX/TPX (textil) |
| Metálica · Fluorescente | Fuera de la gama CMYK: su vista en pantalla es orientativa |
| Otra guía · Especial sin guía | HKS, Toyo, DIC… o una fórmula propia que hay que confirmar con la imprenta |
| Blanco | Base bajo el color en soportes transparentes, metalizados u oscuros |
| Troquel / corte · Barniz / acabado | Tintas técnicas: en sobreimpresión, no son color del diseño |
| Registro | `/All`: sólo para marcas |
| Proceso como plana | «Cyan», «Magenta»… definidas como tinta plana: generan una plancha extra |

Señala además los **nombres duplicados** (`PANTONE 185 C` y `Pantone 185C` son dos planchas), la misma referencia en guías distintas, Pantone CP/UP usado como tinta directa y, en offset, la guía C o U que no corresponde al soporte. Esas observaciones pasan a la comprobación «Tintas planas declaradas».

El **mapa** sitúa cada tinta en el arte: en color, las zonas que coinciden con ella; en gris, el resto, con el porcentaje estimado de área. El color equivalente de cada tinta se calcula con el propio PDF.js a partir de su función de tono (tipo 2, o tipo 4 sin comprimir), al 25, 50, 75 y 100 %. Cada tinta se puede **aislar**. Registro y proceso como plana sólo se muestran aisladas, porque coincidirían con todo el negro o el cian del arte.

En JPEG, PNG y demás formatos sin tintas planas, avisa de que todo el color irá en cuatricromía y propone **candidatas a tinta directa**: masas planas muy saturadas que la cuatricromía no alcanza.

En un TIFF CMYK la cobertura se **mide** sobre los canales de tinta. En cualquier otro origen es una **estimación heurística** con un modelo GCR ligero, y la interfaz lo indica.

### Troquel, sangrado y demasías

Sobre un PDF extrae los contornos trazados reales llevando la matriz de transformación, identifica el troquel y lo destaca. Mide el borde efectivo de la tinta y lo compara con TrimBox y BleedBox lado por lado:

- **Sangrado medido en el arte** — cuánta tinta hay realmente más allá del corte, que no siempre coincide con el BleedBox declarado
- **Demasías** — arte que rebasa el sangrado, señalado con trama diagonal

### Visor 3D de orden de apilación

Descompone el arte en las capas en que se aplica —sustrato, base blanca TiO₂, masas de color, imagen ráster, vectores, texto y troquel— y las apila en el orden de impresión. Cada capa hereda las incidencias del preflight que le corresponden. En PDF, la capa de texto son las cajas de texto reales, con los bloques por debajo del cuerpo mínimo marcados en rojo.

Órbita con arrastre, zoom con rueda, separación regulable, modo contornos, giro automático y captura PNG.

---

## Procesos de producción

Sección con **pestañas por proceso** —Offset, Textil y Gran formato— para que el estudiante vea por separado las propiedades técnicas de cada uno. La pestaña activa es la norma que verifica el preflight: cambiarla cambia también la verificación. El selector de la cabecera, las tarjetas del panel 1 y las pestañas están sincronizados.

Cada pestaña tiene su **tabla de especificaciones técnicas**, y cada fila indica **cómo se comprueba**: se mide en el archivo, se estima, verificación manual o referencia.

- **Offset** — tabla de especificaciones (resolución y trama, color, formato, geometría, negros, prensa y tirada), el **preflight de offset por sustrato** y el acceso al gráfico instructivo.
- **Textil** — subpestañas por tecnología, cada una con su norma de verificación:
  - **DTG** y **sublimación**: área 25 × 30 cm, 150 ppi (300 para degradados), CMYK o tintas planas, canal alfa.
  - **DTF**: 300 ppp a tamaño real (mínimo aceptable 200), **RGB sRGB IEC61966-2.1** con perfil incrustado, fondo 100 % transparente, PNG-24 o TIFF con transparencia o PDF/X-4, JPG rechazado, bordes duros para evitar el halo blanco, texto ≥ 6 pt y líneas ≥ **0,5 mm**.
  - **Serigrafía industrial**: el módulo completo de matrices, mallas y fotolito.
- **Gran formato** — parámetros transversales (resolución, color y perfiles, TAC, formato, sangrado y confección) y el **preflight de gigantografía**.

Las verificaciones manuales, la norma aplicada y la Ficha Técnica se ajustan al proceso y, en textil, a la tecnología elegida. El catálogo de sustratos muestra por defecto sólo los del proceso activo.

La interfaz señala dos confusiones frecuentes: la línea mínima de DTF (0,5 mm) frente a la de serigrafía (0,5 pt), y las dos referencias de lineatura para serigrafía que no coinciden (60–85 LPI en la tabla general de sustratos frente a 38–65 LPI por la regla de la malla).

---

## Preflight de offset por sustrato

Módulo dentro de **Procesos de producción › Offset**. Clasifica el soporte y normaliza la salida. El configurador fija la clase de sustrato, el acabado, la lineatura y la resolución del CTP.

| Tipo de sustrato | Acabados | Entrada | Salida CTP | Lineatura | Perfil / estándar |
|---|---|---|---|---|---|
| Estucado / couché | Brillo · mate | 300 ppi | 2400–2540 dpi | 150–200 lpi | FOGRA39 / FOGRA51 |
| No estucado | Offset · woodfree · bond | 300 ppi | 2400–2540 dpi | 120–150 lpi | FOGRA47 / FOGRA52 |
| Papel prensa | Coldset | 150–200 ppi | 1200–2400 dpi | 85–100 lpi | ISO 12647-3 |
| Cartulina y cartón | C1S · C2S · sin recubrimiento | 300 ppi | 2400–2540 dpi | 150–175 lpi | FOGRA39 / FOGRA51 |
| Sintéticos | Polipropileno · acetato autoadhesivo | 300 ppi | 2400–2540 dpi | 133–150 lpi | Perfil adaptado a tintas de oxidación / UV |

Las tres resoluciones —entrada, lineatura y CTP— quedan a la vista con la lectura del archivo, y el trabajo se organiza en cinco pestañas:

- **Verificación del arte** — el preflight con la clase activa. Añade la relación PPI/LPI (1,5–2×), la lineatura frente al rango del soporte, la resolución del CTP con los niveles de gris resultantes (DPI ≥ LPI × 16) y, en JPEG, la **integridad de los metadatos de resolución**: el lector separa 8BIM, JFIF y EXIF y calcula lo que leerá el RIP (8BIM > JFIF; sin ellos, 72 ppi y otro tamaño físico). El perfil esperado cambia con la clase.
- **Sustratos y acabados** — tabla con la calificación del archivo y fichas con características, salida, ganancia de punto y uso habitual.
- **Normalización de la salida** — cálculo PPI · LPI · DPI y una tabla que comprueba si el CTP de cada clase sostiene 256 niveles en todo su rango de lineatura.
- **Checklist de producción** — 17 puntos en cuatro fases: preimpresión, RIP y planchas, reología de tinta, tirada. Los medibles muestran el resultado del preflight. Incluye una calculadora de temperatura y viscosidad (5–6 % por °C).
- **Referencia por papel comercial** — la tabla de ocho papeles con la calificación del archivo.

Los sustratos de offset del catálogo están asociados a una clase: aplicar uno cambia la clase del preflight.

**Todo el panel de verificación se subordina a la clase elegida** en la tarjeta de offset o en el configurador:

- **Nombre del destino:** es específico de la clase, para que ningún título diga «Empaques» cuando no lo es:

  | Clase | Nombre | Norma |
  |---|---|---|
  | Estucado / couché | Impresión Offset (Comercial y editorial · Estucado) | ISO 12647-2 · FOGRA39 / FOGRA51 · PDF/X-1a |
  | No estucado | Impresión Offset (Editorial y papelería · No estucado) | ISO 12647-2 · FOGRA47 / FOGRA52 · PDF/X-1a |
  | Papel prensa | Impresión Offset Coldset (Periódicos · Papel prensa) | ISO 12647-3 · ISOnewspaper26v4 · PDF/X-1a |
  | Cartulina / cartón | Impresión Offset (Empaques · Cartulina y cartón) | ISO 12647-2 · FOGRA39 / FOGRA51 · PDF/X-1a |
  | Sintético / plástico | Impresión Offset (Sintéticos · Polipropileno y acetato) | ISO 12647-2 · perfil según la tinta · PDF/X-1a |

  Tarjeta, pestaña, título de especificaciones, norma aplicada, alertas, catálogo, portada, ficha y JSON usan ese nombre. La descripción cita el ámbito de la clase, el acabado, la lineatura y el CTP. El gráfico instructivo se rotula como ejemplo fijo de empaque en cartulina a 175 lpi.
- **Checklist obligatorio:** resolución mínima y rango de la clase, y el perfil de conversión de la clase en los consejos de modo de color y en el perfil esperado.
- **Especificaciones:** límite de tinta del perfil de la clase (330 % FOGRA39, 300 % con FOGRA51 o en no estucados, 240 % en papel prensa; en sintéticos, 330 % por defecto a confirmar), sangrado de 3–5 mm, lineatura en el consejo de cuerpo mínimo y consejo de TAC con el perfil de la clase.
- **Verificación manual:** OutputIntent con el perfil de la clase; troquel en todas menos papel prensa; curva tonal previa en no estucados, ganancia ISO 12647-3 en prensa y tinta de oxidación o UV en sintéticos.
- **Norma aplicada y preajuste `.joboptions`:** perfil, TAC y resolución mínima de la clase.

Si hay un sustrato de offset aplicado desde el catálogo, su límite de tinta manda sobre el de la clase; al retirarlo vuelve el de la clase.

---

## Preflight de gigantografía

Módulo dentro de **Procesos de producción › Gran formato**. El configurador fija sustrato, uso, distancia de visión, escala del documento y confección de bordes; en pantallas DOOH, los píxeles de la pantalla y el pixel pitch.

| Sustrato | Uso y distancia | Entrada a 1:1 | Salida | Efectiva |
|---|---|---|---|---|
| Lona Frontlit (PVC opaca) | Mupi / marquesina · 1–3 m | 150 ppp | 300–720+ DPI | 100–150 ppp |
| | Valla de carretera · 10–16 m | 72–100 ppp | 300–720+ DPI | 5–15 ppp |
| Lona Mesh (microperforada) | Fachada / andamio · 5–50 m | 72 ppp | 300–600 DPI | 5–25 ppp |
| Lona ecológica PE | Valores de la frontlit (asumidos) | | | |
| Vinilo adhesivo | Escaparate / vehículo · 0,5–2 m | 150–300 ppp | 720–1440 DPI | 100–300 ppp |
| Soporte rígido (Dibond, PVC espumado) | Panel interior / stand · 0,5–1,5 m | 150–300 ppp | 600–1200 DPI | 150–300 ppp |
| | Mural exterior grande · 1,5–3 m | 72–100 ppp | 600–1200 DPI | 38–76 ppp |
| Pantalla LED (DOOH) | Píxeles exactos · RGB a 72 ppi | — | Pixel pitch 2,5–16 mm | 25,4 ÷ pitch |

La calificación es **Óptimo** si el archivo alcanza la entrada recomendada, **Apto** si alcanza la efectiva mínima, **Al límite** hasta un 10 % por debajo e **Insuficiente** por debajo.

Comprueba el tamaño y la resolución efectiva a tamaño final —a escala 1:N la del documento se divide por N—, la regla de 1:10 (300–720 ppp en el documento), CMYK obligatorio, Coated FOGRA39 / ISO Coated v2, TAC 320 %, PDF/X-1a:2001 o TIFF sin compresión (JPG rechazado), sangrado de 3–5 mm a tamaño final, 7 cm libres de texto en vainas y el cuerpo del texto a tamaño final. En DOOH: dimensiones exactas en píxeles y RGB.

Pestañas: **Verificación del arte** · **Sustratos** · **Resolución y distancia** (tabla de distancia de visión y tabla por tamaño) · **Escala de trabajo** (calculadora con el ejemplo de la valla de 8 × 3 m).

---

## Serigrafía industrial

Módulo dentro de **Procesos de producción › Textil › Serigrafía industrial** para verificar arte que va a generarse e insolarse como matriz serigráfica.

**Configurador.** Técnica (cuatricromía, proceso simulado, tintas planas, color indexado, PCB), soporte (algodón, sintético, rígido, PCB), fondo claro u oscuro, malla de 34 a 140 hilos/cm, colores previstos, método de leyenda PCB y efecto 3D. Al cambiar técnica o soporte se propone la malla recomendada.

**Parámetros calculados.** Lineatura (hilos/cm ÷ 2), número de pantallas, ángulos de trama, forma del punto, color y paso de malla, durómetro de rasqueta, emulsión y dictamen.

**Verificación del arte** contra esa combinación: malla y técnica, lineatura según malla, resolución ≥ 300 ppi, modo de color por técnica, tintas y pantallas, base blanca sobre fondo oscuro, texto a contornos y ≥ 6 pt, grosor de trazo ≥ 0,5 pt, detalle frente a malla, color de registro y método de leyenda PCB.

En PDF se mide el **grosor real de cada trazo** con la matriz de transformación vigente, y se detectan los trazos de grosor 0, que desaparecen en el fotolito.

**Diez pestañas** de parámetros técnicos: Verificación · Cuatricromía (calculadora malla → lineatura, roseta de ángulos, trama frente a malla) · Proceso simulado · Tintas planas · Color indexado (AM frente a FM) · PCB · Arte y fotolito (registro y reventado) · Mallas (simulador de detalle a escala real) · Grabado de pantalla (cuatro fases, emulsiones, exposición) · Rasquetas.

**12 verificaciones manuales** del proceso de grabado y un bloque opcional en la Ficha Técnica, activado por defecto al elegir la tecnología de serigrafía.

En PCB, **LPI** significa *Liquid Photo-Imageable*, no líneas por pulgada: la interfaz lo advierte.

---

## Referencia incluida

- **6 tablas de especificaciones técnicas** —offset, gran formato, DTG, DTF, sublimación y serigrafía— que indican cómo se comprueba cada parámetro.
- **17 sustratos en el catálogo** con perfil ICC, límite de tinta, lineatura, ganancia de punto y preajuste PDF, filtrados por el proceso activo. Al seleccionar uno, el motor de preflight y el mapa de tinta adoptan sus valores.
- **5 clases de sustrato offset** con acabados, entrada, CTP, lineatura y perfil, y un **checklist de producción** de 17 puntos.
- **6 sustratos de gran formato** con entrada, salida y efectiva por uso, y la **tabla de distancia de visión** de 5 filas.
- **4 preajustes PDF**: PDF/X-1a:2001, PDF/X-4:2010, PDF/X-3:2003 y ráster de alta densidad.
- **10 formatos de imagen** con descripción, uso, profundidad y compresión.
- **8 papeles comerciales** con resolución de entrada (ppi), salida digital (dpi), lineatura (lpi) y modo mapa de bits.
- **6 bandas de gigantografía por tamaño**, como referencia.
- **30 fichas de glosario** accesibles desde el botón «Glosario» y desde botones de ayuda repartidos por los paneles.
- **Gráfico instructivo** del destino offset: anatomía del pliego, carga de tinta, trama de 175 lpi y registro del negro, con siete llamadas explicadas.

---

## Salidas

| Salida | Contenido |
|---|---|
| **Ficha Técnica en PDF** | 7 bloques con logotipo institucional a color, dictamen, metadatos, los tres checklists, declaración de flujo con IA, norma aplicada y casillas de firma, más bloques opcionales de serigrafía industrial, preflight de gigantografía o clasificación offset con su checklist de producción |
| **Diagnóstico JSON** | Metadatos completos, análisis de píxel, checklists, sustrato, formato, perfiles del taller, calificación por papel, preflight de gigantografía, clasificación offset y verificación de serigrafía |
| **Captura PNG** | Vista actual del visor 3D |

---

## Límites conocidos

Se declaran en la propia interfaz, no sólo aquí.

- La cobertura de tinta y el modo de color derivados de píxel son **heurísticos**: no sustituyen una separación con perfil ICC ni la verificación en RIP. Sólo con canales de tinta reales (TIFF CMYK) la medición es directa.
- En PDF 1.5+ con flujos de objetos comprimidos, la lectura de cajas, fuentes y separaciones puede ser **parcial**. La app lo advierte cuando ocurre.
- La descomposición en capas del visor 3D es una **reconstrucción de diagnóstico** por análisis de imagen, no la separación interna del documento. La única capa que sale de datos reales del archivo es la de texto en PDF.
- El troquel se identifica como el contorno trazado de mayor extensión: hay que confirmar que esté en su propia tinta plana y marcado como no imprimible.
- TIFF con estructura en mosaicos, planar separado, 32 bits en coma flotante o JPEG interno: se leen los metadatos, no los píxeles.
- En serigrafía, el número de tintas planas y de colores dominantes es una **estimación** por cuantización del render; si el PDF declara separaciones, mandan las separaciones.
- El criterio de detalle frente a malla —que un trazo abarque al menos dos pasos de malla— es **orientativo** y así se indica.
- Las marcas de registro se infieren por el uso del color de registro (`/Separation /All`), no por su geometría.
- El mapa de tintas planas es una **estimación por color**: no ve la tinta en sobreimpresión ni bajo otros colores y puede marcar colores de proceso iguales. Sin función de tono legible (tipo 0 o comprimida) la tinta se lista pero no se sitúa. En PDF con objetos comprimidos puede haber tintas que el escaneo no vea, y del PSD sólo se leen los canales de la cabecera.
- En gran formato, la zona de seguridad se mide sobre las cajas de texto vivo del PDF: es **orientativa** y no ve los elementos gráficos. La lona PE usa los valores de resolución de la frontlit, porque la referencia no da los suyos. En DOOH no se verifica el formato del reproductor.
- Las referencias de resolución no siempre coinciden y la interfaz lo muestra: en gran formato, la tabla de distancia, la regla por sustrato y la tabla por tamaño; en offset, la clasificación por sustrato y la tabla de papeles comerciales, y la regla DPI ≥ LPI × 16 frente a las lineaturas altas a 2400–2540 dpi.
- Los parámetros de prensa y tirada (curvas, prueba de contrato, reología, densidad, registro) no se pueden medir en el archivo: son verificación manual.
- AI y EPS no se procesan. Exporta a PDF antes del preflight.
- HEIF/AVIF dependen del soporte del navegador.

---

## Dependencias

Cuatro librerías desde CDN, todas de terceros y sin telemetría:

| Librería | Uso |
|---|---|
| Tailwind CSS | Estilos de la interfaz |
| PDF.js 3.4.120 | Páginas, metadatos, texto e imágenes del PDF |
| Three.js r128 | Visor 3D de capas |
| anime.js 3.2.2 | Animación de cámara y separación de capas |

El resto —parsers de TIFF, JPEG, PNG, WebP, BMP, GIF, PSD e ICC, descompresores LZW y PackBits, motor de reglas, medición de tinta y extracción de contornos— está escrito para esta aplicación.

El logotipo institucional va embebido como PNG en base64, de modo que `index.html` funciona por sí solo.

---

## Estructura del archivo

`index.html` — 10121 líneas, ~708 KB. Organizado en 36 bloques numerados:

```
 0  Utilidades              11  Interfaz y eventos        22  Arranque de formatos
 1  Glosario                12  Arranque                  23  Imágenes colocadas
 2  Normas por destino      13  Catálogo de sustratos     24  Lector .joboptions
 3  Estado                  14  Capas de aplicación       25  Cobertura de tinta
 4  Parsers de cabecera     15  Visor 3D                  26  Troquel y demasías
 5  Análisis de píxel       16  Arranque de extensiones   27  Previsualización
 6  Carga y parsing         17  Catálogo de formatos      28  Glosario emergente
 7  Tamaño físico y PPI     18  Lector TIFF               29  Gráfico instructivo
 8  Motor de reglas         19  TAC sobre canales         30  Contornos 3D
 9  Renderizado             20  Biblioteca ICC            31  Arranque didáctico
10  Ciclo de evaluación     21  Formatos y sustratos      32  Serigrafía industrial
                                                      33  Procesos de producción
                                                      34  Preflight de gigantografía
                                                      35  Preflight de offset por sustrato
                                                      36  Tintas planas
```

---

## Créditos

Instituto de Educación Superior Pública «Diseño y Comunicación»
Docente: **Mg. Mario Quiroz**
Unidad didáctica de Producción y publicación de piezas gráficas · 2026 · Semana 1
