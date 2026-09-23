# Segmentación y Agrupamiento de la Producción Mundial de Carne (K-Means vs. DBSCAN)

Pipeline de Aprendizaje No Supervisado (*Unsupervised Learning*) desarrollado en Python y Scikit-Learn para identificar patrones temporales, regímenes de volumen y anomalías en la cantidad anual de animales terrestres sacrificados a nivel mundial.

---

## 📌 Descripción del Proyecto

El conjunto de datos registra las series históricas de producción y faena animal anual provenientes de la FAO (*Food and Agriculture Organization* / *Our World in Data*). 

Este proyecto aplica un flujo de preprocesamiento y comparación entre dos enfoques clásicos de agrupamiento:
1. **K-Means:** Método basado en partición centroidal y distancias euclidianas.
2. **DBSCAN:** Método basado en densidad espacial capaz de aislar datos atípicos y puntos de ruido (*noise points*).

Ambos métodos se evalúan cuantitativamente a través del **Silhouette Score** y se contrastan mediante visualizaciones comparativas.

---

## 📂 Estructura de Datos

El repositorio incluye el conjunto de datos en la carpeta `data/`:

| Columna Original | Columna Procesada | Tipo | Descripción |
| :--- | :--- | :--- | :--- |
| `Entity` | `Entity` | Categórico | País, región o agregación geográfica. |
| `Code` | *(Eliminada)* | Categórico | Código de país ISO. |
| `Year` | `Year` | Numérico | Año del registro. |
| `Meat, total \| 00001765...` | `Animals_Slaughtered` | Numérico | Total de animales sacrificados en ese período. |

---

## ⚙️ Arquitectura del Pipeline

El flujo de trabajo se organiza en los siguientes pasos:

1. **Limpieza e Ingesta:** Renombrado sintáctico de variables extensas y descarte de metadatos no predictivos.
2. **Escalado de Atributos:** Implementación de `MinMaxScaler` dentro de una rutina estandarizada para equilibrar el rango temporal (`Year`) con magnitudes exponenciales (`Animals_Slaughtered`).
3. **Clustering Particional (K-Means):**
   * Configuración de $k = 4$ clusters (identificado previamente mediante el método del codo / *Elbow Method*).
   * Inicialización con `k-means++` para estabilizar la convergencia.
4. **Clustering Basado en Densidad (DBSCAN):**
   * Hiperparámetros base: `eps = 0.05`, `min_samples = 5`.
   * Identificación y etiquetado automático de valores de ruido/anomalías (`label = -1`).
5. **Evaluación de Cohesión y Separación:**
   * Cálculo de **Silhouette Score** para K-Means sobre la partición completa.
   * Silhouette Score filtrado en DBSCAN (excluyendo el conjunto de ruido para no penalizar artificialmente la densidad interna).
6. **Visualización Diagnóstica:** Gráficos de dispersión lado a lado con Seaborn y Matplotlib.

---

## 🛠️ Instalación y Requisitos

Clona el repositorio e instala el entorno de dependencias:

```bash
git clone [https://github.com/tu-usuario/meat-production-clustering-analysis.git](https://github.com/tu-usuario/meat-production-clustering-analysis.git)
cd meat-production-clustering-analysis
pip install -r requirements.txt

```
Requisitos principales:
python >= 3.8

pandas

numpy

scikit-learn

matplotlib

seaborn

🚀 Ejecución
Para reproducir el pipeline y generar los gráficos comparativos:
python main.py

Algoritmo,Configuración,Tratamiento de Ruido,Silhouette Score
K-Means,"k=4, k-means++",Sin detección de ruido,Calculado en ejecución
DBSCAN,"eps=0.05, min_samples=5",Puntos -1 aislados,Calculado en ejecución

🔍 Observaciones Técnicas
Manejo de escalas temporales vs. magnitud: Las magnitudes en cabezas de ganado difieren en varios órdenes respecto al eje temporal (Year). El uso riguroso de escalado relativo (MinMaxScaler) es indispensable para evitar que la variable con mayor valor absoluto domine por completo el cálculo de distancias euclidianas.

Comportamiento de DBSCAN: Dado que los datos presentan densidades heterogéneas entre países de gran escala y pequeños territorios, DBSCAN actúa tanto como segmentador como detector de anomalías de alta productividad.
