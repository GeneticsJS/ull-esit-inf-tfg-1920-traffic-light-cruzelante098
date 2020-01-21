# Notas

Página web de SUMO: https://sumo.dlr.de/userdoc/index.html

## 21/01/2020: Limpieza de mapas de OpenStreetMap e importación en SUMO

TUTORIAL: https://sumo.dlr.de/docs/Tutorials/Import_from_OpenStreetMap.html 



1. Exportar el mapa desde OpenStreetMap (botón exportar)

2. Limpiar el mapa con JOSM (https://josm.openstreetmap.de/)

   1. JOSM te permite cambiar algunos detalles interesantes del mapa de OSM, como la importancia de la vía, los carriles, la velocidad, etc. Funciona bastante mejor que SUMO, así que es útil, pero hay funciones que JOSM no tiene (como señalar vías de doble carril con sentidos contrarios). En mi caso me limité a establecer algunos límites de velocidad que no estaban (TF5 sentido SC, av. Trinidad, la av. Astrofísico, etc). En general el mapa de OSM está muy completo.
   2. CONVIENE QUITAR AQUÍ LOS NODOS INNECESARIOS

3. Convertirlo al formato de SUMO:

   1. > In a [OpenStreetMap file](https://sumo.dlr.de/docs/OpenStreetMap_file.html), the highway attribute implicitly determines values of some other attributes, like the speed limit. You must tell SUMO these implicit values in the [SUMO edge type file](https://sumo.dlr.de/docs/SUMO_edge_type_file.html). It assigns default values to the road attributes speed limit, number of lanes, priority, one-way street and allowed vehicle classes depending on the highway type. The article [SUMO edge type file](https://sumo.dlr.de/docs/SUMO_edge_type_file.html) provides several pre-defined SUMO edge type files and explains how you can compile your own edge type file.
      >
      > So determine in this step of the tutorial the implicit values of every highway type as defined by the OpenStreetMap community. [Several edge-type files suitable for OpenStreetMap are included with SUMO. Some of them are meant to be combined.](https://sumo.dlr.de/docs/Networks/Import/OpenStreetMap.html#recommended_typemaps)

   2. Yo además de los dos archivos que se mencionan también le metí  **osmNetconvertPedestrians.typ.xml** para ver si me añade las aceras.
      
      ```
      netconvert --xml-type-files osmNetconvert.typ.xml,osmNetconvertUrbanDe.typ.xml,osmNetconvertPedestrians.typ.xml --osm-files anchieta_21012020.osm --output-file anchieta_21012020.net.xml --geometry.remove --roundabouts.guess --ramps.guess --junctions.join --tls.guess-signals --tls.discard-simple --tls.join
      ```
   
   3. Salen varias «warning», tiene pinta de giros muy bruscos. No parece haber problema para generar el fichero, sin embargo: `anchieta_21012020.net.xml`
   
   4. Ya se ve en el comando que se hacen varias asunciones sobre el tema de las conexiones y demás. Habría que mirar si las está haciendo correctamente, pero no parece haber errores tampoco (al menos a simple vista).
   
   5. El archivo se puede abrir con `netedit anchieta21012020.net.xml` para cargarse los nodos innecesarios.

