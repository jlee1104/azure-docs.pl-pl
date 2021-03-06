---
title: Automatyzacja zadań w celu integracji z przedsiębiorstwem
description: Dowiedz się więcej o automatyzacji przepływów pracy, które integrują aplikacje, dane, usługi i systemy przy użyciu minimalnego kodu do integracji w przedsiębiorstwie przy użyciu usługi Azure Logic Apps.
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: overview
ms.custom: mvc
ms.date: 03/11/2020
ms.openlocfilehash: 0ba41d63195c906b57046dc6c9fd57c9f08399ab
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79241668"
---
# <a name="overview---what-is-azure-logic-apps"></a>Omówienie — co to jest usługa Azure Logic Apps?

[Usługa Azure Logic Apps](https://azure.microsoft.com/services/logic-apps) to usługa w chmurze, która pomaga planować, automatyzować i aranżować zadania, procesy biznesowe i [przepływy pracy,](#logic-app-concepts) gdy trzeba zintegrować aplikacje, dane, systemy i usługi w przedsiębiorstwach lub organizacjach. Usługa Logic Apps upraszcza sposób projektowania i tworzenia skalowalnych rozwiązań do [integracji](https://azure.microsoft.com/product-categories/integration/)aplikacji, integracji danych, integracji systemów, integracji aplikacji przedsiębiorstwa (EAI) i komunikacji między przedsiębiorstwami (B2B), zarówno w chmurze, lokalnie, jak i obu.

Poniżej przedstawiono kilka przykładowych obciążeń, które można zautomatyzować za pomocą aplikacji logiki:

* Przetwarzanie i kierowanie zamówień w systemach lokalnych i usługach w chmurze.

* Wysyłaj powiadomienia e-mail przy użyciu usługi Office 365, kiedy wystąpią zdarzenia w różnych systemach, aplikacjach i usługach.

* Przenoszenie przekazywanych plików z serwera SFTP lub FTP do usługi Azure Storage.

* Monitorowanie tweetów dotyczących określonego tematu, analizowanie tonacji i tworzenie alertów lub zadań dla elementów, które wymagają przeglądu.

Aby tworzyć rozwiązania integracji przedsiębiorstwa za pomocą usługi Azure Logic Apps, możesz wybierać spośród rosnącej galerii z [setkami gotowych do użycia łączników,](../connectors/apis-list.md)które obejmują usługi, takie jak usługa Azure Service Bus, usługi Azure Functions, Usługa Azure Storage, SQL Server, Office 365, Dynamics, Salesforce, BizTalk, SAP, Oracle DB, udziały plików i inne. [Łączniki](#logic-app-concepts) udostępniają [wyzwalacze](#logic-app-concepts) i [akcje](#logic-app-concepts), które pozwalają tworzyć aplikacje logiki umożliwiające uzyskiwanie bezpiecznego dostępu do danych i ich przetwarzanie w czasie rzeczywistym.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Introducing-Azure-Logic-Apps/player]

## <a name="how-does-logic-apps-work"></a>Jak działa usługa Logic Apps? 

Każdy przepływ pracy aplikacji logiki rozpoczyna się od wyzwalacza, który jest aktywowany, gdy zajdzie określone zdarzenie lub gdy nowe dostępne dane spełniają określone kryteria. Wiele wyzwalaczy dostarczonych przez łączniki w aplikacji logiki obejmują podstawowe możliwości planowania, dzięki czemu można skonfigurować, jak regularnie uruchamiać obciążenia. W przypadku bardziej złożonego planowania lub zaawansowanych cykli można użyć wyzwalacza cyklu jako pierwszego kroku w dowolnym przepływie pracy. Dowiedz się więcej o [przepływach pracy opartych na harmonogramie](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

Po każdym aktywowaniu wyzwalacza aparat usługi Logic Apps tworzy wystąpienie aplikacji logiki, które uruchamia akcje przepływu pracy. Te akcje mogą również obejmować konwersje danych i formanty przepływu pracy, takie jak instrukcje warunkowe, instrukcje przełączania, pętle i rozgałęzienia. Ta aplikacja logiki rozpoczyna się na przykład od wyzwalacza usług Dynamics 365 z wbudowanymi kryteriami „Gdy rekord zostanie zaktualizowany”. Jeśli wyzwalacz wykryje zdarzenie zgodne z tymi kryteriami, aktywuje się i uruchamia akcje przepływu pracy. W tym przypadku akcje obejmują transformacje XML, aktualizacje danych, rozgałęzienia decyzji i powiadomienia e-mail.

![Projektant aplikacji usługi Logic Apps — przykładowa aplikacja logiki](./media/logic-apps-overview/azure-logic-apps-designer.png)

Aplikacje logiki można tworzyć wizualnie za pomocą Projektanta aplikacji usługi Logic Apps dostępnego w witrynie Azure Portal za pośrednictwem przeglądarki i w programie Visual Studio. Aby zbudować kolejne niestandardowe aplikacje logiki, można utworzyć lub zmodyfikować definicje aplikacji logiki w formacie JavaScript Object Notation (JSON) w edytorze „widoku kodu”. Na potrzeby wybierania zadań można również użyć poleceń programu Azure PowerShell i szablonów usługi Azure Resource Manager. Aplikacje logiki są wdrażane i uruchamiane w chmurze na platformie Azure. Aby uzyskać bardziej szczegółowe wprowadzenie, obejrzyj film [Use Azure Enterprise Integration Services to run cloud apps at scale](https://channel9.msdn.com/Events/Connect/2017/T119/) (Korzystanie z usług Enterprise Integration Services platformy Azure na potrzeby uruchamiania aplikacji w chmurze na dużą skalę).

## <a name="why-use-logic-apps"></a>Po co korzystać z usługi Logic Apps?

W firmach przechodzących na platformy cyfrowe aplikacje logiki ułatwiają i przyspieszają łączenie starszych systemów z nowoczesnymi, oferując wbudowane interfejsy API działające jako łączniki zarządzane przez firmę Microsoft. Dzięki temu można skoncentrować się na logice biznesowej i funkcjach swoich aplikacji. Nie trzeba się martwić o tworzenie, hostowanie, skalowanie, monitorowanie i konserwowanie aplikacji ani o zarządzanie nimi. Usługa Logic Apps wykonuje wszystkie te działania za Ciebie. Ponadto płacisz wyłącznie za rzeczywiste użycie według [modelu cen](../logic-apps/logic-apps-pricing.md) zużycia.

W wielu przypadkach nie będzie trzeba nawet pisać kodu. Jeśli zajdzie jednak potrzeba napisania kodu, fragmenty kodu można utworzyć, korzystając z rozwiązania [Azure Functions](../azure-functions/functions-overview.md), a następnie uruchomić ten kod na żądanie z poziomu aplikacji logiki. Ponadto jeśli aplikacje logiki muszą wchodzić w interakcje ze zdarzeniami usług platformy Azure, aplikacji niestandardowych lub rozwiązań innych firm, możesz użyć usługi [Azure Event Grid](../event-grid/overview.md) razem z aplikacjami logiki na potrzeby monitorowania, routingu i publikowania zdarzeń.

Usługi Logic Apps, Functions i Event Grid są w pełni zarządzane za pośrednictwem platformy Microsoft Azure, dzięki czemu nie musisz się martwić o tworzenie, hostowanie, skalowanie, monitorowanie i konserwowanie swoich rozwiązań ani o zarządzanie nimi. Dzięki możliwości tworzenia [„bezserwerowych” aplikacji i rozwiązań](../logic-apps/logic-apps-serverless-overview.md) możesz po prostu skoncentrować się na logice biznesowej. Te usługi są automatycznie skalowane do Twoich potrzeb, przyspieszają integracje i ułatwiają tworzenie niezawodnych aplikacji w chmurze z minimalną ilością kodu.

Aby dowiedzieć się, jak firmy zwiększyły swoją elastyczność i poprawiły koncentrację na swoich podstawowych zadaniach biznesowych, kiedy połączyły usługę Logic Apps z innymi usługami platformy Azure i produktami firmy Microsoft, zapoznaj się z tymi [historiami klientów](https://aka.ms/logic-apps-customer-stories).

Poniżej przedstawiono szczegółowe informacje o możliwościach i korzyściach wynikających z korzystania z usługi Logic Apps:

### <a name="visually-build-workflows-with-easy-to-use-tools"></a>Wizualne tworzenie przepływów pracy za pomocą łatwych w użyciu narzędzi

Oszczędzaj czas i upraszczaj złożone procesy przy użyciu narzędzi do projektowania wizualnego. Twórz aplikacje logiki od początku do końca przy użyciu Projektanta aplikacji usługi Logic Apps w przeglądarce w witrynie Azure Portal lub w programie Visual Studio. Uruchamiaj przepływy pracy z wyzwalaczem i dodawaj dowolną liczbę akcji z [galerii łączników](../connectors/apis-list.md).

### <a name="get-started-faster-with-logic-app-templates"></a>Szybsze rozpoczynanie pracy dzięki użyciu szablonów aplikacji logiki

Szybciej twórz często używane rozwiązania po wybraniu wstępnie zdefiniowanych przepływów pracy z [galerii szablonów](../logic-apps/logic-apps-create-logic-apps-from-templates.md). Wybierać można spośród różnych szablonów: od prostej łączności z aplikacjami typu oprogramowanie jako usługa (SaaS) do zaawansowanych rozwiązań B2B oraz szablonów „tylko do zabawy”. Dowiedz się, jak [tworzyć aplikacje logiki na podstawie wbudowanych szablonów](../logic-apps/logic-apps-create-logic-apps-from-templates.md).

### <a name="connect-disparate-systems-across-different-environments"></a>Łączenie różnych systemów w różnych środowiskach

Niektóre wzorce i przepływy pracy łatwo opisać, ale trudno zaimplementować w postaci kodu. Aplikacje logiki ułatwiają bezproblemowe łączenie różnych systemów w środowiskach lokalnych i w chmurze. Na przykład można połączyć rozwiązanie marketingowe w chmurze z lokalnym systemem rozliczeniowym lub scentralizować przesyłanie komunikatów przez interfejsy API i systemy przy użyciu usługi Enterprise Service Bus. Aplikacje logiki udostępniają szybką, niezawodną i spójną metodę dostarczania możliwych do ponownego skonfigurowania rozwiązań wielokrotnego użytku dla tych scenariuszy.

### <a name="first-class-support-for-enterprise-integration-and-b2b-scenarios"></a>Obsługa na najwyższym poziomie na potrzeby integracji dla przedsiębiorstw i scenariuszy B2B

Firmy i organizacje komunikują się ze sobą elektronicznie przy użyciu różnych branżowych protokołów przesyłania komunikatów i formatów, takich jak EDIFACT AS2 i X12. Korzystając z funkcji dostępnych w [Pakiecie integracyjnym dla przedsiębiorstw (EIP)](../logic-apps/logic-apps-enterprise-integration-overview.md), można tworzyć aplikacje logiki, które przekształcają formaty przesyłania komunikatów używane przez partnerów w formaty, które mogą być interpretowane i przetwarzane przez systemy w organizacji. Usługa Logic Apps sprawnie obsługuje te przekształcenia, zabezpieczając je dodatkowo przy użyciu szyfrowania i podpisów cyfrowych.

Rozpocznij, korzystając ze swoich bieżących systemów oraz usług, i stopniowo powiększaj rozwiązania we własnym tempie. W odpowiednim czasie usługa Logic Apps i pakiet EIP ułatwią Ci wdrożenie bardziej dojrzałych scenariuszy integracji i skalowanie ich przez udostępnienie następujących możliwości i innych funkcji:

* Tworzenie na podstawie następujących produktów i usług:

  * [Microsoft BizTalk Server](https://docs.microsoft.com/biztalk/core/introducing-biztalk-server)
  * [Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md)
  * [Azure Functions](../azure-functions/functions-overview.md)
  * [Azure API Management](../api-management/api-management-key-concepts.md)

* Przetwarzanie [wiadomości XML](../logic-apps/logic-apps-enterprise-integration-xml.md)

* Przetwarzanie [plików płaskich](../logic-apps/logic-apps-enterprise-integration-flatfile.md)

* Wymienianie komunikatów za pomocą protokołów [EDIFACT](../logic-apps/logic-apps-enterprise-integration-edifact.md), [AS2](../logic-apps/logic-apps-enterprise-integration-as2.md) i [X12](../logic-apps/logic-apps-enterprise-integration-x12.md)

* Przechowywanie artefaktów B2B i innych elementów (oraz zarządzanie nimi) w jednym miejscu dzięki korzystaniu z [kont integracji](../logic-apps/logic-apps-enterprise-integration-accounts.md):

  * [Partnerzy](../logic-apps/logic-apps-enterprise-integration-partners.md)
  * [Umowy](../logic-apps/logic-apps-enterprise-integration-agreements.md) 
  * [Mapy przekształceń XML](../logic-apps/logic-apps-enterprise-integration-maps.md)
  * [Schematy walidacji XML](../logic-apps/logic-apps-enterprise-integration-schemas.md)

Jeśli na przykład używasz serwera Microsoft BizTalk Server, aplikacje logiki mogą komunikować się z tym serwerem przy użyciu [łącznika programu BizTalk Server](../connectors/apis-list.md#on-premises-connectors). Następnie możesz rozszerzyć operacje lub wykonywać operacje przypominające operacje BizTalk w usłudze Logic Apps, uwzględniając [łączniki konta integracji](../connectors/apis-list.md#integration-account-connectors), które są dostępne z pakietem integracyjnym dla przedsiębiorstw.

Idąc w tym kierunku, program BizTalk Server może nawiązać połączenie i komunikować się z usługą Logic Apps przy użyciu [adaptera programu Microsoft BizTalk Server dla usługi Logic Apps](https://www.microsoft.com/download/details.aspx?id=54287). Dowiedz się, jak [skonfigurować i używać adaptera programu BizTalk Server](https://docs.microsoft.com/biztalk/core/logic-app-adapter) na serwerze BizTalk Server.

### <a name="write-once-reuse-often"></a>Jednokrotny zapis, częste ponowne używanie

Utwórz aplikacje logiki jako szablony usługi Azure Resource Manager, dzięki czemu można [zautomatyzować wdrażanie aplikacji logiki](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md) w wielu środowiskach i regionach.

### <a name="access-resources-inside-azure-virtual-networks"></a>Dostęp do zasobów w sieciach wirtualnych platformy Azure

Aplikacje logiki mogą uzyskiwać dostęp do zabezpieczonych zasobów, takich jak maszyny wirtualne (maszyny wirtualne) i inne systemy lub usługi, które znajdują się wewnątrz [sieci wirtualnej platformy Azure](../virtual-network/virtual-networks-overview.md) podczas tworzenia środowiska usługi [ *integracji* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md). ISE jest izolowanym wystąpieniem usługi Logic Apps, która używa dedykowanych zasobów i działa oddzielnie od "globalnej" usługi aplikacji logiki z wieloma dzierżawami.

Uruchamianie aplikacji logiki we własnym oddzielnym izolowanym wystąpieniu pomaga zmniejszyć wpływ innych dzierżaw platformy Azure na wydajność aplikacji, znany również jako [efekt "hałaśliwych sąsiadów".](https://en.wikipedia.org/wiki/Cloud_computing_issues#Performance_interference_and_noisy_neighbors) Ise zapewnia również następujące korzyści:

* Własne statyczne adresy IP, które są niezależne od statycznych adresów IP, które są współużytkowane przez aplikacje logiki w usłudze wielu dzierżawców. Można również skonfigurować jeden publiczny, statyczny i przewidywalny wychodzący adres IP do komunikowania się z systemami docelowymi. W ten sposób nie trzeba konfigurowania dodatkowych otworów zapory w tych systemach docelowych dla każdej ise.

* Zwiększone limity czasu trwania uruchomienia, przechowywania magazynu, przepływności, limitów czasu żądania i odpowiedzi HTTP, rozmiarów wiadomości i niestandardowych żądań łączników. Aby uzyskać więcej informacji, zobacz [Limity i konfiguracja usługi Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md).

Podczas tworzenia środowiska ISE platforma Azure *wstrzykuje* lub wdraża tę platformę ISE w sieci wirtualnej platformy Azure. Następnie można użyć tego ise jako lokalizacji dla aplikacji logiki i kont integracji, które wymagają dostępu. Aby uzyskać więcej informacji na temat tworzenia środowiska ISE, zobacz [Łączenie się z sieciami wirtualnymi platformy Azure z usługi Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment.md).

### <a name="built-in-extensibility"></a>Wbudowana rozszerzalność

Jeśli nie znajdziesz łącznika, który ma być uruchamiany kod niestandardowy, możesz rozszerzyć aplikacje logiki, tworząc i wywołując własne fragmenty kodu na żądanie za pośrednictwem [usługi Azure Functions.](../azure-functions/functions-overview.md) Utwórz swoje własne [interfejsy API](../logic-apps/logic-apps-create-api-app.md) i [łączniki niestandardowe](../logic-apps/custom-connector-overview.md), które można wywoływać z aplikacji logiki.

### <a name="pay-only-for-what-you-use"></a>Płatność wyłącznie za rzeczywiste użycie
  
Usługa Logic Apps używa [cennika i mechanizmu pomiarów](../logic-apps/logic-apps-pricing.md) opartych na użyciu, chyba że masz aplikacje logiki utworzone wcześniej w oparciu o plany usługi App Service.

Dowiedz się więcej o usłudze Logic Apps z tych klipów wideo z wprowadzeniem:

* [Integration with Logic Apps - Go from zero to hero](https://channel9.msdn.com/Events/Build/2017/C9R17) (Integracja z usługą Logic Apps — od zera do bohatera)
* [Enterprise integration with Microsoft Azure Logic Apps](https://channel9.msdn.com/Events/Ignite/Microsoft-Ignite-Orlando-2017/BRK2188) (Integracja dla przedsiębiorstw w usłudze Microsoft Azure Logic Apps)
* [Building advanced business processes with Logic Apps](https://channel9.msdn.com/Events/Ignite/Microsoft-Ignite-Orlando-2017/BRK3179) (Budowanie zaawansowanych procesów biznesowych dzięki usłudze Logic Apps)

## <a name="how-does-logic-apps-differ-from-functions-webjobs-and-power-automate"></a>Czym różnią się aplikacje logiki od funkcji, funkcji WebJobs i Power Automate?

Wszystkie te usługi pomagają „skleić” i połączyć ze sobą różne systemy. Każda z tych usług ma swoje zalety i korzyści, dlatego połączenie ich możliwości jest najlepszym sposobem na szybkie tworzenie skalowalnego, w pełni funkcjonalnego systemu integracji. Aby uzyskać więcej informacji, zobacz [Wybieranie między aplikacjami logiki, funkcjami, funkcjami WebJobs i Automatyzacją zasilania](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md).

<a name="logic-app-concepts"></a>

## <a name="key-terms"></a>Kluczowe terminy

* **Przepływ pracy**: wizualizowanie, projektowanie, tworzenie, automatyzowanie i wdrażanie procesów biznesowych w postaci serii kroków.

* **Zarządzane łączniki**: aplikacje logiki muszą mieć dostęp do danych, usług i systemów. Możesz użyć wbudowanych łączników zarządzanych przez firmę Microsoft, zaprojektowanych w celu nawiązywania połączeń z danymi, uzyskiwania do nich dostępu i pracowania z nimi. Zobacz [łączniki dla usługi Azure Logic Apps](../connectors/apis-list.md).

* **Wyzwalacze**: wiele łączników zarządzanych przez firmę Microsoft udostępnia wyzwalacze, które są aktywowane, kiedy zdarzenia lub nowe dane spełniają określone warunki. Na przykład zdarzenie może odbierać wiadomość e-mail lub wykrywać zmiany na koncie usługi Azure Storage. Po każdym aktywowaniu wyzwalacza aparat usługi Logic Apps tworzy nowe wystąpienie aplikacji logiki, które uruchamia przepływ pracy.

* **Akcje**: akcje to wszystkie kroki wykonywane po aktywowaniu wyzwalacza. Każda akcja jest zwykle zmapowana na operację, która jest definiowana za pomocą łącznika zarządzanego, niestandardowego interfejsu API lub niestandardowego łącznika.

* **Pakiet integracyjny dla przedsiębiorstw**: w przypadku bardziej zaawansowanych scenariuszy integracji usługa Logic Apps oferuje możliwości platformy BizTalk Server. Pakiet integracyjny dla przedsiębiorstw udostępnia łączniki, które ułatwiają wykonywanie sprawdzania poprawności, przekształcania i innych akcji przez aplikacje logiki.

## <a name="get-started"></a>Wprowadzenie

Logic Apps to jedna z wielu usług hostowanych na platformie Microsoft Azure. Dlatego przed rozpoczęciem należy uzyskać subskrypcję Azure. Jeśli nie masz subskrypcji, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

Jeśli masz subskrypcję platformy Azure, wypróbuj ten [przewodnik Szybki start, aby utworzyć pierwszą aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md), która za pośrednictwem kanału informacyjnego RSS monitoruje witryną internetową pod kątem nowej zawartości i jeśli się taka pojawi, wysyła wiadomość e-mail.

## <a name="next-steps"></a>Następne kroki

* [Sprawdzanie ruchu za pomocą aplikacji logiki opartej na harmonogramie](../logic-apps/tutorial-build-schedule-recurring-logic-app-workflow.md)
* Dowiedz się więcej o [rozwiązaniach nieużywających serwera na platformie Azure](../logic-apps/logic-apps-serverless-overview.md)
* Dowiedz się więcej o [integracji B2B z Pakietem integracyjnym dla przedsiębiorstw](../logic-apps/logic-apps-enterprise-integration-overview.md)
