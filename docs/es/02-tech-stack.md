# 02 · Stack técnico — de qué está hecho el sitio

[English](../en/02-tech-stack.md) · [简体中文](../zh/02-tech-stack.md) · [日本語](../ja/02-tech-stack.md) · [한국어](../ko/02-tech-stack.md) · [Deutsch](../de/02-tech-stack.md) · [Italiano](../it/02-tech-stack.md) · `Español`

## Framework

Next.js 16 con el App Router, React 19, TypeScript, Tailwind CSS. El estado de la herramienta de diseño vive en un único store de Zustand; la animación es Framer Motion, usado con moderación; las primitivas de UI vienen de Radix. El formateo lo llevan Biome más ESLint; ambos deben pasar antes de que algo llegue a producción.

Dos ajustes importan más que el resto del stack:

- **Server components primero.** Páginas de marketing, precios, páginas legales: todo renderizado en servidor, con JavaScript de cliente casi nulo. El bundle de cliente se concentra donde la interactividad lo justifica: la herramienta de diseño.
- **`output: 'standalone'`.** Cada artefacto de producción es un bundle de servidor autocontenido; es lo que mantiene pequeñas las imágenes Docker e idénticos los dos despliegues regionales (véase [05 · Despliegue](05-deployment.md)).

## El pipeline de renderizado

El bucle central, simplificado:

1. **Subida.** Una foto, 10 MB como máximo, solo JPEG/PNG/WebP — se valida en el cliente por UX y otra vez en el servidor, porque ahí manda la verdad.
2. **Creación de la tarea.** El cliente envía un tipo de habitación, hasta 4 IDs de estilo y/o una descripción en texto libre (máximo 500 caracteres), junto con la foto. Recibe un ID de tarea y queda haciendo polling.
3. **El ensamblado del prompt ocurre en el servidor.** Merece la pena subrayarlo: el cliente envía *solo IDs de estilo*. Los fragmentos de texto que convierten un estilo predefinido en un prompt para el modelo de imagen viven exclusivamente en el servidor y nunca entran en el bundle de cliente. Son el libro de recetas del producto.
4. **Generación.** Un motor de tareas enruta el trabajo hacia modelos de imagen de la familia Flux, servidos mediante APIs de proveedores alojados, detrás de un gateway interno muy ligero. El gateway existe para poder cambiar o asignar motores por funcionalidad sin tocar el código del producto — el lock-in de un proveedor es un riesgo de precios, así que se abstrae desde el primer día.
5. **Entrega.** Los pares antes/después se renderizan en la vista de resultados; cada render consume un crédito y un render fallido se reembolsa automáticamente — sin necesidad de abrir un ticket de soporte.

Las imágenes se posprocesan con sharp (redimensionar, codificar, limpiar metadatos) antes de almacenarlas. Subidas y resultados son privados por defecto: excluidos de los rastreadores en `robots.txt` *y* servidos con `X-Robots-Tag: noindex` en la capa de proxy, de modo que las fotos de los usuarios nunca acaban en la búsqueda de imágenes.

## Cuentas, créditos y pagos

- **Inicio de sesión.** Google (One Tap) y Microsoft OAuth, además de enlaces por correo. Las sesiones son JWT firmados (JOSE). No hay contraseñas nuestras que se puedan filtrar.
- **Créditos.** Un crédito es una unidad de renderizado. El saldo, los packs y los reembolsos viven en una pequeña capa de contabilidad junto a la base de datos; los renders fallidos se reembolsan automáticamente a nivel del motor de tareas.
- **Pagos.** El checkout internacional pasa por **Creem como merchant of record** — asume la carga fiscal y de cumplimiento del lado del pago, un intercambio razonable para un equipo pequeño que vende a escala global. La propiedad china usa un checkout independiente y localizado. Los handlers de webhook son idempotentes y verificados; nada en el camino de facturación confía en el cliente.

## Capa de datos

SQLite, al que se accede mediante una fina capa de acceso a datos tipada. Con dos despliegues aislados y un volumen de escritura modesto, una base de datos embebida elimina toda una superficie de operaciones (no hay servidor de base de datos que parchear, hacer copia de seguridad = copiar un archivo). Si y cuando el volumen de escritura diga lo contrario, la capa de acceso a datos es lo único que habrá que cambiar. El correo transaccional sale por SMTP a secas.

## Analítica y privacidad

La analítica es **autoalojada y sin cookies**. Sin rastreadores publicitarios de terceros, sin banner de consentimiento para los visitantes de la UE, sin reventa de datos — la decisión de analítica se tomó junto con la postura frente al GDPR, no después. Renunciamos a las comodidades de los embudos de conversión para tener una historia de privacidad que cabe en una frase, y para este producto ese intercambio es el correcto.

## Comprobaciones automáticas que vale la pena robar

- **Diffs HTML de golden master.** Antes de cualquier refactor que toque código de páginas, se graba el HTML renderizado de las páginas centrales; después del refactor, el diff debe estar vacío. Así se hizo la extracción del diccionario de seis locales sin una sola regresión visual.
- **Presupuestos de bundle.** Los chunks de cliente de la herramienta de diseño tienen techos de tamaño explícitos; los diccionarios y funcionalidades que los reventarían se dividen o se rechazan.
- **Aserciones de SEO como script.** Las entradas del sitemap, la reciprocidad de hreflang y las URLs canonical/OG (incluido el requisito de que «las URLs canonical deben devolver realmente 200») las comprueba un script, no un vistazo humano. Véase [04 · Manual de SEO](04-seo.md).

Siguiente: [03 · Arquitectura i18n](03-i18n.md)
