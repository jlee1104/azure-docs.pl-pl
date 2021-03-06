---
title: Co to jest Azure Security Center? | Microsoft Docs
description: Na tej stronie opisano Security Center kluczowe korzyści — odnajdywanie stanu zabezpieczeń i ulepszanie go z pokryciem zasobów w chmurze i Premium.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 45b9756b-6449-49ec-950b-5ed1e7c56daa
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/06/2019
ms.author: memildin
ms.openlocfilehash: 84ed5db54874231d8bde4d892eedbc60c42a3430
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/28/2020
ms.locfileid: "77918761"
---
# <a name="what-is-azure-security-center"></a>Co to jest Centrum zabezpieczeń Azure?

Usługa Azure Security Center to ujednolicony system zarządzania bezpieczeństwem infrastruktury. Ma on za zadanie zwiększenie poziomu bezpieczeństwa centrów danych i zapewnienie zaawansowanej ochrony przed zagrożeniami w przypadku obciążeń hybrydowych w chmurze (zarówno na platformie Azure, jak i poza nią) oraz w środowisku lokalnym.

Utrzymywanie bezpieczeństwa zasobów to wspólna odpowiedzialność dostawcy usług w chmurze, platformy Azure i klienta. Podczas przechodzenia do chmury trzeba upewnić się, że obciążenia będą bezpieczne. Trzeba też pamiętać, że przejście na model IaaS (infrastruktura jako usługa) to większa odpowiedzialność po stronie klienta niż w przypadku korzystania z modeli PaaS (platforma jako usługa) i SaaS (oprogramowanie jako usługa). Usługa Azure Security Center zapewnia narzędzia potrzebne do zwiększenia bezpieczeństwa sieci, zabezpieczenia usług i zapewnienia maksymalnego poziomu bezpieczeństwa.

Azure Security Center rozwiązuje trzy najbardziej pilne problemy związane z zabezpieczeniami:

-   **Szybko zmieniające się obciążenia** — jest to mocna strona rozwiązań chmurowych, a jednocześnie wyzwanie. Z jednej strony przed użytkownikami końcowymi otwiera się więcej możliwości. Z drugiej jednak pojawia się problem — jak sprawić, by ciągle zmieniające się usługi, które tworzą i wykorzystują użytkownicy, spełniały standardy bezpieczeństwa i były zgodne z najlepszymi rozwiązaniami dotyczącymi bezpieczeństwa?

-   **Coraz bardziej zaawansowane ataki** — wszędzie, gdzie uruchamiasz obciążenia, ataki są bardziej zaawansowane. Trzeba zabezpieczyć pracę w chmurze publicznej, która jest połączona z Internetem, co sprawia, że jest jeszcze bardziej narażona na ataki w przypadku braku zastosowania najlepszych zabezpieczeń.

-   **Umiejętność zabezpieczeń jest w krótkim dostawie** — liczba alertów zabezpieczeń i systemów zgłaszających alerty pozwala uzyskać pewność, że Twoje środowiska są chronione. Bycie na bieżąco z najnowszymi atakami jest dużym wyzwaniem. Nie można stać w miejscu, gdy sytuacja w zakresie zabezpieczeń ciągle się zmienia.

Aby sprostać tym wyzwaniom, Security Center oferuje narzędzia umożliwiające:

-   **Wzmocnienie stan zabezpieczeń**: Security Center ocenia środowisko i umożliwia zrozumienie stanu zasobów oraz tego, czy są one bezpieczne.

-   **Ochrona przed zagrożeniami**: Security Center ocenia obciążenia i wywołuje zalecenia dotyczące zapobiegania zagrożeniom oraz alerty zabezpieczeń.

-   **Szybsza ochrona**: w Security Center wszystko jest wykonywane w szybkości chmury. Dzięki natywnej integracji wdrożenie usługi Security Center jest łatwe i zapewnia automatyczne aprowizowanie i ochronę w ramach usług platformy Azure.

[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]

## <a name="architecture"></a>Architektura

Ponieważ usługa Security Center jest częścią platformy Azure, usługi PaaS na platformie Azure — w tym Service Fabric, bazy danych SQL i konta magazynu — są monitorowane i chronione przez usługę Security Center bez konieczności każdorazowego wdrażania.

Ponadto usługa Security Center dzięki zainstalowaniu programu Microsoft Monitoring Agent chroni również nienależące do platformy Azure serwery i maszyny wirtualne, działające w chmurze lub lokalnie, zarówno w przypadku serwerów opartych na systemach Windows, jak i Linux. Maszyny wirtualne Azure są automatycznie aprowizowane w usłudze Security Center.

