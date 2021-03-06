---
title: Interfejsy API REST usługi Azure Enterprise
description: W tym artykule opisano interfejsy API REST, których można używać z rejestracją usługi Azure Enterprise.
author: bandersmsft
ms.author: banders
ms.date: 03/03/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.reviewer: boalcsva
ms.openlocfilehash: a29af1c9239cc44b3d4a41d17df88e88ee926fbe
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "78272380"
---
# <a name="azure-enterprise-rest-apis"></a>Interfejsy API REST usługi Azure Enterprise

W tym artykule opisano interfejsy API REST, których można używać z rejestracją usługi Azure Enterprise. Wyjaśniono również, jak rozwiązywać typowe problemy z interfejsami API REST.

## <a name="consumption-and-usage-apis"></a>Interfejsy API użycia

Klienci korporacyjni platformy Azure firmy Microsoft mogą uzyskać informacje o użyciu i rozliczeniach za pośrednictwem interfejsów API REST. Właściciel roli (administrator przedsiębiorstwa, administrator działu, właściciel konta) musi włączyć dostęp do interfejsu API przez wygenerowanie klucza z poziomu witryny Azure EA Portal. Następnie każda osoba posiadająca numer rejestracji i klucz może uzyskać dostęp do danych za pośrednictwem interfejsu API.

### <a name="available-apis"></a>Dostępne interfejsy API

**Saldo i podsumowanie** — [Interfejs API salda i podsumowania](../../billing/billing-enterprise-api-balance-summary.md) zawiera comiesięczne podsumowanie informacji dotyczących sald, nowych zakupów, opłat za usługę Azure Marketplace, korekt i opłat za użycie nadwyżkowe. Aby uzyskać więcej informacji, zobacz [Interfejsy API raportowania dla klientów Enterprise — saldo i podsumowanie](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary).

**Szczegóły użycia** — [interfejs API szczegółów użycia](../../billing/billing-enterprise-api-usage-detail.md) zawiera dzienny podział ilości wykorzystanych zasobów i szacowane opłaty według rejestracji. Wynik zawiera również informacje na temat wystąpień, mierników i działów. Do interfejsu API można wysyłać zapytania według okresu rozliczeniowego lub określonej daty rozpoczęcia i zakończenia. Aby uzyskać więcej informacji, zobacz [Interfejsy API raportowania dla klientów Enterprise — szczegóły użycia](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail).

**Opłaty za sklep Marketplace** — [Interfejs API opłat za sklep Marketplace](../../billing/billing-enterprise-api-marketplace-storecharge.md) zwraca opłaty za witrynę Marketplace obliczone na podstawie użycia według dnia dla określonego okresu rozliczeniowego lub dat rozpoczęcia i zakończenia. Aby uzyskać więcej informacji, zobacz [Interfejsy API raportowania dla klientów Enterprise — opłaty za sklep Marketplace](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge).

**Arkusz cen** — [Interfejs API arkusza cen](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) udostępnia odpowiednią stawkę za każdy miernik dla rejestracji i okresu rozliczeniowego. Aby uzyskać więcej informacji, zobacz [Interfejsy API raportowania dla klientów Enterprise — arkusz cen](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet).

**Okresy rozliczeniowe** — [interfejs API okresów rozliczeniowych](../../billing/billing-enterprise-api-billing-periods.md) zwraca listę okresów rozliczeniowych, które zawierają dane dotyczące użycia dla rejestracji w odwrotnej kolejności chronologicznej. Każdy okres zawiera właściwość wskazującą trasę interfejsu API dla czterech zestawów danych: BalanceSummary, UsageDetails, Marketplace Charges i PriceSheet (Podsumowanie salda, Szczegóły użycia, Opłaty za witrynę Marketplace i Arkusz cen). Aby uzyskać więcej informacji, zobacz [Interfejsy API raportowania dla klientów Enterprise — okresy rozliczeniowe](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods).

