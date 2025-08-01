## Descripcion del modelo
AERMOD cuenta con varios componentes regulatorios del sistema de modelación (AERMOD, AERMAP, AERMET) y componentes no regulatorios (BPIPPRIME, AERSURFACE, AERMINUTE, AESCREEN).

BPIPPRIME es también un modelo o pre-procesador. Su nombre “Building Profile Input Program – Plume Rise Model Enhancements (BPIP-PRIME)” significa Programa de Ingreso de Perfiles de Edificaciones – Modelo de Ascenso de Pluma Mejorado, fue diseñado para incorporar análisis de datos mejorado de lavado por edificaciones en el modelo U.S. EPA ISC-PRIME.

AERSURFACE es uno de los programas que se emplean como herramienta de apoyo para generar datos de entrada para el sistema de modelación AERMOD. Este programa fue desarrollado para estimar las características del suelo (albedo, cociente Bowen, y rugosidad) que son empleados por AERMET para generar datos meteorológicos requeridos por AERMOD.

AERMINUTE procesa datos de viento registrados cada minuto de estaciones automáticas de observaciones superficiales (“Automated Surface Observing Stations, ASOS”). Los datos de entrada de AERMINUTE están limitados a datos con formato de la “U.S. National Climatic Data Center, NCDC” conocidos como DS-6405, registrados únicamente por estaciones ASOS de los Estados Unidos.

AERSCREEN estima la concentración máxima de contaminantes al nivel del suelo causado por el impacto de una fuente fija, simulando así el peor escenario, empleando los algoritmos de cálculo del modelo AERMOD en su versión SCREEN. Los posibles escenarios que puede simular esta versión simple son: terreno simple (llano), terreno complejo (llano y montañoso), lavado de edificaciones, y el comportamiento del NO2 empleando un esquema sencillo de la química en aire.

AERMOD es un modelo de pluma en estado estable. En la capa límite estable (SBL por sus siglas en inglés), asume que la distribución de la concentración es Gaussiana en la vertical y la horizontal. En la capa límite convectiva (CBL por sus siglas en inglés), se asume que la distribución horizontal es Gaussiana, pero en la distribución vertical se describe como una función de distribución de probabilidad (pdf por sus siglas en inglés) de comportamiento bi-Gaussiana. Este comportamiento de la distribución de la concentración en la CBL fue demostrado por Willis & Deardorff (1981) y Briggs (1973). Adicionalmente, en la CBL, AERMOD trata la “pluma lofting” (pluma de galería) como una porción de masa de pluma, liberada desde una fuente flotante, que asciende hasta y permanece cerca a la cima de la capa límite antes de mezclarse dentro de la CBL. AERMOD también sigue cualquier masa de pluma que penetra dentro de la SBL elevada y que después re-ingresa a la capa límite si se dan las condiciones para que esto ocurra. Para fuentes en ambas capas, SBL y CBL, AERMOD tiene en cuenta el mejoramiento de la dispersión lateral resultado del serpenteo de la pluma.

Figura 25 Estructura general del sistema de modelación AERMOD. EPA (2004). Fuente. El proyecto.

Empleando una aproximación relativamente simple, AERMOD ha incorporado los conceptos actuales acerca de flujo y dispersión en terrenos complejos. Donde sea apropiado, la pluma es modelada con impacto y/o flujo paralelo al terreno. Esta aproximación ha sido diseñada para hacerlo más fiel a la realidad, ha sido fácil de implementar, y evita la necesidad de diferenciar entre terreno simple, intermedio y terreno complejo, como lo requería el anterior modelo regulatorio (Industrial Source Complex Short Term, ISCST3 e Industrial Source Complex Long Term, ISCLT3). 

Todos los terrenos son manejados de una manera consistente y continua, a la vez que se considera el concepto de división de líneas de corriente (Snyder et al., 1985) en condiciones estables estratificadas.