Zdarzenia zbierane od agentów i z platformy Azure są skorelowane w aparacie analizy zabezpieczeń, aby zapewnić dostosowane zalecenia (zadania z ograniczeniami funkcjonalności), które należy wykonać, aby upewnić się, że obciążenia są bezpieczne i alerty zabezpieczeń. Alerty należy badać jak najszybciej, aby upewnić się, że nie dochodzi do złośliwych ataków na działające obciążenia.

Po włączeniu usługi Security Center zasady zabezpieczeń wbudowane w usługę Security Center są odzwierciedlane w usłudze Azure Policy jako wbudowana inicjatywa w kategorii Security Center. Wbudowana inicjatywa jest automatycznie przypisywana do wszystkich subskrypcji zarejestrowanych w usłudze Security Center (w warstwach Bezpłatna lub Standardowa). Wbudowana inicjatywa zawiera tylko zasady inspekcji. Aby uzyskać więcej informacji na temat zasad usługi Security Center w usłudze Azure Policy, zobacz [Praca z zasadami zabezpieczeń](tutorial-security-policy.md).

## <a name="strengthen-security-posture"></a>Zwiększanie poziomu bezpieczeństwa

Usługa Azure Security Center umożliwia zwiększanie poziomu bezpieczeństwa. Oznacza to, że będzie ona pomagać w identyfikowaniu i wykonywaniu zalecanych jako najlepsze rozwiązania zadań polegających na zwiększaniu poziomu zabezpieczeń, a także we wdrażaniu tych zadań na maszynach, w usługach związanych z danymi i w aplikacjach. Obejmuje to zarządzanie zasadami zabezpieczeń i ich wymuszanie oraz upewnienie się, że maszyny wirtualne na platformie Azure, serwery spoza Azure oraz usługi PaaS platformy Azure je spełniają. Security Center zapewnia narzędzia dające syntetyczne informacje na temat obciążeń ze szczególnym uwzględnieniem stanu zabezpieczeń sieci. 

### <a name="manage-organization-security-policy-and-compliance"></a>Zarządzanie zasadami zabezpieczeń oraz zgodnością w organizacji

Podstawową sprawą jest zapewnienie, że działające obciążenia są bezpieczne. Aby to osiągnąć, trzeba wdrożyć dostosowane zasady zabezpieczeń. Ze względu na to, że wszystkie zasady w Security Center są zbudowane na podstawie kontroli zasad platformy Azure, uzyskujesz pełen zakres i elastyczność **rozwiązań opartych na światowej klasie**. W usłudze Security Center można ustawić uruchamianie zasad bezpieczeństwa na poziomie grupy zarządzania, subskrypcji lub nawet dla jednego dzierżawcy.

![Pulpit nawigacyjny usługi Security Center](media/security-center-intro/sc-dashboard.png)

Security Center pomaga **identyfikować subskrypcje IT w tle**. Sprawdzając subskrypcje oznaczone na pulpicie nawigacyjnym etykietą **Nieobjęte**, można dowiedzieć się natychmiast, że zostały utworzone nowe subskrypcje, i upewnić się, że są objęte zasadami oraz chronione przez usługę Azure Security Center.

![Pulpit nawigacyjny z zasadami w usłudze Security Center](media/security-center-intro/sc-policy-dashboard.png)

Zaawansowane możliwości monitorowania w Security Center umożliwiają również **śledzenie zgodności i zarządzania oraz zarządzanie nimi**wraz z upływem czasu. **Ogólna zgodność** zapewnia miarę zgodności subskrypcji z zasadami związanymi z obciążeniem. 

![Zasady w usłudze Security Center w czasie](media/security-center-intro/sc-policy-time.png)

### <a name="continuous-assessments"></a>Ciągłe oceny

Usługa Security Center ciągle wykrywa nowe zasoby wdrażane w różnych obciążeniach i ocenia, czy są one skonfigurowane zgodnie z najlepszymi rozwiązaniami w zakresie zabezpieczeń. Zasoby, które nie spełniają tego wymogu, są oznaczane, a przy tym generowana jest lista zaleceń z określonymi priorytetami. Zalecenia wskazują, co należy zrobić, aby chronić maszyny.

