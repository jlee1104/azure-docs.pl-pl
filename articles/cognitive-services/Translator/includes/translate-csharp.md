---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: 64a934196bb964561f36b9d95a2467b149847225
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "69906713"
---
[!INCLUDE [Prerequisites](prerequisites-csharp.md)]

[!INCLUDE [Set up and use environment variables](setup-env-variables.md)]

## <a name="create-a-net-core-project"></a>Tworzenie projektu platformy .NET Core

Otwórz nowy wiersz polecenia (lub sesję terminalową), a następnie uruchom następujące polecenia:

```console
dotnet new console -o translate-sample
cd translate-sample
```

Pierwsze polecenie wykonuje dwie czynności. Tworzy nową aplikację konsolową platformy .NET i katalog o nazwie `translate-sample`. Drugie polecenie zmienia katalog na katalog projektu.

Następnie należy zainstalować element Json.Net. Z poziomu katalogu projektu uruchom:

```console
dotnet add package Newtonsoft.Json --version 11.0.2
```

## <a name="select-the-c-language-version"></a>Wybieranie wersji językowej języka języka C#

Ten szybki start wymaga języka C# 7.1 lub nowszego. Istnieje kilka sposobów, aby zmienić wersję języka C# dla projektu. W tym przewodniku pokażemy, jak `translate-sample.csproj` dostosować plik. Aby uzyskać wszystkie dostępne opcje, takie jak zmiana języka w programie Visual Studio, zobacz [Wybieranie wersji języka języka C#.](https://docs.microsoft.com/dotnet/csharp/language-reference/configure-language-version)

Otwórz projekt, a `translate-sample.csproj`następnie otwórz . Upewnij się, że `LangVersion` jest ustawiona na 7.1 lub nowszą. Jeśli nie ma grupy właściwości dla wersji językowej, dodaj następujące wiersze:

```xml
<PropertyGroup>
   <LangVersion>7.1</LangVersion>
</PropertyGroup>
```

## <a name="add-required-namespaces-to-your-project"></a>Dodawanie wymaganych obszarów nazw do projektu

Uruchomione wcześnie polecenie `dotnet new console` spowodowało utworzenie projektu obejmującego plik `Program.cs`. W tym pliku umieścisz kod aplikacji. Otwórz plik `Program.cs` i zastąp istniejące instrukcje using. Te instrukcje gwarantują dostęp do wszystkich typów wymaganych do skompilowania i uruchomienia przykładowej aplikacji.

```csharp
using System;
using System.Net.Http;
using System.Text;
using System.Threading.Tasks;
// Install Newtonsoft.Json with NuGet
using Newtonsoft.Json;
```

## <a name="create-classes-for-the-json-response"></a>Tworzenie klas dla odpowiedzi JSON

Następnie utworzymy zestaw klas, które są używane podczas deserializacji odpowiedzi JSON zwróconej przez interfejs API tekstu translatora.

```csharp
/// <summary>
/// The C# classes that represents the JSON returned by the Translator Text API.
/// </summary>
public class TranslationResult
{
    public DetectedLanguage DetectedLanguage { get; set; }
    public TextResult SourceText { get; set; }
    public Translation[] Translations { get; set; }
}

public class DetectedLanguage
{
    public string Language { get; set; }
    public float Score { get; set; }
}

public class TextResult
{
    public string Text { get; set; }
    public string Script { get; set; }
}

public class Translation
{
    public string Text { get; set; }
    public TextResult Transliteration { get; set; }
    public string To { get; set; }
    public Alignment Alignment { get; set; }
    public SentenceLength SentLen { get; set; }
}

public class Alignment
{
    public string Proj { get; set; }
}

public class SentenceLength
{
    public int[] SrcSentLen { get; set; }
    public int[] TransSentLen { get; set; }
}
```

## <a name="get-subscription-information-from-environment-variables"></a>Uzyskaj informacje o subskrypcji ze zmiennych środowiskowych

Dodaj następujące wiersze `Program` do klasy. Te wiersze odczytują klucz subskrypcji i punkt końcowy ze zmiennych środowiskowych i zgłasza błąd, jeśli napotkasz jakiekolwiek problemy.

```csharp
private const string key_var = "TRANSLATOR_TEXT_SUBSCRIPTION_KEY";
private static readonly string subscriptionKey = Environment.GetEnvironmentVariable(key_var);

private const string endpoint_var = "TRANSLATOR_TEXT_ENDPOINT";
private static readonly string endpoint = Environment.GetEnvironmentVariable(endpoint_var);

static Program()
{
    if (null == subscriptionKey)
    {
        throw new Exception("Please set/export the environment variable: " + key_var);
    }
    if (null == endpoint)
    {
        throw new Exception("Please set/export the environment variable: " + endpoint_var);
    }
}
// The code in the next section goes here.
```

## <a name="create-a-function-to-translate-text"></a>Tworzenie funkcji na potrzeby tłumaczenia tekstu

W `Program` klasie należy utworzyć funkcję asynchronizacyjną o nazwie `TranslateTextRequest()`. Ta funkcja przyjmuje `subscriptionKey`cztery `host` `route`argumenty: `inputText`, , i .

```csharp
// This sample requires C# 7.1 or later for async/await.
// Async call to the Translator Text API
static public async Task TranslateTextRequest(string subscriptionKey, string endpoint, string route, string inputText)
{
  /*
   * The code for your call to the translation service will be added to this
   * function in the next few sections.
   */
}
```

## <a name="serialize-the-translation-request"></a>Serializowanie żądania tłumaczenia

