# Laboratorio 1: Aplicación de Minería de Datos en Desarrollo de Software Seguro
**Universidad de las Fuerzas Armadas ESPE**  
**Departamento de Ciencias de la Computación**  
**Asignatura:** Desarrollo de Software Seguro  

---

## 1. Contexto y Objetivo
El objetivo de este laboratorio es desarrollar un **modelo predictivo de vulnerabilidades** capaz de clasificar código fuente como *Seguro* o *Vulnerable* en un entorno de CI/CD. 

Siguiendo estrictas restricciones de seguridad y diseño:
1.  **Prohibición de LLMs Generativos:** No se utiliza GPT/Claude para generar código o respuestas. La inteligencia reside en algoritmos de Minería de Datos Tradicional (**XGBoost, Random Forest**).
2.  **Arquitectura Multi-Agente (Ensemble):** Implementamos un sistema de decisión híbrido que combina:
    *   **Agente Estructural (CPU):** Analiza la complejidad matemática del código (Métricas de Halstead, Complejidad Ciclomática).
    *   **Agente Semántico (GPU):** Analiza el contexto y patrones del lenguaje usando representaciones vectoriales (Embeddings de CodeBERT) procesadas por Gradient Boosting.
3.  **Interpretabilidad:** El modelo final explicará sus decisiones resaltando las líneas peligrosas mediante valores SHAP.

---

## 2. Metodología SEMMA
El desarrollo se rige por el proceso iterativo SEMMA (Sample, Explore, Modify, Model, Assess) promovido por el SAS Institute.

### 🔹 Fase 1: Sample (Muestreo)
**Objetivo:** Recolectar y unificar un dataset masivo y diverso que cubra múltiples lenguajes (C, C++, Java, Python, PHP).
*   **Fuentes:** 
    *   *CodeXGLUE (Microsoft):* Para detección profunda de defectos en C/C++.
    *   *CVEFixes (NVD):* Vulnerabilidades reales mapeadas a CVEs.
    *   *Vulnerability Fix Dataset:* Pares exactos de código roto vs. arreglado.
*   **Acción:** Descarga, validación y unificación en un `Dataset Maestro`.

### 🔹 Fase 2: Explore (Exploración)
**Objetivo:** Comprender la naturaleza de los datos.
*   **Acción:** Análisis estadístico de la longitud del código, distribución de clases (Safe vs Vuln) y detección de lenguajes predominantes. Visualización de patrones iniciales.

### 🔹 Fase 3: Modify (Modificación e Ingeniería de Características)
**Objetivo:** Transformar texto crudo en representaciones numéricas aptas para los agentes.
*   **Para el Agente Estructural:** Extracción de métricas estáticas usando librerías como `radon`.
*   **Para el Agente Semántico:** Generación de Embeddings densos usando **CodeBERT** (como encoder, no generador) acelerado por GPU (CUDA).

### 🔹 Fase 4: Model (Modelado)
**Objetivo:** Entrenar el sistema Multi-Agente.
*   **Entrenamiento:** 
    *   Entrenamiento paralelo de **Random Forest** (en CPU) y **XGBoost** (en GPU).
    *   Configuración de un **Voting Classifier** (Juez) que pondera las decisiones de ambos agentes.

### 🔹 Fase 5: Assess (Evaluación)
**Objetivo:** Validar la robustez del modelo para producción.
*   **Métricas:** Precisión, Recall, F1-Score y matriz de confusión.
*   **Explicabilidad:** Generación de reportes visuales con **SHAP** para identificar qué tokens dispararon la alerta de vulnerabilidad.

---