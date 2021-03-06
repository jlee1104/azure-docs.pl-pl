---
title: Konfiguracja usługi — QnA Maker
description: Dowiedz się, jak i gdzie skonfigurować zasoby.
ms.topic: reference
ms.date: 02/21/2020
ms.openlocfilehash: 8ef6fecbfeb119d0c68ec5bc3bbc90ec449dbb7d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77651862"
---
# <a name="service-configuration"></a>Konfiguracja usługi

Program QnA Maker korzysta z kilku zasobów platformy Azure (usług), w tym funkcji Wyszukiwanie funkcji Cognitive Search, Usługa app service, plan usługi app service i usługa Application Insights.

Poniżej wymieniono wszystkie dostosowania tych ustawień obsługiwanych przez QnA Maker.

## <a name="app-service"></a>App Service

Program QnA Maker używa usługi App Service do zapewnienia środowiska wykonawczego kwerendy używanego przez [interfejs API generateAnswer.](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer)


Te ustawienia są dostępne w witrynie Azure portal dla usługi App Service. Ustawienia są dostępne po wybraniu **opcji Ustawienia**, a następnie **opcji Konfiguracja**.

Można ustawić indywidualne ustawienie za pomocą listy Ustawienia aplikacji lub zmodyfikować kilka ustawień, wybierając pozycję **Edycja zaawansowana**.

|Zasób|Ustawienie|
|--|--|
|Narzędzie AzureSearchAdminKey|Wyszukiwanie poznawcze - używane do przechowywania zestawu QnA i #1 Ranker|
|Usługa AzureSearchName|Wyszukiwanie poznawcze - używane do przechowywania zestawu QnA i #1 Ranker|
|Domyślnaaaaarwer|Tekst odpowiedzi, gdy nie znaleziono dopasowania|
|Identyfikator UserAppInsightsAppId|Dziennik czatu i telemetria|
|Klawisz UserAppInsightsKey|Dziennik czatu i telemetria|
|Nazwa aplikacji UserAppInsights|Dziennik czatu i telemetria|

Dowiedz [się, jak dodać usługę wyszukiwania funkcji Cognitive Search](./how-to/set-up-qnamaker-service-azure.md#configure-qna-maker-to-use-different-cognitive-search-resource) do usługi.

Należy **ponownie uruchomić** usługę ze strony **Przegląd** witryny Azure portal, po zakończeniu wprowadzania zmian.

## <a name="qna-maker-service"></a>Usługa QnA Maker

Usługa QnA Maker zapewnia konfigurację dla następujących użytkowników do współpracy nad jedną usługą QnA Maker i wszystkimi jej bazami wiedzy.

Dowiedz [się, jak dodać współpracowników](./how-to/collaborate-knowledge-base.md) do usługi.

## <a name="application-insights"></a>Application Insights

Usługa Application Insights nie ma żadnych ustawień konfiguracyjnych specyficznych dla programu QnA Maker.

## <a name="app-service-plan"></a>Plan usługi App Service

Plan usługi aplikacji nie ma ustawień konfiguracji specyficznych dla programu QnA Maker.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [formatach](reference-document-format-guidelines.md) dokumentów i adresów URL, które chcesz zaimportować do bazy wiedzy.