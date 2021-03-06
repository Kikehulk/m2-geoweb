

### Visualizador de terremotos

!!! warning "Atencion!!"
    <h4>

    Nos han encargado realizar un mapa mundial para visualizar los últimos terremotos acontecidos

    * Sabemos que GeoNames.org tiene un servicio JSON para interrogar los terremotos

        [https://www.geonames.org/](https://www.geonames.org/){target=_blank}

    * Podemos utilizar el estilo dark de fondo

        [mapbox://styles/mapbox/dark-v10](mapbox://styles/mapbox/dark-v10) 


    * El estilo de Mapbox tiene un tipo "circle" que puedo cambiar de color y tamaño

        [https://docs.mapbox.com/mapbox-gl-js/style-spec/#layers-circle](https://docs.mapbox.com/mapbox-gl-js/style-spec/#layers-circle){target=_blank} 

    </h4>



#### Paso 1: Servicio GeoNames

* [Geonames.org](https://www.geonames.org/){target=_blank} Buscamos el servicio de terremotos y cuál es su implementación

* [https://www.geonames.org/export/JSON-webservices.html#earthquakesJSON](https://www.geonames.org/export/JSON-webservices.html#earthquakesJSON){target=_blank}


#### Paso 2:

 * Crearemos el archivo **terremotos.html**

 * Añadimos código con mapa base "dark"

```html
    <html>
    <head>
    <meta charset='utf-8' />
    <title>Terremotos</title>
    <meta name='viewport' content='initial-scale=1,maximum-scale=1,user-scalable=no' />
    <script src='https://api.mapbox.com/mapbox-gl-js/v2.0.1/mapbox-gl.js'></script>
    <link href='https://api.mapbox.com/mapbox-gl-js/v2.0.1/mapbox-gl.css' rel='stylesheet' />
    <link href='css/estilobase.css' rel='stylesheet' />
    <script>
        var map;
        function init() {

            mapboxgl.accessToken = "pk.eyJ1IjoiZ2lzbWFzdGVybTIiLCJhIjoiY2plZHhubTQxMTNoYzMza3Rqa3kxYTdrOCJ9.53B1E6mKD_EQOVb2Y0-SsA";
            map = new mapboxgl.Map({
                container: 'map',
                style: 'mapbox://styles/mapbox/dark-v10',
                center: [9.746, 40.473],
                zoom: 5.5,
                hash: true,
                pitch: 0,
                attributionControl: false
            });
            map.addControl(new mapboxgl.AttributionControl({
                compact: true
            }));
            map.addControl(new mapboxgl.NavigationControl());           

        } //fin init

    </script>
    </head>

    <body onload="init()">
        <div id='map'></div>
    </body>

    </html>

```

#### Paso 3:Creamos archivo JS

 * Dentro de nuestro directorio **/geoweb/js/** creamos el archivo **terremotos.js**, dónde crearemos funciones especificas de nuestro proyect  
 * Dentro de nuestro directorio **/geoweb/js/** creamos el archivo **utils.js** , dónde crearemos funciones genéricas que puedan ser reutilizadas    


#### Paso 4. Añadimos JS

 * Añadimos estos archivos (de momento vacíos) a **terremotos.html**

```html hl_lines="9 10"
    <html>
    <head>
    <meta charset='utf-8' />
    <title>Terremotos</title>
    <meta name='viewport' content='initial-scale=1,maximum-scale=1,user-scalable=no' />
    <script src='https://api.mapbox.com/mapbox-gl-js/v2.0.1/mapbox-gl.js'></script>
    <link href='https://api.mapbox.com/mapbox-gl-js/v2.0.1/mapbox-gl.css' rel='stylesheet' />
    <link href='css/estilobase.css' rel='stylesheet' />
    <script src='js/utils.js'></script>
    <script src='js/terremotos.js'></script>
    
    <script>
        var map;
        function init() {

            mapboxgl.accessToken = "pk.eyJ1IjoiZ2lzbWFzdGVybTIiLCJhIjoiY2plZHhubTQxMTNoYzMza3Rqa3kxYTdrOCJ9.53B1E6mKD_EQOVb2Y0-SsA";
            map = new mapboxgl.Map({
                container: 'map',
                style: 'mapbox://styles/mapbox/dark-v10',
                center: [9.746, 40.473],
                zoom: 5.5,
                hash: true,
                pitch: 0,
                attributionControl: false
            });
            map.addControl(new mapboxgl.AttributionControl({
                compact: true
            }));
            map.addControl(new mapboxgl.NavigationControl());           

        } //fin init

    </script>
    </head>

    <body onload="init()">
        <div id='map'></div>
    </body>

    </html>

```

#### Paso 5: función genérica GET

 * Dentro del archivo **js/utils.js**  vamos a crear una función asíconcrona genérica para enviar y recibir respuesta de cualquier servicio GET que retorne un JSON 

 * ``` async function ```: Es un funcion en JavaScript que retornará  un valor sólo cuando haya recibido la respuesta

 * ``` fetch ```: La API Fetch proporciona una interfaz JavaScript para acceder y manipular partes del canal HTTP, tales como peticiones y respuestas https://developer.mozilla.org/es/docs/Web/API/Fetch_API/Utilizando_Fetch. Para hacer peticiones tipo Ajax

```javascript

 async function enviarPeticion(url) {

  return fetch(url)
      .then(function (response) {
          return response.json()
      })
      .then(function (data) {
          //console.log('Respuesta', data);
          return data;
      }).catch(function (error) {
          console.log('Error', error);
          alert("Error peticion");
          return null;
      });

}

```

#### Paso 6 :convertir la respuesta de JSON GeoNames al formato GeoJSON

 * Creamos una función para convertir la respuesta de JSON GeoNames al formato GeoJSON en **terremotos.js**


 * Ejemplo peticion: [https://secure.geonames.org/earthquakesJSON?north=44.1&south=-9.9&east=-22.4&west=55.2&username=masterupc](https://secure.geonames.org/earthquakesJSON?north=44.1&south=-9.9&east=-22.4&west=55.2&username=masterupc){target=_blank}

* Estructura respuesta GeoNames

```json
{
   "earthquakes":[
      {
         "datetime":"2011-03-11 04:46:23",
         "depth":24.4,
         "lng":142.369,
         "src":"us",
         "eqid":"c0001xgp",
         "magnitude":8.8,
         "lat":38.322
      },
      {
         "datetime":"2012-04-11 06:38:37",
         "depth":22.9,
         "lng":93.0632,
         "src":"us",
         "eqid":"c000905e",
         "magnitude":8.6,
         "lat":2.311
      },
      {
         "datetime":"2007-09-12 09:10:26",
         "depth":30,
         "lng":101.3815,
         "src":"us",
         "eqid":"2007hear",
         "magnitude":8.4,
         "lat":-4.5172
      }
     
   ]
} 
```


 * Miramos web [https://geojson.io](https://geojson.io){target=_blank} cómo és un GeoJSON

* Creamos dentro de **/js/terremotos.js** la funcion terremotosGeonamesToGeoJSON

```javascript

 function terremotosGeonamesToGeoJSON(respuestaGeonames){

    var geoJSON ={
        "type": "FeatureCollection",
        "features": []
    };

    for (var i =0; i < respuestaGeonames.earthquakes.length; i++){

        geoJSON.features.push(
            {
                "type": "Feature",
                "properties": {"magnitude":respuestaGeonames.earthquakes[i].magnitude,
                                "datetime":respuestaGeonames.earthquakes[i].datetime },
                "geometry": {
                  "type": "Point",
                  "coordinates": [
                    respuestaGeonames.earthquakes[i].lng,
                    respuestaGeonames.earthquakes[i].lat
                  ]
                }
              }
        );

    } //fin loop

    return geoJSON;

    } //fin funcion

```

#### Paso 7 : Función generarPeticionTerremotos()

 * Vamos a añadir otra funcion dentro **terremotos.js** de para: 
    * Capturar las coordenadas de mapa (BoundigBox)
    * Crear la petición a Geonames
    * Enviar petición
    * Capturar la respuesta y convertirla a GeoJSON
    * Generar Source y Layer de Mapbox Style y añadir datos

!!! warning "Justo debajo toda de la función terremotosGeonamesToGeoJSON()  añadimos""

```javascript

 function generarPeticionTerremotos() {

    var peticion = 'https://secure.geonames.org/earthquakesJSON?' +
        'north=' + map.getBounds()._ne.lat + '&' +
        'south=' + map.getBounds()._sw.lat + '&' +
        'east=' + map.getBounds()._ne.lng + '&' +
        'west=' + map.getBounds()._sw.lng + '&' +
        'maxRows=50&' +
        'minMagnitude=5&' +
        'username=masterupc&';
        //date : 'yyyy-MM-d

    enviarPeticion(peticion).then(function (respuestaGeonames) {

        var geoJSON = terremotosGeonamesToGeoJSON(respuestaGeonames);

        if (!map.getSource("terremotos_source")) {

    map.addSource("terremotos_source", {
                type: "geojson",
                data: geoJSON
            });

            map.addLayer({
                'id': 'terremotos',
                'type': 'circle',
                'source': 'terremotos_source',
                'paint': {
                    'circle-color': [
                        'interpolate',
                        ['linear'],
                        ['get', 'magnitude'],
                        3, '#ebe709',
                        5, '#eb1809',
                        7, '#ef4bf2',
                    ],
                    'circle-opacity': 0.75,
                    'circle-radius': [
                        'interpolate',
                        ['linear'], ['get', 'magnitude'],
                        3, 8,
                        5, 16,
                        8, 32
                    ]
                }
            });

            map.addLayer({
                'id': 'terremotos-textos',
                'type': 'symbol',
                'source': 'terremotos_source',
                'layout': {
                    'text-field': [                            
                        'format', ['get', 'magnitude'],                               
                    ],
                    'text-size': 10
                },
                'paint': {
                    'text-color': 'rgba(255,255,255,1)'
                }
            });
           

        } else {

            map.getSource("terremotos_source").setData(geoJSON);

        }

    });

} // fin funcion

```

#### Paso 8:La función **generarPeticionTerremotos()**

* La función **generarPeticionTerremotos()** és la función principal que necesitamos que se ejecute:

    * Al cargar el mapa (evento `load`)
    * Cada vez que me muevo por el mapa (eventos `moveend` y `zoomend`)

```html hl_lines="30 31 32 33 34 35 36 37 38 39 40 41 42 43 44"

    <html>
    <head>
    <meta charset='utf-8' />
    <title>Terremotos</title>
    <meta name='viewport' content='initial-scale=1,maximum-scale=1,user-scalable=no' />
    <script src='https://api.mapbox.com/mapbox-gl-js/v2.0.1/mapbox-gl.js'></script>
    <link href='https://api.mapbox.com/mapbox-gl-js/v2.0.1/mapbox-gl.css' rel='stylesheet' />
    <link href='css/estilobase.css' rel='stylesheet' />
    <script src='js/utils.js'></script>
    <script src='js/terremotos.js'></script>
    <script>
        var map;
        function Init() {

            mapboxgl.accessToken ='pk.eyJ1IjoiZ2lzbWFzdGVybTIiLCJhIjoiY2plZHhubTQxMTNoYzMza3Rqa3kxYTdrOCJ9.53B1E6mKD_EQOVb2Y0-SsA';
            map = new mapboxgl.Map({
                container: 'map',
                style: 'mapbox://styles/mapbox/dark-v10',
                center: [9.746, 40.473],
                zoom: 5.5,
                hash: true,
                pitch: 0,
                attributionControl: false
            });
            map.addControl(new mapboxgl.AttributionControl({
                compact: true
            }));
            map.addControl(new mapboxgl.NavigationControl());

            map.on("load", function () {

                generarPeticionTerremotos();

            }); // fin load mapa

            map.on("zoomend", function () {

                generarPeticionTerremotos();
            });

            map.on("moveend", function () {

                generarPeticionTerremotos();
            });


        } //fin init

    </script>
    </head>

    <body onload="Init()">
    <div id='map'></div>
    </body>

    </html>

```

#### Paso 9:función para generar popups

* Dentro del archivo  **utils.js**  añadimos una función para enseñar popup, pero NO al hacer click sino simplemente al passar por encima


```javascript

var popup = new mapboxgl.Popup({
    closeButton: false,
    closeOnClick: false
    });

function addPopupToMap(nombreCapa) {

    map.on('mousemove', nombreCapa, function (e) {
  
      var text = "";
      //console.info(e);
      for (key in e.features[0].properties) {
  
        text += "<b>" + key + "</b>:" + e.features[0].properties[key] + "<br>";
      }
      
      popup.setLngLat(e.lngLat)
        .setHTML(text)
        .addTo(map);
  
    });
  
    map.on('mouseenter', nombreCapa, function () {
      map.getCanvas().style.cursor = 'pointer';
    });
  
    map.on('mouseleave', nombreCapa, function () {
      map.getCanvas().style.cursor = '';
      popup.remove();
    });
  
  }// fin funcion

```

#### Paso 10: Llamamos a la funcion **addPopupToMap()**

* Llamamos a la funcion **addPopupToMap()** pasando el nombre de nuestra capa activa:

  
```html hl_lines="33"

    <html>
    <head>
    <meta charset='utf-8' />
    <title>Terremotos</title>
    <meta name='viewport' content='initial-scale=1,maximum-scale=1,user-scalable=no' />
    <script src='https://api.mapbox.com/mapbox-gl-js/v2.0.1/mapbox-gl.js'></script>
    <link href='https://api.mapbox.com/mapbox-gl-js/v2.0.1/mapbox-gl.css' rel='stylesheet' />
    <link href='css/estilobase.css' rel='stylesheet' />
    <script src='js/utils.js'></script>
    <script src='js/terremotos.js'></script>
    <script>
        var map;
        function Init() {

            mapboxgl.accessToken ='pk.eyJ1IjoiZ2lzbWFzdGVybTIiLCJhIjoiY2plZHhubTQxMTNoYzMza3Rqa3kxYTdrOCJ9.53B1E6mKD_EQOVb2Y0-SsA';
            map = new mapboxgl.Map({
                container: 'map',
                style: 'mapbox://styles/mapbox/dark-v10',
                center: [9.746, 40.473],
                zoom: 5.5,
                hash: true,
                pitch: 0,
                attributionControl: false
            });
            map.addControl(new mapboxgl.AttributionControl({
                compact: true
            }));
            map.addControl(new mapboxgl.NavigationControl());

            map.on("load", function () {

                generarPeticionTerremotos();
                addPopupToMap("terremotos");

            }); // fin load mapa

            map.on("zoomend", function () {

                generarPeticionTerremotos();
            });

            map.on("moveend", function () {

                generarPeticionTerremotos();
            });


        } //fin init

    </script>
    </head>

    <body onload="Init()">
    <div id='map'></div>
    </body>

    </html>

```
![alt text](img/terremotos.png "terremotos.png")

!!! tip "¿Añadimos Titulo?"


!!! success "¿Subimos el ejemplo al GitHub?"
	
	```bash

		git pull
        git add .
        git commit -m "terremotos"
        git push

	```   

