---
title: Zmienianie strumieni w interfejsie API usługi Azure Cosmos DB dla usługi MongoDB
description: Dowiedz się, jak korzystać ze strumieni zmian n Interfejsu API usługi Azure Cosmos DB dla mongodb, aby uzyskać zmiany wprowadzone do danych.
author: srchi
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 11/16/2019
ms.author: srchi
ms.openlocfilehash: ec1ec1a8a80953f8988355341ee7128bd29b982d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77467781"
---
# <a name="change-streams-in-azure-cosmos-dbs-api-for-mongodb"></a>Zmienianie strumieni w interfejsie API usługi Azure Cosmos DB dla usługi MongoDB

[Obsługa kanału informacyjnego zmiany](change-feed.md) w interfejsie API usługi Azure Cosmos DB dla usługi MongoDB jest dostępna przy użyciu interfejsu API strumieni zmian. Za pomocą interfejsu API strumieni zmian, aplikacje można uzyskać zmiany wprowadzone do kolekcji lub elementów w jednym niezależnego fragmentu. Później można podjąć dalsze działania na podstawie wyników. Zmiany elementów w kolekcji są przechwytywane w kolejności ich czasu modyfikacji i kolejność sortowania jest gwarantowana dla klucza niezależnego fragmentu.

> [!NOTE]
> Aby użyć strumieni zmian, utwórz konto w wersji 3.6 interfejsu API usługi Azure Cosmos DB dla usługi MongoDB lub nowszej wersji. Po uruchomieniu przykłady strumienia zmian względem wcześniejszej `Unrecognized pipeline stage name: $changeStream` wersji, może zostać wyświetlony błąd. 

W poniższym przykładzie pokazano, jak uzyskać strumienie zmian na wszystkie elementy w kolekcji. W tym przykładzie tworzy kursor do oglądania elementów, gdy są one wstawiane, aktualizowane lub zastępowane. Aby uzyskać strumienie zmian, wymagany jest $match etap, $project etap i opcja fullDocument. Obserwowanie operacji usuwania przy użyciu strumieni zmian nie jest obecnie obsługiwane. Aby obejść ten problem, można dodać znacznik miękki do elementów, które są usuwane. Na przykład można dodać atrybut w elemencie o nazwie "usunięte" i ustawić go na "true" i ustawić czas wygaśnięcia na elemencie, dzięki czemu można automatycznie usunąć go, a także śledzić go.

```javascript
var cursor = db.coll.watch(
    [
        { $match: { "operationType": { $in: ["insert", "update", "replace"] } } },
        { $project: { "_id": 1, "fullDocument": 1, "ns": 1, "documentKey": 1 } }
    ],
    { fullDocument: "updateLookup" });

while (!cursor.isExhausted()) {
    if (cursor.hasNext()) {
        printjson(cursor.next());
    }
}
```

W poniższym przykładzie pokazano, jak uzyskać zmiany elementów w pojedynczym niezależnego fragmentu. W tym przykładzie pobiera zmiany elementów, które mają klucz niezależnego fragmentu równa "a" i wartość klucza niezależnego fragmentu równa "1".

```javascript
var cursor = db.coll.watch(
    [
        { 
            $match: { 
                $and: [
                    { "fullDocument.a": 1 }, 
                    { "operationType": { $in: ["insert", "update", "replace"] } }
                ]
            }
        },
        { $project: { "_id": 1, "fullDocument": 1, "ns": 1, "documentKey": 1} }
    ],
    { fullDocument: "updateLookup" });

```

## <a name="current-limitations"></a>Bieżące ograniczenia

Następujące ograniczenia mają zastosowanie podczas korzystania ze strumieni zmian:

* Właściwości `operationType` `updateDescription` i właściwości nie są jeszcze obsługiwane w dokumencie wyjściowym.
* `insert`Typy `update`operacji `replace` i operacje są obecnie obsługiwane. Operacja usuwania lub inne zdarzenia nie są jeszcze obsługiwane.

Ze względu na te ograniczenia wymagane są opcje $match, $project i fullDocument, jak pokazano w poprzednich przykładach.

## <a name="error-handling"></a>Obsługa błędów

Następujące kody błędów i komunikaty są obsługiwane podczas korzystania ze strumieni zmian:

* **Kod błędu HTTP 429** — gdy strumień zmian jest ograniczany, zwraca pustą stronę.

* **NamespaceNotFound (OperationType Invalidate)** — po uruchomieniu strumienia zmian w kolekcji, która `NamespaceNotFound` nie istnieje lub jeśli kolekcja zostanie porzucona, zwracany jest błąd. Ponieważ `operationType` nie można zwrócić właściwości w dokumencie wyjściowym, `operationType Invalidate` zamiast `NamespaceNotFound` błędu zwracany jest błąd.

## <a name="next-steps"></a>Następne kroki

* [Automatyczne wykorzystywanie czasu do automatycznego wygaśnięcia danych w interfejsie API usługi Azure Cosmos DB dla usługi MongoDB](mongodb-time-to-live.md)
* [Indeksowanie w interfejsie API usługi Azure Cosmos DB dla usługi MongoDB](mongodb-indexing.md)
