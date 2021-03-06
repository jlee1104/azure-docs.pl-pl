---
title: Schemat zdarzeń usługi Azure Event Grid dla usługi Azure Key Vault
description: Zawiera opis właściwości i schematu dla zdarzeń usługi Azure Key Vault za pomocą usługi Azure Event Grid
services: event-grid
author: msmbaldwin
ms.service: event-grid
ms.topic: reference
ms.date: 10/25/2019
ms.author: mbaldwin
ms.openlocfilehash: 17404388b2b6c3fee1c6ab666f7233a66817f642
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74082865"
---
# <a name="azure-event-grid-event-schema-for-azure-key-vault-preview"></a>Schemat zdarzeń usługi Azure Event Grid dla usługi Azure Key Vault (wersja zapoznawcza)

Ten artykuł zawiera właściwości i schemat zdarzeń w [usłudze Azure Key Vault](../key-vault/index.yml), obecnie w wersji zapoznawczej. Aby zapoznać się ze schematem zdarzeń, zobacz [Schemat zdarzeń usługi Azure Event Grid](event-schema.md).

## <a name="available-event-types"></a>Dostępne typy zdarzeń

Konto usługi Azure Key Vault generuje następujące typy zdarzeń:

| Pełna nazwa wydarzenia | Nazwa wyświetlana zdarzenia | Opis |
| ---------- | ----------- |---|
| Microsoft.KeyVault.CertificateNewVersionTworzenie | Utworzono nową wersję certyfikatu | Wyzwalane podczas tworzenia nowego certyfikatu lub nowej wersji certyfikatu. |
| Microsoft.KeyVault.CertificateNearExpiry | Certyfikat bliski wygaśnięcia | Wyzwalane, gdy bieżąca wersja certyfikatu wkrótce wygaśnie. (Wartość domyślna to 30 dni przed datą wygaśnięcia). |
| Microsoft.KeyVault.CertificateExexpired | Certyfikat wygasł | Wyzwalane po wygaśnięciu certyfikatu. |
| Microsoft.KeyVault.KeyNewVersionTworzenie | Utworzono nową wersję klucza | Wyzwalane podczas tworzenia nowego klucza lub nowej wersji klucza. |
| Microsoft.KeyVault.KeyNearExpiry | Klucz bliski wygaśnięcia | Wyzwalane, gdy bieżąca wersja klucza wkrótce wygaśnie. (Wartość domyślna to 30 dni przed datą wygaśnięcia). |
| Microsoft.KeyVault.KeyExpired | Klucz wygasł | Wyzwalane po wygaśnięciu klucza. |
| Microsoft.KeyVault.SecretNewVersionTworzone | Utworzono tajną nową wersję | Wyzwalane po utworzeniu nowej tajnej lub nowej tajnej wersji. |
| Microsoft.KeyVault.SecretNearExpiry | Sekret bliski wygaśnięcia | Wyzwalane, gdy bieżąca wersja klucza tajnego wkrótce wygaśnie. (Wartość domyślna to 30 dni przed datą wygaśnięcia). |
| Microsoft.KeyVault.SecretExpired | Tajemnica wygasła | Wyzwalane po wygaśnięciu klucza tajnego. |

## <a name="event-examples"></a>Przykłady zdarzeń

Poniższy przykład pokazuje schemat dla **microsoft.keyvault.SecretNewVersionCreated**:

```JSON
[
   {
      "id":"00eccf70-95a7-4e7c-8299-2eb17ee9ad64",
      "topic":"/subscriptions/{subscription-id}/resourceGroups/sample-rg/providers/Microsoft.KeyVault/vaults/sample-kv",
      "subject":"newsecret",
      "eventType":"Microsoft.KeyVault.SecretNewVersionCreated",
      "eventTime":"2019-07-25T01:08:33.1036736Z",
      "data":{
         "Id":"https://sample-kv.vault.azure.net/secrets/newsecret/ee059b2bb5bc48398a53b168c6cdcb10",
         "vaultName":"sample-kv",
         "objectType":"Secret",
         "objectName ":"newsecret",
         "version":" ee059b2bb5bc48398a53b168c6cdcb10",
         "nbf":"1559081980",
         "exp":"1559082102"
      },
      "dataVersion":"1",
      "metadataVersion":"1"
   }
]
```

## <a name="event-properties"></a>Właściwości zdarzenia

Zdarzenie ma następujące dane najwyższego poziomu:

| Właściwość | Typ | Opis |
| ---------- | ----------- |---|
| id | ciąg | Identyfikator obiektu, który wyzwolił to zdarzenie |
| nazwa skarbca | ciąg | Nazwa magazynu kluczy obiektu, który wyzwolił to zdarzenie |
| Objecttype | ciąg | Typ obiektu, który wyzwolił to zdarzenie |
| Nazwaobiektu | ciąg | Nazwa obiektu, który wyzwolił to zdarzenie |
| version | ciąg | Wersja obiektu, który wyzwolił to zdarzenie |
| Nbf | numer | Data niedoprzednia w sekundach od 1970-01-01T00:00:00Z obiektu, który wyzwolił to zdarzenie |
| exp | numer | Data ważności w sekundach od 1970-01-01T00:00:00Z obiektu, który wyzwolił to zdarzenie |


## <a name="next-steps"></a>Następne kroki

* Aby uzyskać wprowadzenie do usługi Azure Event Grid, zobacz [Co to jest siatka zdarzeń?](overview.md).
* Aby uzyskać więcej informacji na temat tworzenia subskrypcji usługi Azure Event Grid, zobacz [schemat subskrypcji usługi Event Grid](subscription-creation-schema.md).
* Aby dowiedzieć się więcej o integracji magazynu kluczy z siatką zdarzeń, zobacz [Monitorowanie usługi Key Vault za pomocą usługi Azure Event Grid (wersja zapoznawcza)](../key-vault/event-grid-overview.md).
* Aby zapoznać się z samouczkiem na temat integracji usługi Key Vault z siatką zdarzeń, zobacz [Odbieranie powiadomień o przechowalni kluczy i odpowiadanie na nie za pomocą usługi Azure Event Grid (wersja zapoznawcza)](../key-vault/event-grid-tutorial.md).
* Aby uzyskać dodatkowe wskazówki dotyczące usługi Key Vault i usługi Azure Automation, zobacz:
    - [Co to jest usługa Azure Key Vault?](../key-vault/key-vault-overview.md)
    - [Monitorowanie magazynu kluczy za pomocą usługi Azure Event Grid (wersja zapoznawcza)](../key-vault/event-grid-overview.md)
    - [Odbieranie powiadomień o przechowalni kluczy i odpowiadanie na nie za pomocą usługi Azure Event Grid (wersja zapoznawcza)](../key-vault/event-grid-tutorial.md)
    - [Omówienie usługi Azure Automation](../automation/index.yml)
