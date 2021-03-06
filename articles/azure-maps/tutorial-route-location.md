---
title: 'Samouczek: Znajdź trasę do lokalizacji | Mapy platformy Microsoft Azure'
description: W tym samouczku pokazano, jak renderować trasę do lokalizacji (punktu zainteresowania) na mapie przy użyciu usługi routingu map microsoft azure.
author: philmea
ms.author: philmea
ms.date: 01/14/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 98c36176ecd2996e5f735c52017162a076ef4bde
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80333758"
---
# <a name="tutorial-route-to-a-point-of-interest-using-azure-maps"></a>Samouczek: Kierowanie do punktu zainteresowania za pomocą usługi Azure Maps

W tym samouczku pokazano, jak używać konta usługi Azure Maps i zestawu Route Service SDK w celu znalezienia trasy do punktu orientacyjnego. Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie nowej strony internetowej przy użyciu interfejsu API kontrolki mapy
> * Ustawianie współrzędnych adresu
> * Wysyłanie do usługi Route Service zapytania dotyczącego wskazówek dojazdu do punktu orientacyjnego

## <a name="prerequisites"></a>Wymagania wstępne

Zanim zaczniesz kontynuować, [postępuj](quick-demo-map-app.md#create-an-account-with-azure-maps)zgodnie z instrukcjami w sekcji Tworzenie konta , potrzebujesz subskrypcji z warstwą cenową S1. Wykonaj kroki opisane w [celu uzyskania klucza podstawowego,](quick-demo-map-app.md#get-the-primary-key-for-your-account) aby uzyskać klucz podstawowy dla swojego konta. Aby uzyskać więcej informacji na temat uwierzytelniania w usłudze Azure Maps, zobacz [zarządzanie uwierzytelnianiem w usłudze Azure Maps](how-to-manage-authentication.md).

<a id="getcoordinates"></a>

## <a name="create-a-new-map"></a>Tworzenie nowej mapy

Poniższe kroki pokazują, jak utworzyć statyczną stronę HTML osadzoną przy użyciu interfejsu API kontrolki mapy.

1. Na komputerze lokalnym utwórz nowy plik i nadaj mu nazwę **MapRoute.html**.
2. Dodaj następujące składniki HTML do pliku:

    ```HTML
    <!DOCTYPE html>
    <html>
    <head>
        <title>Map Route</title>
        <meta charset="utf-8">
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

        <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

        <!-- Add a reference to the Azure Maps Services Module JavaScript file. -->
        <script src="https://atlas.microsoft.com/sdk/javascript/service/2/atlas-service.min.js"></script>

        <script>
            var map, datasource, client;

            function GetMap() {
                //Add Map Control JavaScript code here.
            }
        </script>
        <style>
            html,
            body {
                width: 100%;
                height: 100%;
                padding: 0;
                margin: 0;
            }

            #myMap {
                width: 100%;
                height: 100%;
            }
        </style>
    </head>
    <body onload="GetMap()">
        <div id="myMap"></div>
    </body>
    </html>
    ```

    Zwróć uwagę, że nagłówek HTML zawiera pliki zasobów CSS i JavaScript obsługiwane przez bibliotekę kontrolek mapy platformy Azure. Zwróć uwagę na zdarzenie `onload` w treści strony, które spowoduje wywołanie funkcji `GetMap` po załadowaniu treści strony. Ta funkcja będzie zawierać śródwierszowy kod JavaScript umożliwiający dostęp do interfejsów API usługi Azure Maps. 

3. Dodaj następujący kod JavaScript do funkcji `GetMap`. Zastąp ciąg `<Your Azure Maps Key>` kluczem podstawowym skopiowanym z konta Mapy.

    ```JavaScript
   //Instantiate a map object
   var map = new atlas.Map("myMap", {
        //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
        authOptions: {
           authType: 'subscriptionKey',
           subscriptionKey: '<Your Azure Maps Key>'
        }
   });
   ```

    Element `atlas.Map` zapewnia kontrolkę dla wizualnej interakcyjnej mapy internetowej i jest składnikiem interfejsu API kontrolki mapy platformy Azure.

4. Zapisz plik i otwórz go w przeglądarce. Masz teraz podstawową mapę, którą możesz rozbudowywać.

   ![Wyświetlanie podstawowej mapy](media/tutorial-route-location/basic-map.png)

## <a name="define-how-the-route-will-be-rendered"></a>Definiowanie sposobu renderowania trasy

W tym samouczku zostanie wyrenderowana prosta trasa przy użyciu ikon symboli przedstawiających początek i koniec trasy oraz linii przedstawiającej przebieg trasy.

1. Po zainicjowaniu mapy dodaj następujący kod JavaScript.

    ```JavaScript
    //Wait until the map resources are ready.
    map.events.add('ready', function() {

        //Create a data source and add it to the map.
        datasource = new atlas.source.DataSource();
        map.sources.add(datasource);

        //Add a layer for rendering the route lines and have it render under the map labels.
        map.layers.add(new atlas.layer.LineLayer(datasource, null, {
            strokeColor: '#2272B9',
            strokeWidth: 5,
            lineJoin: 'round',
            lineCap: 'round'
        }), 'labels');

        //Add a layer for rendering point data.
        map.layers.add(new atlas.layer.SymbolLayer(datasource, null, {
            iconOptions: {
                image: ['get', 'icon'],
                allowOverlap: true
           },
            textOptions: {
                textField: ['get', 'title'],
                offset: [0, 1.2]
            },
            filter: ['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']] //Only render Point or MultiPoints in this layer.
        }));
    });
    ```
    
    W programie `ready` obsługi zdarzeń map tworzone jest źródło danych do przechowywania linii trasy oraz punktów początkowych i końcowych. Tworzona jest warstwa linii, która jest następnie dołączana do źródła danych w celu zdefiniowania sposobu renderowana linii trasy. Linia trasy będzie renderowana jako ładny odcień niebieskiego. Będzie miał szerokość pięciu pikseli, zaokrąglone sprzężenia linii i wersaliki. Podczas dodawania warstwy do mapy przekazywany jest drugi parametr o wartości `'labels'`. Określa on, że ta warstwa ma być renderowana poniżej etykiet mapy. Dzięki temu linia trasy nie zakryje etykiet dróg. Tworzona jest warstwa symboli, która jest następnie dołączana do źródła danych. Ta warstwa określa sposób renderowania punktów początkowych i końcowych. W takim przypadku dodano wyrażenia w celu pobrania informacji o obrazie ikony i etykiecie tekstowej z właściwości każdego obiektu punktu. 
    
2. W tym samouczku ustawimy punkt początkowy w siedzibie firmy Microsoft, a punkt końcowy na stacji paliw w Seattle. W programie `ready` obsługi zdarzeń map dodaj następujący kod.

    ```JavaScript
    //Create the GeoJSON objects which represent the start and end points of the route.
    var startPoint = new atlas.data.Feature(new atlas.data.Point([-122.130137, 47.644702]), {
        title: "Redmond",
        icon: "pin-blue"
    });

    var endPoint = new atlas.data.Feature(new atlas.data.Point([-122.3352, 47.61397]), {
        title: "Seattle",
        icon: "pin-round-blue"
    });

    //Add the data to the data source.
    datasource.add([startPoint, endPoint]);

    map.setCamera({
        bounds: atlas.data.BoundingBox.fromData([startPoint, endPoint]),
        padding: 80
    });
    ```

    Ten kod tworzy dwa [GeoJSON Point obiektów](https://en.wikipedia.org/wiki/GeoJSON) do reprezentowania punktów początkowych i końcowych trasy i dodaje punkty do źródła danych. Do każdego punktu są dodawane właściwości `title` i `icon`. Ostatni blok ustawia widok kamery przy użyciu szerokości i długości geograficznej punktów początkowych i końcowych, przy użyciu właściwości [Map setCamera.](/javascript/api/azure-maps-control/atlas.map#setcamera-cameraoptions---cameraboundsoptions---animationoptions-)

3. Zapisz plik **MapRoute.html** i odśwież przeglądarkę. Teraz mapa jest wyśrodkowany nad Seattle, i można zobaczyć niebieski pin oznaczający punkt początkowy i okrągły niebieski pin oznaczający punkt mety.

   ![Wyświetlanie tras punkt początkowy i końcowy na mapie](media/tutorial-route-location/map-pins.png)

<a id="getroute"></a>

## <a name="get-directions"></a>Uzyskiwanie wskazówek dojazdu

W tej sekcji pokazano, jak korzystać z interfejsu API usługi marszruty usługi Azure Maps. Interfejs API usługi marszruty znajduje trasę od danego punktu początkowego do punktu końcowego. W ramach tej usługi dostępne są interfejsy API do planowania *najszybszych,* *najkrótszych,* *ekologicznych*lub *ekscytujących* tras między dwoma lokalizacjami. Ta usługa umożliwia również użytkownikom planowanie tras w przyszłości przy użyciu rozległej historycznej bazy danych ruchu platformy Azure. Użytkownicy mogą zobaczyć przewidywanie czasów trwania trasy w dowolnym wybranym dniu i godzinie. Aby uzyskać więcej informacji, zobacz [Get route directions (Uzyskiwanie wskazówek dojazdu)](https://docs.microsoft.com/rest/api/maps/route/getroutedirections). Wszystkie następujące funkcje powinny zostać dodane **w ramach listy zdarzeń gotowych** do mapy, aby upewnić się, że ładują się po gotowym dostępie do zasobów mapy.

1. W funkcji GetMap dodaj następujące elementy do kodu JavaScript.

    ```JavaScript
    // Use SubscriptionKeyCredential with a subscription key
    var subscriptionKeyCredential = new atlas.service.SubscriptionKeyCredential(atlas.getSubscriptionKey());

    // Use subscriptionKeyCredential to create a pipeline
    var pipeline = atlas.service.MapsURL.newPipeline(subscriptionKeyCredential);

    // Construct the RouteURL object
    var routeURL = new atlas.service.RouteURL(pipeline);
    ```

   Tworzy `SubscriptionKeyCredential` do `SubscriptionKeyCredentialPolicy` uwierzytelniania żądań HTTP do usługi Azure Maps z kluczem subskrypcji. Przyjmuje `atlas.service.MapsURL.newPipeline()` w `SubscriptionKeyCredential` zasadach i tworzy [Pipeline wystąpienie.](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-maps-typescript-latest) Reprezentuje `routeURL` adres URL do operacji [marszruty](https://docs.microsoft.com/rest/api/maps/route) usługi Azure Maps.

2. Po skonfigurowaniu poświadczeń i adresu URL dodaj następujący kod JavaScript, aby utworzyć trasę od punktu początkowego do końcowego. Usługa `routeURL` routingu usługi Azure Maps żąda obliczania kierunków trasy. Kolekcja funkcji GeoJSON z odpowiedzi jest następnie `geojson.getFeatures()` wyodrębniana przy użyciu metody i dodawana do źródła danych.

    ```JavaScript
    //Start and end point input to the routeURL
    var coordinates= [[startPoint.geometry.coordinates[0], startPoint.geometry.coordinates[1]], [endPoint.geometry.coordinates[0], endPoint.geometry.coordinates[1]]];

    //Make a search route request
    routeURL.calculateRouteDirections(atlas.service.Aborter.timeout(10000), coordinates).then((directions) => {
        //Get data features from response
        var data = directions.geojson.getFeatures();
        datasource.add(data);
    });
    ```

3. Zapisz plik **MapRoute.html** i odśwież przeglądarkę. W przypadku pomyślnego połączenia z interfejsami API usługi Maps powinna pojawić się mapa podobna do poniższej.

    ![Kontrolka mapy platformy Azure i usługa Route Service](./media/tutorial-route-location/map-route.png)

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie nowej strony internetowej przy użyciu interfejsu API kontrolki mapy
> * Ustawianie współrzędnych adresu
> * Wysyłanie do usługi Route Service zapytania dotyczącego wskazówek dojazdu do punktu orientacyjnego

> [!div class="nextstepaction"]
> [Wyświetl pełny kod źródłowy](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/route.html)

> [!div class="nextstepaction"]
> [Zobacz próbkę na żywo](https://azuremapscodesamples.azurewebsites.net/?sample=Route%20to%20a%20destination)

Następny samouczek przedstawia tworzenie zapytania o trasę z ograniczeniami dotyczącymi np. sposobu podróży lub rodzaju ładunku, a następnie wyświetlanie wielu tras na tej samej mapie.

> [!div class="nextstepaction"]
> [Znajdowanie tras dla różnych sposobów podróży](./tutorial-prioritized-routes.md)
