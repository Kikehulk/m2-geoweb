### Ejemplos avanzados

Vamos a ver algunos ejemplos más avanzados y complejos
 


#### ¿Para que no puede servir?

 * Como base para nuestra práctica final

 * Para ver la potencialidad de los estilos de mapbox

 * Para aprender nuevos métodos y funcionalidades de Mapbox GL JS

 * Para connectarnos y utlizar servicios externos

 * Para observar formas de programar y solucionar problemas
 
 * Para aprender a reutilizar código


### Animación y rotación del mapa

* En  JavaScript existe el método **requestAnimationFrame(callback)** informa al navegador que quieres realizar una animación y solicita que el navegador programe el repintado de la ventana para el próximo ciclo de animación. El método acepta como argumento una función a la que llamar antes de efectuar el repintado.
* Para cancelar animación podemos llamar **cancelAnimationFrame()**

* **MapBox GL Js** tiene diferentes métodos pensados para animar geometría o la cámara

* Rotar mapa:  [https://docs.mapbox.com/mapbox-gl-js/api/map/#map#rotateto](https://docs.mapbox.com/mapbox-gl-js/api/map/#map#rotateto){target=_blank}
* Ejemplo : [https://docs.mapbox.com/mapbox-gl-js/example/animate-camera-around-point/](https://docs.mapbox.com/mapbox-gl-js/example/animate-camera-around-point/){target=_blank}

* Mover camera: [https://docs.mapbox.com/mapbox-gl-js/api/properties/#freecameraoptions](https://docs.mapbox.com/mapbox-gl-js/api/properties/#freecameraoptions){target=_blank}
* Ejemplo: [https://docs.mapbox.com/mapbox-gl-js/example/free-camera-point/](https://docs.mapbox.com/mapbox-gl-js/example/free-camera-point/){target=_blank}


#### Ejemplo rotación de camara com mapa_rutas3d.html

* Añadimos funciones de rotación a **rutas.js**

``` javascript
        var animacion;
        function rotarCamara(timestamp) {

            rotacion =timestamp /100 ==360 ?0 : timestamp /100;
            map.rotateTo(rotacion, { duration: 0 });
        
            animacion = requestAnimationFrame(rotarCamara);
        }


        function finalRotarCamara() {

            cancelAnimationFrame(animacion);
        }   

```

* LLamamos y controlamos funciones de rotacion en **mapa-rutas3d.html**

```html hl_lines="34 38-50"

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
            rotarCamara(0);

            }); //fin onload      
            
            map.on("click",function(e){
                finalRotarCamara()

                });
            map.on("zoomstart",function(e){
                finalRotarCamara()

                });  
                
            map.on("zoomend",function(e){
                rotarCamara(0);

            });          

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

### Servicios WMS y plugins

* MapBox GL Js permite visualizar servicios WMS siempre que soporten el EPSG:3857
* Mapbox tiene tambien algunos pluguins, como por ejemplo el plugin **mapbox-gl-compare**
[https://docs.mapbox.com/mapbox-gl-js/example/mapbox-gl-compare](https://docs.mapbox.com/mapbox-gl-js/example/mapbox-gl-compare){target=_blank}

#### Ejemplo comparar Ortos del año 1945-46 (Vuelo Americano B) y Orto actual

WMS Ortos Históricas
[https://www.icgc.cat/es/Administracion-y-empresa/Servicios/Servicios-en-linea-Geoservicios/WMS-Ortoimatges/WMS-Ortofotos](https://www.icgc.cat/es/Administracion-y-empresa/Servicios/Servicios-en-linea-Geoservicios/WMS-Ortoimatges/WMS-Ortofotos){target=_blank}


* Creamos página **mapbox-compare.html**

```html 
<html>

<head>
    <meta charset='utf-8' />
    <title>Comparador</title>
    <meta name='viewport' content='initial-scale=1,maximum-scale=1,user-scalable=no' />
    <script src='https://api.mapbox.com/mapbox-gl-js/v2.0.1/mapbox-gl.js'></script>
    <link href='https://api.mapbox.com/mapbox-gl-js/v2.0.1/mapbox-gl.css' rel='stylesheet' />
    <script src="https://api.mapbox.com/mapbox-gl-js/plugins/mapbox-gl-compare/v0.4.0/mapbox-gl-compare.js"></script>
    <link rel="stylesheet"
        href="https://api.mapbox.com/mapbox-gl-js/plugins/mapbox-gl-compare/v0.4.0/mapbox-gl-compare.css"
        type="text/css" />
    <style>
        body {
            margin: 0;
            padding: 0;
            overflow: hidden
        }

        #map {
            position: absolute;
            top: 0;
            bottom: 0;
            width: 100%;
        }

        .map {
            position: absolute;
            top: 0;
            bottom: 0;
            width: 100%;
        }
    </style>
    <script>
        //Añadir vuestor token!!
        var map;
        function init() {
            mapboxgl.accessToken =
                'pk.eyJ1IjoiZ2lzbWFzdGVybTIiLCJhIjoiY2plZHhubTQxMTNoYzMza3Rqa3kxYTdrOCJ9.53B1E6mKD_EQOVb2Y0-SsA';


            var mapa1 = new mapboxgl.Map({
                container: 'mapa1',
                style: 'mapbox://styles/mapbox/satellite-v9',
                center: [2.16859, 41.3954],
                zoom: 13,
                attributionControl: false,
            });

            var mapa2 = new mapboxgl.Map({
                container: 'mapa2',
                style: 'mapbox://styles/mapbox/light-v10',
                center: [2.16859, 41.3954],
                zoom: 13,
                attributionControl: false,
            });

            mapa2.on('load', function () {
                mapa2.addSource("orto1945", {
                    type: "raster",
                    tiles: [ 
                        "https://geoserveis.icgc.cat/icc_ortohistorica/wms/service?layers=ovaa10m&bbox={bbox-epsg-3857}&format=image/png&styles=&service=WMS&version=1.1.1&request=GetMap&srs=EPSG:3857&transparent=true&width=512&height=512",
                    ],
                    tileSize: 512,
                    maxzoom: 19,
                    minZoom: 7.5,
                });
                //	ovab5m
                mapa2.addLayer({
                    id: "orto1945",
                    source: "orto1945",
                    type: "raster",
                    maxzoom: 18

                });
            })


            var map = new mapboxgl.Compare(mapa1, mapa2, "#comparador");

        } // final init
    </script>
</head>

<body onload="init()">
    <div id="comparador">
        <div id="mapa1" class="map"></div>
        <div id="mapa2" class="map"></div>
    </div>
</body>

</html>

```

!!! tip "Si quisieramos que fuera un estilo propio"

```js
    var miestilo= {
        version: 8,
        center: [1.537786, 41.837539],
        zoom: 12,
        bearing: 0,
        pitch: 0,
        sources: {
            orto1945: {
                type: "raster",
                tiles: [
                    "https://geoserveis.icgc.cat/icc_ortohistorica/wms/service?bbox={bbox-epsg-3857}&format=image/png&styles=&service=WMS&version=1.1.1&request=GetMap&srs=EPSG:3857&transparent=true&width=512&height=512&layers=ovaa10m",
                ],
                tileSize: 512,
                maxzoom: 19,
                minZoom: 7.5,
            },

        },
        sprite: "https://tilemaps.icgc.cat/tileserver/sprites/geologic/sprite",
        glyphs:
            "https://tilemaps.icgc.cat/tileserver/glyphs/{fontstack}/{range}.pbf",
        layers: [
            {
                id: "orto1945",
                source: "orto1945",
                type: "raster",
                maxzoom: 18

            },

        ],
    };
```


!!! success "¿Subimos el ejemplo al GitHub?"
	
	```bash

		git pull
        git add .
        git commit -m "ejemplos"
        git push

	```