Una de las grandes mejoras que trae AERMOD a la modelación de dispersión de contaminantes es su habilidad para caracterizar la PBL a través del “escalado” de la capa superficial y de la capa de mezcla. AERMOD construye el perfil vertical de las variables meteorológicas requeridas basado en mediciones y extrapolaciones de esas mediciones empleando relaciones (escalado) de similitud. Los perfiles verticales de velocidad del viento, dirección del viento, turbulencia, temperatura y gradientes de temperatura son estimados empleando todas las observaciones meteorológicas disponibles. AERMOD está diseñado para ejecutarse con un mínimo de observaciones (o parámetros) meteorológicos.

A diferencia de otros modelos (ISC3), AERMOD tiene en cuenta la heterogeneidad de la PBL en sus cálculos de dispersión. Esto lo realiza “ponderando” los parámetros de la PBL en parámetros “efectivos” de una PBL homogénea equivalente.

Las características de la superficie del terreno en forma de albedo, rugosidad y cociente Bowen, más observaciones meteorológicas estándar (velocidad del viento, dirección del viento, temperatura y nubosidad), son datos de entrada para AERMET. El pre-procesador AERMET después calcula los parámetros de la PBL: velocidad de fricción (𝑢*), longitud Monin-Obukhov (𝐿), velocidad de escala convectiva (𝑤*), temperatura de escala (𝜃*), altura de mezcla (𝑧𝑖) y flujo de calor superficial (𝐻). Estos parámetros son pasados a la subrutina en el código fuente llamada INTERFACE (en el ejecutable de AERMOD), y los parámetros de similitud (con las observaciones de campo) son empleados para calcular los perfiles verticales de velocidad del viento (𝑢), las fluctuaciones de turbulencia lateral y vertical (𝜎𝑣, 𝜎𝑤), el gradiente potencial de temperatura (𝑑𝜃/𝑑𝑧) y la temperatura potencial (𝜃).

## componentes del modelo de dispersion
### Procesador AERMET
AERMET procesa datos de observaciones meteorológicas para generar la meteorología de entrada de AERMOD en forma de dos archivos: un archivo de superficie (.sfc), en adelante AS y un archivo de perfil (.pfl), en adelante AP. El archivo de superficie contiene las siguientes variables en forma horaria:
-	Flujo de calor sensible.
-	Velocidad de fricción superficial.
-	Velocidad de escala convectiva.
-	Gradiente de temperatura por encima de la capa mezclada.
-	Altura de la capa límite convectiva.
-	Altura de la capa límite mecánica.
-	Longitud Monin-Obukhov (M-O).
-	Altura de la rugosidad del suelo.
-	Cociente de Bowen.
-	Albedo de superficie.
-	Velocidad del viento.
-	Dirección del viento.
-	Temperatura.

El AP meteorológico contiene las siguientes variables en función de la altura:
-	Desviación estándar de las fluctuaciones de la componente de la velocidad vertical.
-	Desviación estándar de las fluctuaciones de la componente de la velocidad horizontal.

Estas variables son estimadas empleando un modelo de capa límite unidimensional, el cual asume que las condiciones horizontales son homogéneas.
Características del Suelo para Diferentes Periodos Estacionales. El Albedo, cociente Bowen y Rugosidad son parámetros del suelo utilizados en el cálculo de flujos y estabilidades de la atmosfera. Los valores que toman estos parámetros son los que se muestran en Tabla 17, Tabla 18, Tabla 19 y Tabla 20.

Tabla 17 Comportamiento del albedo por uso del suelo y periodo estacional.
| USO SUELO | PRIMAVERA | VERANO | OTOÑO | INVIERNO |
| --- | --- | --- | --- | --- |
| Mar, lagos, ríos | 0,12 | 0,10 | 0,14 | 0,20 |
| Bosques hoja caduca | 0,12 | 0,12 | 0,12 | 0,50 |
| Bosques de coníferas | 0,12 | 0,12 | 0,12 | 0,35 |
| Ciénagas | 0,12 | 0,14 | 0,16 | 0,30 |
| Cultivos | 0,14 | 0,20 | 0,18 | 0,60 |
| Pastizales, praderas. | 0,18 | 0,18 | 0,20 | 0,60 |
| Urbano | 0,14 | 0,16 | 0,18 | 0,35 |
| Matorrales desérticos | 0,30 | 0,28 | 0,28 | 0,45 |


