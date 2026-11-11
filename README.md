# ConnectaTel: análisis de uso y segmentación de clientes

Proyecto de análisis exploratorio de datos con Python para estudiar el comportamiento de los clientes de una empresa de telecomunicaciones en Latinoamérica, a partir de información registrada hasta 2024.

## 🎯 Objetivo del proyecto

Integrar información de planes, clientes y registros de uso para identificar patrones de llamadas y mensajes, detectar problemas de calidad y valores atípicos, y construir segmentos que apoyen el diseño de planes y las estrategias de satisfacción y retención.

## 💡 Preguntas del negocio

- ¿Qué segmentos de clientes muestran mayor o menor uso de llamadas y mensajes?
- ¿Qué usuarios presentan valores atípicos que ameriten investigar comportamientos inusuales, posibles errores o fraude?
- ¿Cómo varía el uso según la edad y el tipo de plan contratado?
- ¿Qué patrones pueden ayudar a diseñar mejores planes, optimizar la oferta y mejorar la satisfacción del cliente?

Un valor atípico es una señal para investigar; por sí solo no demuestra fraude ni un error de registro.

## 📚 Objetivos de aprendizaje

1. Trabajar con tres fuentes de datos y limpiar la información necesaria para el análisis.
2. Aplicar validación, estandarización de tipos de datos y detección de valores inconsistentes.
3. Construir un perfil estadístico de llamadas y mensajes por cliente y segmento demográfico.
4. Detectar valores atípicos mediante métodos estadísticos y visualizaciones.
5. Crear segmentaciones por edad y comportamiento de uso, y explorar su ampliación geográfica.
6. Visualizar diferencias entre segmentos y extraer conclusiones comerciales.
7. Documentar el proceso en un Jupyter Notebook y un README con instrucciones de reproducción.

**Alcance geográfico:** el objetivo formativo contempla la segmentación por país. La versión actual contiene `city`, pero no una columna de país, y únicamente implementa segmentos por edad y uso. Para analizar países se necesita incorporar y validar una correspondencia entre ciudades y países.

## 🗂️ Datasets utilizados

| Archivo de entrada | Contenido | Tamaño inicial |
| --- | --- | --- |
| `plans.csv` | Planes Básico y Premium: tarifa mensual, mensajes, minutos y GB incluidos, y precios por excedentes. | 2 filas × 8 columnas |
| `users_latam.csv` | Identificador del cliente, nombre, edad, ciudad, fecha de registro, plan y fecha de cancelación. | 4,000 filas × 8 columnas |
| `usage.csv` | Identificador del evento y del usuario, tipo de servicio, fecha, duración de llamada y longitud de mensaje. | 40,000 filas × 6 columnas |

El notebook carga los tres archivos. Los registros de `usage.csv` se agregan por `user_id` y se combinan con `users_latam.csv` mediante una unión izquierda. La tabla de planes se utiliza para contextualizar la oferta; no se incorpora mediante un `merge` al perfil final.

### Archivos del proyecto

- **Notebook:** `S7 Version-Estudiante-Project-ConnectaTel (2).ipynb`.
- **Salida generada:** `ladb_mobility_economy_2024_clean.csv`, con el perfil agregado y los segmentos de los clientes.
- **Documentación:** `README.md`.

La copia exportada puede incluir un sufijo como `(2)` en su nombre. El archivo de salida no sustituye a los tres CSV originales: estos son necesarios para reproducir la limpieza y el análisis completo.

## 🔎 Etapas del análisis

1. **Carga y exploración:** revisión de dimensiones, tipos de datos, primeras filas y estadísticas descriptivas.
2. **Diagnóstico de calidad:** cuantificación de nulos, identificación de edades inválidas, ciudades con `?` y fechas posteriores al corte de 2024.
3. **Limpieza:** sustitución del valor de edad `-999` por la mediana utilizada en el notebook, conversión de `?` a nulo y marcado de fechas futuras como `NaT`. Revisión de ausencias de duración y longitud según el tipo de servicio.
4. **Perfil por cliente:** cálculo de cantidad de mensajes, cantidad de llamadas y minutos acumulados; integración con los datos de usuarios.
5. **Exploración visual y outliers:** histogramas por plan, boxplots y cálculo del límite superior mediante `Q3 + 1.5 × IQR`.
6. **Segmentación:** clasificación por edad y nivel de uso, seguida de gráficos de distribución.
7. **Interpretación ejecutiva:** síntesis de hallazgos y recomendaciones comerciales en el apartado final del notebook. La versión de estudiante incluye un espacio para completar este texto.
8. **Exportación y documentación:** generación del CSV agregado y preparación del proyecto para GitHub.

### Reglas de segmentación

| Variable | Segmento | Regla |
| --- | --- | --- |
| Edad | Joven | Menor de 30 años |
| Edad | Adulto | De 30 a 59 años |
| Edad | Adulto mayor | 60 años o más |
| Uso | Bajo uso | Menos de 5 llamadas y menos de 5 mensajes |
| Uso | Uso medio | Menos de 10 llamadas y menos de 10 mensajes, excluyendo bajo uso |
| Uso | Alto uso | Al menos 10 llamadas o al menos 10 mensajes, para clientes con datos de consumo |

