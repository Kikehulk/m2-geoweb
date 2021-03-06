## Introducción
>Utilizando el mapa 3D vamos a crear un pequeño visor de rutas

>Dentro del directorio de **/geoweb/datos** tenmos el archivo **rutas.geojson** que contiene tres cuatro rutas


### Visor de rutas


#### Paso 1: Con VSCODE abrimos mapa3d.html i lo guardamos cómo mapa-rutas3d.html

* Cambiamos el título
* Cambiamos center y zoom para abrir mapa sobre una ruta

```html hl_lines="4 20 21"

    <html>
    <head>
    <meta charset='utf-8' />
    <title>Mapa rutas 3D</title>
    <meta name='viewport' content='initial-scale=1,maximum-scale=1,user-scalable=no' />
    <script src='https://api.mapbox.com/mapbox-gl-js/v2.0.1/mapbox-gl.js'></script>
    <link href='https://api.mapbox.com/mapbox-gl-js/v2.0.1/mapbox-gl.css' rel='stylesheet' />
    <link href='css/estilobase.css' rel='stylesheet' />
 
    <script src='js/3d.js'></script>
    <script>
        //Añadir vuestor token!!
        var map;
        function init() {
            mapboxgl.accessToken =
                'pk.eyJ1IjoiZ2lzbWFzdGVybTIiLCJhIjoiY2plZHhubTQxMTNoYzMza3Rqa3kxYTdrOCJ9.53B1E6mKD_EQOVb2Y0-SsA';
             map = new mapboxgl.Map({
                container: 'map',
                style: 'mapbox://styles/mapbox/satellite-streets-v10',
                center: [1.77878, 41.60044],
                zoom: 14,,
                attributionControl: false,
                pitch: 45,
                hash: true
            });

            map.addControl(new mapboxgl.AttributionControl({ compact: true }));
            map.addControl(new mapboxgl.NavigationControl());

            map.on('load', function () {
             add3D();

            }); //fin onload         

        } // final init
    </script>
    </head>

    <body onload="init()">
        
        <div id="map"></div>
    </body>

    </html>

```

#### Paso 2: Creamos archivo rutas.js

 * Dentro de nuestro directorio **/geoweb/js/** creamos el archivo **rutas.js**, dónde crearemos funciones especificas de nuestro proyecto  

 * Creamos la función **addRutas()**

```javascript

   function addRutas() {

            var url = 'datos/rutas.geojson';
            map.addSource('rutas', { type: 'geojson', data: url});

                 map.addLayer({
            'id': 'route',
            'type': 'line',
            'source': 'rutas',
            'layout': {
            'line-join': 'round',
            'line-cap': 'round'
            },
            'paint': {
            'line-color': '#ff0000',
            'line-width': 3
            }
            });

} //fin funcion

```

#### Paso 3: Llamar función addRuta

* Llamamos a la función **addRuta()** y cambiamos coordenadas iniciales y nivel de zoom del mapa para empezar encima de una ruta

```html hl_lines="11 33"

    <html>
    <head>
    <meta charset='utf-8' />
    <title>Mapa rutas 3D</title>
    <meta name='viewport' content='initial-scale=1,maximum-scale=1,user-scalable=no' />
    <script src='https://api.mapbox.com/mapbox-gl-js/v2.0.1/mapbox-gl.js'></script>
    <link href='https://api.mapbox.com/mapbox-gl-js/v2.0.1/mapbox-gl.css' rel='stylesheet' />
    <link href='css/estilobase.css' rel='stylesheet' />
 
    <script src='js/3d.js'></script>
    <script src='js/rutas.js'></script>
    <script>
        //Añadir vuestor token!!
        var map;
        function init() {
            mapboxgl.accessToken =
                'pk.eyJ1IjoiZ2lzbWFzdGVybTIiLCJhIjoiY2plZHhubTQxMTNoYzMza3Rqa3kxYTdrOCJ9.53B1E6mKD_EQOVb2Y0-SsA';
             map = new mapboxgl.Map({
                container: 'map',
                style: 'mapbox://styles/mapbox/satellite-streets-v10',
                center: [1.77878, 41.60044],
                zoom: 14,
                attributionControl: false,
                pitch: 45,
                hash: true
            });

            map.addControl(new mapboxgl.AttributionControl({ compact: true }));
            map.addControl(new mapboxgl.NavigationControl());

            map.on('load', function () {
             add3D();
             addRutas();

            }); //fin onload         

        } // final init
    </script>
    </head>

    <body onload="init()">
        
        <div id="map"></div>
    </body>

    </html>
```


!!! note "Visualizamos mapa"


#### Paso 4: Opciones de seleción

 * Vamos a crear una funcionalidad para hacer zoom a las tres rutas

 * Creamos un elementos HTML de tipo `<select>` para poder seleccionar una ruta

```html hl_lines="42 43 44 45 46 47 48"

<html>
    <head>
    <meta charset='utf-8' />
    <title>Mapa rutas 3D</title>
    <meta name='viewport' content='initial-scale=1,maximum-scale=1,user-scalable=no' />
    <script src='https://api.mapbox.com/mapbox-gl-js/v2.0.1/mapbox-gl.js'></script>
    <link href='https://api.mapbox.com/mapbox-gl-js/v2.0.1/mapbox-gl.css' rel='stylesheet' />
    <link href='css/estilobase.css' rel='stylesheet' />
 
    <script src='js/3d.js'></script>
    <script src='js/rutas.js'></script>
    <script>
        //Añadir vuestor token!!
        var map;
        function init() {
            mapboxgl.accessToken =
                'pk.eyJ1IjoiZ2lzbWFzdGVybTIiLCJhIjoiY2plZHhubTQxMTNoYzMza3Rqa3kxYTdrOCJ9.53B1E6mKD_EQOVb2Y0-SsA';
             map = new mapboxgl.Map({
                container: 'map',
                style: 'mapbox://styles/mapbox/satellite-streets-v10',
                center: [1.77878, 41.60044],
                zoom: 14,
                attributionControl: false,
                pitch: 45,
                hash: true
            });

            map.addControl(new mapboxgl.AttributionControl({ compact: true }));
            map.addControl(new mapboxgl.NavigationControl());

            map.on('load', function () {
             add3D();
             addRutas();

            }); //fin onload         

        } // final init
    </script>
    </head>

    <body onload="init()">
        <div class="panelTopIzquierda">
            <select onChange="zoomToRutas(this.value)">
                <option selected value="14/41.60044/1.77878">Ruta 1</option>
                <option value="12.96/41.76589/2.30274">Ruta 2</option>
                <option value="13.04/42.31892/3.26054">Ruta 3</option>
            </select>
         </div>
        <div id="map"></div>
    </body>

    </html>

```

#### Paso 6: Función de zoomToRutas

 * Añadimos a  **rutas.js** la funcion **zoomToRutas()**

 * Utilizamos método [https://docs.mapbox.com/mapbox-gl-js/api/map/#map#flyto](https://docs.mapbox.com/mapbox-gl-js/api/map/#map#flyto)

``` javascript
   function zoomToRutas(valores) {

    var coord = valores.split("/");

    map.flyTo({
            center: [coord[2], coord[1]],
            zoom: coord[0]
        });

    } //fin funcion

```


![alt text](img/mapbox-rutas.png "mapbox-rutas.png")


!!! success "¿Subimos el ejemplo al GitHub?"
	
	```bash

		git pull
        git add .
        git commit -m "visor rutas"
        git push

	``` 