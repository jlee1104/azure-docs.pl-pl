---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: d75c925ef55163ce06b2ceff585e230d95b38c77
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71837502"
---
[!INCLUDE [Prerequisites](prerequisites-go.md)]

[!INCLUDE [Setup and use environment variables](setup-env-variables.md)]

## <a name="create-a-project-and-import-required-modules"></a>Tworzenie projektu i importowanie wymaganych modułów

Utwórz nowy projekt w języku Go przy użyciu ulubionego środowiska IDE lub edytora. Następnie skopiuj ten fragment kodu do swojego projektu do pliku o nazwie `detect-language.go`.

```go
package main

import (
    "bytes"
    "encoding/json"
    "fmt"
    "log"
    "net/http"
    "net/url"
    "os"
)
```

## <a name="create-the-main-function"></a>Tworzenie funkcji main

W tym przykładzie spróbuje odczytać klucz subskrypcji i `TRANSLATOR_TEXT_SUBSCRIPTION_KEY` punkt `TRANSLATOR_TEXT_ENDPOINT`końcowy usługi Translator Text z następujących zmiennych środowiskowych: i . Jeśli nie znasz zmiennych środowiskowych, można `subscriptionKey` ustawić `endpoint` i jako ciągi i komentować instrukcje warunkowe.

Skopiuj ten kod do projektu:

```go
func main() {
    /*
    * Read your subscription key from an env variable.
    * Please note: You can replace this code block with
    * var subscriptionKey = "YOUR_SUBSCRIPTION_KEY" if you don't
    * want to use env variables. If so, be sure to delete the "os" import.
    */
    if "" == os.Getenv("TRANSLATOR_TEXT_SUBSCRIPTION_KEY") {
      log.Fatal("Please set/export the environment variable TRANSLATOR_TEXT_SUBSCRIPTION_KEY.")
    }
    subscriptionKey := os.Getenv("TRANSLATOR_TEXT_SUBSCRIPTION_KEY")
    if "" == os.Getenv("TRANSLATOR_TEXT_ENDPOINT") {
      log.Fatal("Please set/export the environment variable TRANSLATOR_TEXT_ENDPOINT.")
    }
    endpoint := os.Getenv("TRANSLATOR_TEXT_ENDPOINT")
    uri := endpoint + "/detect?api-version=3.0"
    /*
     * This calls our breakSentence function, which we'll
     * create in the next section. It takes a single argument,
     * the subscription key.
     */
    detect(subscriptionKey, uri)
}
```

## <a name="create-a-function-to-detect-the-text-language"></a>Tworzenie funkcji na potrzeby wykrywania języka tekstu

Utwórzmy funkcję na potrzeby wykrywania języka tekstu. Ta funkcja będzie przyjmować jeden argument — Twój klucz subskrypcji na potrzeby tłumaczenia tekstu w usłudze Translator.

```go
func detect(subscriptionKey string, uri string) {
    /*  
     * In the next few sections, we'll add code to this
     * function to make a request and handle the response.
     */
}
```

Następnie utwórzmy adres URL. Adres URL jest tworzony przy użyciu metod `Parse()` i `Query()`.

Skopiuj ten kod do funkcji `detect`.

```go
// Build the request URL. See: https://golang.org/pkg/net/url/#example_URL_Parse
u, _ := url.Parse(uri)
q := u.Query()
u.RawQuery = q.Encode()
```

>[!NOTE]
> Aby uzyskać więcej informacji na temat punktów końcowych, tras i parametrów żądania, zobacz [Translator Text API 3.0: Detect](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-detect) (Interfejs API 3.0 tłumaczenia tekstu w usłudze Translator: wykrywanie).

## <a name="create-a-struct-for-your-request-body"></a>Tworzenie struktury treści żądania

Następnie utwórz anonimową strukturę treści żądania i zakoduj ją jako dane JSON przy użyciu metody `json.Marshal()`. Dodaj ten kod do funkcji `detect`.

```go
// Create an anonymous struct for your request body and encode it to JSON
body := []struct {
    Text string
}{
    {Text: "Salve, Mondo!"},
}
b, _ := json.Marshal(body)
```

## <a name="build-the-request"></a>Tworzenie żądania

Teraz, gdy treść żądania została zakodowana jako dane JSON, możesz utworzyć żądanie POST i wywołać interfejs API tłumaczenia tekstu w usłudze Translator.

```go
// Build the HTTP POST request
req, err := http.NewRequest("POST", u.String(), bytes.NewBuffer(b))
if err != nil {
    log.Fatal(err)
}
// Add required headers to the request
req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)
req.Header.Add("Content-Type", "application/json")

// Call the Translator Text API
res, err := http.DefaultClient.Do(req)
if err != nil {
    log.Fatal(err)
}
```

Jeśli korzystasz z subskrypcji wielu usług usług Cognitive Services, należy również uwzględnić `Ocp-Apim-Subscription-Region` parametry żądania. [Dowiedz się więcej o uwierzytelnieniu za pomocą subskrypcji wielu usług](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication).

## <a name="handle-and-print-the-response"></a>Obsługa i wyświetlanie odpowiedzi

Dodaj następujący kod do funkcji `detect`, aby zdekodować odpowiedź JSON, a następnie sformatować i wyświetlić wynik.

```go
// Decode the JSON response
var result interface{}
if err := json.NewDecoder(res.Body).Decode(&result); err != nil {
    log.Fatal(err)
}
// Format and print the response to terminal
prettyJSON, _ := json.MarshalIndent(result, "", "  ")
fmt.Printf("%s\n", prettyJSON)
```

## <a name="put-it-all-together"></a>Zebranie wszystkich elementów

To wszystko. Utworzono prosty program, który będzie wywoływał interfejs API tłumaczenia tekstu w usłudze Translator i zwracał odpowiedź w formacie JSON. Teraz nadszedł czas, aby uruchomić program:

```console
go run detect-language.go
```

Jeśli chcesz porównać swój kod z naszym, kompletny przykład jest dostępny w witrynie [GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Go).

## <a name="sample-response"></a>Przykładowa odpowiedź

Po uruchomieniu próbki powinny zostać wyświetlone następujące wydruki do terminala:

> [!NOTE]
> Znajdź skrót kraju/regionu na tej [liście języków](https://docs.microsoft.com/azure/cognitive-services/translator/language-support).


```json
[
  {
    "alternatives": [
      {
        "isTranslationSupported": true,
        "isTransliterationSupported": false,
        "language": "pt",
        "score": 1
      },
      {
        "isTranslationSupported": true,
        "isTransliterationSupported": false,
        "language": "en",
        "score": 1
      }
    ],
    "isTranslationSupported": true,
    "isTransliterationSupported": false,
    "language": "it",
    "score": 1
  }
]
```

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z odwołaniem do interfejsu API, aby zrozumieć wszystko, co można zrobić z interfejsem API tekstu translatora.

> [!div class="nextstepaction"]
> [Odwołanie API](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
