# Almoloya del Río · proyecto completo

Este paquete contiene `src/`, `public/` y los archivos de la raíz necesarios para instalar y compilar la aplicación React con Vite. El código y los datos de las dos carpetas originales se conservan sin modificaciones.

## Subir a GitHub desde Windows

1. Extraer todo el ZIP a una carpeta nueva.
2. Abrir esa carpeta en el Explorador de Windows. Junto a `src` y `public` deben verse `package.json`, `package-lock.json`, `index.html`, `vite.config.ts`, `tsconfig.json` y `vercel.json`.
3. En GitHub, entrar en la raíz del repositorio y elegir **Add file → Upload files**. Si se abrió la ventana **Abrir**, cancelarla para volver al área de carga.
4. Desde el Explorador de Windows, arrastrar al área de GitHub los archivos de la raíz y la carpeta `src`. Confirmar con **Commit changes**.
5. Hacer una segunda carga arrastrando la carpeta `public` completa y confirmar otro commit. Se separan las cargas para respetar el máximo de 100 archivos simultáneos de GitHub.

Hay que arrastrar el contenido extraído, no el ZIP ni la carpeta exterior que lo contiene. Al terminar, `package.json`, `src` y `public` deben estar al mismo nivel en la raíz del repositorio.

Si el repositorio ya contiene `src` y `public` de este mismo paquete, basta con subir los nuevos archivos de raíz. No es necesario borrar carpetas existentes.

## Vercel

| Ajuste | Valor |
|---|---|
| Framework Preset | Vite |
| Root Directory | Raíz del repositorio, `./` |
| Install Command | `npm ci` |
| Build Command | `npm run build` |
| Output Directory | `dist` |
| Node.js | 22.x o 24.x |

`vercel.json` contiene los comandos, la salida y la reescritura de las rutas `/app/…` a `index.html` para permitir recargar directamente las vistas de la aplicación.

Si se está actualizando un proyecto de Vercel existente, revisar que no tenga una carpeta raíz distinta configurada ni un comando de compilación anterior guardado en sus ajustes.

La compilación comprueba TypeScript antes de generar la web. El mapa usa Stadia para el fondo y los GeoJSON locales para las capas municipales. La configuración de dominio de Stadia es independiente de la compilación; el proyecto no incorpora credenciales.

## Uso local

Con Node.js 22.12 o superior, hasta la rama 24:

```bash
npm ci
npm run dev
```

Para generar y revisar la versión de producción:

```bash
npm run build
npm run preview
```

El archivo `package-lock.json` fija las versiones que se instalaron y verificaron. No subir `node_modules` ni `dist` al repositorio: Vercel los genera durante la instalación y compilación.

## Verificación de esta entrega

Se comprobó una instalación limpia con `npm ci`, el análisis completo de tipos con `tsc --noEmit` y la compilación de producción con Vite. Se verificó que los 102 archivos originales de `src/` y `public/` coinciden byte por byte con el ZIP anterior y que los archivos públicos se copian a `dist/`.

Referencias:

- https://vite.dev/guide/
- https://vercel.com/docs/frameworks/frontend/vite
- https://docs.github.com/en/repositories/working-with-files/managing-files/adding-a-file-to-a-repository