Tabla 18 Comportamiento de la rugosidad por uso del suelo y periodo estacional.
| USO SUELO | PRIMAVERA | VERANO | OTOÑO | INVIERNO |
| --- | --- | --- | --- | --- |
| Mar, lagos, ríos | 0,0001 | 0,0001 | 0,0001 | 0,0001 |
| Bosques hoja caduca | 1,00 | 1,30 | 0,80 | 0,50 |
| Bosques de coníferas | 1,30 | 1,30 | 1,30 | 1,30 |
| Ciénagas | 0,20 | 0,20 | 0,20 | 0,05 |
| Cultivos | 0,03 | 0,20 | 0,05 | 0,01 |
| Pastizales, praderas. | 0,05 | 0,10 | 0,01 | 0,001 |
| Urbano | 1,00 | 1,00 | 1,00 | 1,00 |
| Matorrales desérticos | 0,30 | 0,30 | 0,30 | 0,15 |

Tabla 19 Comportamiento del cociente Bowen por uso del suelo y periodo estacional, en condiciones secas.
| USO SUELO | PRIMAVERA | VERANO | OTOÑO | INVIERNO |
| --- | --- | --- | --- | --- |
| Mar, lagos, ríos | 0,1 | 0,1 | 0,1 | 2,0 |
| Bosques hoja caduca | 1,5 | 0,6 | 2,0 | 2,0 |
| Bosques de coníferas | 1,5 | 0,6 | 1,5 | 2,0 |
| Ciénagas | 0,2 | 0,2 | 0,2 | 2,0 |
| Cultivos | 1,0 | 1,5 | 2,0 | 2,0 |
| Pastizales, praderas. | 1,0 | 2,0 | 2,0 | 2,0 |
| Urbano | 2,0 | 4,0 | 4,0 | 2,0 |
| Matorrales desérticos | 5,0 | 6,0 | 10,0 | 10,0 |


Tabla 20 Comportamiento del cociente Bowen por uso del suelo y periodo estacional, en condiciones húmedas.
| USO SUELO | PRIMAVERA | VERANO | OTOÑO | INVIERNO |
| --- | --- | --- | --- | --- |
| Mar, lagos, ríos | 0,1 | 0,1 | 0,1 | 0,3 |
| Bosques hoja caduca | 0,3 | 0,2 | 0,4 | 0,5 |
| Bosques de coníferas | 0,3 | 0,2 | 0,3 | 0,3 |
| Ciénagas | 0,1 | 0,1 | 0,1 | 0,5 |
| Cultivos | 0,2 | 0,3 | 0,4 | 0,5 |
| Pastizales, praderas. | 0,3 | 0,4 | 0,5 | 0,5 |
| Urbano | 0,5 | 1,0 | 1,0 | 0,5 |
| Matorrales desérticos | 1,0 | 1,5 | 2,0 | 2,0 |

A continuación, se realizan las similitudes de las condiciones ambientales entre el hemisferio norte, zona para la cual fue desarrollado el modelo AERMET/AERMOD, y la zona intertropical (entre el trópico de Cáncer y el ecuador). Este análisis de similitud permite identificar las tres propiedades del suelo. Para el hemisferio norte se presentan cuatro (4) periodos estacionales, primavera, verano, otoño e invierno. En el hemisferio norte la época de lluvia ocurre en verano, y la época seca ocurre en otoño.

Por el contrario, para la zona de convergencia intertropical las condiciones ambientales oscilan entre el periodo lluvioso y el periodo seco, fenómenos que son comparables a las constantes ambientales que se manifiestan para en las estaciones de verano y otoño, del hemisferio norte. En las siguientes tablas se reproducen las constantes recomendadas por la EPA para diferentes tipos de uso de suelo, para diferentes periodos estacionales y los tres parámetros del suelo (Albedo, Bowen y Rugosidad).