Las reglas de uso se aplican a los totales acumulados del periodo observado, no al consumo mensual ni a los límites de cada plan.

## 🛠️ Herramientas

- Python 3.
- Pandas y NumPy para manipulación y análisis de datos.
- Matplotlib y Seaborn para visualizaciones.
- Jupyter Notebook o Google Colab para ejecutar y documentar el análisis.

## ▶️ Cómo ejecutar el notebook

### Opción 1: Google Colab

1. Abre [Google Colab](https://colab.research.google.com/) y carga el archivo `.ipynb`; también puedes abrirlo desde GitHub cuando esté publicado.
2. Obtén los tres CSV originales del proyecto: `plans.csv`, `users_latam.csv` y `usage.csv`.
3. Ejecuta esta celda al inicio para subirlos y colocarlos en la ruta que utiliza el notebook:

```python
from pathlib import Path
from google.colab import files

data_dir = Path('/datasets')
data_dir.mkdir(parents=True, exist_ok=True)

uploaded = files.upload()
required = {'plans.csv', 'users_latam.csv', 'usage.csv'}
missing = required - set(uploaded)
if missing:
    raise FileNotFoundError(f'Faltan archivos: {sorted(missing)}')

for name in required:
    (data_dir / name).write_bytes(uploaded[name])
```

4. Conserva la celda original de carga:

```python
plans = pd.read_csv('/datasets/plans.csv')
users = pd.read_csv('/datasets/users_latam.csv')
usage = pd.read_csv('/datasets/usage.csv')
```

5. Ejecuta las celdas en orden, desde las importaciones hasta la exportación. Si falta alguna biblioteca, instala las dependencias con:

```python
%pip install pandas numpy matplotlib seaborn
```

6. Revisa los gráficos, completa el análisis ejecutivo y descarga el resultado:

```python
files.download('ladb_mobility_economy_2024_clean.csv')
```

Los archivos subidos deben volver a cargarse si se pierde el almacenamiento temporal de la sesión de Colab.

### Opción 2: ejecución local

1. Descarga el proyecto y coloca los tres CSV originales en una carpeta `datasets` junto al notebook.
2. Instala las dependencias y abre Jupyter:

```bash
python -m pip install pandas numpy matplotlib seaborn notebook
python -m notebook
```

3. Abre el notebook desde la carpeta del proyecto y cambia las rutas absolutas de la celda de carga por rutas relativas:

```python
from pathlib import Path

data_dir = Path('datasets')
plans = pd.read_csv(data_dir / 'plans.csv')
users = pd.read_csv(data_dir / 'users_latam.csv')
usage = pd.read_csv(data_dir / 'usage.csv')
```

4. Reinicia el kernel y ejecuta todas las celdas en orden.

## 🔁 Guía breve de reproducción

1. Utiliza los mismos tres archivos de entrada y conserva una copia sin modificar.
2. Verifica sus dimensiones iniciales contra la tabla de datasets de este README.
3. Ajusta las rutas al entorno elegido y ejecuta el notebook desde un kernel limpio.
4. Comprueba que la unión conserve los 4,000 clientes y que las métricas y gráficas se generen sin errores.
5. Revisa los valores nulos y las reglas de segmentación antes de interpretar los resultados.
6. Confirma que se haya generado `ladb_mobility_economy_2024_clean.csv` en el directorio de trabajo.
7. Guarda el notebook con sus resultados y registra cualquier cambio de lógica. Para documentar las versiones utilizadas, puedes ejecutar `python -m pip freeze > requirements.txt` desde el entorno local.

Las versiones de las bibliotecas no están fijadas en el notebook original. Por ello, la reproducción exacta del entorno requiere registrar las versiones con las que se ejecute.

## ⚠️ Consideraciones para interpretar los resultados

- **Cliente sin consumo asociado:** la versión revisada tiene un cliente sin registros de uso que la función original asigna a `Alto uso` al comparar valores nulos. Debe clasificarse como `Sin registros` antes de utilizar los segmentos comercialmente. Esto reduce el grupo de alto uso de 279 a 278 clientes.
- **Minutos de llamadas:** la agregación original suma `duration` sin filtrar por `type`. Se identificaron mensajes con duración registrada; conviene validarlos y sumar únicamente las duraciones de llamadas antes de emitir conclusiones definitivas sobre minutos.
- **Ausencias estructurales:** una duración nula en un mensaje o una longitud nula en una llamada no representa necesariamente un dato perdido. Una fecha de cancelación vacía indica ausencia de cancelación registrada.
- **Periodo de observación:** los totales acumulados no deben compararse directamente con beneficios mensuales. Para evaluar el ajuste de los planes se requiere consumo por cliente y mes activo.
- **Alcance comercial:** no se analiza consumo real de GB, facturación ni costos. El mayor uso o una tarifa superior no demuestran por sí solos mayor rentabilidad.
- **Outliers:** se conservan como posibles comportamientos reales, sujetos a revisión; no se eliminan automáticamente.

Estas observaciones documentan mejoras pendientes de la versión revisada. Aplicarlas puede cambiar algunos resultados respecto a las salidas guardadas en el notebook.