### <a name="enable-api-data-access"></a>Włączanie dostępu do danych interfejsu API

Właściciel roli może wykonywać w witrynie Azure EA Portal następujące czynności. Wybierz kolejno pozycje **Raporty** > **Pobierz zestawienie użycia** > **Klucz dostępu interfejsu API**. Następnie może wykonać następujące czynności:

- Generowanie podstawowych i pomocniczych kluczy dostępu.
- Wyłączanie kluczy dostępu.
- Wyświetlanie dat rozpoczęcia i zakończenia kluczy dostępu.

### <a name="generate-or-retrieve-the-api-key"></a>Generowanie i pobieranie klucza interfejsu API

1. Zaloguj się jako administrator przedsiębiorstwa.
2. Kliknij pozycję **Raporty** w okienku nawigacji po lewej stronie, a następnie kliknij kartę **Pobierz zestawienie użycia**.
3. Kliknij pozycję **Klucz dostępu interfejsu API**.
4. W obszarze **Klucze dostępu rejestracji** wybierz symbol generowania klucza, aby wygenerować klucz podstawowy lub pomocniczy.
5. Wybierz pozycję **Rozwiń klucz**, aby wyświetlić cały wygenerowany klucz dostępu interfejsu API.
6. Wybierz pozycję **Kopiuj**, aby uzyskać klucz dostępu interfejsu API do użytku natychmiastowego.

![Przykład przedstawiający stronę klucza dostępu interfejsu API](./media/ea-portal-rest-apis/ea-create-generate-or-retrieve-the-api-key.png)

Jeśli chcesz dać klucze dostępu interfejsu API osobom, które nie są administratorami przedsiębiorstwa w Twojej rejestracji, wykonaj następujące czynności:

1. W oknie nawigacji po lewej stronie kliknij pozycję **Zarządzaj**.
2. Kliknij symbol ołówka obok pozycji **wyświetlania opłat przez administratora działu**.
3. Kliknij pozycję **Włącz**, a następnie kliknij pozycję **Zapisz**.
4. Kliknij symbol ołówka obok pozycji **wyświetlania opłat przez właściciela konta**.
5. Kliknij pozycję **Włącz**, a następnie kliknij pozycję **Zapisz**.

![Przykład przedstawiający włączone widoki opłat dla administratora działu i dla właściciela konta](./media/ea-portal-rest-apis/create-ea-generate-or-retrieve-api-key-enable-ao-do-view.png) Powyższe kroki zapewniają posiadaczom klucza dostępu interfejsu API dostęp do informacji o kosztach i cenach w raportach użycia.

### <a name="pass-keys-in-the-api"></a>Przekazywanie kluczy w interfejsie API

Przekazuj klucz interfejsu API dla każdego wywołania na potrzeby uwierzytelniania i autoryzacji. Przekaż następującą właściwość do nagłówków HTTP:

| Klucz nagłówka żądania | Wartość |
| --- | --- |
| Autoryzacja | Określ wartość w tym formacie: **bearer {API\_KEY}**
Przykład: bearer \&lt;APIKey\&gt; |

### <a name="swagger"></a>Swagger

