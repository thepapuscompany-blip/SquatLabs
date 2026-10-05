# SquatLab · Análisis bioinstrumental de la sentadilla

Interfaz web para evaluar la **profundidad de la sentadilla** a partir de un video (o un CSV) calculando el **ángulo de flexión de rodilla** (cadera–rodilla–tobillo).

Trabajo del ramo *Análisis Bioinstrumental del Movimiento Humano*, Departamento de Kinesiología, Facultad de Medicina, Universidad de Chile.

**Autores:** Hiroshi Milla · Vicente Sanchez

- 🌐 Interfaz pública: `https://<usuario>.github.io/<repositorio>/` *(reemplazar tras activar GitHub Pages)*
- 💻 Código fuente: `https://github.com/<usuario>/<repositorio>`

---

## 1. Problema y gesto

**Gesto:** sentadilla.
**Problema:** en rehabilitación de rodilla es frecuente que la profundidad alcanzada sea insuficiente o variable entre repeticiones, y se suele evaluar solo a ojo. SquatLab entrega una medición cuantitativa y repetible.

## 2. Variable analizada

**Ángulo interno de rodilla (°)**: ángulo entre los segmentos rodilla→cadera y rodilla→tobillo.
~170–180° = pierna extendida; valores menores = mayor flexión (flexión = 180° − ángulo).

Métricas derivadas por repetición: ángulo mínimo, tiempo de descenso, tiempo de ascenso, velocidad angular de descenso y rango de movimiento (ROM).

Clasificación de profundidad (criterio propio de la interfaz, no un estándar clínico validado):

| Ángulo mínimo medio | Categoría |
|---|---|
| ≤ 95° | Profunda / paralela |
| 96° – 115° | Parcial / media |
| > 115° | Limitada |

## 3. Requisitos

- Navegador moderno de escritorio o móvil (Chrome, Edge, Firefox o Safari recientes).
- **Conexión a internet** al analizar un video (se descargan desde CDN el modelo y el runtime de pose). El modo CSV / datos de demostración funciona sin descargar el modelo.
- No requiere instalar nada ni compilar.

## 4. Dependencias (cargadas por CDN, sin instalación)

| Recurso | Uso |
|---|---|
| `@mediapipe/tasks-vision` 0.10.14 (jsDelivr) | Estimación de pose en el navegador |
| Modelo `pose_landmarker_lite` (Google Storage) | Detección de 33 puntos corporales |

Todo el resto (gráficos, tablas, informe) es JavaScript, HTML y CSS propios dentro de `index.html`.

## 5. Ejecución

**Opción A: usar la versión publicada:** abrir el enlace de la interfaz pública.

**Opción B: local**

```bash
git clone https://github.com/<usuario>/<repositorio>.git
cd <repositorio>
python -m http.server 8000
# abrir http://localhost:8000 en el navegador
```

(También puede abrirse `index.html` directamente, pero se recomienda servirlo por HTTP.)

**Publicar en GitHub Pages:** *Settings → Pages → Build and deployment → Deploy from a branch → `main` / `(root)` → Save.* En 1–2 minutos queda disponible en `https://<usuario>.github.io/<repositorio>/`.

## 6. Tutorial de uso

1. **Entrada de datos**, a elegir:
   - *Cargar video* (o arrastrarlo al recuadro): MP4 H.264 recomendado.
   - *Cargar CSV*: dos columnas, tiempo (s) y ángulo de rodilla (°). Ver `data/ejemplo_sintetico.csv`.
   - *Datos de demostración*: genera 3 repeticiones simuladas.
2. **Parámetros:** lado analizado, suavizado (cuadros), umbral de descenso y umbral de ascenso.
3. Con video, presionar **Analizar video**: se ve el esqueleto y el ángulo en vivo sobre el video. *Detener* finaliza antes de tiempo.
4. **Resultados:** repeticiones, ángulo mínimo medio, ROM, gráfico interactivo (pasar el cursor/dedo), tabla por repetición, evaluación cualitativa y análisis kinesiológico básico.
5. Los sliders recalculan el análisis al instante.
6. **Generar informe (PDF)** abre una vista imprimible (permitir ventanas emergentes → *Guardar como PDF*). **Descargar CSV procesado** exporta la serie suavizada.

**Recomendaciones de registro:** cámara fija a 2–3 m, a la altura de la cadera, vista lateral (plano sagital) con la pierna analizada hacia la cámara, cuerpo completo visible y buena luz.

## 7. Procesamiento (resumen)

1. **Pose:** MediaPipe Pose Landmarker obtiene cadera (23/24), rodilla (25/26) y tobillo (27/28) en cada cuadro. Se descartan puntos con visibilidad < 0,35.
2. **Ángulo:** producto punto entre los vectores rodilla→cadera y rodilla→tobillo, `θ = arccos(u·w / |u||w|)`, en píxeles del video.
3. **Suavizado:** media móvil centrada de ventana ajustable, ignorando cuadros sin dato.
4. **Detección de repeticiones:** histéresis con dos umbrales. Una repetición comienza cuando el ángulo baja del umbral de descenso (por defecto 130°) y termina al volver sobre el umbral de ascenso (160°).
5. **Métricas:** mínimo por repetición, tiempos de descenso/ascenso, velocidad = (ángulo inicial − mínimo) / tiempo de descenso, ROM, desviación estándar entre repeticiones y tendencia primera–última repetición.

## 8. Archivos principales

```
├── index.html                 # Aplicación completa (HTML + CSS + JS)
├── data/ejemplo_sintetico.csv # CSV de ejemplo (datos simulados)
├── docs/guion_video.md        # Guion del video de presentación
├── README.md
├── LICENSE
└── .gitignore
```

## 9. Limitaciones

- El ángulo es una **proyección 2D**: depende de la posición de la cámara y no reemplaza la goniometría ni la evaluación clínica.
- La precisión de la pose disminuye con mala iluminación, ropa holgada u oclusión de la pierna.
- Los umbrales de profundidad son orientativos y no están validados contra un instrumento de referencia.
- El `.csv` de ejemplo es **sintético**, no corresponde a una persona real.

## 10. Licencia

MIT. Ver `LICENSE`.