Następnie musimy utworzyć i serializować obiekt JSON obejmujący tekst, który chcesz przetłumaczyć. Należy pamiętać, że można przekazać więcej `body`niż jeden obiekt w pliku .

```csharp
object[] body = new object[] { new { Text = inputText } };
var requestBody = JsonConvert.SerializeObject(body);
```

## <a name="instantiate-the-client-and-make-a-request"></a>Tworzenie wystąpienia klienta i wysyłanie żądania

W tych wierszach są tworzone wystąpienia elementów `HttpClient` i `HttpRequestMessage`:

```csharp
using (var client = new HttpClient())
using (var request = new HttpRequestMessage())
{
  // In the next few sections you'll add code to construct the request.
}
```

## <a name="construct-the-request-and-print-the-response"></a>Konstruowanie żądania i wyświetlanie odpowiedzi

Wewnątrz elementu `HttpRequestMessage` wykonasz następujące czynności:

* Deklarowanie metody HTTP
* Konstruowanie identyfikatora URI żądania
* Wstawianie treści żądania (serializowanego obiektu JSON)
* Dodawanie wymaganych nagłówków
* Wykonywanie żądania asynchronicznego
* Drukowanie odpowiedzi przy użyciu klas utworzonych wcześniej

Dodaj następujący kod do elementu `HttpRequestMessage`:

```csharp
// Build the request.
// Set the method to Post.
request.Method = HttpMethod.Post;
// Construct the URI and add headers.
request.RequestUri = new Uri(endpoint + route);
request.Content = new StringContent(requestBody, Encoding.UTF8, "application/json");
request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);

// Send the request and get response.
HttpResponseMessage response = await client.SendAsync(request).ConfigureAwait(false);
// Read response as a string.
string result = await response.Content.ReadAsStringAsync();
// Deserialize the response using the classes created earlier.
TranslationResult[] deserializedOutput = JsonConvert.DeserializeObject<TranslationResult[]>(result);
// Iterate over the deserialized results.
foreach (TranslationResult o in deserializedOutput)
{
    // Print the detected input language and confidence score.
    Console.WriteLine("Detected input language: {0}\nConfidence score: {1}\n", o.DetectedLanguage.Language, o.DetectedLanguage.Score);
    // Iterate over the results and print each translation.
    foreach (Translation t in o.Translations)
    {
        Console.WriteLine("Translated to {0}: {1}", t.To, t.Text);
    }
}
```

Jeśli korzystasz z subskrypcji wielu usług usług Cognitive Services, należy również uwzględnić `Ocp-Apim-Subscription-Region` parametry żądania. [Dowiedz się więcej o uwierzytelnieniu za pomocą subskrypcji wielu usług](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication).

## <a name="put-it-all-together"></a>Zebranie wszystkich elementów

Ostatnim krokiem jest wywołanie instrukcji `TranslateTextRequest()` w funkcji `Main`. W tym przykładzie tłumaczymy na`de`niemiecki`it`( ),`ja`włoski (`th`), japoński ( ) i tajski ( ). Zlokalizuj `static void Main(string[] args)` i zastąp go tym kodem:

```csharp
static async Task Main(string[] args)
{
    // This is our main function.
    // Output languages are defined in the route.
    // For a complete list of options, see API reference.
    // https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate
    string route = "/translate?api-version=3.0&to=de&to=it&to=ja&to=th";
    // Prompts you for text to translate. If you'd prefer, you can
    // provide a string as textToTranslate.
    Console.Write("Type the phrase you'd like to translate? ");
    string textToTranslate = Console.ReadLine();
    await TranslateTextRequest(subscriptionKey, endpoint, route, textToTranslate);
    Console.WriteLine("Press any key to continue.");
    Console.ReadKey();
}
```

Zauważysz, że `Main`w , `subscriptionKey`deklarujesz `endpoint`, `route`i . Ponadto monitujesz użytkownika o wprowadzenie danych `Console.Readline()` z i przypisywanie wartości do `textToTranslate`.

## <a name="run-the-sample-app"></a>Uruchamianie przykładowej aplikacji

To wszystko. Teraz możesz uruchomić przykładową aplikację. Z wiersza polecenia (lub sesji terminalowej) przejdź do katalogu projektu, a następnie uruchom:

```console
dotnet run
```

## <a name="sample-response"></a>Przykładowa odpowiedź

Po uruchomieniu próbki powinny zostać wyświetlone następujące wydruki do terminala:

```bash
Detected input language: en
Confidence score: 1

Translated to de: Hallo Welt!
Translated to it: Salve, mondo!
Translated to ja: ハローワールド！
Translated to th: หวัดดีชาวโลก!
```

Ten komunikat jest zbudowany z surowego JSON, który będzie wyglądać następująco:

```json
[
  {
    "detectedLanguage": {
      "language": "en",
      "score": 1.0
    },
    "translations": [
      {
        "text": "Hallo Welt!",
        "to": "de"
      },
      {
        "text": "Salve, mondo!",
        "to": "it"
      },
      {
        "text": "ハローワールド！",
        "to": "ja"
      },
      {
        "text": "หวัดดีชาวโลก!",
        "to": "th"
      }
    ]
  }
]
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Upewnij się, że wszystkie poufne informacje, takie jak klucze subskrypcji, zostały usunięte z kodu źródłowego przykładowej aplikacji.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z odwołaniem do interfejsu API, aby zrozumieć wszystko, co można zrobić z interfejsem API tekstu translatora.

> [!div class="nextstepaction"]
> [Odwołanie API](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