Uso del suelo
El Albedo, cociente Bowen y Rugosidad son parámetros utilizados en el cálculo de flujos y estabilidades de la atmosfera. El Albedo es la fracción de radiación solar incidente reflejada por la superficie, sin absorción. El rango de valores típicos es desde 0.1 para bosques espesos caducifolios hasta 0.90 para nieve fresca. El cociente Bowen, o indicador de humedad, es el cociente del flujo de calor sensible frente al flujo de calor latente. Aunque el cociente Bowen puede tener variaciones diurnas significativas, este se usa para determinar los parámetros de la capa limite planetaria para condiciones convectivas. Durante el periodo diurno, el cociente Bowen logra un valor positivo constante, con rango de 0.1 sobre el agua hasta 10.0 sobre un desierto al medio día. La rugosidad de la superficie está relacionada con la altura de los obstáculos para el flujo del viento, y es en principio, la altura a la cual la velocidad media horizontal del viento es cero. Los valores van desde menos de 0.001 metros sobre el agua superficial en calma hasta 1.0 metro o más sobre área urbana o bosques.

En conclusión, el usuario puede indicar al procesador AERMET las variaciones mensuales de las tres características de la superficie (albedo del medio día (r0); cociente Bowen (B0); rugosidad (z0)) para un máximo de 12 sectores del viento. El albedo (r) es la fracción de radiación reflejada por la superficie; el cociente Bowen (B0) es el cociente del flujo de calor sensible y el flujo de calor de evaporación; y la rugosidad (z0) es la altura por encima del nivel del suelo en la cual la velocidad horizontal es usualmente cero. La guía del usuario de AERMET presenta tablas guía para definir sus valores según variaciones estacionales y tipos de uso del suelo.

Características del suelo en el área del proyecto

Si bien la unidad de análisis que se emplea para analizar el componente aire esta circunscrita a una franja alrededor de la zona de potencial afectación, el área a modelar es mucho más extensa, por cuenta de la vía de acceso que utilizara el proyecto para el transporte de materiales. Esto significa que debemos considerar las características del suelo de una zona más amplia.

El uso del suelo es fácilmente identificado para el dominio de modelación. Este tiene un componente fuertemente con presencia de agua de mar y sub-urbano, para el desarrollo turístico y residencial. 

El Albedo, cociente Bowen y Rugosidad son parámetros utilizados en el cálculo de flujos y estabilidades de la atmosfera. El Albedo es la fracción de radiación solar incidente reflejada por la superficie, sin absorción. El rango de valores típicos es desde 0.1 para bosques espesos caducifolios hasta 0.90 para nieve fresca. El cociente Bowen, o indicador de humedad, es el cociente del flujo de calor sensible frente al flujo de calor latente. Aunque el cociente Bowen puede tener variaciones diurnas significativas, este se usa para determinar los parámetros de la capa limite planetaria para condiciones convectivas. Durante el periodo diurno, el cociente Bowen logra un valor positivo constante, con rango de 0.1 sobre el agua hasta 10.0 sobre un desierto al medio día. La rugosidad de la superficie está relacionada con la altura de los obstáculos para el flujo del viento, y es en principio, la altura a la cual la velocidad media horizontal del viento es cero. Los valores van desde menos de 0.001 metros sobre el agua superficial en calma hasta 1.0 metro o más sobre área urbana o bosques.

El usuario puede indicar a AERMET variaciones mensuales de las tres características de la superficie (albedo del medio día (r0); cociente Bowen (B0); rugosidad (z0)) para un máximo de 12 sectores del viento. El albedo (r) es la fracción de radiación reflejada por la superficie; el cociente Bowen (B0) es el cociente del flujo de calor sensible y el flujo de calor de evaporación; y la rugosidad (z0) es la altura por encima del nivel del suelo en la cual la velocidad horizontal es usualmente cero. La guía del usuario de AERMET presenta tablas guía para definir sus valores según variaciones estacionales y tipos de uso del suelo. 

Tabla 21 Características del suelo de la región.

Clasificación del suelo, en valor promedio anual, de acuerdo con el tipo de suelo de los sectores antes descritos. Teniendo presente que la clasificación entregada por la EPA considera cuatro periodos estacionales (primavera, verano, otoño e invierno), tomaremos el promedio entre primavera y verano, condiciones que se acercarían al patrón anual para el trópico.

