---
title: 'Szybki start: Sprawdzanie pisowni za pomocą interfejsu API REST i Pythona — sprawdzanie pisowni bing'
titleSuffix: Azure Cognitive Services
description: Rozpocznij korzystanie z interfejsu API REST sprawdzania pisowni bing, aby sprawdzić pisownię i gramatykę za pomocą tego przewodnika Szybki start.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 12/16/2019
ms.author: aahi
ms.openlocfilehash: 6b0977628f7c3d971804d8597f42425608028081
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75448458"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-python"></a>Szybki start: sprawdzanie pisowni za pomocą interfejsu API REST sprawdzania pisowni Bing i języka Python

Użyj tego przewodnika Szybki start, aby wykonać pierwsze wywołanie interfejsu API REST sprawdzania pisowni Bing. Ta prosta aplikacja w języku Python wysyła żądanie do interfejsu API i zwraca listę sugerowanych poprawek. Chociaż ta aplikacja jest napisana w języku Python, interfejs API jest usługą internetową zgodną z wzorcem REST i większością języków programowania. Kod źródłowy dla tej aplikacji jest dostępny w [usłudze GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingEntitySearchv7.py)

## <a name="prerequisites"></a>Wymagania wstępne

* Python [3.x](https://www.python.org)

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]

## <a name="initialize-the-application"></a>Inicjowanie aplikacji

1. Utwórz nowy plik w języku Python w ulubionym środowisku IDE lub edytorze i dodaj następującą instrukcję importu.

   ```python
   import requests
   import json
   ```

2. Utwórz zmienne dla tekstu, w którym ma być sprawdzana pisownia, klucza subskrypcji oraz punktu końcowego sprawdzania pisowni Bing. Można użyć globalnego punktu końcowego poniżej lub niestandardowego punktu końcowego [poddomeny](../../../cognitive-services/cognitive-services-custom-subdomains.md) wyświetlanego w witrynie Azure portal dla zasobu.

    ```python
    api_key = "<ENTER-KEY-HERE>"
    example_text = "Hollo, wrld" # the text to be spell-checked
    endpoint = "https://api.cognitive.microsoft.com/bing/v7.0/SpellCheck"
    ```

## <a name="create-the-parameters-for-the-request"></a>Tworzenie parametrów dla żądania

1. Utwórz nowy słownik, w którym element `text` jest kluczem, a tekst — wartością.

    ```python
    data = {'text': example_text}
    ```

2. Dodaj parametry dla żądania. Dołącz kod rynkowy `mkt=`po pliku . Kod rynku to kraj, z którego składasz wniosek. Ponadto po dorobić tryb `&mode=`sprawdzania pisowni po pliku . Tryb jest `proof` albo (połowy większość błędów pisowni `spell` / gramatyki) lub (połowy większość pisowni, ale nie tyle błędów gramatycznych).

    ```python
    params = {
        'mkt':'en-us',
        'mode':'proof'
        }
    ```

3. Dodaj nagłówek `Content-Type` i dodaj klucz subskrypcji do nagłówka `Ocp-Apim-Subscription-Key`.

    ```python
    headers = {
        'Content-Type': 'application/x-www-form-urlencoded',
        'Ocp-Apim-Subscription-Key': api_key,
        }
    ```

## <a name="send-the-request-and-read-the-response"></a>Wysyłanie żądania i odczytywanie odpowiedzi

1. Wyślij żądanie POST, używając biblioteki żądań.

    ```python
    response = requests.post(endpoint, headers=headers, params=params, data=data)
    ```

2. Uzyskaj i wydrukuj odpowiedź w formacie JSON.

    ```python
    json_response = response.json()
    print(json.dumps(json_response, indent=4))
    ```


## <a name="run-the-application"></a>Uruchamianie aplikacji

Jeśli używasz wiersza polecenia, użyj następującego polecenia, aby uruchomić aplikację.

```bash
python <FILE_NAME>.py
```

## <a name="example-json-response"></a>Przykładowa odpowiedź JSON

Po pomyślnym przetworzeniu żądania zostanie zwrócona odpowiedź w formacie JSON, jak pokazano w następującym przykładzie:

```json
{
   "_type": "SpellCheck",
   "flaggedTokens": [
      {
         "offset": 0,
         "token": "Hollo",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "Hello",
               "score": 0.9115257530801
            },
            {
               "suggestion": "Hollow",
               "score": 0.858039839213461
            },
            {
               "suggestion": "Hallo",
               "score": 0.597385084464481
            }
         ]
      },
      {
         "offset": 7,
         "token": "wrld",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "world",
               "score": 0.9115257530801
            }
         ]
      }
   ]
}
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie jednostronicowej aplikacji internetowej](../tutorials/spellcheck.md)

- [Czym jest interfejs API sprawdzania pisowni Bing?](../overview.md)
- [Dokumentacja interfejsu API sprawdzania pisowni Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference)
