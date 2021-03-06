---
title: Transformacja odnośnika w przepływie danych mapowania
description: Dane referencyjne z innego źródła przy użyciu transformacji odnośnika w przepływie danych mapowania.
author: kromerm
ms.reviewer: daperlov
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/23/2020
ms.openlocfilehash: 78c6c1363af011a90865770d88c0037e50e958c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240419"
---
# <a name="lookup-transformation-in-mapping-data-flow"></a>Transformacja odnośnika w przepływie danych mapowania

Transformacja odnośnika służy do odwoływania się do danych z innego źródła w strumieniu przepływu danych. Transformacja odnośnika dołącza kolumny z dopasowanych danych do danych źródłowych.

Transformacja odnośnika jest podobna do lewego sprzężenia zewnętrznego. Wszystkie wiersze ze strumienia podstawowego będą istnieć w strumieniu danych wyjściowych z dodatkowymi kolumnami ze strumienia odnośnych. 

## <a name="configuration"></a>Konfigurowanie

![Transformacja odnośnika](media/data-flow/lookup1.png "Wyszukiwanie")

**Strumień podstawowy:** Przychodzący strumień danych. Ten strumień jest odpowiednikiem lewej strony sprzężenia.

**Strumień odnośny:** Dane, które są dołączane do strumienia podstawowego. Dane, które są dodawane, zależą od warunków wyszukiwania. Ten strumień jest odpowiednikiem prawej strony sprzężenia.

**Dopasuj wiele wierszy:** Jeśli ta opcja jest włączona, wiersz z wieloma dopasowaniami w strumieniu podstawowym zwróci wiele wierszy. W przeciwnym razie tylko jeden wiersz zostanie zwrócony na podstawie warunku "Dopasuj na".

**Mecz na:** Widoczne tylko wtedy, gdy jest włączona opcja "Dopasuj wiele wierszy". Wybierz, czy ma być dopasowywać dowolne wiersze, pierwszy czy ostatni mecz. Zaleca się dowolny wiersz, ponieważ wykonuje on najszybciej. Jeśli wybrano pierwszy lub ostatni wiersz, musisz określić warunki sortowania.

**Warunki odnośne:** Wybierz kolumny, które mają być zgodne. Jeśli warunek równości jest spełniony, wiersze będą traktowane jako dopasowanie. Umieść wskaźnik myszy i wybierz opcję "Obliczona kolumna", aby wyodrębnić wartość przy użyciu [języka wyrażenia przepływu danych](data-flow-expression-functions.md).

Transformacja odnośnika obsługuje tylko dopasowania równości. Aby dostosować wyrażenie odnośnika, tak aby uwzględnić inne operatory, takie jak większe niż, zaleca się użycie [sprzężenia krzyżowego w transformacji sprzężenia](data-flow-join.md#custom-cross-join). Sprzężenie krzyżowe pozwoli uniknąć ewentualnych błędów produktu kartezjańskiego podczas wykonywania.

Wszystkie kolumny z obu strumieni są zawarte w danych wyjściowych. Aby upuścić zduplikowane lub niechciane kolumny, dodaj [transformację wyboru](data-flow-select.md) po transformacji odnośnika. Kolumny mogą być również porzucone lub zmienione w transformacji ujścia.

## <a name="analyzing-matched-rows"></a>Analizowanie dopasowanych wierszy

Po transformacji odnośnika `isMatch()` funkcja może służyć do sprawdzić, czy wyszukiwanie dopasowane dla poszczególnych wierszy.

![Wzór odnośnika](media/data-flow/lookup111.png "Wzór odnośnika")

Przykładem tego wzorca jest użycie transformacji podziału `isMatch()` warunkowego do podziału na funkcję. W powyższym przykładzie pasujące wiersze przechodzą przez górny strumień ```NoMatch``` i niepasujące wiersze przepływają przez strumień.

## <a name="testing-lookup-conditions"></a>Warunki wyszukiwania testowania

Podczas testowania transformacji wyszukiwania z podglądem danych w trybie debugowania należy użyć niewielkiego zestawu znanych danych. Podczas próbkowania wierszy z dużego zestawu danych nie można przewidzieć, które wiersze i klucze będą odczytywane do testowania. Wynik nie jest deterministyczny, co oznacza, że warunki sprzężenia mogą nie zwracać żadnych dopasowań.

## <a name="broadcast-optimization"></a>Optymalizacja emisji

W usłudze Azure Data Factory przepływy danych mapowania są wykonywane w środowiskach platformy Spark skalowane w poziomie. Jeśli zestaw danych może zmieścić się w przestrzeni pamięci węzła procesu roboczego, wydajność wyszukiwania można zoptymalizować, włączając transmisję.

![Sprzężenie emisji](media/data-flow/broadcast.png "Sprzężenie emisji")

Włączenie transmisji wypycha cały zestaw danych do pamięci. W przypadku mniejszych zestawów danych zawierających tylko kilka tysięcy wierszy nadawanie może znacznie poprawić wydajność wyszukiwania. W przypadku dużych zestawów danych ta opcja może prowadzić do wyjątku braku pamięci.

## <a name="data-flow-script"></a>Skrypt przepływu danych

### <a name="syntax"></a>Składnia

```
<leftStream>, <rightStream>
    lookup(
        <lookupConditionExpression>,
        multiple: { true | false },
        pickup: { 'first' | 'last' | 'any' },  ## Only required if false is selected for multiple
        { desc | asc }( <sortColumn>, { true | false }), ## Only required if 'first' or 'last' is selected. true/false determines whether to put nulls first
        broadcast: { 'none' | 'left' | 'right' | 'both' }
    ) ~> <lookupTransformationName>
```
### <a name="example"></a>Przykład

![Transformacja odnośnika](media/data-flow/lookup-dsl-example.png "Wyszukiwanie")

Skrypt przepływu danych dla powyższej konfiguracji wyszukiwania znajduje się we wzorze kodu poniżej.

```
SQLProducts, DimProd lookup(ProductID == ProductKey,
    multiple: false,
    pickup: 'first',
    asc(ProductKey, true),
    broadcast: 'none')~> LookupKeys
```
## 
Następne kroki

* [Sprzężenie](data-flow-join.md) i [istnieje](data-flow-exists.md) przekształcenia zarówno wziąć w wielu danych wejściowych strumienia
* Transformacja [podziału warunkowego](data-flow-conditional-split.md) umożliwia ```isMatch()``` dzielenie wierszy na pasujące i niepasujące wartości
