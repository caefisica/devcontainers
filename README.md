
# 📦 Contenedores

Bienvenidos al repositorio de `devcontainers` de @caefisica. Aquí encontrarás configuraciones de contenedores para las principales herramientas que utilizamos en nuestros proyectos, facilitando así un entorno de desarrollo uniforme y confiable.

---

## Contenidos

1. [Dockerfile para LaTeX](#-dockerfile-para-latex)

---

## 📄 Dockerfile para LaTeX

Perfecto para aquellos que necesitan compilar documentos en LaTeX sin preocuparse por configuraciones y dependencias.

**Uso:**

```bash
docker build -t caefisica/latex ./latex
docker run --rm -v $PWD:/workdir caefisica/latex latexmk documento.tex
```
