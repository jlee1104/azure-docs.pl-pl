---
title: Szybki start biblioteki klienta javascript wyszukiwania jednostek Bing
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/06/2020
ms.author: aahi
ms.openlocfilehash: ac2f83c824014e16cfbe9ab18483b914ed8b077d
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "79136767"
---
Ten przewodnik Szybki start umożliwia rozpoczęcie wyszukiwania jednostek w bibliotece klienta wyszukiwania jednostek Bing dla języka JavaScript. Usługa Bing Entity Search ma interfejs API REST zgodny z większością języków programowania, ale biblioteka klienta zapewnia łatwy sposób integracji usługi z aplikacjami. Kod źródłowy tego przykładu można znaleźć w usłudze [GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/entitySearch.js).

## <a name="prerequisites"></a>Wymagania wstępne

* Najnowsza wersja środowiska [Node.js](https://nodejs.org/en/download/).

* [Zestaw SDK wyszukiwania jednostek Bing dla platformy Node.js](https://www.npmjs.com/package/@azure/cognitiveservices-entitysearch)

Aby zainstalować zestaw SDK wyszukiwania jednostek Bing:

1. Uruchom polecenie `npm install ms-rest-azure` w środowisku programistycznym.
2. Uruchom polecenie `npm install @azure/cognitiveservices-entitysearch` w środowisku programistycznym.

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](~/includes/cognitive-services-bing-entity-search-signup-requirements.md)]


## <a name="create-and-initialize-the-application"></a>Tworzenie i inicjowanie aplikacji

1. Utwórz nowy plik w języku JavaScript w ulubionym środowisku IDE lub edytorze i dodaj następujące wymagania.

    ```javascript
    const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
    const EntitySearchAPIClient = require('@azure/cognitiveservices-entitysearch');
    ```

2. Utwórz wystąpienie obiektu `CognitiveServicesCredentials` przy użyciu klucza subskrypcji. Następnie za pomocą tego obiektu utwórz wystąpienie klienta wyszukiwania.

    ```javascript
    let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
    let entitySearchApiClient = new EntitySearchAPIClient(credentials);
    ```

## <a name="send-a-request-and-receive-a-response"></a>Wysyłanie żądania i odbieranie odpowiedzi

1. Wyślij żądanie wyszukiwania jednostek za pomocą funkcji `entitiesOperations.search()`. Po otrzymaniu odpowiedzi wyświetl obiekt `queryContext`, liczbę zwróconych wyników i opis pierwszego wyniku.

    ```javascript
    entitySearchApiClient.entitiesOperations.search('seahawks').then((result) => {
        console.log(result.queryContext);
        console.log(result.entities.value);
        console.log(result.entities.value[0].description);
    }).catch((err) => {
        throw err;
    });
    ```

<!-- Removing until we can replace with a sanitized version.
![Entity results](media/entity-search-sdk-node-quickstart-results.png)
-->

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie jednostronicowej aplikacji sieci Web](../../tutorial-bing-entities-search-single-page-app.md)

* [Czym jest interfejs API wyszukiwania jednostek Bing?](../../overview.md)