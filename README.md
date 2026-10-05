# SquatLab - Análisis bioinstrumental de la sentadilla

Interfaz web que evalúa la sentadilla a partir de un video (o un CSV de ángulos) usando estimación de pose con MediaPipe. Calcula el **ángulo de flexión de rodilla** (cadera-rodilla-tobillo), detecta repeticiones y entrega el ángulo mínimo (profundidad), el rango de movimiento y los tiempos de descenso/ascenso derivados de esa curva.

Asignatura: Análisis Bioinstrumental del Movimiento Humano, Depto. de Kinesiología, Universidad de Chile.
Autores: [NOMBRE(S)]

## Enlace público
[PEGAR AQUÍ la URL de GitHub Pages, p. ej. https://usuario.github.io/squatlab/]

## Requisitos
- Navegador moderno (Chrome, Edge o Firefox actualizados).
- Conexión a internet la primera vez: se descargan MediaPipe Tasks Vision 0.10.14 (cdn.jsdelivr.net) y el modelo `pose_landmarker_lite` (storage.googleapis.com).
- No requiere instalación, servidor ni compilación. Todo el procesamiento ocurre en el navegador; los videos no se suben a ningún servidor.

## Dependencias
Solo MediaPipe `@mediapipe/tasks-vision@0.10.14`, cargada por CDN dentro de `index.html`. Sin npm.

## Archivos principales
- `index.html`: aplicación completa (interfaz, adquisición, procesamiento, gráfico).
- `ejemplo_demo.csv`: datos **sintéticos** para probar la entrada por CSV.
- `README.md`: este archivo.

## Ejecución
**Opción A (en línea):** abrir el enlace público.

**Opción B (local):** abrir una terminal en esta carpeta y ejecutar `python3 -m http.server 8000`; luego visitar `http://localhost:8000`. (El módulo de MediaPipe puede fallar abriendo el archivo con doble clic por restricciones de `file://`).

**Publicar en GitHub Pages:** crear repositorio público, subir `index.html`, ir a Settings > Pages > Deploy from branch > `main` / root. La URL queda disponible en 1-2 minutos.

## Uso
1. Presionar **Cargar video** (o **Cargar CSV**, o **Datos de demostración**).
2. Ajustar parámetros: lado analizado (izquierda o derecha), suavizado, umbrales de descenso (<130° por defecto) y ascenso (>=160°).
3. Presionar **Analizar video**. Se dibujan piernas sobre el video y se registra el ángulo de ambas rodillas.
4. Al terminar se muestran repeticiones, tabla por repetición y gráfico ángulo-tiempo. Los parámetros se pueden cambiar después y el análisis se recalcula al instante.
5. **Descargar CSV procesado** exporta las series suavizadas.

## Procesamiento
1. Landmarks 23/25/27 (izq.) y 24/26/28 (der.); se descartan cuadros con visibilidad < 0.5.
2. Ángulo de rodilla del lado seleccionado, en píxeles (corrige la proporción del video) con el producto punto entre los vectores rodilla-cadera y rodilla-tobillo. 180° = extensión completa.
3. Promedio móvil centrado (ventana configurable) ignorando datos faltantes.
4. Repeticiones: máquina de estados; inicia el descenso en el último punto sobre el umbral de ascenso, el fondo es el mínimo mientras se está bajo el umbral de descenso, y termina al volver sobre el umbral de ascenso.
5. Métricas por repetición, todas derivadas del ángulo: ángulo mínimo, rango de movimiento (ángulo inicial - mínimo) y tiempos de descenso y ascenso.

## Limitaciones
Ángulo 2D proyectado (depende de la posición de la cámara); la estimación de pose tiene error respecto de sistemas de captura de movimiento; los umbrales de interpretación son orientativos y no están validados clínicamente. No es una herramienta diagnóstica.
