# Prueba-NEQUI
### Juan Camilo Ortiz Moreno

# Solucion prueba técnica 
Para el desarrollo de la prubea partimos de el objetivo que se nos plantea.

# Objetivo
El objetivo de la prueba es idear una solución para identificar transacciones que evidencian un
comportamiento de Mala Practica Transaccional, empleando un producto de datos.

Para el desarrollo del objetivo se nos proporcionan datos que contienen las siguientes variables:

1. _id: Identificador único del registro
2. merchant_id: Código único del Comercio o aliado.
3. subsidiary: Código unico de la sede o sucursal.
4. transaction_date: Fecha de contabilización de la transacción en el core Financiero.
5. account_number: numero único de cuenta
6. user_id: Código único del usuario dueño de la cuenta desde donde se registran las
7. transacciones.
8. transaction_amount: Monto de la transaction en moneda ficticia.
9. transaction_type: Naturaleza de la transacción valores (DEBITO o CREDITO)

contamos con una cantidad de 8 datasets de los cuales procedemos a usar 2 debido a que como se nos hace referencia que los datos conservan la distribución estadistica.
# Desarrollo
para el desarrollo de la prueba realizamos los sigueintes puntos:

1. entendemos el onjetivo del problema planteado y realizamos un breve analisis descriptivo para entender el problema.
2. comenzamos el proceso de codificacion referenciando que paquete vamos a utilizar y lo dividimos en 3 secciones  fundamentales,graficas y de modelado (para el modelado cabe resaltar que hicimos enfasis en las herramientas que nos presta la libreria sklearn).  
3. cargamos la información y construminos el data frame que vamos a trabajar. comenzamos por realizar un resumen básico de las variables de nuestro data frame y cambiamos algunos formatos flotantes y de fecha. esto con el fin de facilitar un posterior procesamiento. (apartado entre las lineas 4 y 10 del codigo)
4. procedemos con realizar un análisis descriptivo en el cual buscamos inicialmente entender como se comportan las variables que tenemos y observamos inicialmente que contamos con 0 datos faltantes lo cual nos permite descartar procesos adicionales para eliminar o simular los datos faltantes. luego verificamos la distribución de la variable monto de txs para empezar a entender como se comportan estos montos y como entender cuando un monto es atipico. ademas de como se comportan estos montos en ventanas de tiempo mensuales o diarias para identificar insights básicos como que dia se presentan mas y menos txs aqui evidenciamos el dia jueves y domingo respectivamente. desde la misma perspectiva analizamos las cantidades de txs todo esto enfocados en entender cuando un cliente fracciona sus txs.y finalizamos con una evidencia cara de que el tipo de txs mas utilizado es debito.(el análisis se encuentra entre la linea 11 y 32 del codigo)
5. Entendiento el problema vamos a transformar la basepara dar respuesta al objetivo de encontrar cleintes con una mala practica apra esto entonces procedemos a realizar estos cambios puntuales.comenzamos con separar dia, mes, year, horasem, y hora de la columna transaccion_date esto con el fin de agrupar por estos detalles cantidad de txs y monto. luego creo las variables monto real y fraccionamiento agrupando inicialmente la suma del los montos y la cantidad de txs en un lapso de 24 horas.de igual manera las txs por mes. para luego crear la variable respuesta malapract partiendo de si un cliente tiene fraccionamientos.(se encuentra en el codigo las lineas 41 a 53)
6. limpio la base del analisis anterior y dejo para el modelo 6 variables merchant_id ,ransaction_amount ,transaction_type ,hora ,dia ,sec y malapract.(linea 50 de codigo)
7. separo la base en entrenamiento y testeo en un porcentaje respectivo de 70% y 30% respectivamente verificamos el balanceo de los 1 y 0en esta separacion y decidimos a tra ves de una herramienta como lo es el oversample crear una muestra rebalanceada para verificar en los modelos a ejecutar tanto el entrenamiento y testeo normal como el balanceado esto ya que una muestra desbalanceada nos puede llevar a sobreestimar el modelo y lo debemos evitar.se ejecuta el pipe_line que realiza de forma automatica:onehotencoding para variables categoricas, ajuste de hiperparametros por medio de una malla optima y el escalado de variables en caso de ser necesario. (lineas de codigo correspondientes de 54 a 60)
8. Modelamiento para el modelamiento escogimos 3 metodologias. la primera es el random forest un algoritmo clasico para este tipo de problemas de clasificación el cual reduce dimensionalidad y combina diferentes modelos para hacer su emtodologia mas robusta. como lo plantee anteriormente ademas del rndom forest clasico propongo el random forest con nuestra muestra de entrenamiento y testeo balanceada. luego de esto propongo un modelo de gradient boosting conocido como xgboost el cual es importante ya que a medida d que se trabaja con una cantidad mas grande de datos se peuda usar de mejor manera y me parece que se encuentra un poco mas refinado que otros modelos de igual amnera lo tomaremos tanto con la muestra balanceada y por ultimo s epropone una regresión logistica el metodo convencional solo para verificar que los otros modelos son mas potentes.

# Metricas 
la metrica que usaremos para evaluar los modelos y tomar una desición usaremos el reciver operating charasteristic ó mas conocido como ROC que nos va  apermitir usar el acierto de nuestros eventos 1 y 0 que son mala practica o no para toamr una desición tomaremos el ROC AUC que nos dira entre mas cercano sea a uno es mejor la rpedicción del modelo. adémas de tener en cuenta un posible efecto de sobreestimación influira en neustra desición. y graficamente la interpretación del ROC AUC es entre mas alejada se encuentre de la recta y = x es mejor.