Punkt końcowy struktury Swagger jest dostępny w [interfejsach API raportowania klientów Enterprise w wersji 3](https://consumption.azure.com/swagger/ui/index) dla następujących interfejsów API. Struktura Swagger pomaga w inspekcji interfejsu API. Użyj struktury Swagger w celu wygenerowania zestawów SDK klienta przy użyciu funkcji [AutoRest](https://github.com/Azure/AutoRest) lub narzędzia [Swagger CodeGen](https://swagger.io/swagger-codegen/). Dane dostępne po 1 maja 2014 r. są dostępne za pośrednictwem interfejsu API.

### <a name="api-response-codes"></a>Kody odpowiedzi interfejsu API

W przypadku korzystania z interfejsu API wyświetlane są kody stanu odpowiedzi. Poniższa tabela zawiera odpowiednie opisy.

| Kod stanu odpowiedzi | Komunikat | Opis |
| --- | --- | --- |
| 200 | OK | Brak błędów |
| 401 | Brak autoryzacji | Nie znaleziono klucza interfejsu API, jest on nieprawidłowy, wygasł itd. |
| 404 | Niedostępny | Nie znaleziono punktu końcowego raportu |
| 400 | Nieprawidłowe żądanie | Nieprawidłowe parametry — zakresy dat, numery EA itd. |
| 500 | Błąd serwera | Nieoczekiwany błąd podczas przetwarzania żądania |

### <a name="usage-and-billing-data-update-frequency"></a>Częstotliwość aktualizowania danych użycia i rozliczeń

Pliki danych użycia i rozliczeń są aktualizowane co 24 godziny dla bieżącego miesiąca rozliczeniowego. Może jednak wystąpić opóźnienie danych wynoszące maksymalnie trzy dni. Jeśli na przykład użycie występuje w poniedziałek, dane mogą nie być widoczne w pliku danych do czwartku.

### <a name="test-enrollment-for-development"></a>Rejestracja testowa na potrzeby programowania

Jeśli jesteś partnerem lub deweloperem bez rejestracji w usłudze Azure Enterprise i chcesz uzyskać dostęp do interfejsu API, możesz użyć rejestracji testowej. Nazwa rejestracji to _EnrollmentNumber 100_. Informacje o użyciu można znajdować i testować do czerwca 2018 r. Następnie można użyć następującego klucza, aby wywołać interfejs API i zobacz przykładowe dane.

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImpoeXA2UU9DWlZmY1pmdmhDVGR1OFdxeTJ5byJ9.eyJFbnJvbGxtZW50TnVtYmVyIjoiMTAwIiwiSWQiOiI1ZTc2ZmNiMy0xN2I4LTQ5ZDItYjdkOC0zMDU0YjUwOWY0MWYiLCJSZXBvcnRWaWV3IjoiU3lzdGVtIiwiUGFydG5lcklkIjoiIiwiRGVwYXJ0bWVudElkIjoiIiwiQWNjb3VudElkIjoiIiwiaXNzIjoiZWEubWljcm9zb2Z0YXp1cmUuY29tIiwiYXVkIjoiY2xpZW50LmVhLm1pY3Jvc29mdGF6dXJlLmNvbSIsImV4cCI6MTU4NjM5MDA2OSwibmJmIjoxNTcwNTc4ODY5fQ.lENR5pCBph6iZCVexUlN1b-j7StaILCyBewVHoILD-_fn8S2o2bHY1qUseGOkBwNlaFQfk2OZIo-jQYvnf3eP3UNrNVTCINT0APbc1RqgwSjZSxugVVHH9jnSzEjONkJaSKmi4tlidk6zkF1-uY-TPJkKxYN_9ar7BgLshF9JGXk7t8OZhxSCxDZc-smntu6ORFDl4gRZZVBKXhqOGjOAdYX5tPiGDF2Bxb68RSzh9Xyr5PXxKLx5yivZzUdo0-GFHo13V9w6a5VQM4R1w4_ro8jF8WAo3mpGZ_ovx_U5IY6zMNmi_AoA1mUyvTGotgcu94RragutoJRxAGHbNJZ0Q
```

### <a name="azure-service-catalog"></a>Wykaz usług platformy Azure

Wszystkie usługi platformy Azure są ogłaszane w wykazie w formacie CSV w blogu usługi Azure Storage. Wykaz ten jest przydatny, jeśli musisz utworzyć nadzorowany wykaz wszystkich usług platformy Azure dla Twojego systemu. Bieżący katalog znajduje się pod adresem [https://azurecatalog.blob.core.windows.net/catalog/AzureCatalog.csv](https://azurecatalog.blob.core.windows.net/catalog/AzureCatalog.csv).

### <a name="csv-data-file-details"></a>Szczegóły pliku danych CSV

Poniższe informacje opisują właściwości raportów interfejsu API.

#### <a name="usage-summary"></a>Podsumowanie użycia

Format JSON jest generowany na podstawie raportu CSV. W związku z tym format jest taki sam, jak w przypadku formatu CSV podsumowania. Nazwa kolumny jest dzierżona, dlatego w przypadku korzystania z danych podsumowania w formacie JSON należy wykonać deserializację do tabeli danych.

| Nazwa kolumny CSV | Nazwa kolumny JSON | Nowa kolumna JSON | Komentarz |
| --- | --- | --- | --- |
| AccountOwnerId | AccountOwnerLiveId | AccountOwnerLiveId |   |
| Nazwa konta | AccountName | AccountName |   |
| ServiceAdministratorId | ServiceAdministratorLiveId | ServiceAdministratorLiveId |   |
| SubscriptionId | SubscriptionId | SubscriptionId |   |
| SubscriptionGuid | MOCPSubscriptionGuid | SubscriptionGuid |   |
| Subscription Name | SubscriptionName | SubscriptionName |   |
| Data | Data | Data | Pokazuje datę uruchomienia raportu wykazu usług. Format to ciąg daty bez sygnatury czasowej. |
| Month | Month | Month |   |
| Day | Day | Day |   |
| Year | Year | Year |   |
| Product (Produkt) | BillableItemName | Product (Produkt) |   |
| Identyfikator miernika | ResourceGUID | Identyfikator miernika |   |
| Kategoria miernika | Usługa | MeterCategory | Przydatne do znajdowania usług. Dotyczy usług, które mają wiele właściwości ServiceType. Na przykład maszyn wirtualnych. |
| Podkategoria miernika | ServiceType | MeterSubCategory | Zapewnia drugi poziom szczegółowości dla usługi. Na przykład maszyna wirtualna A1 (system inny niż Windows).  |
| Region miernika | ServiceRegion | MeterRegion | Trzeci poziom szczegółowości wymagany dla usługi. Przydatne do znajdowania kontekstu regionu identyfikatora GUID zasobu. |
| Nazwa miernika | ServiceResource | MeterName | Nazwa usługi. |
| Zużyta ilość | ResourceQtyConsumed | ConsumedQuantity |   |
| ResourceRate | ResourceRate | ResourceRate |   |
| ExtendedCost | ExtendedCost | ExtendedCost |   |
| Lokalizacja zasobu | ServiceSubRegion | ResourceLocation |   |
| Użyta usługa | ServiceInfo | ConsumedService |   |
| Identyfikator wystąpienia | Składnik | InstanceId |   |
| ServiceInfo1 | ServiceInfo1 | ServiceInfo1 |   |
| ServiceInfo2 | ServiceInfo2 | ServiceInfo2 |   |
| AdditionalInfo | AdditionalInfo | AdditionalInfo |   |
| Tagi | Tagi | Tagi |   |
| Store Service Identifier   | OrderNumber | StoreServiceIdentifier   |   |
| Department Name | DepartmentName | DepartmentName |   |
| Cost Center | CostCenter | CostCenter |   |
| Jednostka miary | UnitOfMeasure | UnitOfMeasure | Przykładowe wartości: godziny, GB, zdarzenia, wypchnięcia, jednostka, godziny korzystania z jednostki, MB, jednostki dzienne |
| ResourceGroup | ResourceGroup | ResourceGroup |   |

#### <a name="azure-marketplace-report"></a>Raport witryny Azure Marketplace

| Nazwa kolumny CSV | Nazwa kolumny JSON | Nowa kolumna JSON |
| --- | --- | --- |
| AccountOwnerId | AccountOwnerId | AccountOwnerId |
| Nazwa konta | AccountName | AccountName |
| SubscriptionId | SubscriptionId | SubscriptionId |
| SubscriptionGuid | SubscriptionGuid | SubscriptionGuid |
| Subscription Name | SubscriptionName |  SubscriptionName |
| Data | BillingCycle |  Date (Tylko ciąg daty. Bez sygnatury czasowej)
| Month | Month |  Month |
| Day | Day |  Day |
| Year | Year |  Year |
| Identyfikator miernika | MeterResourceId |  MeterId |
| Publisher Name | PublisherFriendlyName |  PublisherName |
| Offer Name | OfferFriendlyName |  OfferName |
| Plan Name | PlanFriendlyName |  PlanName |
| Zużyta ilość | BilledQty |  ConsumedQuantity |
| ResourceRate | ResourceRate | ResourceRate |
| ExtendedCost | ExtendedCost | ExtendedCost |
| Jednostka miary | UnitOfMeasure | UnitOfMeasure |
| Identyfikator wystąpienia | InstanceId | InstanceId |
| Dodatkowe informacje | AdditionalInfo | AdditionalInfo |
| Tagi | Tagi | Tagi |
| Order Number | OrderNumber | OrderNumber |
| Department Name | DepartmentNames | DepartmentName |
| Cost Center | CostCenters |  CostCenter |
| Grupa zasobów | ResourceGroup |  ResourceGroup |

#### <a name="price-sheet"></a>Arkusz cen

| Nazwa kolumny CSV | Nazwa kolumny JSON | Komentarz |
| --- | --- | --- |
| Usługa | Usługa |  Brak zmian w cenie |
| Jednostka miary | UnitOfMeasure |   |
| Overage Part Number | ConsumptionPartNumber |   |
| Overage Unit Price | ConsumptionPrice |   |
| Currency Code | CurrencyCode |     |

### <a name="common-api-issues"></a>Typowe problemy związane z interfejsem API

Podczas korzystania z interfejsów API REST usługi Azure Enterprise mogą wystąpić następujące typowe problemy.

Możesz próbować użyć klucza interfejsu API, który nie ma poprawnego typu autoryzacji. Klucze interfejsu API są generowane przez:

- Administratora przedsiębiorstwa
- Administratora działu (DA)
- Właściciela konta (AO)

Klucz wygenerowany przez administratora przedsiębiorstwa zapewnia dostęp do wszystkich informacji dotyczących danej rejestracji. Administrator przedsiębiorstwa z prawami tylko do odczytu nie może wygenerować klucza interfejsu API.

Klucz wygenerowany przez administratora działu lub właściciela konta nie zapewnia dostępu do informacji o saldzie, opłatach i arkuszach cen.

Klucz interfejsu API wygasa co sześć miesięcy. Jeśli wygaśnie, należy go wygenerować ponownie.

Jeśli zostanie wyświetlony komunikat o błędzie przekroczenia limitu czasu, można go rozwiązać, zwiększając próg limitu czasu.

Może wystąpić błąd wygaśnięcia 401 (bez autoryzacji). Ten błąd jest zwykle spowodowany przez wygasły klucz. Jeśli klucz wygasł, można go wygenerować ponownie.

Mogą zostać wyświetlone komunikaty o błędach 400 i 404 (niedostępne) zwracane z wywołania interfejsu API, gdy nie ma dostępnych bieżących danych dla wybranego zakresu dat. Ten błąd może wystąpić na przykład z powodu zainicjowanego ostatnio przeniesienia rejestracji. Dane od określonej daty i z późniejszego okresu są teraz przechowywane w nowej rejestracji. Ten błąd może wystąpić również wtedy, gdy używasz nowego numeru rejestracji w celu pobrania informacji, które znajdują się w starej rejestracji.

## <a name="next-steps"></a>Następne kroki

- Administratorzy witryny Azure EA Portal powinni przeczytać artykuł [Administracja w witrynie Azure EA Portal](ea-portal-administration.md), aby poznać typowe zadania administracyjne.
- Jeśli potrzebujesz pomocy w rozwiązywaniu problemów z witryną Azure EA Portal, zobacz [Rozwiązywanie problemów z dostępem do witryny Azure EA Portal](ea-portal-troubleshoot.md).
