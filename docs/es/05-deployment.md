# 05 · Despliegue — Docker, flags de alcance, caché y reversión

[English](../en/05-deployment.md) · [简体中文](../zh/05-deployment.md) · [日本語](../ja/05-deployment.md) · [한국어](../ko/05-deployment.md) · [Deutsch](../de/05-deployment.md) · [Italiano](../it/05-deployment.md) · `Español`

## Dos despliegues, una receta de imagen

Producción son dos despliegues independientes del mismo código base:

- **roomake.top** — alcance de locale global, checkout internacional, base de datos y almacenamiento de medios propios.
- **www.roomake.cn** — alcance solo zh, checkout para China continental, datos totalmente aislados.

No comparten nada en tiempo de ejecución. Un mal release en uno no puede tocar al otro; un bug de datos no puede cruzar. El costo es mantener dos pipelines, y el costo vale la pena — el aislamiento regional es a la vez un argumento de cumplimiento y un argumento de radio de explosión.

## Los flags en tiempo de compilación son un contrato

Todo lo que varía entre despliegues es un flag en tiempo de compilación —alcance (scope) de locale, índice activado/desactivado, URLs públicas—, incrustado en el bundle durante `next build`. Tres reglas mantienen esto seguro:

1. **Cambiar un flag significa reconstruir, no reiniciar.** Cambiar variables de entorno en tiempo de ejecución no hace silenciosamente nada sobre las constantes ya incrustadas — la forma clásica de «lanzar» exactamente nada.
2. **El flag peligroso falla cerrado.** El interruptor `NOINDEX` está *activado* por defecto: un release a cuyo archivo de entorno le falta una línea produce un sitio oculto, nunca uno accidentalmente público.
3. **Las claves de entorno pasan una allowlist, dos veces** — una en las declaraciones `ARG` del Dockerfile, otra en los build args del archivo compose. Una variable que no pasa ambas puertas sencillamente no está en el build; no hay un tercer camino para que la configuración se cuele.

El ritual de release, en orden: `cat` del archivo de entorno contra la checklist → build → deploy → inmediatamente `curl` del `robots.txt` en vivo, el sitemap y una URL canonical. Treinta segundos de paranoia evitan los dos desastres clásicos (sitio en noindex por accidente; sitio exponiendo por accidente un build de previsualización).

## Topología de caché

- **Recursos estáticos** (chunks de JS, fuentes, imágenes): cabeceras de caché con vencimiento lejano en el borde de la CDN, nombres de archivo con hash de contenido — caché eterna, se invalida al no reutilizar jamás los nombres.
- **HTML**: TTL corto en el borde. Las páginas de marketing cambian poco, pero cuando cambian, la corrección debe verse en minutos, no cuando expire la caché global. Las correcciones urgentes pasan por una purga de caché explícita, un procedimiento documentado y ensayado — no un remedio casero.
- **Superficies dinámicas** (la herramienta, las páginas de cuenta): nunca cacheadas en el borde; cabeceras seguras para sesión establecidas en la aplicación.

Las fuentes se autoalojan en tiempo de compilación, así que la entrega de fuentes nunca depende de la disponibilidad de un tercero ni añade una ida y vuelta DNS entre orígenes al first paint.

## Golden masters antes de los refactors

El hábito más valioso del pipeline de despliegue es una disciplina de grabación: **antes** de tocar código que renderiza páginas, graba el HTML renderizado de las páginas centrales como golden masters (uno por alcance de locale); **después**, exige que el diff esté vacío. «A mí me se ve igual» no es un test de regresión. La línea base debe grabarse desde el código *viejo* — grabarla después del refactor es grabar tu bug como estándar.

## La checklist de release (condensada)

1. Archivo de entorno auditado contra la allowlist (ambas puertas: `ARG` del Dockerfile, args del compose).
2. Diffs de golden master en verde (todos los alcances activos).
3. Comprobación de presupuesto de bundle en las rutas interactivas.
4. Deploy → smoke test en vivo: `robots.txt` (estado de indexación correcto), `/sitemap.xml` (matriz de locales correcta), una URL canonical por locale que devuelva 200 y sin redirecciones 308 por barra final.
5. Analítica/heartbeat verificados desde una sesión limpia.
6. Sitemap reenviado si cambiaron rutas; caché purgada si el release era un arreglo que los usuarios están esperando.

## La escalera de reversión, en orden creciente

1. **Flags a nivel de funcionalidad** — apaga la funcionalidad, conserva el release.
2. **Flag de alcance/entorno + rebuild** — revierte el despliegue a un conjunto de comportamiento anterior (por ejemplo, retirar un locale).
3. **Mapas 301 a nivel de proxy** — antes de eliminar cualquier ruta que un motor de búsqueda pueda haber indexado, instala redirecciones. Los borrados llegan después de las redirecciones, nunca antes.
4. **Imagen anterior** — redespliega el último build conocido como bueno; los datos están separados, así que las imágenes son intercambiables.

La regla permanente detrás de la escalera: *una reversión jamás debe convertir una URL indexada en un 404.* Las redirecciones absorben el valor; con los 404 no se lo regala a nadie.

## Observabilidad y copias de seguridad

- Analítica autoalojada y sin cookies para las métricas de producto; logs del servidor enviados fuera de la máquina para depuración; comprobaciones de uptime en `/` y la ruta de la herramienta.
- Copias de seguridad: archivo de base de datos y medios de usuario, a diario, hacia un almacenamiento que *no* es la máquina que sirve el tráfico — y con la restauración probada, porque una copia de seguridad sin probar es una esperanza, no una copia de seguridad.

## Lo que le diríamos a un equipo que empieza de cero

1. Añade el generador de metadatos y las aserciones de sitemap/hreflang **antes** de la semana de lanzamiento — incorporar las tuberías de SEO a posteriori es una miseria.
2. Haz que el interruptor de apagado (kill switch) de noindex exista desde el primer día y que su valor por defecto sea seguro.
3. Mantén los motores/proveedores detrás de una interfaz interna ligera; todos los proveedores de IA prometen disponibilidad y ninguno promete estabilidad de precios.
4. A pequeña escala, dos despliegues regionales aislados le ganan a un sistema multitenant ingenioso.
5. Documenta la construcción. Tu yo futuro, y tus compañeros de equipo, lanzarán más rápido gracias a ello.

Estas notas: [README](../../README.es.md) · [01 · Visión general](01-overview.md) · [02 · Stack técnico](02-tech-stack.md) · [03 · i18n](03-i18n.md) · [04 · SEO](04-seo.md)
