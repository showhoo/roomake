# 04 · Manual de SEO — SEO técnico, datos estructurados y una política honesta de rastreadores de IA

[English](../en/04-seo.md) · [简体中文](../zh/04-seo.md) · [日本語](../ja/04-seo.md) · [한국어](../ko/04-seo.md) · [Deutsch](../de/04-seo.md) · [Italiano](../it/04-seo.md) · `Español`

## Un generador de metadatos, cero meta escritos a mano

El título, la descripción, el canonical, las tarjetas Open Graph/Twitter, el meta robots y el conjunto de hreflang de cada página salen de un único generador tipado. Nada se escribe a mano por página, y precisamente por eso la deriva de metadatos — canonical duplicados, hreflang ausentes, títulos degradados — no puede ocurrir en silencio. Si unos metadatos merecen existir, merecen generarse.

## Política de URLs y locales para SEO

- **Locales en subdirectorios** (`/ja`, `/de`…): las señales se consolidan en un solo dominio; los subdominios dividen la autoridad, los parámetros dividen el rastreo.
- **Sin redirecciones automáticas por `Accept-Language`.** Nunca. Le sirven a Googlebot la variante equivocada y parten las cachés de la CDN según el User-Agent. Hreflang más un selector de idioma visible es la pareja correcta; la redirección automática es la respuesta equivocada y tentadora.
- **Sin memoria de locale basada en cookies** para las páginas públicas: la primera visita debe ser reproducible por un rastreador.
- **`x-default` apunta al inglés** (la experiencia del idioma por defecto).

## hreflang: reciprocidad o nada

La regla que aplicamos: **hreflang solo conecta páginas que son verdaderos equivalentes 1:1, y cada miembro de un conjunto debe corresponderse.** Las páginas centrales (inicio, herramienta, precios) forman un clúster completo, generado y verificado por script.

El contenido editorial localizado *no* entra en el clúster: nuestras guías por idioma se escriben de forma nativa para cada mercado — temas distintos, enfoques distintos — así que no son equivalentes, y marcarlas como tales sería mentir a los motores de búsqueda con pasos adicionales. Las páginas localizadas no equivalentes llevan un self-canonical limpio y ningún `alternates`. Si la elección es entre hreflang incompleto y hreflang deshonesto, no elijas ninguno de los dos.

Las entradas del sitemap reflejan la misma política: alternates `xhtml:link` solo en URLs equivalentes, generados como una matriz de rutas × locales, con las páginas legales como entradas solo en EN.

## Higiene del canonical (un bug real que lanzamos)

Durante un tiempo, las páginas de inicio por locale producían canonical como `/ja/` — con barra final — cuando la forma canónica de las URLs del sitio iba sin barra. Cada canonical y cada `og:url` apuntaban a una redirección 308. Google Search Console lo reporta como «página con redirección», la dilución de señales duplicadas viene detrás, y en el navegador no se ve roto nada. Dos correcciones, ambas ya permanentes:

1. Normalizar las barras finales en el punto donde se construyen las URLs canonical (excepto la raíz `/`).
2. Un script de aserciones que resuelve cada URL canonical/OG y exige HTTP 200 — un canonical que termine en una redirección 3xx se trata como fallo de build.

## Datos estructurados, elegidos a conciencia

- **Incluidos**: `Organization` (con la razón social), `WebSite`, `SoftwareApplication` — con `availableLanguage` generado a partir de la lista de locales en producción, no hardcodeado. Cuando cambian los locales, el schema cambia con ellos.
- **Excluidos a conciencia**: los rich results de `FAQPage`. Google restringe los rich results de FAQ a sitios de reconocida autoridad; para un dominio nuevo, el marcado no rinde nada. Decide el schema por rendimiento esperado, no por lista de comprobación.

## SEO de imágenes

