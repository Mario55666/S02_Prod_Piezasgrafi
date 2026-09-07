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
2. Elige el destino de producción: **Offset (Empaques)** o **Textil (Estampados)**.
3. Arrastra el archivo a la zona de carga, pégalo con `Ctrl+V` o selecciónalo.
4. Introduce el **tamaño físico de reproducción** en el panel 2. Sin ese dato no existe resolución efectiva.
5. Opcional: aplica un **sustrato** del catálogo para que el límite de tinta y el perfil esperado se ajusten a él.
6. Revisa el dictamen, completa las verificaciones manuales y genera la **Ficha Técnica**.

La primera carga necesita conexión para traer las librerías desde CDN. Una vez cargada la página, el análisis funciona sin conexión.

---

## Destinos de producción

| Destino | Norma | Criterio |
|---|---|---|
| **Impresión Offset (Empaques)** | ISO 12647-2 · PDF/X-1a:2001 | 300 ppi, CMYK, TAC 330 %, sangrado 3 mm, texto ≥ 6 pt, trazo ≥ 0,25 pt |
| **Personalización Textil (Estampados)** | DTG · Sublimación · Serigrafía | Área 25 × 30 cm, 150 ppi (≥ 1476 × 1771 px), canal alfa, Pantone+ Solid Coated |

---

## Qué verifica

### Checklist obligatorio — 6 campos

Dimensiones finales · Resolución efectiva · Modo de color · Perfil ICC · Formato de exportación · Peso del archivo.

### Especificaciones del destino

Hasta 25 comprobaciones adicionales según el destino y el tipo de archivo: sangrado medido, demasías, página única, fuentes incrustadas, transparencias, cuerpo mínimo de texto, tintas planas, sobreimpresión, cobertura de tinta, negros neutros frente a compuestos, gama CMYK, canal alfa, halo de recorte, definición de borde, máscaras de línea y más.

### Verificación manual del operador

8 puntos por destino que la máquina no puede comprobar sola: troquel en sobreimpresión, pretratamiento DTG, poliéster ≥ 80 % blanco, prueba de lavado, marcas de registro, etc. Quedan registrados en la ficha técnica.

---

## Lectura de archivos

### Formatos reconocidos por firma binaria

No se confía en la extensión del archivo.

| Formato | Alcance |
|---|---|
| **PDF** | Cajas de página, PDF/X, OutputIntent, fuentes, tintas planas, transparencia, texto vivo, imágenes colocadas, contornos vectoriales |
| **TIFF** | IFD completo, LZW · PackBits · Deflate · sin comprimir, ICC incrustado, tintas declaradas, **TAC medido sobre canales reales** |
| **JPEG** | Marcadores SOF, JFIF/EXIF, ICC multi-segmento, APP14 Adobe |
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

Incluye histograma de distribución y botón «Norma» para volver al límite del sustrato activo.

En un TIFF CMYK la cobertura se **mide** sobre los canales de tinta. En cualquier otro origen es una **estimación heurística** con un modelo GCR ligero, y la interfaz lo indica.

### Troquel, sangrado y demasías

Sobre un PDF extrae los contornos trazados reales llevando la matriz de transformación, identifica el troquel y lo destaca. Mide el borde efectivo de la tinta y lo compara con TrimBox y BleedBox lado por lado:

- **Sangrado medido en el arte** — cuánta tinta hay realmente más allá del corte, que no siempre coincide con el BleedBox declarado
- **Demasías** — arte que rebasa el sangrado, señalado con trama diagonal

### Visor 3D de orden de apilación

Descompone el arte en las capas en que se aplica —sustrato, base blanca TiO₂, masas de color, imagen ráster, vectores, texto y troquel— y las apila en el orden de impresión. Cada capa hereda las incidencias del preflight que le corresponden. En PDF, la capa de texto son las cajas de texto reales, con los bloques por debajo del cuerpo mínimo marcados en rojo.

Órbita con arrastre, zoom con rueda, separación regulable, modo contornos, giro automático y captura PNG.

---

## Referencia incluida

- **13 sustratos** con perfil ICC, límite de tinta, lineatura, ganancia de punto y preajuste PDF. Al seleccionar uno, el motor de preflight y el mapa de tinta adoptan sus valores.
- **4 preajustes PDF**: PDF/X-1a:2001, PDF/X-4:2010, PDF/X-3:2003 y ráster de alta densidad.
- **10 formatos de imagen** con descripción, uso, profundidad y compresión.
- **8 sustratos** con resolución de entrada (ppi), salida digital (dpi), lineatura (lpi) y modo mapa de bits.
- **6 bandas de gigantografía**, seleccionadas automáticamente por el lado mayor del tamaño declarado.
- **14 fichas de glosario** accesibles desde el botón «Glosario» y desde botones de ayuda repartidos por los paneles.
- **Gráfico instructivo** del destino offset: anatomía del pliego, carga de tinta, trama de 175 lpi y registro del negro, con siete llamadas explicadas.

---

## Salidas

| Salida | Contenido |
|---|---|
| **Ficha Técnica en PDF** | 7 bloques con logotipo institucional a color, dictamen, metadatos, los tres checklists, declaración de flujo con IA, norma aplicada y casillas de firma |
| **Diagnóstico JSON** | Metadatos completos, análisis de píxel, checklists, sustrato, formato, perfiles del taller y calificación por sustrato y banda de gigantografía |
| **Captura PNG** | Vista actual del visor 3D |

---

## Límites conocidos

Se declaran en la propia interfaz, no sólo aquí.

- La cobertura de tinta y el modo de color derivados de píxel son **heurísticos**: no sustituyen una separación con perfil ICC ni la verificación en RIP. Sólo con canales de tinta reales (TIFF CMYK) la medición es directa.
- En PDF 1.5+ con flujos de objetos comprimidos, la lectura de cajas, fuentes y separaciones puede ser **parcial**. La app lo advierte cuando ocurre.
- La descomposición en capas del visor 3D es una **reconstrucción de diagnóstico** por análisis de imagen, no la separación interna del documento. La única capa que sale de datos reales del archivo es la de texto en PDF.
- El troquel se identifica como el contorno trazado de mayor extensión: hay que confirmar que esté en su propia tinta plana y marcado como no imprimible.
- TIFF con estructura en mosaicos, planar separado, 32 bits en coma flotante o JPEG interno: se leen los metadatos, no los píxeles.
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

`index.html` — 5707 líneas, ~420 KB. Organizado en 31 bloques numerados:

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
10  Ciclo de evaluación     21  Formatos y sustratos
```

---

## Créditos

Instituto de Educación Superior Pública «Diseño y Comunicación»
Docente: **Mg. Mario Quiroz**
Unidad didáctica de Producción y publicación de piezas gráficas · 2026 · Semana 1
