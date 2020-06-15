# Notas

Página web de SUMO: https://sumo.dlr.de/userdoc/index.html

## 21 ene. 2020: Limpieza de mapas de OpenStreetMap e importación en SUMO

TUTORIAL: https://sumo.dlr.de/docs/Tutorials/Import_from_OpenStreetMap.html 

1. Exportar el mapa desde OpenStreetMap (botón exportar)

2. Limpiar el mapa con JOSM (https://josm.openstreetmap.de/)

   - JOSM te permite cambiar algunos detalles interesantes del mapa de OSM, como la importancia de la vía, los carriles, la velocidad, etc. Funciona bastante mejor que SUMO, así que es útil, pero hay funciones que JOSM no tiene (como señalar vías de doble carril con sentidos contrarios). En mi caso me limité a establecer algunos límites de velocidad que no estaban (TF5 sentido SC, av. Trinidad, la av. Astrofísico, etc). En general el mapa de OSM está muy completo.
   - CONVIENE QUITAR AQUÍ LOS NODOS INNECESARIOS

3. Convertirlo al formato de SUMO:

     > In a [OpenStreetMap file](https://sumo.dlr.de/docs/OpenStreetMap_file.html), the highway attribute implicitly determines values of some other attributes, like the speed limit. You must tell SUMO these implicit values in the [SUMO edge type file](https://sumo.dlr.de/docs/SUMO_edge_type_file.html). It assigns default values to the road attributes speed limit, number of lanes, priority, one-way street and allowed vehicle classes depending on the highway type. The article [SUMO edge type file](https://sumo.dlr.de/docs/SUMO_edge_type_file.html) provides several pre-defined SUMO edge type files and explains how you can compile your own edge type file.
      >
      > So determine in this step of the tutorial the implicit values of every highway type as defined by the OpenStreetMap community. [Several edge-type files suitable for OpenStreetMap are included with SUMO. Some of them are meant to be combined.](https://sumo.dlr.de/docs/Networks/Import/OpenStreetMap.html#recommended_typemaps)

    Yo además de los dos archivos que se mencionan también le metí  **osmNetconvertPedestrians.typ.xml** para ver si me añade las aceras.
    
      ```
    netconvert --xml-type-files osmNetconvert.typ.xml,osmNetconvertUrbanDe.typ.xml,osmNetconvertPedestrians.typ.xml --osm-files anchieta_21012020.osm --output-file anchieta_21012020.net.xml --geometry.remove --roundabouts.guess --ramps.guess --junctions.join --tls.guess-signals --tls.discard-simple --tls.join
      ```
   
   Salen varias «warning», tiene pinta de giros muy bruscos. No parece haber problema para generar el fichero: `anchieta_21012020.net.xml`
   
   Ya se ve en el comando que se hacen varias asunciones sobre el tema de las conexiones y demás. Habría que mirar si las está haciendo correctamente, pero no parece haber errores tampoco (al menos a simple vista).
   
   El archivo se puede abrir con `netedit anchieta21012020.net.xml` para cargarse los nodos innecesarios.

4. MUY INTERESANTE: Se pueden generar caminos aleatorios gracias a un archivo que provee SUMO (https://sumo.dlr.de/docs/Tools/Trip.html#randomtripspy). Se encarga de absolutamente todo:

   ```
   python "C:\Program Files (x86)\Eclipse\Sumo\tools\randomTrips.py" -n anchieta_21012020_v2.net.xml --route-file viajes_aleatorios.rou.xml --min-distance 50
   ```

   También sirve para generar caminos de peatones:

   ```
   python "C:\Program Files (x86)\Eclipse\Sumo\tools\randomTrips.py" -n anchieta_21012020_v2.net.xml --route-file peatones_aleatorios.rou.xml --min-distance 50 --pedestrians
   ```

   Luego se añade todo a sumo:

   ```
   sumo-gui -n anchieta_21012020_v2.net.xml -r viajes_aleatorios.rou.xml -a peatones_aleatorios.rou.xml
   ```

## 5 feb. 2020: Intensidades de tráfico en las carreteras — Cabildo de Tenerife

Las técnicas de ingeniería del tráfico se clasifican en tres grupos:

1. Planeamiento de redes futuras.
2. **Ordenación del tráfico existente.**
3. Estudio y análisis de la circulación.

El informe del Cabildo trata del punto 3 dado que solo recoge datos. El TFG va del punto 2 y 3, puesto que se propone la modificación de la ordenación del tráfico con la posible introducción de semáforos.

Las mediciones del Cabildo son automáticas (p. ej.: cámaras).

Se analizan «tres variables fundamentales del tráfico»:

1. La intensidad.
2. La velocidad.
3. La composición (clases de vehículos).

Otras características interesantes:

- Separación entre vehículos.
- Intervalo (?).
- Ocupación.

### La intensidad

> Se define como intensidad de tráfico al número de vehículos que pasan a través de una sección fija de la carretera por unidad de tiempo.

Normalmente suele ser *vehículos por hora* o *vehículos por día*. 

Si el periodo es un año (365 días), entonces hablamos de **intensidad media diaria (IMD).**

El tiempo es el factor que más influye en la intensidad. Los dos estados de la variable intensidad en función del tiempo más interesantes son:

- IMD, visto antes.
- Intensidad horaria punta. Es el número de vehículos que pasan por una sección durante la hora que se considera con más circulación.

La IMD se usa principalmente para planeamiento, mientras que la intensidad horaria se emplea para el proyecto y la ordenación.

#### Ciclos de la intensidad del tráfico

> La intensidad de tráfico en cualquier carretera varía a lo largo del tiempo siguiendo una ley formada por una tendencia a largo plazo, normalmente creciente, a la que se superponen unas oscilaciones cíclicas (de año, semana, día) y unas variaciones aleatorias.

##### Ciclo anual 

Normalmente, la variación de la intensidad de un día cualquiera del mes no varía de un año a otro salvo que la vía o el suelo próximo se vea alterado física o funcionalmente. Estas variaciones son más acusadas en entornos rurales.

Factores que contribuyen a variaciones acusadas:

- El carácter turístico.
- La proximidad de una gran población que suele hacer viajes cortos de recreo.
- Las bajas intensidades de tráfico que hacen que los valores de las intensidades diarias
  sean más sensibles a situaciones extraordinarias. Esto se nota mucho en vías con
  I.M.D. inferior a 500 vehículos.

Factores que contribuyen a una distribución uniforme:

- El carácter industrial de la zona.
- La mayor proporción de tráfico pesado.
- La situación próxima al centro de una ciudad (donde normalmente el tráfico de un día laborable cualquiera no difiere en más de 10% de IMD).

##### Ciclo semanal

Las intensidad entre la semana laboral (L-V) y los fines de semana suele ser diferente y depende del tipo y función de cada vía.

En las vías urbanas el domingo suele ser el día de menor tráfico y el sábado no muy diferente del  de los días laborables.

En las vías interurbanas la influencia del tráfico en S y D varía a lo largo del año, siendo frecuente que el domingo, si hace buen tiempo, vea un repunte. La intensidad en los días festivos varía mucho más que la de los días laborables.

Factores que contribuyen a variaciones dentro del ciclo:

- La proximidad a una ciudad (que aumenta la influencia de los viajes de recreo en fines de semana).
- Las condiciones atmosféricas favorables.

##### Ciclo diario

El más importante desde el punto de vista técnico.

No es uniforme. Es normal que el 80-90% del tráfico se concentre en 16 horas, desde las 06:00-22:00.

#### Intensidad de hora punta

[sin notas]

#### Intensidad del tráfico

> Número de vehículos que hay por unidad de longitud. Se suele medir en vehículos/km. 

El valor máximo de densidad se obtiene cuando todos los vehículos están parados en fila sin huecos entre ellos.

Al aumentar la densidad resulta más difícil mantener la velocidad, aumenta la cantidad de maniobras y origina una incomodidad a la hora de conducir.

#### Velocidad

La velocidad de un vehículo se puede definir de tres maneras:

1. **Velocidad local o instantánea.** Velocidad de un vehículo al atravesar una sección de una vía.
2. **Velocidad de circulación (Vc).** Cociente entre la distancia recorrida en un tramo y el tiempo que el vehículo está en movimiento.
3. **Velocidad de recorrido (Vr).** Cociente entre la distancia recorrida en un tramo y el tiempo que transcurre desde que el vehículo inicia el viaje hasta que llega a su destino.

### 5/3/2020: Mejora en la conversión del archivo OSM

Comando mejorado: 

```
netconvert --type-files osmNetconvert.typ.xml,osmNetconvertPedestrians.typ.xml --osm-files mapa_anchieta.osm --output-file mapa_anchieta22.net.xml --geometry.remove --roundabouts.guess --ramps.guess --junctions.join --tls.guess-signals --tls.discard-simple --tls.join --no-turnarounds.except-deadend --crossings.guess --osm.all-attributes --sidewalks.guess
```

Preguntar a Segredo por qué las fases de los semáforos no coinciden con la cantidad de cruces.

Script para obtener el mapa desde la web:

"C:\Program Files (x86)\Eclipse\Sumo\tools\osmWebWizard.py"

### 17/3/2020: Mejora de las conexiones y adición de semáforos

Con respecto a la red:

- Se han ajustado mejor las intersecciones para poder introducir los semáforos adecuadamente.
- Se han ajustado los semáforos para permitir que el carril interior siempre permita la circulación, o sea, está siempre en verde cuando es posible (porque la entrada es de un carril; si tiene dos, no es posible).
- Se han ajustado los semáforos en cada paso adecuándolos a los pasos de peatones si los hubiera.

Con respecto al código: 

- Se ha creado:
  - Un ejecutor de sumo.
  - Un lector de archivos XML para leer las fases de los semáforos extraídas del archivo de red.
  - Un conjunto de clases que permitan manipular fácilmente los datos de los semáforos.

## 19 abr. 2020

- Creación de una instancia reducida, con corrección de errores de preferencia (en el caso de la incorporación a la TF-5 desde la vía del instituto) y supresión de algunas aceras inútiles
- Implementación de funcionalidad en el código: permite que se generen valores fijos para las fases que contengan ámbar

- Trabajo con induction loop detectors y con los datos del Cabildo para generar rutas y flujos de tráfico:

```
dfrouter.exe --net-file anchieta.net.xml --detector-files flowroute\induction-loop-detectors-TF-5.add.xml --measure-files flowroute\flow_TF-5_2020-02-27_8h_normal.csv --routes-output flowroute\routes_output.add.xml --detector-output flowroute\detectors_output.add.xml
```

```
python "C:\Program Files (x86)\Eclipse\Sumo\tools\detector\flowrouter.py" -n anchieta.net.xml -d flowroute\detectors_output.add.xml -f flowroute\flow_TF-5_2020-02-27_8h_normal.csv -o flowroute\ROUTES.add.xml -e flowroute\TRAFFICFLOW.add.xml -v --vclass passenger --revalidate-detectors --respect-zero
```

Nota: el `--revalidate-detectors` en el comando anterior hay que quitarlo si ya los he escrito a mano.

```
sumo-gui -n anchieta.net.xml -a flowroute\ROUTES.add.xml,flowroute\TRAFFICFLOW.add.xml
```



## 20 abr. 2020

- Se cambian de posición los datos de Guajara ascendente y descendente.
- Estimación de valores de flujo de tráfico para la rotonda.

## 21 abr 2020

- Había una mala conexión en geneto que permitía que los vehículos que entraban hicieran un giro de 180 grados para incorporarse a la autopista.
- No existe posibilidad de incorporación a la TF-5 desde la vía de la entrada a Anchieta. Cambios radicales en la instancia para permitirlo.
- Incorporación de nuevos detectores de tráfico para las vías de la rotonda, con valores estimados.

## 6 may. 2020

Generación de matrices y viajes:

```
od2trips.exe --taz-files taz.add.xml --od-matrix-files od_matrix_vissum_o_format.txt --flow-output OD_FLOW_DEF.add.xml
```



