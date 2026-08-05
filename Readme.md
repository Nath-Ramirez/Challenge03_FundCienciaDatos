# Taller Práctico 03 — Centauros

**Curso:** Fundamentos en Ciencia de Datos — Maestría en Ciencia de Datos y Analítica, EAFIT

**Integrantes del equipo:**

| Nombre completo              | Cédula         |
| ---------------              | -------------- |
| Delvin José Rodriguez Jimenez| 1236422        |
| John Esteban Úsuga Duarte    | 1038926380     |
| Nathaly Ramirez Henao        | 1025641821     |
# Inteligencia Geo-Temporal y de Redes — TechLogistics S.A.

Análisis multidimensional desarrollado en Python (Jupyter Notebook) para el diagnóstico de series de tiempo, señales, redes y geoespacialidad de los activos críticos de **TechLogistics S.A.** (Cadena de frio y red eléctrica). Tiene pruebas de estacionariedad, filtrado de señales (FFT/Butterworth), análisis de grafos con NetworkX, visualización geoespacial con Plotly y modelado predictivo (Granger, ARIMAX) bajo la metodología **CRISP-DM**.

---

## Descripción del Problema

TechLogistics S.A. enfrenta un problema de visibilidad: los datos de la cadena de frío y de la red eléctrica están georreferenciados pero desconectados entre sí, generando puntos ciegos sobre cómo se propaga el ruido en la red de sensores, dónde se ubican los puntos críticos de calor y cuál es el pronóstico real de carga.

Este proyecto aborda y resuelve cinco frentes de análisis clave:

1. **Exploración Geo-Temporal y Clustering de Biomasa:**
   - Visualización geoespacial (`scatter_map`) de los sensores agroclimáticos del Oriente Antioqueño, codificando color por NDVI (`Agro_5`) y tamaño por Humedad (`Agro_1`).
   - Identificación de un clúster geográfico contiguo con biomasa consistentemente baja (NDVI bajo), en lugar de una distribución aleatoria.

2. **Estacionariedad y Naturaleza de las Series de Energía:**
   - Test de Dickey-Fuller Aumentado (ADF) sobre las 10 series de energía para clasificarlas en I(0) / I(1).
   - Ventana móvil (50 registros) sobre las series no estacionarias y diagnóstico de Drift vs. Random Walk puro en el Costo del Gas (`Ener_5`).

3. **Procesamiento de Señales — Ruido y Filtrado:**
   - Análisis espectral (FFT) y espectrogramas de la Generación Eólica (`Ener_4`), comparando la versión `clean` vs. `noise` para localizar la banda de frecuencias donde se concentra el ruido inyectado (SNR ∈ [5, 12] dB).
   - Filtro Butterworth pasa-bajo (orden 4) sobre la Humedad Relativa ruidosa (`Agro_3_noise`), con reducción sustancial del RMSE frente a la señal original al preservar la dinámica lenta de la serie.

4. **Topología de Red y Nodo Cuello de Botella:**
   - Construcción de grafos dirigidos (`NetworkX`) `Source_Node → Target_Node` para las redes de Energía y Agro, y cálculo de centralidad de grado y telemetría entrante ponderada.
   - Hallazgo de que ambas redes son **bipartitas dirigidas** (Source y Target no se solapan), por lo que el nodo crítico se identifica como el `Target_Node` que concentra el mayor volumen de telemetría entrante.

5. **Modelado y Toma de Decisiones (Falla del Nodo 214):**
   - **P1 — Causalidad y Redes:** Test de Causalidad de Granger entre Factor de Potencia (`Ener_10`) y Voltaje (`Ener_9`); sin evidencia de causalidad significativa en ninguna dirección.
   - **P2 — Optimización Geo-Agrónoma:** Relación entre zonas de NDVI bajo (tras filtrar el ruido GPS) y varianza del viento (`Agro_10`), con recomendación de inversión priorizada en riego de precisión y barreras vegetales.
   - **P3 — Analítica Predictiva (ARIMAX):** Modelo de Demanda (`Ener_1`) con Temperatura y centralidad del nodo de origen como exógenas; la centralidad no mejora el AIC (Δ = +0.08) ni es estadísticamente significativa, mientras que la temperatura sí aporta poder explicativo.

---

## Estructura del Repositorio

```text
.
├── data/
│   ├── agro_clean.csv                     # Sensores agroclimáticos (Oriente Antioqueño) — señal limpia
│   ├── agro_noise.csv                     # Sensores agroclimáticos — con ruido/jitter GPS inyectado
│   ├── ener_clean.csv                     # Red eléctrica nacional — señal limpia
│   └── ener_noise.csv                     # Red eléctrica nacional — con AWGN inyectado
├── Challenge_03_TechLogistics.ipynb   # Notebook documentado con las 4 fases del CRISP-DM
├── documents
│   └── Informe_Tecnico_Challenge03.pdf    # Informe ejecutivo con respuestas a las preguntas de negocio
└── README.md                              # Documentación general del proyecto
```

---

## Guía de Instalación y Ejecución Local

Sigue estos pasos para ejecutar el análisis en tu entorno local:

### 1. Clonar el repositorio
```bash
git clone https://github.com/Nath-Ramirez/Challenge03_FundCienciaDatos.git
cd Challenge03_TechLogistics
```

### 2. Crear y activar un entorno virtual
* **En Linux / macOS:**
  ```bash
  python3 -m venv venv
  source venv/bin/activate
  ```
* **En Windows:**
  ```cmd
  python -m venv venv
  venv\Scripts\activate
  ```

### 3. Instalar dependencias
```bash
pip install --upgrade pip
pip install numpy pandas networkx matplotlib seaborn plotly scipy statsmodels jupyter
```

### 4. Ejecutar el notebook
```bash
jupyter notebook Challenge_03_TechLogistics.ipynb
```
El notebook corre secuencialmente las cuatro fases: Data Understanding y Geo-Visualización, Procesamiento de Señales, Análisis de Grafos, y Modelado/Toma de Decisiones.

---