A continuación, se realizan las similitudes de las condiciones ambientales entre el hemisferio norte, zona para la cual fue desarrollado el modelo AERMOD, y la zona tropical (entre el trópico de Cáncer y el ecuador). Para el hemisferio norte la época lluviosa ocurre en verano, y la época seca ocurre en otoño. Las condiciones ambientales para el trópico sería periodo lluvioso y periodo seco, reproduciendo de esta manera las constantes ambientales para verano y otoño. A continuación, las tablas que identifican los valores que adoptan estos parámetros físicos, para diferentes periodos estaciones, según el manual del usuario del procesador AERMET, y son utilizadas para realizar la modelación de dispersión de contaminantes del proyecto.

Tabla 22 Comportamiento del albedo por uso del suelo y periodo estacional

**Rosa de vientos**

A continuación se ilustra la representación gráfica del comportamiento de los vientos, según registros históricos del aeropuerto de Montería comprendidos entre el 1 de enero de 1970 a 2 de septiembre de 2024. En la Figura 26 se aprecia el comportamiento promedio anual o multianual de los vientos y entre la Figura 27 a Figura 38 la representación de esta variable para los doce meses del año.  

En general el vector viento indica que la dirección predominante es hacia el Sureste, tanto en el comportamiento anual, como en los doce meses del año. Sin embargo, hay una cierta variación en la velocidad promedio, destacando velocidades altas los meses febrero, marzo y abril con velocidades superiores a 3 millas por hora (4.82 km/h, esto es 1.3 m/s).

Estas son velocidades muy bajas, situación que se confirma con las calmas, las cuales son muy frecuentes, con valores que oscilan entre 52.3% a 70.4%. Las calmas corresponden a velocidades cercanas a cero, o sea donde la velocidad del viento es indetectable por el instrumento de medición.

### Procesador AERMAP
AERMAP es un ejecutable que realiza el procesamiento de la topografía, empleado para obtener registros del terreno, además de determinar la altura del terreno en el área influencia, también referida como escala de altura del terreno. Este procesador es usado también para crear grillas (mallas) de receptores, que representan los sitios geográficos donde se desea obtener información de la dispersión de contaminantes.

AERMAP está diseñado para simplificar y estandarizar los datos de terrenos de AERMOD. Los datos de entrada son las elevaciones de los receptores, carreteras, coordenadas y curvas de nivel. Los datos de salida incluyen para cada receptor: localización y escala en z, que son usados para cálculos que determinan el flujo de viento de ladera, es decir, en la parte más baja de una montaña.

Hay dos tipos de datos básicos que requiere AERMAP para su ejecución. El primero es el archivo que orienta la ejecución a través de un conjunto de opciones de procesamiento y define la ubicación de los receptores y las fuentes de emisión. La estructura y sintaxis de un archivo de ejecución está basado en las mismas rutas y etiquetas que emplea el archivo de control de ejecución de AERMOD.

El segundo hace referencia a archivos computarizados que contienen codificados datos de terreno. Estos datos se encuentran disponibles en tres tipos de formatos. Está el formato “Digital Elevation Model (DEM)” que sigue el estándar antiguo USGS del “Blue Book”. Hay un nuevo formato “Spatial Data Transfer Standard (SDTS)” que estructura los archivos DEM y otros datos asociados en forma de metadatos. Finalmente, están los datos “National Elevation Dataset (NED)” que es actualizado constantemente y está disponible en varios formatos para importación por paquetes de software comercial como ARCGRID, GridFloat y BILS. De estos formatos y estándares, AERMAP está programado para leer solamente formatos USGS “Blue Book”. Los datos SDTS, XYZ y NED tienen que ser convertidos a formato “Blue Book”. La EPA suministra en su portal web programas de conversión SDTS y XYZ.

Cada archivo DEM cubre una sección del terreno basado en sistemas de coordenadas en latitud y longitud. Estos archivos de datos se encuentran disponibles a través de varios sitios comerciales URL y en el portal del USGS.

Los datos DEM se pueden obtener en diferente resolución de espaciado de datos horizontales. AERMAP está en capacidad de procesar archivos en formato “Blue Boook” de
7.5 minutos y 1 grado, datos DEM con cualquier distancia uniforme entre nodos. Los archivos DEM de 1 grado se obtienen de forma gratuita a través de internet (protocolo HTTP) y vía FTP. Los datos DEM de 7.5 minutos se pueden adquirir a través de un proveedor internacional de este servicio.

