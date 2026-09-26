# Portfolio — Verónica Varela

Sitio personal de portfolio de Verónica Varela (Lead Product Designer). Página única, bilingüe (ES/EN).
Repo: https://github.com/vvarelamortola/portafolio-vero-varela

## Estructura

```
index.html        # Todo el sitio: export empaquetado de Claude Design (~427 KB, una sola línea larga por bloque)
uploads/
  resume.pdf      # CV que descarga el botón "Descargar CV"
  *.jpg           # Avatares de testimonios (Brad, Carlos, Leonardo, Martin, Seba, Su, Valentina)
```

No hay build, `package.json` ni dependencias locales. Se abre `index.html` en el navegador y listo.

## Cómo está armado `index.html`

Es un **bundle autocontenido exportado desde Claude Design**, no HTML escrito a mano:

- El `<head>`/`<body>` externos son solo un cargador ("Unpacking...") que al cargar desempaqueta el contenido.
- `<script type="__bundler/template">` — string JSON con el HTML real de la página (título, favicon, secciones, estilos inline y la lógica). Usa sintaxis de plantilla propia: `{{ t.about.title }}`, `<sc-for>`, `<sc-if>`, `<image-slot>`.
- `<script type="__bundler/manifest">` — recursos embebidos en base64 (p. ej. la foto de perfil en WebP), referenciados por UUID.
- `<script type="__bundler/ext_resources">` — dependencias externas: React 18.3.1 y ReactDOM desde unpkg.
- Los textos ES/EN viven en un diccionario de traducciones dentro del template (`t.nav`, `t.hero`, `t.about`, …); el idioma se cambia en runtime.

Secciones (anclas): `#top` (hero), `#about`, `#experience`, `#skills`, `#education`, `#portfolio`, `#testimonials`, `#contact`.

- Portfolio: tarjetas que enlazan a proyectos de Behance; las imágenes se cargan desde el CDN de Behance (`mir-s3-cdn-cf.behance.net`).
- Contacto: el formulario arma un `mailto:` (no hay backend); también hay links a WhatsApp y LinkedIn.

## Cómo editar

- **Cambios de texto** (ES/EN): decodificar el JSON del bloque `__bundler/template`, editar, y volver a serializarlo con `json.dumps` en el mismo lugar. Editar siempre **ambos idiomas**.
- **Cambios grandes de diseño**: se hacen en Claude Design y se re-exporta. Al re-exportar hay que **volver a aplicar** a mano, tanto en el `<head>` externo (lo que leen buscadores y previews de links, que no ejecutan JS) como dentro del template (que reemplaza el documento al cargar):
  - `<title>`, favicon "VV" y `lang="es"` en `<html>` (ver commits `3391521`, `86106cd`)
  - meta `description`, `author`, Open Graph (`og:*`) y `twitter:*`
- **CV**: reemplazar `uploads/resume.pdf` manteniendo el nombre.
- Los assets en `uploads/` se referencian con rutas relativas, así que el sitio debe servirse con `uploads/` al lado de `index.html`.

## Convenciones

- Mensajes de commit en inglés, en imperativo, indicando `(ES/EN)` cuando el cambio toca ambos idiomas.
- No commitear `.DS_Store`.
