---
title: Tworzenie źródła danych dla mapy | Mapy platformy Microsoft Azure
description: W tym artykule dowiesz się, jak utworzyć źródło danych i dodać je do mapy przy użyciu zestawu SDK w sieci Web microsoft azure maps.
author: rbrundritt
ms.author: richbrun
ms.date: 08/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: codepen
ms.openlocfilehash: 1675d63fd3a65beda46042f4a78535bb4e066e62
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77190227"
---
# <a name="create-a-data-source"></a>Tworzenie źródła danych

Zestaw Azure Maps Web SDK przechowuje dane w źródłach danych. Korzystanie ze źródeł danych optymalizuje operacje danych do wykonywania zapytań i renderowania. Obecnie istnieją dwa typy źródeł danych:

**Źródło danych GeoJSON**

A GeoJSON na źródło danych obciążenia i `DataSource` przechowywania danych lokalnie przy użyciu klasy. Dane GeoJSON można ręcznie tworzyć lub tworzyć przy użyciu klas pomocnika w obszarze nazw [atlas.data.](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data) Klasa `DataSource` udostępnia funkcje importowania lokalnych lub zdalnych plików GeoJSON. Zdalne pliki GeoJSON muszą być hostowane w punkcie końcowym obsługującym cors. Klasa `DataSource` zapewnia funkcjonalność dla danych punktu klastrowania. Dane można łatwo dodawać, usuwać i `DataSource` aktualizować za pomocą klasy.


> [!TIP]
> Załóżmy, że chcesz zastąpić wszystkie `DataSource`dane w pliku . Jeśli wywołasz `clear` ówczesne `add` funkcje, mapa może zostać ponownie renderowana dwukrotnie, co może spowodować pewne opóźnienie. Zamiast tego `setShapes` użyj funkcji, która usunie i zastąpi wszystkie dane w źródle danych i wyzwoli tylko jeden ponowny render mapy.

**Źródło kafli wektorowych**

Źródło kafli wektorowych opisuje sposób uzyskiwania dostępu do warstwy kafli wektorowych. Klasa [VectorTileSource służy](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.vectortilesource) do tworzenia wystąpienia źródła kafelków wektorowych. Warstwy kafli wektorowych są podobne do warstw kafli, ale nie są takie same. Warstwa kafli jest obrazem rastrowym. Warstwy kafli wektorowych są skompresowanym plikiem w formacie PBF. Ten skompresowany plik zawiera dane mapy wektora i jedną lub więcej warstw. Plik może być renderowany i stylizowany na kliencie, na podstawie stylu każdej warstwy. Dane na kafelku wektorowym zawierają obiekty geograficzne w postaci punktów, linii i wielokątów. Istnieje kilka zalet korzystania z warstw płytek wektorowych zamiast warstw płytek rastrowych:

 - Rozmiar pliku kafelka wektorowego jest zazwyczaj znacznie mniejszy niż równoważny kafelek rastrowy. W związku z tym używana jest mniejsza przepustowość. Oznacza to mniejsze opóźnienie, szybszą mapę i lepsze środowisko użytkownika.
 - Ponieważ kafelki wektorowe są renderowane na kliencie, dostosowują się do rozdzielczości urządzenia, na którym są wyświetlane. W rezultacie renderowane mapy wydają się bardziej dobrze zdefiniowane, z krystalicznie czystymi etykietami.
 - Zmiana stylu danych na mapach wektorowych nie wymaga ponownego pobrania danych, ponieważ nowy styl można zastosować na kliencie. Natomiast zmiana stylu warstwy kafli rastrowych zwykle wymaga załadowania kafelków z serwera, a następnie zastosowania nowego stylu.
 - Ponieważ dane są dostarczane w formie wektorowej, jest mniej przetwarzania po stronie serwera wymagane do przygotowania danych. W rezultacie nowsze dane mogą być udostępniane szybciej.

Wszystkie warstwy korzystające ze źródła `sourceLayer` wektorowego muszą określać wartość.

Po utworzeniu źródła danych można dodać do `map.sources` mapy za pośrednictwem właściwości, która jest [Menedżerem Źródłowym.](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.sourcemanager) Poniższy kod pokazuje, `DataSource` jak utworzyć i dodać go do mapy.

```javascript
//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);
```