Algunos organismos cartográficos ofrecen gratuitamente al público bases de datos importantes, a las que se puede acceder a través de Internet. Citemos algunos de los principales: la NASA (DEM ASTER, SRTM-1, SRTM-3, SRTM30, MOLA MEGDR), la National Imagery and Mapping Agency (NIMA) (SRTMs) y el USGS (DEM SDTS, 1 grado, 7.5 minutos, NED, GTOPO30). La resolución, es decir, la distancia entre puntos adyacentes del archivo digital que ofrecen ellos es variados, como ejemplo el archivo DEM Aster tiene una resolución de 30 m.

AERMAP utiliza el sistema de coordenadas Universal Transverse Mercator (UTM) para identificar la localización de las fuentes y los receptores. El sistema de coordenadas es un método de representa los meridianos y paralelos de la superficie de la tierra en formato plano.

El trazado de las cuadrículas se realiza en base a estos husos y a zonas UTM, y es válido en una gran parte de la superficie total de la Tierra, pero no en toda. Concretamente, la zona de proyección de la UTM se define entre paralelos 80º S y 84 º N, mientras que el resto de las zonas de la Tierra - las zonas polares - utilizan el sistema de coordenadas UPS (Universal Polar Stereographic). Por tanto, en el sistema UTM la Tierra se divide en 60 husos de 6º de longitud que completan sus 360º. Cada huso se numera con un número entre el 1 y el 60, siendo el huso 1 el limitado entre las longitudes 180° y 174° W, centrado en el meridiano 177º W. Los husos se numeran en orden ascendente hacia el este.

En cuanto a las zonas, la Tierra se divide en 20 zonas de 8º Grados de Latitud, que son denominadas mediante letras desde la "C" hasta la "X" inclusive (exclusión hecha de la CH, I y LL para evitar confusiones, y de la A, B, Y y Z que se reservan para las zonas polares).

### Procesador AERMOD
En general, AERMOD modela una pluma como la combinación de dos casos limitantes: una pluma horizontal (impactando el terreno) y una pluma que sigue paralela a la altitud del terreno. Sin embargo, para todas las situaciones, la concentración total, en un receptor, está acotado por la predicción de la concentración a partir de estos estados. En terreno plano estos estados son equivalentes. Introduciendo el concepto división de líneas de corriente de altura, en terreno elevado, AERMOD calcula la concentración total como la suma ponderada de las concentraciones asociadas con estos dos casos limitantes o estados de la pluma (Venkatram et al., 2001).

El procesador de terreno AERMOD emplea una malla de datos de terreno para calcular una altura representativa de la influencia (hc) para cada receptor Mediante esta aproximación, AERMOD calcula el impacto de contaminantes en terreno plano y terreno elevado, con la misma infraestructura de modelación, obviando así la necesidad de diferenciar entre formulaciones para terreno simple y para terreno complejo (que era requerido por modelos regulatorios previos).

La ecuación de concentración general, que aplica para condiciones estables y convectiva está dada por,

Donde,

CT{xr, yr, zr}: Concentración total.

Cc,s{xr, yr, zr}: Contribución de la pluma en estado horizontal (subíndices c y s se refiere a condiciones convectivas y estables, respectivamente).

Cc,s{fx,r, yr, zp}: Contribución de la pluma de estado cercano al terreno, f es función de ponderación del estado de la pluma.

{xr, yr, zr}: Coordenada que representa a un receptor (con zr relativo a la elevación de la base de la chimenea).

zp = zr – zt: Altura de un receptor por encima del suelo local, y zt es la altura del terreno para un receptor.

Hay matemáticamente tres fracciones principales que contribuyen a las concentraciones modeladas: 1) la fuente directa (en la chimenea), 2) la fuente indirecta, y 3) la fuente que se introduce en la capa limite. La intensidad del ingreso a la capa limite es (1 – fp) Q que es la fracción de la emisión de la fuente que ingresa en la capa estable elevada. Adicional a las tres fracciones principales, se incluyen otras fracciones para satisfacer las condiciones sin flujo en z = 0 y zi.

