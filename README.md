# Análisis Predictivo de Accidentes de Tráfico
### Evaluación Parcial 2 — SCY1101 Programación para la Ciencia de Datos
**Docente:** Mauricio González V.

---

##  Descripción del Proyecto

Este proyecto aplica técnicas de **Machine Learning supervisado y no supervisado** para analizar y predecir la gravedad de accidentes de tráfico. Utilizamos el dataset `accidentes_limpios.csv` con aproximadamente **100.000 registros** y **18 variables** de carácter temporal, ambiental, vial y tipológico.

**Pregunta central:** ¿Es posible predecir si un accidente de tráfico será grave basándose en las condiciones en que ocurrió?

---

## Integrantes del Grupo

[Diego Gonzalez] 
[Carlos Contreras] 
[Ricardo Leon] 

---

## Estructura del Repositorio

```
EP2-Machine-Learning/
│
├── 📓 Prueba_2.ipynb              ← Notebook principal (ejecutar de arriba a abajo)
├── 📊 accidentes_limpios.csv      ← Dataset (100.000 registros)
├── 📄 Informe_Tecnico_v2.docx     ← Informe técnico (12-15 páginas)
└── 📖 README.md                   ← Este archivo
```

---

## Modelos Implementados

### Aprendizaje No Supervisado
| Algoritmo | Configuración | Resultado |
|-----------|--------------|-----------|
| **K-Means** | K evaluado de 2 a 10 | K=2 óptimo (Silhouette Score: 0.2621) |

### Aprendizaje Supervisado
| Modelo | Accuracy | Precision | Recall | F1-Score |
|--------|----------|-----------|--------|----------|
| Árbol de Decisión (base) | 0.7152 | 0.3218 | 0.2932 | 0.3068 |
| **Árbol Optimizado (GridSearchCV)** | **0.7774** | **0.4580** | 0.1915 | 0.2700 |
| SVM kernel RBF | 0.7936 | 0.6140 | 0.1081 | 0.1838 |

>  **Nota sobre el SVM:** Se intentó optimizar con GridSearchCV (>40 min sin converger) y RandomizedSearchCV (>18 min sin converger). Limitación computacional documentada: el SVM tiene complejidad O(n²) a O(n³), inviable con 100.000 registros en Google Colab.

---

##  Optimización de Hiperparámetros

### GridSearchCV — Árbol de Decisión
```python
param_grid = {
    'criterion':          ['gini', 'entropy'],    # 2 valores
    'max_depth':          [3, 5, 10, 15],          # 4 valores
    'min_samples_split':  [2, 5, 10],              # 3 valores
    'min_samples_leaf':   [1, 2, 4]               # 3 valores
}
# Total: 72 combinaciones | cv=3 folds | scoring='f1'
```

**Mejores hiperparámetros encontrados:**
```
criterion = 'entropy'
max_depth = 15
min_samples_split = 2
min_samples_leaf = 1
Mejor F1 (CV) = 0.2731
```

---

## Variables Más Importantes

| Ranking | Variable | Importancia |
|---------|----------|-------------|
| 1° | TOTAL_VEHICULOS | 18.84% |
| 2° | HORA | 14.98% |
| 3° | TIPO_ACCIDENTE | 13.81% |
| 4° | MES | 12.64% |
| 5° | TIPO_VIA | 12.55% |

---

##  Requisitos e Instalación

### Dependencias
```bash
pip install pandas numpy matplotlib seaborn scikit-learn scipy
```

### Ejecución
1. Clonar el repositorio:
```bash
git clone https://github.com/[usuario]/EP2-Machine-Learning.git
cd EP2-Machine-Learning
```

2. Abrir el notebook:
```bash
jupyter notebook Prueba_2.ipynb
# o en Google Colab: subir el notebook y el dataset
```

3. Ejecutar todas las celdas de arriba a abajo (Runtime → Run All).

>  **Importante:** El dataset `accidentes_limpios.csv` debe estar en la misma carpeta que el notebook, o actualizar la ruta en la celda de carga de datos.

---

##  Conceptos Clave del Proyecto

### ¿Por qué StandardScaler?
Los algoritmos SVM y K-Means son sensibles a la escala de las variables. Sin escalar, variables con valores grandes (como TRAZADO_PLANTA) dominarían artificialmente sobre variables binarias (HAY_NIEBLA).

### ¿Por qué K=2 en K-Means?
Evaluamos el Silhouette Score para K entre 2 y 10. K=2 obtuvo el mayor valor (0.2621), identificando dos perfiles: accidentes bajo condiciones adversas vs. condiciones normales.

### ¿Por qué el Accuracy alto no es suficiente?
El dataset tiene un desbalance de 78.5% (clase 0) vs 21.5% (clase 1). Un modelo que prediga siempre la clase mayoritaria obtendría 78.5% de Accuracy sin detectar ningún accidente grave. Por eso usamos F1-Score y Recall como métricas principales.

### ¿Por qué GridSearchCV para el Árbol y no para el SVM?
El Árbol de Decisión es rápido de entrenar → GridSearchCV viable (72 combinaciones).  
El SVM tiene complejidad O(n²) → con 100.000 muestras cada entrenamiento tarda minutos → GridSearchCV y RandomizedSearchCV inviables.

---


## Conclusiones Principales

1. **El Árbol de Decisión base** fue el modelo más útil para detección de accidentes graves (mejor F1 y Recall).
2. **El SVM RBF** tuvo el mayor Accuracy pero el menor Recall, evidenciando el problema del desbalance de clases.
3. **La optimización con GridSearchCV** mejoró Accuracy y Precision pero redujo Recall, demostrando que optimizar hiperparámetros no siempre mejora todas las métricas simultáneamente.
4. **K-Means** identificó dos perfiles de accidentes: condiciones adversas del entorno vs. condiciones normales.
5. **TOTAL_VEHICULOS** es la variable predictora más importante (18.84%).

---

##  Referencias

- Pedregosa et al. (2011). Scikit-learn: Machine Learning in Python. *JMLR*, 12, 2825-2830.
- Cortes, C., & Vapnik, V. (1995). Support-vector networks. *Machine Learning*, 20(3), 273-297.
- Breiman et al. (1984). *Classification and Regression Trees*. Wadsworth.
- Rousseeuw, P.J. (1987). Silhouettes. *Journal of Computational and Applied Mathematics*, 20, 53-65.
- Bergstra, J., & Bengio, Y. (2012). Random search for hyper-parameter optimization. *JMLR*, 13(2), 281-305.

---

*Evaluación Parcial 2 — SCY1101 — Fecha de entrega: Martes 19/05/2026*