Jednym z najbardziej zaawansowanych narzędzi Security Center zapewnia stałe monitorowanie stanu zabezpieczeń sieci to **Mapa sieciowa**. Mapa umożliwia wyświetlenie topologii obciążeń, aby sprawdzić, czy każdy węzeł jest odpowiednio skonfigurowany. Można zobaczyć, jak węzły są połączone, co pomaga blokować niechciane połączenia, które mogą potencjalnie ułatwić osobie atakującej dostanie się do sieci.

![Mapa sieci w usłudze Security Center](media/security-center-intro/sc-net-map.png)

Security Center upraszczają alerty zabezpieczeń w jednym kroku, przez dodanie **bezpiecznego wyniku**. Ważne oceny są teraz skojarzone z każdym zaleceń, które otrzymujesz, aby pomóc zrozumieć, w jaki sposób ważna jest ogólna stan zabezpieczeń. Jest to kluczowe znaczenie w umożliwieniu **określania priorytetów pracy w zabezpieczeniach**.

![Security Center zabezpieczonych wyników](media/security-center-intro/sc-secure-score.png)

### <a name="optimize-and-improve-security-by-configuring-recommended-controls"></a>Optymalizacja i zwiększanie bezpieczeństwa dzięki konfigurowaniu zalecanych środków kontroli

Główną wartość usługi Azure Security Center stanowią zalecenia. Zalecenia są dostosowane do określonych problemów z bezpieczeństwem wykrytych w obciążeniach klienta. Usługa Security Center wykonuje pracę administratora zabezpieczeń, nie tylko znajdując luki w zabezpieczeniach, ale też udostępniając szczegółowe instrukcje na temat sposobu ich usunięcia.

![Zalecenia usługi Security Center](media/security-center-intro/sc-recommendations.png)

W ten sposób usługa Security Center umożliwia nie tylko ustanawianie zasad zabezpieczeń, ale też zastosowanie bezpiecznych standardów konfiguracji we wszystkich zasobach.

Zalecenia pomagają zmniejszyć obszar ataków w każdym z zasobów. Obejmują one maszyny wirtualne platformy Azure, serwery spoza platformy Azure oraz usługi PaaS platformy Azure takie jak konta SQL i konta magazynu. Każdy rodzaj zasobu jest oceniany oddzielnie i ma swoje własne standardy.

![Przykład zaleceń usługi Security Center](media/security-center-intro/sc-recommendation-example.png)

## <a name="protect-against-threats"></a>Ochrona przed zagrożeniami

Ochrona przed zagrożeniami w usłudze Security Center umożliwia wykrywanie zagrożeń i zapobieganie im w warstwie infrastruktury jako usługi (IaaS), na serwerach spoza platformy Azure, jak również w przypadku platform działających jako usługa (PaaS) na platformie Azure.

Ochrona przed zagrożeniami w usłudze Security Center obejmuje kompleksową analizę łańcucha ataku, która automatycznie koreluje alerty w danym środowisku na podstawie modelu łańcucha ataku cybernetycznego. Dzięki temu uzyskujesz lepszy wgląd we wszystkie etapy ataku, jego punkt początkowy oraz wpływ na zasoby.



![Zalecenie usługi Security Center dotyczące ataku](media/security-center-intro/sc-attack-recommendation.png)

### <a name="integration-with-microsoft-defender-advanced-threat-protection"></a>Integracja z usługą Microsoft Defender Advanced Threat Protection

Security Center obejmuje automatyczną, natywną integrację z usługą Microsoft Defender Advanced Threat Protection. Oznacza to, że bez żadnej konfiguracji maszyny z systemami Windows i Linux są w pełni zintegrowane z zaleceniami i ocenami Security Center.

Ponadto usługa Security Center pozwala zautomatyzować zasady kontroli aplikacji w środowisku serwera. Funkcje adaptacyjnego sterowania aplikacjami w usłudze Security Center umożliwiają włączenie kompleksowego tworzenia listy dozwolonych aplikacji na serwerach z systemem Windows. Nie musisz tworzyć reguł i sprawdzać naruszeń, ponieważ wszystko odbywa się automatycznie.

### <a name="protect-paas"></a>Ochrona usług PaaS

Usługa Security Center ułatwia wykrywanie zagrożeń w usługach PaaS platformy Azure. Możesz wykrywać zagrożenia dotyczące usług platformy Azure, w tym usługi Azure App Service, Azure SQL, konta usługi Azure Storage i innych usług danych. Możesz również korzystać z natywnej integracji z funkcją analizy zachowań użytkowników i jednostek (UEBA) dostępną w usłudze Microsoft Cloud App Security w celu wykrywania anomalii w dziennikach aktywności platformy Azure.

