# 📦 Contenedores para LaTeX

Obtén entornos de desarrollo LaTeX listos para usar:

- 🚀 **Instalación Mínima** (en `carpeta min`): Ideal para usuarios avanzados que deseen una configuración liviana y rápida. Perfecto si ya sabes cómo agregar paquetes según lo necesites.
  
- 🌟 **Instalación Completa** (en `carpeta full`): Un entorno completo para aquellos que buscan una solución llave en mano. Incluye paquetes extensivos, soporte para español y manejo de archivos EPS. Toma más tiempo en abrirse.

## 🛠 Uso Rápido

Para construir una de las imágenes Docker:

```bash
cd devcontainers/latex/ # 'min' para instalación mínima o 'full' para completa
docker build -t [nombre_de_tu_imagen] .
```

**Ejemplo:** Para la instalación completa:

```bash
cd full
docker build -t caefisica/latex-full .
```

💡 **Nota:** Estos Dockerfiles están optimizados para GitHub Actions y usan el directorio `workspace`. Si los usas en una configuración estándar de Linux, podrías enfrentar problemas.

## 🌐 Características Detalladas

### Instalación Mínima

- Configuración ligera de LaTeX.
- Incluye paquetes esenciales.

### Instalación Completa

- Configuración extensa de LaTeX.
- Soporte completo para español.
- Manejo integrado de archivos EPS.
