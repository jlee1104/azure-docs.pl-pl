---
title: Integruj operacje zabezpieczeń i zarządzaj nimi & Microsoft Graph zabezpieczenia
description: Zwiększ ochronę przed zagrożeniami, wykrywanie i reagowanie aplikacji, korzystając z Microsoft Graph & zabezpieczeń Azure Logic Apps
services: logic-apps
ms.suite: integration
author: preetikr
ms.author: preetikr
ms.reviewer: v-ching, estfan, logicappspm
ms.topic: article
ms.date: 02/21/2020
tags: connectors
ms.openlocfilehash: b4f51b192d1a7c0ee14a769321793753e8217dea
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/25/2020
ms.locfileid: "77598837"
---
# <a name="improve-threat-protection-by-integrating-security-operations-with-microsoft-graph-security--azure-logic-apps"></a>Poprawa ochrony przed zagrożeniami przez integrację operacji zabezpieczeń z Microsoft Graph & zabezpieczeń Azure Logic Apps

Za pomocą [Azure Logic Apps](../logic-apps/logic-apps-overview.md) i łącznika [zabezpieczeń Microsoft Graph](https://docs.microsoft.com/graph/security-concept-overview) można ulepszyć, w jaki sposób aplikacja wykrywa, chroni i reaguje na zagrożenia przez Tworzenie zautomatyzowanych przepływów pracy na potrzeby integracji produktów, usług i partnerów zabezpieczeń firmy Microsoft. Można na przykład utworzyć [Azure Security Center elementy PlayBook](../security-center/security-center-playbooks.md) , które monitorują Microsoft Graph jednostki zabezpieczeń i zarządzają nimi, na przykład alerty. Poniżej przedstawiono niektóre scenariusze, które są obsługiwane przez łącznik zabezpieczeń Microsoft Graph:

* Otrzymuj alerty na podstawie zapytań lub identyfikatora alertu. Możesz na przykład uzyskać listę zawierającą alerty o wysokiej ważności.

* Aktualizowanie alertów. Można na przykład zaktualizować przypisania alertów, dodać komentarze do alertów lub alerty tagów.

* Monitoruj, kiedy alerty są tworzone lub zmieniane, tworząc [subskrypcje alertów (webhook)](https://docs.microsoft.com/graph/api/resources/webhooks).

* Zarządzaj subskrypcjami alertów. Możesz na przykład uzyskać aktywne subskrypcje, zwiększyć czas wygaśnięcia subskrypcji lub usunąć subskrypcje.

Przepływ pracy aplikacji logiki może korzystać z akcji, które pobierają odpowiedzi z łącznika zabezpieczeń Microsoft Graph i udostępniają te dane wyjściowe innym akcjom w przepływie pracy. Możesz również mieć inne akcje w przepływie pracy, używając danych wyjściowych akcji łącznika zabezpieczeń Microsoft Graph. Jeśli na przykład alerty o wysokiej ważności są uzyskiwane za pośrednictwem łącznika zabezpieczeń Microsoft Graph, można wysłać te alerty w wiadomości e-mail przy użyciu łącznika programu Outlook. 

Aby dowiedzieć się więcej o zabezpieczeniach Microsoft Graph, zobacz [Omówienie interfejsu API zabezpieczeń Microsoft Graph](https://aka.ms/graphsecuritydocs). Jeśli jesteś nowym sposobem logiki aplikacji, zapoznaj [się z tematem Azure Logic Apps?](../logic-apps/logic-apps-overview.md). Jeśli szukasz Microsoft Flow lub PowerApps, zobacz [co to jest przepływ?](https://flow.microsoft.com/) lub [co to jest usługa powerapps?](https://powerapps.microsoft.com/)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/). 

* Aby korzystać z łącznika zabezpieczeń Microsoft Graph, musisz mieć *jawnie daną* zgodę administratora dzierżawy Azure Active Directory (AD), która jest częścią [Microsoft Graph wymagania dotyczące uwierzytelniania zabezpieczeń](https://aka.ms/graphsecurityauth). Ta zgoda wymaga identyfikatora aplikacji i nazwy łącznika zabezpieczeń Microsoft Graph, który można również znaleźć w [Azure Portal](https://portal.azure.com):

  | Właściwość | Value |
  |----------|-------|
  | **Nazwa aplikacji** | `MicrosoftGraphSecurityConnector` |
  | **Identyfikator aplikacji** | `c4829704-0edc-4c3d-a347-7c4a67586f3c` |
  |||

  Aby przyznać zgodę na łącznik, Administrator dzierżawy usługi Azure AD może wykonać następujące czynności:

  * [Udziel zgody administratora dzierżawy na aplikacje usługi Azure AD](../active-directory/develop/v2-permissions-and-consent.md).

  * Podczas pierwszego uruchomienia aplikacji logiki aplikacja może zażądać zgody od administratora dzierżawy usługi Azure AD za pośrednictwem narzędzia do [wyrażania zgody na korzystanie z aplikacji](../active-directory/develop/application-consent-experience.md).
   
* Podstawowa wiedza [na temat tworzenia aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikacja logiki, do której chcesz uzyskać dostęp do Microsoft Graph jednostek zabezpieczeń, takich jak alerty. Aby można było użyć wyzwalacza zabezpieczeń Microsoft Graph, potrzebna jest pusta aplikacja logiki. Aby można było użyć akcji zabezpieczeń Microsoft Graph, potrzebna jest aplikacja logiki, która rozpoczyna się od odpowiedniego wyzwalacza dla danego scenariusza.

## <a name="connect-to-microsoft-graph-security"></a>Łączenie z zabezpieczeniami Microsoft Graph 

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Zaloguj się do [Azure Portal](https://portal.azure.com/)i Otwórz aplikację logiki w Projektancie aplikacji logiki, jeśli nie jest jeszcze otwarta.

1. W przypadku pustych aplikacji logiki Dodaj wyzwalacz i wszelkie inne akcje, które chcesz wykonać przed dodaniem akcji zabezpieczeń Microsoft Graph.

   lub

   W przypadku istniejących aplikacji logiki w ostatnim kroku, w którym chcesz dodać akcję zabezpieczeń Microsoft Graph, wybierz pozycję **nowy krok**.

   lub

   Aby dodać akcję między krokami, przesuń wskaźnik myszy nad strzałkę między krokami. Wybierz wyświetlony znak plus (+), a następnie wybierz pozycję **Dodaj akcję**.

1. W polu wyszukiwania wprowadź "zabezpieczenia programu Microsoft Graph" jako filtr. Z listy Akcje wybierz żądaną akcję.

1. Zaloguj się przy użyciu poświadczeń zabezpieczeń Microsoft Graph.

1. Podaj niezbędne szczegóły wybranej akcji i Kontynuuj tworzenie przepływu pracy aplikacji logiki.

## <a name="add-triggers"></a>Dodaj wyzwalacze

W Azure Logic Apps każda aplikacja logiki musi rozpoczynać się od [wyzwalacza](../logic-apps/logic-apps-overview.md#logic-app-concepts), który jest uruchamiany w przypadku wystąpienia konkretnego zdarzenia lub spełnienia określonego warunku. Za każdym razem, gdy wyzwala wyzwalacz, aparat Logic Apps tworzy wystąpienie aplikacji logiki i uruchamia przepływ pracy aplikacji.

> [!NOTE] 
> Gdy wyzwalany jest wyzwalacz, wyzwalacz przetwarza wszystkie nowe alerty. Jeśli nie otrzymasz żadnych alertów, uruchomienie wyzwalacza zostanie pominięte. Sonda następnego wyzwalacza odbywa się na podstawie interwału cyklu określonego we właściwościach wyzwalacza.

Ten przykład pokazuje, jak uruchomić przepływ pracy aplikacji logiki, gdy do aplikacji wysyłane są nowe alerty.

1.  W Azure Portal lub programie Visual Studio Utwórz pustą aplikację logiki, która spowoduje otwarcie projektanta aplikacji logiki. Ten przykład używa Azure Portal.

1.  W projektancie w polu wyszukiwania wprowadź "zabezpieczenia programu Microsoft Graph" jako filtr. Z listy Wyzwalacze wybierz ten wyzwalacz: **wszystkie nowe alerty**

1.  W wyzwalaczu podaj informacje o alertach, które chcesz monitorować. Aby uzyskać więcej właściwości, Otwórz listę **Dodaj nowy parametr** i wybierz parametr, aby dodać tę właściwość do wyzwalacza.

   | Właściwość | Właściwość (JSON) | Wymagane | Typ | Opis |
   |----------|-----------------|----------|------|-------------|
   | **Interwał** | `interval` | Tak | Liczba całkowita | Dodatnia liczba całkowita, która opisuje, jak często przebiega przepływ pracy na podstawie częstotliwości. Poniżej znajdują się minimalne i maksymalne interwały: <p><p>-Miesiąc: 1-16 miesięcy <br>-Dzień: 1-500 dni <br>-Godz.: 1 – 12 godzin <br>-Minutę: 1 – 72000 minut <br>-Sekunda: 1 – 9999999 s <p>Jeśli na przykład interwał wynosi 6, a częstotliwość to "miesiąc", cykl jest co 6 miesięcy. |
   | **Częstotliwość** | `frequency` | Tak | String | Jednostka czasu dla cyklu: **sekunda**, **minuta**, **godzina**, **dzień**, **tydzień**lub **miesiąc** |
   | **Strefa czasowa** | `timeZone` | Nie | String | Ma zastosowanie tylko w przypadku określenia czasu rozpoczęcia, ponieważ ten wyzwalacz nie akceptuje [przesunięcia czasu UTC](https://en.wikipedia.org/wiki/UTC_offset). Wybierz strefę czasową, która ma zostać zastosowana. |
   | **Godzina rozpoczęcia** | `startTime` | Nie | String | Podaj datę i godzinę rozpoczęcia w tym formacie: <p><p>RRRR-MM-DDTgg: mm: SS w przypadku wybrania strefy czasowej <p>lub <p>RRRR-MM-DDTgg: mm: SSS, jeśli nie wybierzesz strefy czasowej <p>Na przykład jeśli chcesz, aby 18 września 2017 o 2:00 PM, określ wartość "2017-09-18T14:00:00" i wybierz strefę czasową, na przykład Pacyfik (czas standardowy). Lub określ wartość "2017-09-18T14:00:00Z" bez strefy czasowej. <p>**Uwaga:** Ta godzina rozpoczęcia ma maksymalnie 49 lat w przyszłości i musi być zgodna ze [specyfikacją ISO 8601 Data Time](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) w [formacie czasu UTC](https://en.wikipedia.org/wiki/Coordinated_Universal_Time), ale bez [przesunięcia czasu UTC](https://en.wikipedia.org/wiki/UTC_offset). Jeśli nie wybierzesz strefy czasowej, musisz dodać literę "Z" na końcu bez spacji. Ten "Z" odnosi się do odpowiadającego [czasu morskich](https://en.wikipedia.org/wiki/Nautical_time). <p>W przypadku prostych harmonogramów czas rozpoczęcia jest pierwszym wystąpieniem, a w przypadku harmonogramów złożonych wyzwalacz nie jest uruchamiany dłużej niż godzina rozpoczęcia. [*Jakie są sposoby używania daty i godziny rozpoczęcia?* ](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time) |
   ||||||

1.  Gdy skończysz, na pasku narzędzi projektanta wybierz pozycję **Zapisz**.

1.  Teraz Kontynuuj dodawanie co najmniej jednej akcji do aplikacji logiki w celu wykonywania zadań, które chcesz wykonać z wynikami wyzwalacza.

## <a name="add-actions"></a>Dodaj akcje

Poniżej przedstawiono bardziej szczegółowe informacje dotyczące korzystania z różnych akcji dostępnych w ramach łącznika zabezpieczeń Microsoft Graph.

### <a name="manage-alerts"></a>Zarządzanie alertami

Aby odfiltrować, posortować lub pobrać najnowsze wyniki, podaj *tylko* [parametry zapytania ODATA obsługiwane przez Microsoft Graph](https://docs.microsoft.com/graph/query-parameters). *Nie określaj* kompletnego podstawowego adresu URL lub akcji http, na przykład `https://graph.microsoft.com/v1.0/security/alerts`, lub operacji `GET` lub `PATCH`. Oto przykład, w którym przedstawiono parametry akcji **Pobierz alerty** , gdy chcesz uzyskać listę z alertami o wysokiej ważności:

`Filter alerts value as Severity eq 'high'`

Aby uzyskać więcej informacji na temat zapytań, których można użyć z tym łącznikiem, zobacz [dokumentację dotyczącą alertów zabezpieczeń Microsoft Graph](https://docs.microsoft.com/graph/api/alert-list). Aby tworzyć udoskonalone środowiska przy użyciu tego łącznika, Dowiedz się więcej o [właściwościach schematu](https://docs.microsoft.com/graph/api/resources/alert) , które obsługuje łącznik.

| Akcja | Opis |
|--------|-------------|
| **Pobierz alerty** | Odfiltruj alerty na podstawie co najmniej jednej [właściwości alertu](https://docs.microsoft.com/graph/api/resources/alert), na przykład `Provider eq 'Azure Security Center' or 'Palo Alto Networks'`. | 
| **Pobierz Alert według identyfikatora** | Pobierz konkretny alert na podstawie identyfikatora alertu. | 
| **Aktualizowanie alertu** | Aktualizowanie określonego alertu na podstawie identyfikatora alertu. Aby upewnić się, że wymagane i edytowalne właściwości zostały przekazane do żądania, zobacz [edytowalne właściwości alertów](https://docs.microsoft.com/graph/api/alert-update). Na przykład, aby przypisać alert do analityka zabezpieczeń, aby umożliwić badanie, można zaktualizować właściwość **przypisane do** alertu. |
|||

### <a name="manage-alert-subscriptions"></a>Zarządzanie subskrypcjami alertów

Microsoft Graph obsługuje [*subskrypcje*](https://docs.microsoft.com/graph/api/resources/subscription)lub elementy [*webhook*](https://docs.microsoft.com/graph/api/resources/webhooks). Aby uzyskać, zaktualizować lub usunąć subskrypcje, podaj [parametry zapytania OData obsługiwane przez Microsoft Graph](https://docs.microsoft.com/graph/query-parameters) do konstrukcji Microsoft Graph Entity i Dołącz `security/alerts`, a następnie zapytanie OData. *Nie dołączaj* podstawowego adresu URL, na przykład `https://graph.microsoft.com/v1.0`. Zamiast tego należy użyć formatu w tym przykładzie:

`security/alerts?$filter=status eq 'New'`

| Akcja | Opis |
|--------|-------------|
| **Tworzenie subskrypcji** | [Utwórz subskrypcję](https://docs.microsoft.com/graph/api/subscription-post-subscriptions) , która powiadamia o wszelkich zmianach. Możesz odfiltrować subskrypcję dla określonych typów alertów. Można na przykład utworzyć subskrypcję, która powiadamia o alertach o wysokiej ważności. |
| **Pobierz aktywne subskrypcje** | [Uzyskaj niewygasłe subskrypcje](https://docs.microsoft.com/graph/api/subscription-list). | 
| **Aktualizowanie subskrypcji** | [Zaktualizuj subskrypcję](https://docs.microsoft.com/graph/api/subscription-update) , podając Identyfikator subskrypcji. Na przykład aby zwiększyć subskrypcję, możesz zaktualizować Właściwość `expirationDateTime` subskrypcji. | 
| **Usuń subskrypcję** | [Usuń subskrypcję](https://docs.microsoft.com/graph/api/subscription-delete) , podając Identyfikator subskrypcji. | 
||| 

### <a name="manage-threat-intelligence-indicators"></a>Zarządzanie wskaźnikami analizy zagrożeń

Aby odfiltrować, posortować lub pobrać najnowsze wyniki, podaj *tylko* [parametry zapytania ODATA obsługiwane przez Microsoft Graph](https://docs.microsoft.com/graph/query-parameters). *Nie określaj* kompletnego podstawowego adresu URL lub akcji http, na przykład `https://graph.microsoft.com/beta/security/tiIndicators`, lub operacji `GET` lub `PATCH`. Oto przykład, w którym przedstawiono parametry akcji **Get tiIndicators** , gdy lista ma typ zagrożenia `DDoS`:

`Filter threat intelligence indicator value as threatType eq 'DDoS'`

Aby uzyskać więcej informacji na temat zapytań, których można użyć z tym łącznikiem, zobacz ["opcjonalne parametry zapytania" w dokumentacji referencyjnej dotyczącej analizy zagrożeń Microsoft Graph](https://docs.microsoft.com/graph/api/tiindicators-list?view=graph-rest-beta&tabs=http). Aby utworzyć ulepszone środowiska przy użyciu tego łącznika, Dowiedz się więcej o [wskaźniku analizy zagrożeń właściwości schematu](https://docs.microsoft.com/graph/api/resources/tiindicator?view=graph-rest-beta) , który obsługuje łącznik.

| Akcja | Opis |
|--------|-------------|
| **Pobierz wskaźniki analizy zagrożeń** | Pobierz tiIndicators filtrowanie na podstawie jednej lub więcej [Właściwości tiIndicator](https://docs.microsoft.com/graph/api/resources/tiindicator?view=graph-rest-beta), na przykład `threatType eq 'MaliciousUrl' or 'DDoS'` |
| **Pobierz wskaźnik analizy zagrożeń według identyfikatora** | Pobierz konkretny tiIndicator w oparciu o identyfikator tiIndicator. | 
| **Utwórz wskaźnik analizy zagrożeń** | Utwórz nowy tiIndicator przez zaksięgowanie do kolekcji tiIndicators. Aby upewnić się, że w żądaniu zostały przekazane wymagane właściwości, zapoznaj się z [właściwościami wymaganymi do utworzenia tiIndicator](https://docs.microsoft.com/graph/api/tiindicators-post?view=graph-rest-beta&tabs=http). |
| **Prześlij wiele wskaźników analizy zagrożeń** | Utwórz wiele nowych tiIndicators przez zaksięgowanie kolekcji tiIndicators. Aby upewnić się, że w żądaniu zostały przekazane wymagane właściwości, zapoznaj się z [wymaganymi właściwościami przesyłania wielu tiIndicators](https://docs.microsoft.com/graph/api/tiindicator-submittiindicators?view=graph-rest-beta&tabs=http). |
| **Aktualizuj wskaźnik analizy zagrożeń** | Zaktualizuj określony tiIndicator w oparciu o identyfikator tiIndicator. Aby upewnić się, że wymagane i edytowalne właściwości zostały przekazane do żądania, zobacz [edytowalne właściwości dla tiIndicator](https://docs.microsoft.com/graph/api/tiindicator-update?view=graph-rest-beta&tabs=http). Na przykład aby zaktualizować akcję, która ma zostać zastosowana, jeśli wskaźnik jest dopasowany do narzędzia Security targetProduct, można zaktualizować właściwość **akcji** tiIndicator. |
| **Aktualizowanie wielu wskaźników analizy zagrożeń** | Zaktualizuj wiele tiIndicators. Aby upewnić się, że w żądaniu zostały przekazane wymagane właściwości, zapoznaj się z [wymaganymi właściwościami dotyczącymi aktualizowania wielu tiIndicators](https://docs.microsoft.com/graph/api/tiindicator-updatetiindicators?view=graph-rest-beta&tabs=http). |
| **Usuń wskaźnik analizy zagrożeń według identyfikatora** | Usuń określony tiIndicator w oparciu o identyfikator tiIndicator. |
| **Usuń wiele wskaźników analizy zagrożeń według identyfikatorów** | Usuń wiele tiIndicatorsów według ich identyfikatorów. Aby upewnić się, że w żądaniu zostały przekazane wymagane właściwości, zapoznaj się z [właściwościami wymaganymi do usunięcia wielu tiIndicators według identyfikatorów](https://docs.microsoft.com/graph/api/tiindicator-deletetiindicators?view=graph-rest-beta&tabs=http). |
| **Usuń wiele wskaźników analizy zagrożeń według identyfikatorów zewnętrznych** | Usuń wiele tiIndicators przez identyfikatory zewnętrzne. Aby upewnić się, że w żądaniu zostały przekazane wymagane właściwości, zapoznaj się z [właściwościami wymaganymi do usunięcia wielu tiIndicators według identyfikatorów zewnętrznych](https://docs.microsoft.com/graph/api/tiindicator-deletetiindicatorsbyexternalid?view=graph-rest-beta&tabs=http). |
|||

## <a name="connector-reference"></a>Dokumentacja łączników

Aby uzyskać szczegółowe informacje techniczne na temat wyzwalaczy, akcji i limitów, które są opisane w opisie OpenAPI łącznika (dawniej Swagger), przejrzyj [stronę odwołania](https://aka.ms/graphsecurityconnectorreference)łącznika.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat innych [łączników Logic Apps](../connectors/apis-list.md)