El diseño de interiores es una categoría de búsqueda visual. Cada render de muestra lleva texto `alt` localizado en cada locale, nombres de archivo descriptivos y entrega rápida. Es el canal de tráfico con efecto acumulativo más barato que tiene el producto después de la propia búsqueda.

## Rastreadores de IA: dales respuestas, no presupuesto de rastreo

`robots.txt` aplica una política de tres capas:

| Clase de agente | Permitido |
|---|---|
| Motores de búsqueda | Páginas públicas; subidas/resultados y áreas de cuenta no permitidas |
| Asistentes de IA (GPTBot, Claude, PerplexityBot, …) | `/llms.txt` y `/llms-full.txt` **únicamente** |
| Todos los demás | Lista pública de permitidos estándar, crawl-delay 1 |

La línea de IA es una postura, no un descuido: los bots orientados a LLM no necesitan rastrear un sitio de marketing, necesitan *hechos precisos y actuales*. Por eso publicamos `llms.txt` (un índice curado: qué es Roomake, cómo funciona la herramienta, 6 tipos de habitación × 34 estilos, modelo de precios, locales, enlaces oficiales) y `llms-full.txt` (la versión ampliada), y apuntamos a los rastreadores de IA exactamente hacia esos archivos. Cuando un asistente responde «qué es Roomake», preferimos que cite nuestro propio resumen a que reconstruya uno a partir de seis páginas en caché. Los archivos se generan a partir de los mismos metadatos de locale que el schema, así que ellos tampoco pueden derivar de la realidad.

## Palabras clave: nativas, no traducidas

Las palabras clave en inglés traducidas directamente tienen volumen de búsqueda cero. Cada mercado recibe una **lista semilla nativa** escrita para cómo busca realmente la gente allí, por ejemplo:

- **ja**: AI インテリア, インテリア AI, リフォーム プレビュー, 室内シミュレーション
- **de**: KI Inneneinrichtung, Wohnraum KI gestalten, Renovierung visualisieren
- **ko**: AI 인테리어, 집 꾸미기 AI, 리모델링 시뮬레이션

Las listas semilla aterrizan de forma orgánica en títulos/descripciones, en los temas de las guías por mercado y en los metadatos de locale — nunca como keyword stuffing, que los motores de búsqueda descuentan y los lectores castigan.

## Medición y verificación

- Google Search Console + Bing Webmaster Tools, por propiedad, con los sitemaps enviados en cada release.
- **Verifica desde la salida renderizada.** Las comprobaciones de cabeceras con `curl` pueden no coincidir con lo que los navegadores y los rastreadores ven realmente (etiquetas inyectadas, cambios de hidratación, capas de caché). Las aserciones se ejecutan contra el DOM/HTML renderizado, no solo contra el fuente.
- Dejando el ranking a un lado, los indicadores adelantados que vigilamos: páginas indexadas por locale, errores de hreflang en GSC, impresiones en búsqueda de imágenes y descargas de los archivos llms por parte de bots de IA.

## La privacidad gana al SEO donde debe

Las subidas de los usuarios y los resultados generados nunca aparecen en los resultados de búsqueda — no permitidos en `robots.txt` y con doble cobertura de `X-Robots-Tag: noindex` en el proxy. La foto del dormitorio de un usuario no es marketing de contenidos. Algunas páginas sencillamente no son para el índice.

## Lanzamiento suave: un flag para ocultar el sitio entero

Durante las pruebas privadas, un único flag `NOINDEX` en tiempo de compilación convierte `robots.txt` en `Disallow: /` para todo. El flag **falla hacia el lado seguro** (un release al que le falte su configuración de entorno oculta el sitio en lugar de exponerlo), y el paso a indexación pública se verifica leyendo el `robots.txt` en vivo inmediatamente después del release — la misma disciplina que cualquier interruptor de apagado (kill switch): fallar cerrado, verificar después de accionarlo.

Siguiente: [05 · Despliegue](05-deployment.md)