Usługa Azure Maps jest zgodna [ze specyfikacją kafelka wektorowego mapbox](https://github.com/mapbox/vector-tile-spec), otwartym standardem.

## <a name="connecting-a-data-source-to-a-layer"></a>Łączenie źródła danych z warstwą

Dane są renderowane na mapie przy użyciu warstw renderowania. Do pojedynczego źródła danych może odwoływać się jedna lub więcej warstw renderowania. Następujące warstwy renderowania wymagają źródła danych:

- [Warstwa bąbelkowa](map-add-bubble-layer.md) - renderuje dane punktowe jako skalowane okręgi na mapie.
- [Warstwa symboli](map-add-pin.md) - renderuje dane punktów jako ikony lub tekst.
- [Warstwa mapy cieplnej](map-add-heat-map-layer.md) - renderuje dane punktowe jako mapę cieplną gęstości.
- [Warstwa liniowa](map-add-shape.md) - renderuj linię lub renderuj kontur wielokątów. 
- [Warstwa wielokąta](map-add-shape.md) - wypełnia obszar wielokąta jednolitym kolorem lub wzorem obrazu.

Poniższy kod pokazuje, jak utworzyć źródło danych, dodać je do mapy i połączyć z warstwą bąbelkową. Następnie zaimportuj dane punktowe GeoJSON z lokalizacji zdalnej do źródła danych. 

```javascript
//Create a data source and add it to the map.
var datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Create a layer that defines how to render points in the data source and add it to the map.
map.layers.add(new atlas.layer.BubbleLayer(datasource));

//Load the earthquake data.
datasource.importDataFromUrl('https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/significant_month.geojson');
```

Istnieją dodatkowe warstwy renderowania, które nie łączą się z tymi źródłami danych, ale bezpośrednio ładują dane do renderowania. 

- [Warstwa obrazu](map-add-image-layer.md) - nakłada pojedynczy obraz na mapę i wiąże jego narożniki z zestawem określonych współrzędnych.
- [Warstwa kafli](map-add-tile-layer.md) - nakłada warstwę płytek rastrowych na mapie.

## <a name="one-data-source-with-multiple-layers"></a>Jedno źródło danych z wieloma warstwami

Wiele warstw można podłączyć do jednego źródła danych. Istnieje wiele różnych scenariuszy, w których ta opcja jest przydatna. Rozważmy na przykład scenariusz, w którym użytkownik rysuje wielokąt. Powinniśmy renderować i wypełniać obszar wielokąta, gdy użytkownik doda punkty do mapy. Dodanie stylizowanej linii w celu zarysowania wielokąta ułatwia wyświetlanie krawędzi wielokąta podczas losowania. Aby wygodnie edytować pojedynczą pozycję w wielokąta, możemy dodać uchwyt, taki jak szpilka lub znacznik, nad każdą pozycją.

![Mapa przedstawiająca wiele warstw renderowania danych z jednego źródła danych](media/create-data-source-web-sdk/multiple-layers-one-datasource.png)

W większości platform mapowania potrzebny jest obiekt wielokątny, obiekt liniowy i pinezkę dla każdej pozycji w wielokącie. Ponieważ wielokąt jest modyfikowany, należy ręcznie zaktualizować linię i piny, które mogą szybko stać się złożone.

Dzięki usłudze Azure Maps wystarczy pojedynczy wielokąt w źródle danych, jak pokazano w poniższym kodzie.

```javascript
//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

//Create a polygon and add it to the data source.
dataSource.add(new atlas.data.Polygon([[[/* Coordinates for polygon */]]]));

//Create a polygon layer to render the filled in area of the polygon.
var polygonLayer = new atlas.layer.PolygonLayer(dataSource, 'myPolygonLayer', {
     fillColor: 'rgba(255,165,0,0.2)'
});

//Create a line layer for greater control of rendering the outline of the polygon.
var lineLayer = new atlas.layer.LineLayer(dataSource, 'myLineLayer', {
     color: 'orange',
     width: 2
});

//Create a bubble layer to render the vertices of the polygon as scaled circles.
var bubbleLayer = new atlas.layer.BubbleLayer(dataSource, 'myBubbleLayer', {
     color: 'orange',
     radius: 5,
     outlineColor: 'white',
     outlineWidth: 2
});

//Add all layers to the map.
map.layers.add([polygonLayer, lineLayer, bubbleLayer]);
```

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o klasach i metodach użytych w tym artykule:

> [!div class="nextstepaction"]
> [Datasource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [Usługi datasourceoptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.datasourceoptions?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [VectorTileSource (Źródło vectorTileSource)](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.vectortilesource?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [VectorTileSourceOptions (VectorTileSourceOptions)](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.vectortilesourceoptions?view=azure-maps-typescript-latest)

Aby uzyskać więcej przykładów kodu do dodania do map, zobacz następujące artykuły:

> [!div class="nextstepaction"]
> [Dodawanie menu podręcznego](map-add-popup.md)

> [!div class="nextstepaction"]
> [Korzystanie z wyrażeń stylu opartych na danych](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [Dodawanie warstwy symboli](map-add-pin.md)

> [!div class="nextstepaction"]
> [Dodawanie warstwy bąbelkowej](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Dodawanie warstwy linii](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Dodawanie warstwy wielokąta](map-add-shape.md)

> [!div class="nextstepaction"]
> [Dodawanie mapy cieplnej](map-add-heat-map-layer.md)

> [!div class="nextstepaction"]
> [Przykłady kodu](https://docs.microsoft.com/samples/browse/?products=azure-maps)