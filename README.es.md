# Roomake — Notas de construcción

[English](README.md) · [简体中文](README.zh-CN.md) · [日本語](README.ja.md) · [한국어](README.ko.md) · [Deutsch](README.de.md) · [Italiano](README.it.md) · [Español](README.es.md)

**Roomake** ([www.roomake.top](https://www.roomake.top)) es una aplicación web de diseño de interiores con IA: subes una foto de una habitación, eliges un tipo de habitación y hasta cuatro estilos (o escribes tu propia descripción), y recibes renders de antes/después. La opera RooMake Teams.

Este repositorio **no** contiene el código fuente del producto. Documenta cómo está construido el sitio —la arquitectura, la solución multilingüe, las decisiones de SEO, la disciplina de despliegue— y lo hace en siete idiomas. Lo escribimos en parte para nosotros mismos y en parte porque no dejábamos de responder a las mismas preguntas: cómo un equipo pequeño lanza un producto de imágenes con IA basado en créditos, con i18n y SEO de verdad.

Si estás construyendo algo parecido —generación de imágenes con IA, créditos, pagos, SEO multilingüe— estas notas deberían ahorrarte más de un callejón sin salida. Cada práctica descrita aquí está realmente en producción, incluidos los errores que nos enseñaron por qué.

## Qué contiene

| Documento | Qué cubre |
|---|---|
| [01 · Visión general](docs/es/01-overview.md) | Qué es Roomake, la forma del producto y las dos propiedades (.top / .cn) |
| [02 · Stack técnico](docs/es/02-tech-stack.md) | Next.js 16, el pipeline de renderizado, créditos y pagos, la capa de datos |
| [03 · Arquitectura i18n](docs/es/03-i18n.md) | Seis locales sobre un solo código base, política de hreflang, fuentes CJK, páginas legales localizadas |
| [04 · Manual de SEO](docs/es/04-seo.md) | SEO técnico, datos estructurados, `llms.txt` y nuestra política de rastreadores de IA |
| [05 · Despliegue](docs/es/05-deployment.md) | Docker, flags de alcance (scope) en tiempo de compilación, caché y disciplina de reversión |

Cada documento está disponible en **English · 简体中文 · 日本語 · 한국어 · Deutsch · Italiano · Español** — cambia de idioma mediante la línea de idiomas de la parte superior de cada archivo.

## De un vistazo

| | |
|---|---|
| Producto | Rediseño de habitaciones con IA: 6 tipos de habitación × 34 estilos predefinidos, mezcla hasta 4 estilos o describe lo que buscas en texto libre |
| Frontend | Next.js 16 (App Router), React 19, TypeScript, Tailwind CSS, con prioridad a los server components |
| Renderizado | Modelos de imagen de la familia Flux mediante APIs de proveedores alojados, detrás de un gateway interno muy ligero |
| Pagos | Créditos prepagados, un crédito por render, reembolso automático de los renders fallidos; Creem (merchant of record) a nivel internacional |
| Idiomas del producto | English + 简体中文 en línea · 日本語 / Deutsch / Français / 한국어 llegando a continuación |
| Idiomas de la documentación | EN · ZH · JA · KO · DE · IT · ES |
| Despliegue | Docker (build standalone) detrás de un proxy inverso y una CDN; dos despliegues regionales independientes |

## Enlaces

- Producto: [www.roomake.top](https://www.roomake.top) · China continental: [www.roomake.cn](https://www.roomake.cn)
- Preguntas o correcciones sobre estas notas: `support@roomake.top`

## Licencia

El texto de este repositorio se publica bajo licencia [Creative Commons Attribution 4.0](LICENSE). Tradúcelo, adáptalo y reutilízalo con libertad — la atribución se agradece, aunque no se exige más allá de los términos de la licencia.
