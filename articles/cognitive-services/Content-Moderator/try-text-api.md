---
title: Moderowanie tekstu za pomocą interfejsu API moderowania tekstu — Moderator treści
titleSuffix: Azure Cognitive Services
description: Moderowanie tekstu na dysku testowym przy użyciu interfejsu API moderowania tekstu w konsoli online.
services: cognitive-services
author: PatrickFarley
ms.author: pafarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 05/29/2019
ms.openlocfilehash: e0930558f31b27a77fa2cd6b44fcea2fe9091086
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "74538824"
---
# <a name="moderate-text-from-the-api-console"></a>Moderowanie tekstu z konsoli interfejsu API

Użyj [interfejsu API moderowania tekstu](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f) w usłudze Azure Content Moderator, aby skanować zawartość tekstową w poszukiwaniu wulgaryzmów i porównywać ją z listami niestandardowymi i udostępnionymi.

## <a name="get-your-api-key"></a>Pobierz klucz interfejsu API

Aby można było przetestować interfejs API w konsoli online, potrzebny jest klucz subskrypcji. Znajduje się on na karcie **Ustawienia** w polu **Ocp-Apim-Subscription-Key.** Aby uzyskać więcej informacji, zobacz [Omówienie](overview.md).

## <a name="navigate-to-the-api-reference"></a>Przejdź do odwołania do interfejsu API

Przejdź do [odwołania interfejsu API moderowania tekstu](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f). 

  Zostanie otwarta strona **Tekst — ekran.**

## <a name="open-the-api-console"></a>Otwieranie konsoli interfejsu API

W przypadku **konsoli testowania otwartego interfejsu API**wybierz region, który najbardziej opisuje Twoją lokalizację. 

  ![Tekst — wybór regionu strony ekranowej](images/test-drive-region.png)

  Zostanie otwarta konsola interfejsu API **Text — Screen.**

## <a name="select-the-inputs"></a>Wybierz dane wejściowe

### <a name="parameters"></a>Parametry

Zaznacz parametry kwerendy, których chcesz użyć na ekranie tekstowym. W tym przykładzie należy użyć wartości domyślnej dla **języka**. Można również pozostawić puste, ponieważ operacja automatycznie wykryje prawdopodobny język jako część jego wykonania.

> [!NOTE]
> Dla **language** parametru języka `eng` przypisz lub pozostaw go pusty, aby wyświetlić odpowiedź **klasyfikacji** wspomaganej maszynowo (funkcja podglądu). **Ta funkcja obsługuje tylko język angielski**.
>
> W przypadku wykrywania **wulgaryzmów** należy użyć [kodu ISO 639-3](http://www-01.sil.org/iso639-3/codes.asp) obsługiwanych języków wymienionych w tym artykule lub pozostawić go pustym.

W przypadku **autokorekty** **, pii**i **klasyfikuj (podgląd)** wybierz **true**. Pozostaw pole **Nrd** puste.

  ![Tekst — parametry kwerendy konsoli ekranowej](images/text-api-console-inputs.PNG)

### <a name="content-type"></a>Typ zawartości

W przypadku **zawartości typu**wybierz typ zawartości, którą chcesz prześwietlić. W tym przykładzie należy użyć domyślnego **tekstu/zwykłego** typu zawartości. W polu **Ocp-Apim-Subscription-Key** wprowadź klucz subskrypcji.

### <a name="sample-text-to-scan"></a>Przykładowy tekst do skanowania

W **treści Żądanie** wprowadź tekst. W poniższym przykładzie pokazano celowe literówki w tekście.

```
Is this a grabage or crap email abcdef@abcd.com, phone: 4255550111, IP: 255.255.255.255, 1234 Main Boulevard, Panapolis WA 96555. These are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 0800 820 3300. Also, 999-99-9999 looks like a social security number (SSN).
```

## <a name="analyze-the-response"></a>Analizowanie odpowiedzi

Poniższa odpowiedź pokazuje różne spostrzeżenia z interfejsu API. Zawiera potencjalne wulgaryzmy, dane osobowe, klasyfikację (wersja zapoznawcza) i wersję automatycznie poprawioną.

> [!NOTE]
> Funkcja "Klasyfikacja" wspomagana maszynowo znajduje się w wersji zapoznawczej i obsługuje tylko język angielski.

```json
{"OriginalText":"Is this a grabage or crap email abcdef@abcd.com, phone: 4255550111, IP: 255.255.255.255, 1234 Main Boulevard, Panapolis WA 96555.\r\nThese are all UK phone numbers: +44 123 456 7890 or 0234 567 8901 or 0456 789 0123.\r\nAlso, 999-99-9999 looks like a social security number (SSN).",
"NormalizedText":"Is this a grabage or crap email abcdef@ abcd. com, phone: 4255550111, IP: 255. 255. 255. 255, 1234 Main Boulevard, Panapolis WA 96555. \r\nThese are all UK phone numbers: +44 123 456 7890 or 0234 567 8901 or 0456 789 0123. \r\nAlso, 999- 99- 9999 looks like a social security number ( SSN) .",
"Misrepresentation":null,
"PII":{  
  "Email":[  
    {  
      "Detected":"abcdef@abcd.com",
      "SubType":"Regular",
      "Text":"abcdef@abcd.com",
      "Index":32
    }
  ],
  "IPA":[  
    {  
      "SubType":"IPV4",
      "Text":"255.255.255.255",
      "Index":72
    }
  ],
  "Phone":[  
    {  
      "CountryCode":"US",
      "Text":"4255550111",
      "Index":56
    },
    {  
      "CountryCode":"US",
      "Text":"425 555 0111",
      "Index":211
    },
    {  
      "CountryCode":"UK",
      "Text":"+44 123 456 7890",
      "Index":207
    },
    {  
      "CountryCode":"UK",
      "Text":"0234 567 8901",
      "Index":227
    },
    {  
      "CountryCode":"UK",
      "Text":"0456 789 0123",
      "Index":244
    }
  ],
  "Address":[  
    {  
      "Text":"1234 Main Boulevard, Panapolis WA 96555",
      "Index":89
    }
  ],
  "SSN":[  
    {  
      "Text":"999999999",
      "Index":56
    },
    {  
      "Text":"999-99-9999",
      "Index":266
    }
  ]
},
"Classification":{  
  "ReviewRecommended":true,
  "Category1":{  
    "Score":1.5113095059859916E-06
  },
  "Category2":{  
    "Score":0.12747249007225037
  },
  "Category3":{  
    "Score":0.98799997568130493
  }
},
"Language":"eng",
"Terms":[  
  {  
    "Index":21,
    "OriginalIndex":21,
    "ListId":0,
    "Term":"crap"
  }
],
"Status":{  
  "Code":3000,
  "Description":"OK",
  "Exception":null
},
"TrackingId":"2eaa012f-1604-4e36-a8d7-cc34b14ebcb4"
}
```

Szczegółowe wyjaśnienie wszystkich sekcji odpowiedzi JSON można znaleźć w przewodniku koncepcyjnym [moderacji tekstu.](text-moderation-api.md)

## <a name="next-steps"></a>Następne kroki

Użyj interfejsu API REST w kodzie lub postępuj zgodnie z [programem Szybki start .NET SDK,](dotnet-sdk-quickstart.md) aby zintegrować się z aplikacją.
