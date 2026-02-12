
# 📊 Informe de Análisis Exploratorio de Datos (EDA) 

## 📖 1. Introducción y Carga de Datos ##

Este proyecto consiste en un Análisis Exploratorio de Datos (EDA) y la creación de un Dashboard interactivo sobre el rendimiento anual de una pizzería. El objetivo principal es identificar patrones de consumo, picos de demanda y eficiencia para optimizar la toma de decisiones operativa. La carga se realizó desde un archivo CSV, verificando la integridad de los registros para asegurar una manipulación fluida de la información en las tablas dinámicas.

  🌐 **Origen de los Datos:** [Kaggle - Pizza Sales Dataset](https://www.kaggle.com/datasets/nextmillionaire/pizza-sales-dataset/data)

## 🗂 2. Estructura del Proyecto ##

  ├ ── Dashboard-Pizza_Sales.xlsx # Archivo principal con Datos, Análisis y Dashboard 
  
  ├ ── README.md # Informe detallado del proyecto (este archivo) 

## 🔍 3. Identificación de Variables ##

Se ha realizado una clasificación de las variables presentes en el dataset para garantizar que el tipo de análisis aplicado a cada una sea el adecuado:

* **Variables Categóricas**: Permiten segmentar el análisis . pizza_name, pizza_category, pizza_size e ingredients.
* **Variables Numéricas Discretas**: Representan conteos enteros . quantity y order_id.
* **Variables Numéricas Continuas**: Métricas de valor y rendimiento . unit_price y total_price.
* **Variables Temporales**: Cruciales para el análisis de estacionalidad . order_date y order_time.

## 🛠 4. Limpieza y Calidad de los Datos ##

Para garantizar la precisión de los resultados en el Dashboard, se ejecutó una fase de limpieza siguiendo estos pasos:

* **Gestión de Duplicados**: Se auditó la columna pizza_id y los importes financieros. Se confirmó la ausencia de valores nulos o registros huérfanos, asegurando que cada pizza contabilizada tenga un impacto real y trazable en la suma de ingresos totales.
* **Tratamiento de Inconsistencias**: Se detectó que el registro de fechas presentaba variaciones en su máscara de entrada (mezclando formatos dd/mm/yyyy y dd-mm-yyyy). Aunque el motor de Excel realizó una identificación automática del tipo de dato, se procedió a una estandarización forzada del formato para garantizar que las funciones de agrupación temporal en las Tablas Dinámicas operaran sin errores de jerarquía.
* **Gestión de la Integridad**: Se realizó un filtrado de la columna order_date para validar que el 100% de los registros se encontraran dentro del rango del año fiscal analizado. Esta limpieza asegura que las métricas de estacionalidad (meses y días) no presenten sesgos por datos fuera de contexto o "outliers" temporales.
* **Auditoría de Pedidos**: Se analizó la columna order_id para entender la relación entre filas y transacciones. Se confirmó que el dataset es transaccional una fila por pizza.
* **Estandarización de Tipos mediante ETL (Power Query)**: Para asegurar que el modelo de datos procesara correctamente las métricas financieras y temporales, se implementó un flujo de transformación mediante el editor de Power Query. Se definieron pasos específicos para forzar el tipado de cada columna, evitando errores de cálculo en las fases posteriores:

    * **Tipado Financiero**: Se configuraron las columnas de precios con formato Moneda $ y Número Decimal, asegurando la precisión en los cálculos de ingresos y ticket medio.
    * **Tipado Temporal**: Se aplicaron pasos de transformación para Hora y Fecha, lo que permitió que Excel reconociera la jerarquía cronológica necesaria para los segmentadores y el análisis de estacionalidad.
    * **Integridad Numérica**: Se definieron los IDs y cantidades como Tipo Entero para optimizar el rendimiento del libro y evitar inconsistencias en el recuento de pedidos.
    * **Nota Técnica**: El diseño de estos "Pasos Aplicados" permite que, ante cualquier nueva carga de datos, el sistema aplique automáticamente las mismas reglas de limpieza, garantizando la escalabilidad y el mantenimiento del Dashboard.

## ⚙️ 5. Variables Derivadas y Transformación ##

Con el objetivo de enriquecer el análisis y facilitar la interpretación de los datos por parte de los usuarios finales, se crearon nuevas dimensiones mediante el uso de fórmulas avanzadas. Estas transformaciones permiten una navegación más natural y profesional por el Dashboard.

* **Normalización Estética de Productos (pizza_name_name)**: Se detectó que la nomenclatura original de los productos era excesivamente redundante y extensa, lo que saturaba visualmente los gráficos. Se aplicó una limpieza para eliminar términos repetitivos como "Pizza" y artículos (“The”) innecesarios al inicio de los nombres.
    * **Justificación**: Esta acción permite mostrar nombres mucho más amigables y directos en el Dashboard, optimizando el espacio en los ejes de los gráficos y facilitando la lectura inmediata de los rankings.

* **Traducción y Categorización de Tamaños (pizza_size_name)**: Para mejorar la interpretación de los datos por parte de cualquier perfil de usuario, se transformaron las siglas técnicas de tamaño (S, M, L, etc.) en etiquetas de lenguaje natural.
    * **Justificación**: Al sustituir códigos por términos como "Pequeña", "Mediana" o "Grande", se garantiza que los segmentadores sean intuitivos. Esto elimina la ambigüedad y asegura que la jerarquía de productos sea clara en el análisis de volumen de ventas.

* **Segmentación Temporal Semanal (order_date_day_week)**: Se derivó el día de la semana a partir de la fecha de pedido para identificar los patrones de consumo diario.
    * **Justificación Técnica**: Esta variable es el motor del análisis de picos de demanda. Permite al negocio entender qué días requieren un refuerzo de personal en cocina (operaciones) y qué días necesitan impulsos promocionales (marketing).

* **Segmentación por Franjas Horarias (order_time_window)**: Dado que la hora exacta es un dato demasiado granular para detectar tendencias, se agruparon las transacciones en intervalos de tres horas para estandarizar los periodos de servicio.
    * **Justificación Técnica**: Esta transformación permite identificar con precisión las horas de mayor carga de trabajo ("horas punta"). Es una herramienta crítica para la gestión de turnos y para decidir, por ejemplo, si es necesario lanzar promociones de happy hour en los tramos de menor actividad para maximizar la rentabilidad del local.


* Se diseñó una arquitectura de tres capas (Datos -> Análisis -> Dashboard), lo que permite una escalabilidad total del modelo. La hoja de Análisis actúa como el motor de cálculo donde se procesan las métricas complejas, garantizando que la interfaz del Dashboard permanezca ligera y orientada exclusivamente a la visualización de resultados.

## 📊 6. Arquitectura de Consultas y Métricas (Tablas Dinámicas) ## 

Para la construcción del Dashboard, se diseñó una estructura de Tablas Dinámicas interconectadas que actúan como el motor de cálculo del análisis. No se trata de tablas aisladas, sino de un sistema dinámico basado en:

* **Agregación por Dimensiones**: Se crearon tablas dinámicas específicas para cruzar las variables derivadas (Día de la semana, Franja horaria, Categoría) con el volumen de ventas e ingresos. Esto permite que el Dashboard responda visualmente a cualquier combinación de filtros.
* **Cálculo de Pedidos Únicos**: Dado que el dataset es transaccional, se aplicó una lógica de recuento sobre el order_id en las tablas dinámicas. Esto permite validar que el volumen real es de 21.350 transacciones, dato fundamental para no confundir "unidades vendidas" con "visitas de clientes".
* **Métricas de Rentabilidad (Ticket Medio)**: Se establecieron cálculos que vinculan el total de ingresos con el volumen de pedidos únicos. Esta métrica es la que permite medir la eficiencia comercial y el impacto de las preferencias de tamaño en el gasto final.
* **Gestión de Rankings (Top 5)**: Se configuraron tablas dinámicas con filtros de valor automáticos. Esto garantiza que el Dashboard muestre siempre los productos más rentables o vendidos de forma actualizada, sin necesidad de intervención manual tras una carga de datos.


## 🔄 7. Análisis de Resultados y KPIs ## 

En esta sección se sintetizan los hallazgos clave derivados del análisis y cómo estos impactan en la estrategia del negocio. Tras el procesamiento y visualización de los datos en el Dashboard interactivo, se han identificado hallazgos que permiten optimizar la operativa y la estrategia comercial de la pizzería:

* **Rendimiento Comercial Global (KPIs)**: El negocio ha generado unos ingresos totales de $817.860,05 a través de 21.350 pedidos únicos. Con un total de 49.574 pizzas vendidas.
    * **Indicador de Rentabilidad**: El Ticket Medio Global se sitúa en $38,31 por pedido. Este dato es fundamental para la gerencia, ya que indica que los clientes suelen adquirir más de una unidad o productos de gama alta en cada compra.
* **Contraste Estratégico: Volumen vs Rentabilidad**: Uno de los hallazgos más relevantes del análisis es que la pizza con mayor éxito en ventas no es la que más ingresos genera para el establecimiento:
    * **Por Volumen**: La Classic Deluxe es la pizza más popular del menú con 2.453 unidades vendidas. Sin embargo, a pesar de su alto volumen, ocupa el cuarto lugar en aportación económica.
    * **Por Ingresos**: La Thai Chicken, con un volumen ligeramente inferior (2.371 unidades), se consolida como la reina de los ingresos con $43.434,25 totales.
    * **Utilidad para la toma de decisiones**: Este fenómeno indica que la Classic Deluxe actúa como un producto de "tracción" (mueve el inventario y atrae clientes), mientras que la Thai Chicken es un producto de "margen" (maximiza el beneficio por venta). Una recomendación estratégica sería diseñar promociones cruzadas que utilicen la popularidad de la primera para incentivar la compra de la segunda.

* **Análisis de Operaciones y Estacionalidad**:
    * **Pico de Demanda Semanal**: El Viernes se confirma como el día de mayor carga de trabajo con un total de 8.242 pizzas despachadas.
    * **Franja Horaria Crítica**: El tramo de 12:00 a 15:00 es el momento de mayor presión en cocina, concentrando 16.802 pizzas vendidas a lo largo del año.
    * **Utilidad**: Estos datos permiten una planificación exacta de los suministros y del personal necesario para cubrir los turnos de mayor afluencia, evitando cuellos de botella en las horas punta.

* **Preferencias de Tamaño y Optimización de Menú**: Los ingresos se concentran de forma masiva en los tamaños estándar, lo que permite simplificar la oferta comercial.
    * **Dominio del Tamaño Grande (L)**: Es el motor financiero del negocio, representando el 45,89% de los ingresos totales anuales.
    * **Equilibrio en Mediana y Pequeña**: El tamaño Mediano aporta el 30,49% de las ventas, mientras que el Pequeño contribuye con un 21,77%.
    * **Marginalidad de los Tamaños Especiales**: Los formatos XL y XXL tienen un impacto económico residual. El tamaño Extra Grande (XL) solo genera el 1,72% de las ventas, y el Extra Extra Grande (XXL) es prácticamente insignificante con apenas un 0,12% de la recaudación total ($1.006,60 de un total de $817.860).
    * **Decisión Estratégica**: Se recomienda evaluar la retirada del tamaño XXL del menú, ya que el coste operativo y de inventario de mantener bases de masa de ese tamaño no se justifica con su mínima aportación al ingreso global.


📈 **Conclusión Final**: El presente proyecto ha transformado exitosamente un conjunto de datos transaccional bruto en una herramienta de inteligencia de negocio estratégica. A través de un riguroso proceso de Análisis Exploratorio de Datos (EDA), se logró sanear la información y derivar variables clave —como las franjas horarias y la normalización de productos— que han permitido identificar patrones de consumo invisibles a simple vista. La implementación del Dashboard interactivo permite concluir que la pizzería posee una base sólida con más de 21.000 pedidos anuales y una facturación superior a los $817.000, liderada por productos de alta rentabilidad como la pizza Thai Chicken. Sin embargo, el análisis también revela oportunidades claras de optimización, especialmente en la simplificación del menú mediante la eliminación de tamaños con rotación casi nula (XXL) y el refuerzo de personal en los picos detectados de los viernes al mediodía. En definitiva, este trabajo dota a la gerencia de una capacidad de toma de decisiones basada en datos.

✒ Autores
José Manuel López Martínez