En AERMOD la concentración total (Cc) en la CBL se determina sumando la concentración de las tres fracciones. Para la pluma en estado horizontal, la concentración total se determina por:
 
Donde Cd, Cr, y Cp: Contribuciones de las fuentes directa, indirecta y que ingresa, respectivamente.

Concentración de la pluma debido a la pluma directa,

Concentración de la pluma debido a la pluma indirecta,

Concentración de la pluma debido a la pluma que ingresa,

Resultados del sistema de modelación AERMOD. 

La EPA estableció a fines de 2005 que AERMOD sería el modelo de uso recomendado para el análisis de la dispersión de contaminantes a escala local, en sustitución del ISCST3 (Industrial Source Complex Short- Term 3), usado hasta ese momento. AERMOD representa un sólido y significativo avance respecto del ISCST3; incorpora las técnicas más avanzadas de parametrización de la capa límite planetaria, dispersión convectiva, formulación de la elevación de la pluma e interacciones complejas del terreno con la pluma. En comparación con el ISCT3, AERMOD contiene nuevos o mejorados algoritmos para: i) la dispersión tanto en la capa límite estable como convectiva; ii) flotabilidad y elevación de la pluma; iii) ingreso de la pluma en la capa limite; iv) tratamiento de fuentes elevadas y bajas; v) perfiles verticales de viento, temperatura y turbulencia, y vi) tratamiento de receptores en todo tipo de terrenos. Una de las ventajas fundamentales de AERMOD es la modelación de terrenos complejos.

AERMOD es capaz de manejar múltiples fuentes de emisión, estas son: puntuales, volumen y área. Las fuentes lineales se pueden modelar como una secuencia de fuentes volumen o fuentes de área emplazadas en la via. Se pueden especificar varios grupos en una sola ejecución, con contribuciones de fuentes combinadas por grupos. El sistema de coordenadas utilizado para este análisis consiste en el sistema de coordenadas WGS84/UTM (Universal Transversal Mercator).

 
Figura 40 Flujo de datos del sistema de modelación AERMOD. Fuente. El proyecto.

Procesamiento de datos meteorológicos. Para la modelación de dispersión de contaminantes se utilizó información obtenida del modelo WRF, que utiliza datos de la red meteorológica mundial.

El procesamiento de la meteorología se realiza con el procesador AERMET. El Ejecutable AERMET está diseñado para ser utilizado en dos etapas, y operar con tres tipos de datos, esto es, registros horarios de meteorología superficial obtenidas de una red climatológica, datos de dos radiosondeos diarios (mañana y tarde), y datos obtenidos en planes de medición in situ con una torre meteorológica con todos los instrumentos o sensores climatológicos.

Archivo formato SAMSON. El archivo de datos meteorológicos superficial SAMSON (Solar and Meteorological Surface Observational Network) consta de múltiples registros. El primer registro contiene datos de la estación. El formato FORTRAN de este registro es: (1X,A5,1X,A22,1X,A2,1X,I3,2X,A1,I2,1X,I2,2X, A1,I3,1X,I2,2X,I4). Cada variable se representa por un número de posición. Esta posición le pertenece a esa variable, no importa cuántas variables sean las que se recuperen o utilicen. El segundo registro contiene la lista de variables (referidas por un número de posición) que aparecen en el archivo de datos. No hay un formato en particular; el número de la variable aparece encima de la columna de datos que representa con al menos un espacio en blanco (y usualmente muchos más) entre las posiciones de números. El tercer, y siguientes registros, contienen los datos climatológicos recuperados de la base de datos SAMSON.

Archivo formato TD-6201. Los primeros siete campos corresponden a una porción denominada ID PORTION, y se presenta al comienzo de cada registro. Los siguientes diez campos del registro contienen una porción denominada DATA PORTION. Esta última se repite por cada nivel de la observación. El máximo número de niveles es 200.

Cada registro lógico es de longitud variable con un máximo de 7232 caracteres. Cada registro lógico contiene una observación completa de aire de altura de la estación de medición para una hora específica de liberación de la sonda meteorológica. Usualmente se realiza en las primeras horas de la mañana y al final de la tarde.
