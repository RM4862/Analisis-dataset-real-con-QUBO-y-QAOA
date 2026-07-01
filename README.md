# Optimización Logística Cuántica: Asignación de Fiscalías Territoriales en la CDMX mediante QAOA (4x4)

## Dataset
* **Nombre del dataset:** Carpetas de investigación de la Fiscalía General de Justicia de la CDMX.
* **Fuente oficial o confiable:** Portal de Datos Abiertos de la Ciudad de México.
* **Institución responsable:** Fiscalía General de Justicia de la CDMX (FGJ-CDMX).
* **URL de la fuente:** https://datos.cdmx.gob.mx/
* **URL raw del CSV usado en data/:** https://github.com/RM4862/Analisis-dataset-real-con-QUBO-y-QAOA.git
* **Licencia o condiciones de uso:** Licencia Abierta de Uso de Datos de la CDMX.
* **Fecha de consulta:** Junio 2026.
* **Dominio del problema:** Despliegue logístico, seguridad pública y asignación óptima de recursos ministeriales uno-a-uno.

## Modelado
* **Conjunto A (Demanda / Alcaldías):** A1 (CUAUHTEMOC), A2 (IZTAPALAPA), A3 (GUSTAVO A MADERO), A4 (BENITO JUAREZ).
* **Criterio de selección para A:** Se seleccionaron automáticamente mediante Pandas las 4 alcaldías con mayor volumen histórico de carpetas de investigación dentro del dataset de más de un millón de registros.
* **Conjunto B (Oferta / Agencias):** B1 (Fiscalía C - Cuauhtémoc), B2 (Fiscalía I - Iztapalapa), B3 (Fiscalía GAM - Gustavo A. Madero), B4 (Fiscalía BJ - Benito Juárez).
* **Criterio de selección para B:** Unidades de coordinación territorial del Ministerio Público asociadas directamente a los núcleos geográficos de alta demanda.
* **Definición de $x_{ij} = 1$:** La Fiscalía del Sector $b_j$ asume la responsabilidad operativa y el procesamiento prioritario de las carpetas de investigación de la Alcaldía $a_i$.
* **Interpretación de $x_{ij} = 0$:** No existe vinculación operativa exclusiva entre la fiscalía y la alcaldía en este bloque de asignación.

## Matriz de score
* **Columnas usadas:** `alcaldia_hechos`, `categoria_delito`, `latitud`, `longitud`.
* **Fórmula exacta de $S_{ij}$:** $S_{ij} = (\text{Gravedad Real}_i \times 5) - \text{Distancia Euclidiana}_{ij}$
* **Normalización aplicada:** La gravedad real se calculó como el ratio porcentual de delitos de alto impacto (con violencia, homicidios, robo de vehículo) sobre el total de la alcaldía. La distancia se calculó usando las medias geométricas de latitud y longitud escaladas a kilómetros (factor $\times 111$).
* **Matriz S 4x4:** Filtra de forma nativa los pesos combinatorios, destacando acoplamientos fuertes en la diagonal (ej. Iztapalapa con Fiscalía I: 52.42; GAM con Fiscalía GAM: 54.72).

## Restricciones
* **Restricción por filas:** Cada Alcaldía debe tener asignada exactamente una Fiscalía coordinadora para evitar la dispersión de carpetas y el vacío institucional.
* **Restricción por columnas:** Cada Fiscalía asume la carga prioritaria de una sola alcaldía crítica para prevenir la saturación de los Ministerios Públicos.
* **Justificación de Matching Bipartito:** Modela un emparejamiento perfecto uno-a-uno donde dos conjuntos disjuntos (Alcaldías y Fiscalías) deben vincularse de forma exclusiva sin cruces internos.
* **Justificación de QUBO:** Al mapear el objetivo cuadrático de maximización de beneficio logístico y restarle las penalizaciones cuadráticas de igualdad de las restricciones, el Hamiltoniano penaliza energéticamente los estados inválidos, permitiendo buscar el mínimo global en un espacio cuántico de 16 qubits.

## Resultados
*(Ejecuta tu cuaderno una última vez con MAXITER=50 y copia aquí los valores finales de la tabla de la Sección 30)*
* **Solución clásica exacta (Fuerza Bruta):** [Pega la asignación óptima y su score]
* **Resultado QAOA local (Mejor Muestra):** [Pega el bitstring y score cuántico]
* **Comparación clásico vs QAOA local:** [Especifica si QAOA local alcanzó el estado basal óptimo o si requirió de la reparación híbrida]

## Ética y limitaciones
* **Riesgos éticos:** Automatizar la asignación de Ministerios Públicos basándose solo en volumen puede desatender zonas vulnerables con baja tasa de denuncia (subregistro).
* **Medidas de mitigación:** Este modelo es de carácter estrictamente educativo y de exploración algorítmica. No utiliza datos personales de víctimas, imputados, CURP ni expedientes privados; la información está anonimizada y agregada geográficamente a nivel macro.
* **Limitaciones del modelo:** El esquema $4 \times 4$ es una simplificación rígida. El despliegue policial real requiere un enfoque multi-capacidad (un Ministerio Público atiende múltiples sectores dinámicos), lo cual rompe el matching bipartito simple.

## Ejecución
1. El archivo original pesado procesa los datos y exporta automáticamente la subinstancia a `data/dataset_real_4x4.csv`.
2. Abre `proyecto_final_ss26.ipynb` en Google Colab.
3. Asegúrate de tener el entorno con las librerías científicas instaladas y ejecuta `Runtime -> Run all` para generar el análisis variacional.