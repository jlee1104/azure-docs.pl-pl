---
title: Tagi zawartości — Wizja komputerowa
titleSuffix: Azure Cognitive Services
description: Poznaj pojęcia związane z funkcją tagowania obrazów interfejsu API przetwarzania obrazów.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/08/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 887f6d9163f800ecbe8a5ab9a0715fd171f1d3d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80244515"
---
# <a name="applying-content-tags-to-images"></a>Stosowanie tagów zawartości do obrazów

Funkcja Wizja komputerowa zwraca znaczniki oparte na tysiącach rozpoznawalnych obiektów, żywych istot, scenerii i działań. Gdy tagi są niejednoznaczne lub wykraczają poza popularną wiedzę, odpowiedź interfejsu API zawiera „wskazówki”, aby wyjaśnić ich znaczenie w kontekście znanego otoczenia. Tagi nie są zorganizowane w formie taksonomii i nie występują hierarchie dziedziczenia. Kolekcja tagów zawartości stanowi podstawę „opisu” obrazu wyświetlanego jako język zrozumiały dla użytkownika w formie pełnych zdań. Należy pamiętać, że obecnie jedynym obsługiwanym językiem opisu obrazu jest język angielski.

Po przekazaniu obrazu lub określeniu adresu URL obrazu algorytmy przetwarzania obrazów tworzą tagi wyjściowe na podstawie obiektów, żywych istot i akcji zidentyfikowanych na obrazie. Tagowanie nie jest ograniczone do głównego tematu, na przykład do osoby na pierwszym planie, ale uwzględnia także otoczenie (wewnątrz lub na zewnątrz), meble, narzędzia, rośliny, zwierzęta, akcesoria, gadżety itd.

## <a name="image-tagging-example"></a>Przykład tagowania obrazów

Poniższa odpowiedź JSON ilustruje, co funkcja Przetwarzania Obrazów zwraca podczas oznaczania funkcji wizualnych wykrytych w przykładowym obrazie.

![Niebieski dom i podwórko](./Images/house_yard.png).

```json
{
    "tags": [
        {
            "name": "grass",
            "confidence": 0.9999995231628418
        },
        {
            "name": "outdoor",
            "confidence": 0.99992108345031738
        },
        {
            "name": "house",
            "confidence": 0.99685388803482056
        },
        {
            "name": "sky",
            "confidence": 0.99532157182693481
        },
        {
            "name": "building",
            "confidence": 0.99436837434768677
        },
        {
            "name": "tree",
            "confidence": 0.98880356550216675
        },
        {
            "name": "lawn",
            "confidence": 0.788884699344635
        },
        {
            "name": "green",
            "confidence": 0.71250593662261963
        },
        {
            "name": "residential",
            "confidence": 0.70859086513519287
        },
        {
            "name": "grassy",
            "confidence": 0.46624681353569031
        }
    ],
    "requestId": "06f39352-e445-42dc-96fb-0a1288ad9cf1",
    "metadata": {
        "height": 200,
        "width": 300,
        "format": "Jpeg"
    }
}
```

## <a name="use-the-api"></a>Używanie interfejsu API

Funkcja tagowania jest częścią interfejsu API [analizowania obrazu.](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) Ten interfejs API można wywołać za pośrednictwem natywnego sdk lub za pośrednictwem wywołań REST. Uwzględnij `Tags` w **parametrze kwerendy visualFeatures.** Następnie po otrzymaniu pełnej odpowiedzi JSON, po prostu przeanalizować `"tags"` ciąg zawartości sekcji.

* [Szybki start: obraz komputera .NET SDK](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
* [Szybki start: analizowanie obrazu (INTERFEJS API REST)](./quickstarts/csharp-analyze.md)

## <a name="next-steps"></a>Następne kroki

Poznaj powiązane pojęcia [kategoryzacji obrazów](concept-categorizing-images.md) i [opisywania obrazów.](concept-describing-images.md)