# Criterio de seleccion del modelo
como planteamos en el apartado de Metricas anteriormente vamos a usar el criterio ROC AUC para la elecciñon del modelo y según nuestro resultados tenemos
1. ROC AUC
2. EXTREME GRADIENT BOOSTING	0.8808
3. RANDOM FOREST CLASICO	0.8802
4. RANDOM FOREST BALANCEADO	0.8801
5. EXTREME GRADIENT BOOSTING BALANCEADO		0.8161
6. REGRESION LOGISTICA		0.8161

Entendiendo los resultados anteriores podemos observar que en general todos los modelos que se ejecutaron y aplicaron tienen un buen ROC AUC, lo que permitiria usar cualquiera pero para la elección del mejor tomaremos la desición entendiendo, que el EXTREME GRADIENT BOOSTING y RANDOM FOREST CLASICO aunque tienen un buen ROC AUC tenemos indicios de que el modelo puede estar sobre estimado, ya que la muestra con la que se entreno esta desbalanceada lo que no nos aseguraria una beuna practica para el modelamiento. por lo tanto entre los modelos restantes se tienen RANDOM FOREST BALANCEADO que cuenta con un ROC AUC muy bueno y que cuenta con una entrenamiento balanceado y cuemple con las caracteristicas de loq ue tenemos planteado y por lo tanto se escoge este modelo. pero dejo una nota adicional y es que no descarto el EXTREME GRADIENT BOOSTING BALANCEADO ya que aunque cuenta con un menor ROC AUC en terminos de implementación y de trabajo con mayor volumen de datos este puede ser una solución mas optima. pero de igual manera según los resultados la elección del modelo es RANDOM FOREST BALANCEADO	0.8801.

# Arquitectura del Proceso
1. Captura de la información directamente del servicio de aws dada la conexión que se pueda establecer entre Py y el servicio de aws (propongo el servicio de aws ya que en los data sets que se compartieron se evidencia el uso de aws).
2. transformación de la base para que tenga la estructura que nos permita identifica la mala practica transaccional.
3. validación de la distribución de valores para porder identificar valores atipicos para el modelo y poder retirarlos.
4. partición de la información en 70% para entrenamiento y 30% para posterior validación. (en este paso tambien revisar el balanceo de la variable respuesta para evitar sobreajuste).
5. realizar oversamplig para el balanceo de nuestra información.
ejecutar el pipe_line que realiza de forma automatica:onehotencoding para variables categoricas, ajuste de hiperparametros por medio de una malla optima y el escalado de variables en caso de ser necesario.
6. ejecutar el modelo sobre la base refinada y obtener las metricas para evaluar los resultados del modelo.
7. Selección del modelo verificando tanto la metrica usada como una posible implementación para elegir el mas optimo.
8. ejecución de un backtesting sobre la ultima ejecución para revalidar el funcionamiento del modelo.
9. implementación con el servicio de aws para que se ejecute automaticamente.

para esta arquitectura propongo el uso de pY ya que es una herramienta versatil y de facil uso pra el modelado ademas de que se peude establecer una conexión con el servicio de aws por medio de spark lo cual facilita nuestro proceso. usamos la libreria sklearn la cual es una herramienta que tiene las funciones necesarias ademas de  muchos algoritmos que son sencillos para la solución de un problema analitico ya que podemos elegir un modelo, definir sus parametros, entrenar y probar el modelo para ejercicios de aprendizaje supervisado.

mi propuesta para la frecuencia de actualización es que sea actualización diaria con la nota de dia vencido ya que para hacer un seguimiento y ejecucion del modelo se pueda tener información actualizada y que nos permita hacer un seguimiento de esta mala practica entre mas tiempo se de para la actualización de los datos se peuden eprder acciones a tomar para mitigar esta mala practica. ademas de tener en cuenta que con cada actualizació
para esta arquitectura propongo el uso de pY ya que es una herramienta versatil y de facil uso pra el modelado ademas de que se peude establecer una conexión con el servicio de aws por medio de spark lo cual facilita nuestro proceso. usamos la libreria sklearn la cual es una herramienta que tiene las funciones necesarias ademas de  muchos algoritmos que son sencillos para la solución de un problema analitico ya que podemos elegir un modelo, definir sus parametros, entrenar y probar el modelo para ejercicios de aprendizaje supervisado.nn el modelo se debe reparametrizar para no perder la capacidad de predicción.

# Propuesta 
Entendiendo la necesidad de identificar cuando un cliente fracciona transacciones. me permito el proponer un modelo que nos permita dar un paso adelante de este problema para tomar acciones que minimicen el fraccionamiento.

este modelo consiste en identificar variables transaccionales, demograficas y propias del cliente para poder identificar la probabilidad de que un cliente realice un fraccionamiento en x tiempo y entregar estas probabilidades a el área encargada de tomar acciones apra prevenir estos fraccionamientos. ademas de entender que variables podrian influir directamente sobre la desicion de fraccionar y obtener insights significativos.

para la solucion de esta propuesta utilizariamos una arquitectura similar a la usada en esta prueba pero con la inclusión de diferentes variables y otros modelos que puedan darnos una interpretación mas adpatada a la situación

otro proyecto que se peude realizar es segmentar los clientes que realizan fraccionamientos para identificar si existen grupos de estos que tengan caracteristicas propias que nos permitan conocer los clientes que presentan fraccionamiento y tomar desiciones en cuanto a las posibilidades de reducir esta mala practica.

en caso de este modelo si aplicariamos una arquitectura diferente ya que es una estructura para un modelo no supervisado. no necesitariamos encontrar un clasificador si no conocer similitudes entre los datos.