### <a name="block-brute-force-attacks"></a>Blokowanie ataków siłowych

Usługa Security Center pomaga ograniczyć narażenie na ataki siłowe. Ograniczenie dostępu do portów maszyny wirtualnej i korzystanie z dostępu just-in-time do maszyny wirtualnej umożliwia zwiększenie bezpieczeństwa sieci dzięki zapobieganiu niepotrzebnemu dostępowi. Można ustawić zasady bezpiecznego dostępu na wybranych portach tylko dla autoryzowanych użytkowników, dozwolonych zakresów źródłowych adresów IP lub określonych adresów IP oraz przez określony czas.

![Usługa Security Center i atak siłowy](media/security-center-intro/sc-brute-force.png)

### <a name="protect-data-services"></a>Ochrona usług danych

Usługa Security Center obejmuje funkcje, które ułatwiają wykonywanie automatycznej klasyfikacji danych w Azure SQL. Możesz także uzyskać oceny potencjalnych luk w zabezpieczeniach w usługach Azure SQL i Storage oraz zalecenia dotyczące sposobu rozwiązania tych problemów.

### <a name="protect-iot-and-hybrid-cloud-workloads"></a>Ochrona obciążeń związanych z chmurą IoT i hybrydową

Azure Security Center IoT (Internet rzeczy) upraszczają hybrydową ochronę obciążeń, zapewniając ujednoliconą widoczność i kontrolę, adaptacyjne zapobieganie zagrożeniom oraz inteligentną ochronę przed zagrożeniami i odpowiedzi w ramach obciążeń działających na urządzeniach brzegowych, lokalnych, na platformie Azure oraz w innych chmurach. Aby uzyskać więcej informacji, zobacz [Azure Security Center for IoT](https://docs.microsoft.com/azure/asc-for-iot/).

## <a name="get-secure-faster"></a>Szybsze osiągnięcie bezpieczeństwa

Natywna integracja platformy Azure (w tym Azure Policy i dzienników Azure Monitor) z bezproblemową integracją z innymi rozwiązaniami zabezpieczeń firmy Microsoft, takimi jak Microsoft Cloud App Security i usługa Windows Defender Advanced Threat Protection, należy się upewnić, że Rozwiązanie zabezpieczeń jest kompleksowe, a także proste do dołączenia i wdrożenia.

Ponadto można rozszerzyć pełne rozwiązanie poza platformę Azure na obciążenia działające w innych chmurach, jak i w lokalnych centrach danych.

### <a name="automatically-discover-and-onboard-azure-resources"></a>Automatyczne wykrywanie i dodawanie zasobów platformy Azure

Usługa Security Center zapewnia bezproblemową natywną integrację z platformą Azure i z zasobami Azure. Oznacza to, że można zebrać wszystkie ustawienia zabezpieczeń obejmujące zasady usługi Azure Policy i wbudowane zasady usługi Security Center we wszystkich zasobach Azure oraz mieć pewność, że w przypadku nowych zasobów od razu po ich utworzeniu na platformie Azure te zasady są automatycznie stosowane.

Rozbudowane kolekcje dzienników — dzienniki z systemów Windows i Linux są wykorzystywane przez aparat analizy zabezpieczeń i używane do tworzenia zaleceń i alertów.

## <a name="next-steps"></a>Następne kroki

- Do rozpoczęcia pracy z usługą Security Center jest wymagana subskrypcja platformy Microsoft Azure. Jeśli nie masz subskrypcji, możesz zarejestrować się, aby uzyskać dostęp do [bezpłatnej wersji próbnej](https://azure.microsoft.com/free/).
- Bezpłatna warstwa cenowa Security Center jest włączona w ramach subskrypcji platformy Azure. Aby skorzystać z zaawansowanych możliwości zarządzania zabezpieczeniami i ochrony przed zagrożeniami, musisz przeprowadzić uaktualnienie do warstwy cenowej standardowa. Za 30 dni można bezpłatnie próbować korzystać z warstwy Standardowa. Aby uzyskać więcej informacji, zobacz [stronę z cennikiem Security Center](https://azure.microsoft.com/pricing/details/security-center/).
- Jeśli wszystko jest gotowe do włączenia Security Center w warstwie Standardowa, [Przewodnik Szybki Start: dołączanie subskrypcji platformy Azure do Security Center Standard](security-center-get-started.md) przeprowadzi Cię przez kolejne kroki.