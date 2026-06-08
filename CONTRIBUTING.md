# Guía para actualizar el contenido del sitio

Este archivo es solo para uso interno — no se muestra en el sitio web.

---

## Agregar o editar un proyecto

Cada proyecto es un archivo Markdown en la carpeta `_projects/`. Al hacer build, Jekyll genera automáticamente una página en `/proyectos/nombre-del-archivo/`.

**Para crear un proyecto nuevo**, creá un archivo `.md` en `_projects/` con este formato:

```markdown
---
layout: project
title: "Nombre del proyecto"
description: "Descripción breve que aparece en la tarjeta de la página principal."
status: "En curso"    # Opciones: "En curso", "Próximamente", "En desarrollo", "Finalizado"
tags:
  - "Etiqueta 1"
  - "Etiqueta 2"
---

## Sobre el proyecto

Contenido largo en Markdown. Podés usar títulos, listas, links, etc.
```

El nombre del archivo determina la URL: `_projects/mi-proyecto.md` → `/proyectos/mi-proyecto/`

**Para editar un proyecto existente**, simplemente editá el archivo correspondiente en `_projects/`.

---

## Agregar o editar un socio

Editá el archivo `_data/partners.yml`. Cada socio tiene esta estructura:

```yaml
- name: "Nombre de la organización"
  logo: "/assets/images/partners/nombre-del-logo.svg"
  url: "https://..."
```

**Pasos para agregar un socio:**
1. Agregá el archivo del logo en `assets/images/partners/` (SVG preferido, también funciona PNG/JPG).
2. Agregá la entrada en `_data/partners.yml` con la ruta correcta al logo.

El logo se muestra en escala de grises por defecto y a color al pasar el mouse.

---

## Agregar miembros del equipo (sección futura)

Si en el futuro se agrega una sección de equipo, seguirá el mismo patrón:
creá `_data/team.yml` con campos como `name`, `role`, `bio`, `photo`, `linkedin`.
El include correspondiente iterará sobre esos datos.

---

## Correr el sitio localmente

Necesitás tener Ruby y Bundler instalados.

```bash
bundle install
bundle exec jekyll serve
```

El sitio quedará disponible en `http://localhost:4000`.

---

## Publicar cambios

Simplemente hacé push a la rama `main` del repositorio.
GitHub Pages detecta los cambios y reconstruye el sitio automáticamente en pocos minutos.

---

## Notas técnicas

- Los archivos YAML son sensibles a la indentación. Usá espacios, no tabs.
- Para verificar que el YAML es válido podés usar [yamllint.com](https://www.yamllint.com/).
- Si el sitio no se actualiza después de un push, revisá la pestaña **Actions** en el repositorio de GitHub para ver el log de construcción.
