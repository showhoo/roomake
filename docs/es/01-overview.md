# 01 · Visión general — qué es Roomake y por qué estas notas son públicas

[English](../en/01-overview.md) · [简体中文](../zh/01-overview.md) · [日本語](../ja/01-overview.md) · [한국어](../ko/01-overview.md) · [Deutsch](../de/01-overview.md) · [Italiano](../it/01-overview.md) · `Español`

## El producto

Roomake ([www.roomake.top](https://www.roomake.top)) rediseña habitaciones reales a partir de una sola foto. El flujo cabe en una frase:

> Sube una foto de la habitación (JPEG/PNG/WebP, hasta 10 MB) → elige uno de los 6 tipos de habitación (sala de estar, dormitorio, cocina, baño, comedor, oficina en casa) → selecciona hasta 4 de los 34 estilos predefinidos, o describe con tus palabras el aspecto que buscas → recibe renders de antes/después.

No hay modelo 3D, ni plano, ni catálogo de muebles. La restricción es deliberada: el camino de foto a render es la ruta más corta desde «tengo una habitación» a «puedo verla de otra manera», y es el único que funciona para quien alquila y no puede medir ni modelar nada.

Los precios siguen el mismo minimalismo: créditos prepagados, un crédito por render y reembolso automático cuando un render falla. Sin créditos que caduquen y sin suscripción obligatoria para probar la herramienta.

## Dos propiedades, un código base

| | [www.roomake.top](https://www.roomake.top) | [www.roomake.cn](https://www.roomake.cn) |
|---|---|---|
| Público | Global | China continental |
| Idiomas | Inglés (por defecto) + `/zh`, con 日本語 · Deutsch · Français · 한국어 llegando progresivamente | Solo 简体中文 |
| Pagos | Checkout internacional (tarjetas de crédito, wallets) a través de un proveedor merchant of record | Checkout localizado para China continental |
| Datos | Base de datos y almacenamiento de medios propios | Base de datos y almacenamiento de medios propios — totalmente aislados |

Ambas propiedades se construyen a partir del mismo código base. Un único ajuste en tiempo de compilación decide qué locales recibe cada despliegue; el sitio chino no es una copia traducida servida desde los mismos servidores, sino un despliegue independiente con sus propios datos. Véase [03 · Arquitectura i18n](03-i18n.md) y [05 · Despliegue](05-deployment.md) para saber cómo funciona.

## Lenguaje de diseño

El sitio tira más hacia lo editorial que hacia lo app: tipografía serif en todas partes, generoso espacio en blanco, numeración estilo folio y renders presentados como láminas de un anuario de arquitectura. Los nombres de los estilos (Cream, Wabi Sabi, Modern Chinese…) se tratan como nombres propios y permanecen en inglés en todos los locales — solo sus subetiquetas poéticas se traducen. Es una decisión de marca y, de paso, mantiene un vocabulario de diseño consistente para la búsqueda.

## Por qué publicamos notas de construcción

Tres razones, en orden de honestidad:

1. **Construimos sobre código abierto.** El proyecto nació de un starter de Next.js de código abierto para productos de imágenes con IA. Publicar cómo lo extendimos es devolver el favor.
2. **Los estándares escritos aguantan mejor que la memoria tribal.** La mayor parte de lo que vas a leer —la política de hreflang, el orden de reversión, la regla de «graba la línea base antes de refactorizar»— existe porque alguna vez lo hicimos mal. Escribirlo es la forma de que se quede arreglado.
3. **Es marketing honesto.** Un equipo pequeño capaz de explicar su arquitectura con precisión es más creíble que uno que solo publica renders. Si esa credibilidad se convierte en usuarios, mejor.

## Lo que deliberadamente no documentamos

- Ubicaciones de servidores, endpoints, puertos y toda credencial o valor de entorno.
- Las interioridades del costo de renderizado: qué motor atiende a qué tier y con qué economía unitaria.
- Cualquier cosa del lado de administración del producto.

Estas notas describen la *forma* del sistema, no las llaves del mismo.

## Mapa del repositorio

Cinco documentos × siete idiomas:

```
docs/
├── en/  01-overview · 02-tech-stack · 03-i18n · 04-seo · 05-deployment
├── zh/  same five, in Simplified Chinese
├── ja/  same five, in Japanese
├── ko/  same five, in Korean
├── de/  same five, in German
├── it/  same five, in Italian
└── es/  same five, in Spanish
```

Siguiente: [02 · Stack técnico](02-tech-stack.md)
