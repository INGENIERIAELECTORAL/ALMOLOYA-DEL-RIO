# Sistema Electoral · Almoloya del Río

Versión municipal basada en la organización y las vistas del repositorio electoral de Baja California: Resumen, Secciones, Colonias, Mapa, Metas, Resultados y Documentos. Conserva la navegación entre secciones, colonias, mapa y planos. La interfaz institucional es verde; las tablas mantienen los colores de los partidos.

Aplicación pública de consulta con React, TypeScript, Vite y Leaflet, preparada para GitHub y Vercel. Los datos viven en archivos del proyecto. No requiere cuentas, contraseñas, variables de entorno ni servicios de base de datos.

## Ejecutar y compilar

Requiere Node.js 22 LTS y npm.

```bash
npm ci
npm run dev
npm test
npm run build
npm run preview
```

`npm run build` valida TypeScript y genera `dist/`. La entrega incluye una compilación de producción y el código fuente. `npm run validate` ejecuta las pruebas y la compilación. `node scripts/build-data.mjs` reconstruye los datos de la aplicación a partir de las lecturas de origen conservadas en `sources/`.

## GitHub y Vercel

1. Descomprimir y subir **el contenido de la carpeta `almoloya-del-rio`** a un repositorio nuevo. Incluir `package-lock.json`, `src/`, `public/`, `sources/`, `tests/`, `scripts/` y las configuraciones. Excluir `node_modules/`.
2. Importar ese repositorio en Vercel. Si se sube una carpeta contenedora adicional, seleccionar `almoloya-del-rio` como Root Directory.
3. Framework: Vite. Install Command: `npm ci`. Build Command: `npm run build`. Output Directory: `dist`. Node.js: 22.x.
4. `vercel.json` incluye la redirección de rutas de la aplicación. Abrir directamente `/app/ambito/15006/mapa?seccion=151` debe cargar la consulta de la sección.
5. El mapa utiliza teselas de Stadia Maps con autenticación por dominio. En la cuenta de Stadia utilizada para el proyecto, autorizar el dominio final de Vercel o el dominio propio. No incluir claves privadas en el código. Los polígonos locales, resultados, colonias y selección siguen funcionando si el fondo no está disponible. El botón “Fondo Stadia” permite ocultarlo y volver a solicitarlo.

Esta entrega no crea por sí sola un repositorio remoto ni un despliegue en una cuenta de Vercel.

## Datos incluidos

Municipio 006 del Estado de México (clave compuesta 15006), secciones **151–155**, DF actual **23**, DL actual **29**. Los registros de 2021 conservan su DL histórico **7**. No se certifican límites históricos equivalentes ni se interpolan resultados.

| Elección | Válidos geográficos | No registrados | Nulos | Total geográfico |
| --- | ---: | ---: | ---: | ---: |
| Ayuntamiento 2021 | 5,152 | 769 | 174 | 6,095 |
| Diputaciones locales 2021 | 5,820 | 25 | 245 | 6,090 |
| Gubernatura 2023 | 5,977 | 0 | 165 | 6,142 |
| Ayuntamiento 2024 | 6,810 | 7 | 256 | 7,073 |
| Diputaciones locales 2024 | 6,744 | 27 | 292 | 7,063 |
| Presidencia 2024 | 6,720 | 3 | 146 | 6,869 |

Diputaciones locales 2024 también contiene una fila con sección **0**, dos votos válidos y dos votos totales: el archivo completo suma **7,065**, mientras que las cinco secciones cartografiables suman **7,063**. La fila no se reparte entre secciones. En Resultados se puede incluir explícitamente en el total del archivo y en la exportación CSV.

Padrón: **10,236**. LN actual aportada: **10,139**. No se indicó fecha de corte en el archivo; la interfaz lo informa. La meta 5 % suma **509**: redondear por sección y después sumar. La participación usa la LN histórica de cada elección, nunca la LN actual.

Tres colonias únicas: Almoloya del Río, Loma Alta y Tecalco. El Excel contiene siete relaciones colonia–sección y la cartografía ocho. Tecalco aparece en la sección 152 en el Excel y cruza 151 y 152 en la geometría. Ambas relaciones se consultan separadas y se conservan. La LN mostrada junto a una colonia corresponde a la sección completa: no es una estimación de la colonia. No se asignan votos por superficie ni se suman secciones repetidas por colonia.

