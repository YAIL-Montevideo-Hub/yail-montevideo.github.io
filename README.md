# YAIL Montevideo

Sitio web del hub de **Young AI Leaders** en Montevideo, Uruguay.
Parte de la red global YAIL, presente en más de 25 ciudades.

El sitio está construido con [Jekyll](https://jekyllrb.com/) y alojado en GitHub Pages.

## Ejecutar localmente

Requisitos: Ruby y Bundler.

```bash
bundle install
bundle exec jekyll serve
```

El sitio queda disponible en `http://localhost:4000`.

## Estructura

| Carpeta             | Contenido                                     |
| ------------------- | --------------------------------------------- |
| `_projects/`        | Proyectos del hub (Markdown con front matter) |
| `_data/`            | Datos estructurados (socios en `partners.yml`) |
| `_layouts/`         | Plantillas HTML (default, project)            |
| `_includes/`        | Componentes reutilizables (nav, hero, footer) |
| `_sass/`            | Estilos SCSS parciales                        |
| `assets/`           | Imágenes, JS, CSS                             |
| `.github/workflows/`| CI/CD con GitHub Actions                      |

## Agregar contenido

- **Proyecto nuevo**: crear un archivo `.md` en `_projects/` con el front matter indicado en [CONTRIBUTING.md](CONTRIBUTING.md).
- **Socio nuevo**: editar `_data/partners.yml` y agregar el logo en `assets/images/partners/`.

## Despliegue

Cada push a la rama `main` dispara el workflow `jekyll.yml`, que construye el sitio y lo publica en GitHub Pages automáticamente.

## Licencia

[GPL-3.0](LICENSE)
