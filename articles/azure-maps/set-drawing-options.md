---
title: Moduł narzędzi do rysowania | Mapy Microsoft Azure
description: W tym artykule dowiesz się, jak ustawiać dane dotyczące opcji rysowania przy użyciu zestawu SDK sieci Web Microsoft Azure Maps
author: farah-alyasari
ms.author: v-faalya
ms.date: 01/29/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: f3634149b744b9a03f0ed89aafbc20932701bdbc
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2020
ms.locfileid: "77208186"
---
# <a name="use-the-drawing-tools-module"></a>Korzystanie z modułu narzędzi do rysowania

Zestaw SDK sieci Web Azure Maps udostępnia *moduł narzędzi do rysowania*. Ten moduł ułatwia rysowanie i edytowanie kształtów na mapie przy użyciu urządzenia wejściowego, takiego jak mysz lub ekran dotykowy. Podstawową klasą tego modułu jest [Menedżer rysowania](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest#setoptions-drawingmanageroptions-). Menedżer rysowania udostępnia wszystkie funkcje, które są konieczne do rysowania i edytowania kształtów na mapie. Może być używana bezpośrednio i jest zintegrowana z niestandardowym interfejsem użytkownika paska narzędzi. Można również użyć wbudowanej klasy [paska narzędzi rysunku](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest) . 

## <a name="loading-the-drawing-tools-module-in-a-webpage"></a>Ładowanie modułu narzędzi do rysowania na stronie sieci Web

1. Utwórz nowy plik HTML i [Zaimplementuj mapę w zwykły](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control)sposób.
2. Załaduj moduł narzędzi do rysowania Azure Maps. Możesz załadować go na jeden z dwóch sposobów:
    - Użyj ogólnie hostowanej wersji Content Delivery Network platformy Azure modułu Azure Maps Services. Dodaj odwołanie do arkusza stylów JavaScript i CSS w `<head>` elemencie pliku:

        ```html
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/drawing/0.1/atlas-drawing.min.css" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/javascript/drawing/0.1/atlas-drawing.min.js"></script>
        ```

    - Można też załadować moduł narzędzi do rysowania dla kodu źródłowego Azure Maps Web SDK lokalnie przy użyciu pakietu [Azure-Maps-Drawing-Tools](https://www.npmjs.com/package/azure-maps-drawing-tools) npm, a następnie hostować go przy użyciu aplikacji. Ten pakiet zawiera również definicje języka TypeScript. Użyj tego polecenia:
    
        > **npm Zainstaluj platformę Azure — Maps — narzędzia do rysowania**
    
        Następnie Dodaj odwołanie do arkusza stylów JavaScript i CSS w `<head>` elemencie pliku:

         ```html
        <link rel="stylesheet" href="node_modules/azure-maps-drawing-tools/dist/atlas-drawing.min.css" type="text/css" />
        <script src="node_modules/azure-maps-drawing-tools/dist/atlas-drawing.min.js"></script>
         ```

## <a name="use-the-drawing-manager-directly"></a>Korzystanie z Menedżera rysowania bezpośrednio

Po załadowaniu modułu narzędzi do rysowania w aplikacji można włączyć możliwości rysowania i edytowania za pomocą [Menedżera rysowania](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest#setoptions-drawingmanageroptions-). Możesz określić opcje dla Menedżera rysowania podczas tworzenia jego wystąpienia lub alternatywnie użyć funkcji `drawingManager.setOptions()`.

### <a name="set-the-drawing-mode"></a>Ustawianie trybu rysowania

Poniższy kod tworzy wystąpienie Menedżera rysowania i ustawia opcję **trybu** rysowania. 

```Javascript
//Create an instance of the drawing manager and set drawing mode.
drawingManager = new atlas.drawing.DrawingManager(map,{
    mode: "draw-polygon"
});
```

Poniższy kod stanowi kompletny przykład sposobu ustawiania trybu rysowania Menedżera rysowania. Kliknij mapę, aby rozpocząć rysowanie wielokąta.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Rysowanie wielokątu" src="//codepen.io/azuremaps/embed/YzKVKRa/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zobacz pióro <a href='https://codepen.io/azuremaps/pen/YzKVKRa/'>narysuj wielokąt</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) w <a href='https://codepen.io'>CodePen</a>.
</iframe>


### <a name="set-the-interaction-type"></a>Ustaw typ interakcji

Menedżer rysowania obsługuje trzy różne sposoby współpracy z mapą do rysowania kształtów.

* Współrzędne `click` są dodawane po kliknięciu przycisku myszy lub dotknięciem.
* Współrzędne `freehand ` są dodawane, gdy mysz lub dotknięcie zostanie przeciągnięte na mapie. 
* Współrzędne `hybrid` są dodawane, gdy mysz lub dotknięcie zostanie kliknięte lub przeciągnięte.

Poniższy kod włącza tryb rysowania Wielokąt i ustawia typ interakcji `freehand`do rysowania, która powinna być zgodna z menedżerem rysunku. 

```Javascript
//Create an instance of the drawing manager and set drawing mode.
drawingManager = new atlas.drawing.DrawingManager(map,{
    mode: "draw-polygon",
    interactionType: "freehand"
});
```

 Ten przykładowy kod implementuje funkcje rysowania wielokąta na mapie. Po prostu przytrzymaj lewy przycisk myszy i przeciągnij go wokół siebie, swobodnie.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Rysowanie swobodne" src="//codepen.io/azuremaps/embed/ZEzKoaj/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zapoznaj się <a href='https://codepen.io/azuremaps/pen/ZEzKoaj/'>z piórem</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) w witrynie <a href='https://codepen.io'>CodePen</a>.
</iframe>


### <a name="customizing-drawing-options"></a>Dostosowywanie opcji rysowania

Poprzednie przykłady pokazują, jak dostosować opcje rysowania podczas tworzenia wystąpienia Menedżera rysowania. Możesz również ustawić opcje Menedżera rysowania przy użyciu funkcji `drawingManager.setOptions()`. Poniżej znajduje się narzędzie do testowania dostosowywania wszystkich opcji Menedżera rysowania przy użyciu funkcji SetOptions.

<br/>

<iframe height="685" title="Dostosuj Menedżera rysowania" src="//codepen.io/azuremaps/embed/LYPyrxR/?height=600&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true" style='width: 100%;'>Zobacz pióro <a href='https://codepen.io/azuremaps/pen/LYPyrxR/'>Pobierz dane kształtu</a> według Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) w <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak używać dodatkowych funkcji modułu narzędzi do rysowania:

> [!div class="nextstepaction"]
> [Dodawanie paska narzędzi rysowania](map-add-drawing-toolbar.md)

> [!div class="nextstepaction"]
> [Pobierz dane kształtu](map-get-shape-data.md)

> [!div class="nextstepaction"]
> [Reagowanie na zdarzenia rysowania](drawing-tools-events.md)

> [!div class="nextstepaction"]
> [Typy interakcji i skróty klawiaturowe](drawing-tools-interactions-keyboard-shortcuts.md)

Dowiedz się więcej na temat klas i metod używanych w tym artykule:

> [!div class="nextstepaction"]
> [Zmapować](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Menedżer rysowania](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest)

> [!div class="nextstepaction"]
> [Rysowanie paska narzędzi](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest)