Nueve enlaces a planos por sección: 151 (2), 152 (1), 153 (3), 154 (1), 155 (2). Los enlaces conservan el acceso que tengan configurado en Drive.

## Cálculos y vistas de voto

`src/electoral.ts` es el motor único para tablas, selección del mapa y referencias históricas. Cada elección tiene su propia configuración de candidaturas y columnas: una columna con el mismo nombre puede tener distinto significado según la elección.

- Resultado final: suma disjunta de columnas de una misma candidatura.
- Columnas de origen: votos individuales, marcas combinadas y candidatura común por separado. Es una vista alternativa, no se suma a la anterior.
- Candidaturas comunes: no se reparten entre partidos si la fuente no proporciona ese desglose. En Gubernatura 2023, los 3,503 votos de `PVEM_PT_MORENA` permanecen como un dato común. En Diputaciones locales 2024, la columna común es `CC_PVEM_PT_MORENA`.
- Votación total = válidos + no registrados + nulos. Las columnas de totales nunca se vuelven a sumar como partidos.
- Porcentaje de actor = votos del actor / válidos. Participación = total / LN de esa elección. Denominador cero: dato no disponible.
- Referencia competir = segundo lugar histórico; referencia ganar = primer lugar histórico + 1. Son referencias descriptivas, no pronósticos.

No se inventan nombres personales de candidaturas ni se realiza redistribución jurídica entre partidos. Las columnas sin votos que no integran candidaturas activas se conservan en el desglose completo y en el CSV.

## Cartografía y recuperación de errores

Se conservan los 55 archivos de capas suministrados: 23 contienen datos (421 elementos). Las capas vacías no aparecen en el selector. Los límites distritales están recortados al municipio. Los atributos originales están disponibles al activar las capas de detalle y pulsar sus elementos; los límites municipales/distritales no interceptan la selección de secciones.

Las coordenadas son longitud/latitud WGS84 (EPSG:4326). La selección usa `properties.seccion`, y los detalles resuelven `properties.id` desde el catálogo; `feature.id` es solamente un índice de dibujo. Se valida el universo exacto de cinco secciones antes de dibujar. Cada instancia del mapa cancela solicitudes pendientes y libera eventos y observadores al salir de la vista. Cambiar elección, actor o indicador actualiza el detalle sin sustituir la geometría.

El motor municipal sustituye el controlador DOM heredado del original. Las elecciones se cargan como datos estáticos versionados: los cambios entre elecciones no dependen de una petición remota ni pueden ser sobrescritos por respuestas tardías. Un fallo de capas adicionales o teselas no bloquea la geometría seccional; un fallo de la geometría muestra un estado de error con reintento.

## Pruebas de navegador

La suite de regresión `e2e/` cubre las seis elecciones, sección cero, candidaturas comunes, colonias, fallos de teselas y geometría, selección, montajes repetidos y pantalla pequeña. Para ejecutarla en un equipo con Chromium disponible:

```bash
npx playwright install chromium
npm run test:e2e
```

El resultado de las verificaciones efectivamente ejecutadas se detalla en `docs/VALIDACION.md`.

## Estructura

- `src/app`, `src/components`, `src/views`: navegación y vistas adaptadas del modelo original.
- `src/data`: catálogo municipal y seis elecciones generadas.
- `src/electoral.ts`: cálculos y conciliaciones.
- `src/map`: Leaflet, validación geográfica y gestión de recursos.
- `public/data/map`: cartografía municipal original.
- `public/data/derived`: índice de relaciones geográficas de colonias.
- `sources`: nueve archivos de origen, lecturas y verificaciones previas; no se copian a `dist/`.
- `tests`: pruebas de votos, metas, correspondencias territoriales y documentos.
- `docs/VALIDACION.md`: alcance y resultados de verificación de la entrega.

Los archivos originales no se sobrescribieron. La actualización de datos se realiza modificando las fuentes y su configuración explícita, regenerando y ejecutando las pruebas antes de compilar.
