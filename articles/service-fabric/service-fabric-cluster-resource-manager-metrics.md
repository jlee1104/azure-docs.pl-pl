---
title: Zarządzanie ładowaniem aplikacji Azure Service Fabric przy użyciu metryk
description: Informacje na temat konfigurowania i używania metryk w Service Fabric do zarządzania użyciem zasobów usługi.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: ea21502cdab35b261e20af7f23b7b522f77c6667
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75451994"
---
# <a name="managing-resource-consumption-and-load-in-service-fabric-with-metrics"></a>Zarządzanie zużyciem zasobów i obciążeniem Service Fabric przy użyciu metryk
*Metryki* to zasoby, których dotyczą usługi i które są udostępniane przez węzły w klastrze. Metryka to wszystko, co chcesz zarządzać, aby zwiększyć lub monitorować wydajność usług. Na przykład możesz obserwować użycie pamięci, aby dowiedzieć się, czy usługa jest przeciążona. Innym rozwiązaniem jest określenie, czy usługa może zostać przeniesiona w innym miejscu, w którym ilość pamięci jest mniej ograniczona, aby uzyskać lepszą wydajność.

Takie jak pamięć, dysk i użycie procesora są przykładowe metryki. Te metryki to metryki fizyczne, zasoby odpowiadające zasobom fizycznym w węźle, który musi być zarządzany. Metryki mogą być również (i często) metryki logiczne. Metryki logiczne są takie jak "MyWorkQueueDepth" lub "MessagesToProcess" lub "TotalRecords". Metryki logiczne są zdefiniowane przez aplikację i pośrednio odpowiadają pewnemu zużyciu zasobów fizycznych. Metryki logiczne są wspólne, ponieważ mogą być trudne do mierzenia i raportowania zużycia zasobów fizycznych na podstawie poszczególnych usług. Ze względu na złożoność mierzenia i raportowania własnych metryk fizycznych jest również Service Fabric udostępniają pewne metryki domyślne.

## <a name="default-metrics"></a>Domyślne metryki
Załóżmy, że chcesz zacząć pisać i wdrażać usługę. W tym momencie nie wiesz, jakie zasoby fizyczne lub logiczne zużywają. To dobrze! Klaster Service Fabric Menedżer zasobów używa pewnych metryk domyślnych, gdy nie zostaną określone żadne inne metryki. Oto one:

  - PrimaryCount — liczba replik podstawowych w węźle 
  - ReplicaCount — liczba całkowitych replik stanowych w węźle
  - Liczba — liczba wszystkich obiektów usługi (bezstanowe i stanowe) w węźle

| Metryka | Obciążenie wystąpienia bezstanowego | Pomocnicze obciążenie stanowe | Stanowe obciążenie podstawowe | Waga |
| --- | --- | --- | --- | --- |
| PrimaryCount |0 |0 |1 |Wysoka |
| ReplicaCount |0 |1 |1 |Średnie |
| Liczba |1 |1 |1 |Niska |


W przypadku obciążeń podstawowych metryki domyślne zapewniają dystrybucję znośnego pracy w klastrze. W poniższym przykładzie Zobaczmy, co się dzieje, gdy utworzymy dwie usługi i korzystasz z domyślnych metryk na potrzeby równoważenia. Pierwsza usługa jest usługą stanową z trzema partycjami i docelowym zestawem replik o rozmiarze trzech. Druga usługa to usługa bezstanowa z jedną partycją i liczbą wystąpień trzy.

Oto, co otrzymujemy:

<center>

![układ klastra z domyślnymi metrykami][Image1]
</center>

Dodatkowe kwestie, na które należy zwrócić uwagę:
  - Repliki podstawowe dla usługi stanowej są dystrybuowane między kilka węzłów
  - Repliki dla tej samej partycji znajdują się w różnych węzłach.
  - Łączna liczba Primaries i elementów pomocniczych jest dystrybuowana w klastrze.
  - Całkowita liczba obiektów usługi jest równomiernie alokowana w każdym węźle

Aukcj!

Metryki domyślne działają dobrze jako początek. Jednak metryki domyślne będą przenoszone tylko do tej pory. Na przykład: Jakie jest prawdopodobieństwo, że wybrany schemat partycjonowania będzie idealnie nawet wykorzystany przez wszystkie partycje? Jakie jest prawdopodobieństwo, że obciążenie danej usługi jest stałe z upływem czasu lub nawet jeden raz w wielu partycjach?

Można uruchomić z tylko metrykami domyślnymi. Jednak zazwyczaj oznacza to, że wykorzystanie klastra jest niższe i bardziej nierówne. Wynika to z faktu, że metryki domyślne nie są adaptacyjne i zakłada, że wszystko jest równoważne. Na przykład podstawowa, która jest zajęta i która nie jest współautorem "1" do metryki PrimaryCount. W najgorszym przypadku użycie tylko metryk domyślnych może spowodować, że w przypadku nieplanowanych węzłów występują problemy z wydajnością. Jeśli interesuje Cię maksymalne wykorzystanie klastra i uniknięcie problemów z wydajnością, musisz użyć niestandardowych metryk i dynamicznej sprawozdawczości.

## <a name="custom-metrics"></a>Metryki niestandardowe
Metryki są konfigurowane w zależności od nazwy wystąpienia usługi podczas tworzenia usługi.

Każda Metryka ma pewne właściwości, które go opisują: nazwę, wagę i obciążenie domyślne.

* Nazwa metryki: Nazwa metryki. Nazwa metryki jest unikatowym identyfikatorem dla metryki w klastrze z perspektywy Menedżer zasobów.
* Waga: Waga metryki definiuje, jak ważna jest Metryka względem innych metryk dla tej usługi.
* Domyślne obciążenie: obciążenie domyślne jest reprezentowane w różny sposób w zależności od tego, czy usługa jest bezstanowa, czy stanowa.
  * W przypadku usług bezstanowych każda Metryka ma jedną właściwość o nazwie DefaultLoad
  * W przypadku definiowanych przez siebie usług stanowych:
    * PrimaryDefaultLoad: domyślna liczba tej metryki, która używa tej usługi, gdy jest to podstawowa
    * SecondaryDefaultLoad: Domyślna ilość tej metryki zużywanej przez tę usługę, gdy jest ona pomocnicza

> [!NOTE]
> Jeśli zdefiniowano metryki niestandardowe i chcesz _również_ użyć metryk domyślnych, należy _jawnie_ dodać metryki domyślne i zdefiniować wagi oraz ich wartości. Dzieje się tak, ponieważ należy zdefiniować relację między metrykami domyślnymi i metrykami niestandardowymi. Na przykład, może być ważne ConnectionCount lub WorkQueueDepth więcej niż podstawowa dystrybucja. Domyślnie waga metryki PrimaryCount jest wysoka, więc chcesz ją ograniczyć do średniej, gdy dodasz inne metryki w celu zapewnienia pierwszeństwa.
>

### <a name="defining-metrics-for-your-service---an-example"></a>Definiowanie metryk dla usługi — przykład
Załóżmy, że chcesz wykonać następującą konfigurację:

  - Usługa zgłasza metrykę o nazwie "ConnectionCount"
  - Warto również użyć metryk domyślnych 
  - Zostały wykonane pewne pomiary i wiadomo, że zwykle replika podstawowa tej usługi przyjmuje 20 jednostek "ConnectionCount"
  - Serwery pomocnicze używają 5 jednostek "ConnectionCount"
  - Wiadomo, że "ConnectionCount" jest najważniejszym metryką w zakresie zarządzania wydajnością tej konkretnej usługi
  - Nadal chcesz zrównoważyć repliki podstawowe. Podstawowe repliki są zwykle dobrym pomysłem, niezależnie od tego, co. Pomaga to zapobiec utracie niektórych węzłów lub domen błędów przed wpływem na większość replik podstawowych. 
  - W przeciwnym razie metryki domyślne są ograniczone

Oto kod, który można napisać, aby utworzyć usługę z tą konfiguracją metryk:

Kod:

```csharp
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
StatefulServiceLoadMetricDescription connectionMetric = new StatefulServiceLoadMetricDescription();
connectionMetric.Name = "ConnectionCount";
connectionMetric.PrimaryDefaultLoad = 20;
connectionMetric.SecondaryDefaultLoad = 5;
connectionMetric.Weight = ServiceLoadMetricWeight.High;

StatefulServiceLoadMetricDescription primaryCountMetric = new StatefulServiceLoadMetricDescription();
primaryCountMetric.Name = "PrimaryCount";
primaryCountMetric.PrimaryDefaultLoad = 1;
primaryCountMetric.SecondaryDefaultLoad = 0;
primaryCountMetric.Weight = ServiceLoadMetricWeight.Medium;

StatefulServiceLoadMetricDescription replicaCountMetric = new StatefulServiceLoadMetricDescription();
replicaCountMetric.Name = "ReplicaCount";
replicaCountMetric.PrimaryDefaultLoad = 1;
replicaCountMetric.SecondaryDefaultLoad = 1;
replicaCountMetric.Weight = ServiceLoadMetricWeight.Low;

StatefulServiceLoadMetricDescription totalCountMetric = new StatefulServiceLoadMetricDescription();
totalCountMetric.Name = "Count";
totalCountMetric.PrimaryDefaultLoad = 1;
totalCountMetric.SecondaryDefaultLoad = 1;
totalCountMetric.Weight = ServiceLoadMetricWeight.Low;

serviceDescription.Metrics.Add(connectionMetric);
serviceDescription.Metrics.Add(primaryCountMetric);
serviceDescription.Metrics.Add(replicaCountMetric);
serviceDescription.Metrics.Add(totalCountMetric);

await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton –Metric @("ConnectionCount,High,20,5”,"PrimaryCount,Medium,1,0”,"ReplicaCount,Low,1,1”,"Count,Low,1,1”)
```

> [!NOTE]
> W powyższych przykładach i w pozostałej części niniejszego dokumentu opisano zarządzanie metrykami dla poszczególnych nazw usług. Istnieje również możliwość zdefiniowania metryk dla usług na poziomie _typu_ usługi. Jest to realizowane przez określenie ich w manifestach usługi. Definiowanie metryk na poziomie typu nie jest zalecane z kilku powodów. Pierwszym powodem jest to, że nazwy metryk są często specyficzne dla środowiska. O ile nie istnieje umowa przedsiębiorstwa, nie można się upewnić, że Metryka "rdzenie" w jednym środowisku nie ma wartości "MiliCores" lub "rdzenie" w innych miejscach. Jeśli metryki są zdefiniowane w manifeście, należy utworzyć nowe manifesty dla każdego środowiska. Zwykle prowadzi to do rozmnożenia różnych manifestów z drobnymi różnicami, co może prowadzić do problemów z zarządzaniem.  
>
> Obciążenia metryk są zwykle przypisane do wystąpienia poszczególnych wystąpień usługi. Załóżmy na przykład, że utworzysz jedno wystąpienie usługi dla klienta, który planuje używać go tylko w jasny sposób. Załóżmy również, że tworzysz kolejną dla CustomerB, którzy mają większe obciążenie. W takim przypadku prawdopodobnie chcesz dostosować domyślne obciążenia dla tych usług. Jeśli masz metryki i obciążenia zdefiniowane za pośrednictwem manifestów i chcesz obsługiwać ten scenariusz, wymagane są różne typy aplikacji i usług dla każdego klienta. Wartości zdefiniowane w czasie tworzenia usługi zastępują te zdefiniowane w manifeście, dlatego można użyć, aby ustawić określone ustawienia domyślne. Jednak wykonanie tej czynności powoduje, że wartości zadeklarowane w manifestach nie pasują do tych, w których faktycznie działa usługa. Może to prowadzić do pomyłek. 
>

Jako przypomnienie: Jeśli chcesz, aby użyć domyślnych metryk, nie musisz dotykać kolekcji metryk w ogóle ani wykonywać żadnych specjalnych czynności podczas tworzenia usługi. Metryki domyślne są stosowane automatycznie, gdy nie są zdefiniowane żadne inne. 

Teraz przejdźmy do każdego z tych ustawień bardziej szczegółowo i zapoznaj się z zachowaniem, na które ma to wpływ.

## <a name="load"></a>Ładowanie
Cały punkt definiowania metryk ma reprezentować pewne obciążenie. *Obciążenie* polega na tym, ile danej metryki jest zużywane przez niektóre wystąpienie usługi lub replika w danym węźle. Obciążenie można skonfigurować w prawie każdym punkcie. Przykład:

  - Obciążenie można zdefiniować podczas tworzenia usługi. Jest to nazywane _obciążeniem domyślnym_.
  - Informacje o metrykach, w tym domyślne obciążenia dla usługi, można zaktualizować po utworzeniu usługi. Jest to nazywane _aktualizacją usługi_. 
  - Obciążenia dla danej partycji mogą zostać zresetowane do wartości domyślnych dla tej usługi. Jest to tzw. _Resetowanie obciążenia partycji_.
  - Obciążenie może być zgłaszane osobno dla poszczególnych obiektów usługi podczas środowiska uruchomieniowego. Jest to tzw. _obciążenie raportowaniem_. 
  
Wszystkie te strategie mogą być używane w ramach tej samej usługi w ramach swojego okresu istnienia. 

## <a name="default-load"></a>Domyślne obciążenie
*Domyślne obciążenie* polega na tym, ile metryk każdy obiekt usługi (wystąpienie bezstanowe lub replika stanowa) tej usługi zużywa. Klaster Menedżer zasobów używa tego numeru do ładowania obiektu usługi do momentu odzyskania innych informacji, takich jak dynamiczny raport ładowania. W przypadku łatwiejszych usług domyślne obciążenie jest definicją statyczną. Domyślne obciążenie nigdy nie jest aktualizowane i jest używane w okresie istnienia usługi. Obciążenia domyślne działają doskonale w przypadku scenariuszy planowania pojemności w przypadku, gdy pewne ilości zasobów są przeznaczone dla różnych obciążeń i nie są zmieniane.

> [!NOTE]
> Aby uzyskać więcej informacji na temat zarządzania pojemnością i definiowania pojemności dla węzłów w klastrze, zobacz [ten artykuł](service-fabric-cluster-resource-manager-cluster-description.md#capacity).
> 

Menedżer zasobów klastra umożliwia usługom stanowym Określanie innego domyślnego obciążenia dla swoich Primaries i serwerów pomocniczych. Usługi bezstanowe mogą określać tylko jedną wartość, która ma zastosowanie do wszystkich wystąpień. W przypadku usług stanowych, domyślne obciążenie dla replik podstawowych i pomocniczych zazwyczaj różni się od tego, że repliki są różne rodzaje pracy w każdej roli. Na przykład Primaries obsługuje zarówno operacje odczytu i zapisu, jak i obsługujące większość obciążeń obliczeniowych, natomiast nie są to serwery pomocnicze. Zwykle domyślne obciążenie repliki podstawowej jest wyższe niż domyślne obciążenie dla replik pomocniczych. Liczby rzeczywiste powinny być zależne od własnych pomiarów.

## <a name="dynamic-load"></a>Ładowanie dynamiczne
Załóżmy, że uruchomiono usługę przez pewien czas. W przypadku niektórych monitorowania zauważono, że:

1. Niektóre partycje lub wystąpienia danej usługi zużywają więcej zasobów niż inne
2. Niektóre usługi mają obciążenie, które różnią się w zależności od czasu.

Istnieje wiele rzeczy, które mogą spowodować wahania obciążenia. Na przykład różne usługi lub partycje są skojarzone z różnymi klientami z różnymi wymaganiami. Można również zmienić obciążenie, ponieważ ilość pracy wykonywanej przez usługę różni się w ciągu dnia. Bez względu na to, że zwykle nie ma żadnej pojedynczej liczby, która może być używana domyślnie. Jest to szczególnie prawdziwe, jeśli chcesz uzyskać największe wykorzystanie z klastra. Dowolna wybrana wartość dla domyślnego obciążenia ma zły czas. Nieprawidłowe obciążenia domyślne powodują, że w klastrze Menedżer zasobów przez lub w obszarze przydzielanie zasobów. W związku z tym masz węzły, które są nadmiernie lub są używane, nawet jeśli klaster Menedżer zasobów być zrównoważony w klastrze. Obciążenia domyślne są nadal dobre, ponieważ udostępniają pewne informacje dotyczące początkowego umieszczania, ale nie są kompletną historią dla rzeczywistych obciążeń. W celu dokładnego przechwycenia zmienionych wymagań dotyczących zasobów, klaster Menedżer zasobów umożliwia każdemu obiektowi usługi zaktualizowanie własnego obciążenia w czasie wykonywania. Jest to nazywane raportowaniem obciążenia dynamicznego.

Dynamiczne raporty obciążenia umożliwiają replikom lub wystąpieniu dostosowanie ich alokacji/zgłaszanego obciążenia w czasie ich istnienia. Replika usługi lub wystąpienie, które było zimne i nie wykonywało żadnej pracy, zwykle zgłasza, że używa niskich ilości danej metryki. W przypadku zajętej repliki lub wystąpienia zostałby zaraportowany, że używają one więcej.

Raportowanie obciążenia na replikę lub wystąpienie umożliwia klastrowi Menedżer zasobów Reorganizowanie poszczególnych obiektów usługi w klastrze. Reorganizacja usług pomaga zapewnić, że będą oni musieli uzyskać potrzebne zasoby. Usługi zajęte w praktyce umożliwiają "odzyskiwanie" zasobów z innych replik lub wystąpień, które są obecnie zimne lub mniej efektywne.

W Reliable Services kod do raportowania ładowania dynamicznie wygląda następująco:

Kod:

```csharp
this.Partition.ReportLoad(new List<LoadMetric> { new LoadMetric("CurrentConnectionCount", 1234), new LoadMetric("metric1", 42) });
```

Usługa może raportować wszystkie metryki zdefiniowane dla niego podczas tworzenia. Jeśli usługa zgłasza obciążenie dla metryki, która nie jest skonfigurowana do użycia, Service Fabric ignoruje ten raport. Jeśli w tym samym czasie są zgłaszane inne metryki, te raporty są akceptowane. Kod usługi może mierzyć i raportować wszystkie metryki, które zna, i operatory mogą określać konfigurację metryki, która ma być używana bez konieczności zmiany kodu usługi. 

### <a name="updating-a-services-metric-configuration"></a>Aktualizowanie konfiguracji metryk usługi
Lista metryk skojarzonych z usługą i właściwości tych metryk można aktualizować dynamicznie, gdy usługa jest aktywna. Pozwala to na eksperymentowanie i elastyczność. Oto kilka przykładów, z których jest to przydatne:

  - Wyłączanie metryki z raportem debugowania dla określonej usługi
  - ponowne konfigurowanie wag metryk na podstawie żądanego zachowania
  - Włączanie nowej metryki tylko po jej wdrożeniu i sprawdzeniu poprawności za pośrednictwem innych mechanizmów
  - zmiana domyślnego obciążenia dla usługi na podstawie zaobserwowanego zachowania i zużycia

Główne interfejsy API służące do zmiany konfiguracji metryk są C# `FabricClient.ServiceManagementClient.UpdateServiceAsync` w i `Update-ServiceFabricService` w programie PowerShell. Wszelkie informacje określone za pomocą tych interfejsów API zastępują natychmiast istniejące informacje o metrykach usługi. 

## <a name="mixing-default-load-values-and-dynamic-load-reports"></a>Mieszanie domyślnych wartości ładowania i dynamicznych raportów ładowania
Dla tej samej usługi można użyć domyślnych obciążeń ładowania i dynamicznego. Gdy usługa korzysta zarówno z domyślnych, jak i dynamicznych raportów ładowania, obciążenie domyślne służy jako oszacowanie do momentu wyświetlenia raportów dynamicznych. Domyślne obciążenie jest dobrym sposobem, ponieważ umożliwia klastrowi Menedżer zasobów działanie. Domyślne obciążenie umożliwia klastrowi Menedżer zasobów umieszczenie obiektów usługi w dobrych lokalizacjach podczas ich tworzenia. Jeśli nie zostaną podane żadne domyślne informacje o ładowaniu, rozmieszczenie usług jest skutecznie losowe. Gdy ładowanie raportów zostanie rozpoczęte później, początkowe losowe położenie jest często błędne i klaster Menedżer zasobów ma przenieść usługi.

Skorzystajmy z poprzedniego przykładu i Dowiedz się, co się dzieje, gdy dodamy niestandardowe metryki i dynamiczne raportowanie obciążeń. W tym przykładzie używamy "MemoryInMb" jako przykładowej metryki.

> [!NOTE]
> Pamięć jest jedną z metryk systemu, które Service Fabric mogą [zarządzać zasobami](service-fabric-resource-governance.md), i raporty IT są zwykle trudne. W rzeczywistości nie oczekujemy na raport dotyczący użycia pamięci; Pamięć jest używana tutaj jako pomoc do uczenia się możliwości Menedżer zasobów klastra.
>

Załóżmy, że początkowo została utworzona usługa stanowa przy użyciu następującego polecenia:

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton –Metric @("MemoryInMb,High,21,11”,"PrimaryCount,Medium,1,0”,"ReplicaCount,Low,1,1”,"Count,Low,1,1”)
```

Jako przypomnienie Ta składnia jest następująca: ("Metricname, MetricWeight, PrimaryDefaultLoad, SecondaryDefaultLoad").

Zobaczmy, jak może wyglądać jeden możliwy układ klastra:

<center>

Klaster ![zrównoważony jako metryki domyślne i niestandardowe][Image2]
</center>

Niektóre elementy, które są notowane:

* Każdy z replik pomocniczych w partycji może mieć własne obciążenie
* Ogólne wskaźniki są bilansowane. W przypadku pamięci stosunek między maksymalnym i minimalnym obciążeniem to 1,75 (węzeł o największej obciążeniu wynosi N3, najmniej N2 i 28/16 = 1,75).

Istnieje kilka rzeczy, które należy wyjaśnić:

* Co ustaliło, czy współczynnik 1,75 był rozsądny czy nie? W jaki sposób klaster Menedżer zasobów wiedzieć, czy jest wystarczająco dobry lub czy więcej pracy można wykonać?
* Kiedy odbywa się równoważenie?
* Co oznacza, że pamięć była ważona "wysoka"?

## <a name="metric-weights"></a>Wagi metryk
Śledzenie tych samych metryk w różnych usługach jest ważne. Ten widok globalny umożliwia klastrowi Menedżer zasobów Śledzenie zużycia w klastrze, zrównoważenie zużycia między węzłami i upewnienie się, że węzły nie przechodzą na pojemność. Jednak usługi mogą mieć różne widoki w zakresie ważności tej samej metryki. Ponadto w klastrze zawierającym wiele metryk i wielu usług, w przypadku wszystkich metryk mogą nie istnieć rozwiązania o szerokim równoważeniu. Jak klaster Menedżer zasobów obsługiwać te sytuacje?

Wagi metryk umożliwiają klastrowi Menedżer zasobów decydowanie, jak zrównoważyć klaster, gdy nie ma doskonałej odpowiedzi. Wagi metryk umożliwiają również klastrowi Menedżer zasobów równoważenia określonych usług. Metryki mogą mieć cztery różne poziomy wagi: zero, niski, średni i wysoki. Metryka o wadze zero przyczynia się nie tylko podczas rozważania, czy elementy są zrównoważone, czy nie. Jednak jego obciążenie ma nadal wpływ na zarządzanie pojemnością. Metryki o zerowej wadze są nadal przydatne i często są używane w ramach zachowania usługi i monitorowania wydajności. [Ten artykuł](service-fabric-diagnostics-event-generation-infra.md) zawiera więcej informacji na temat używania metryk do monitorowania i diagnostyki usług. 

Rzeczywisty wpływ różnych wag metryk w klastrze polega na tym, że klaster Menedżer zasobów generuje różne rozwiązania. Wagi metryk informują klaster Menedżer zasobów, że niektóre metryki są ważniejsze niż inne. Gdy nie jest idealnym rozwiązaniem, klaster Menedżer zasobów może preferować rozwiązania, które równoważą wyższy poziom metryk bardziej ważonych. Jeśli usługa uzna, że określona Metryka jest nieważna, może to spowodować, że użycie tej metryki nie zostanie zrównoważone. Dzięki temu inna usługa może uzyskać parzystą dystrybucję pewnej metryki.

Przyjrzyjmy się przykładowi niektórych raportów obciążenia oraz o sposobie, w jaki różne wagi metryki mają różne alokacje w klastrze. W tym przykładzie widzimy, że przełączenie względnej wagi metryk powoduje, że klaster Menedżer zasobów do tworzenia różnych rozwiązań usług.

<center>

Przykład ![wagi metryk i jej wpływ na rozwiązania do równoważenia][Image3]
</center>

W tym przykładzie są cztery różne usługi, a wszystkie raporty różne wartości dla dwóch różnych metryk, metryki i MetricB. W jednym przypadku wszystkie usługi definiują wartość najważniejsze dla jednej (waga = wysoka) i MetricB jako nieważne (waga = niska). W związku z tym widzimy, że klaster Menedżer zasobów umieści usługi, aby Metryka była lepsza od MetricB. "Lepsza zrównoważone" oznacza, że Metryka ma niższą odchylenie standardowe niż MetricB. W drugim przypadku odwracamy wagi metryczne. W związku z tym klaster Menedżer zasobów wymiany usług A i B do przydzielenia, gdzie MetricB jest lepiej zrównoważony niż Metryka.

> [!NOTE]
> Wagi metryk określają sposób, w jaki klaster Menedżer zasobów powinien zrównoważyć, ale nie powinien mieć czasu na zrównoważenie. Aby uzyskać więcej informacji na temat równoważenia, zapoznaj się z [tym artykułem](service-fabric-cluster-resource-manager-balancing.md)
>

### <a name="global-metric-weights"></a>Globalne wagi metryk
Załóżmy, że usługa definiuje metrykę jako wysoką wagi, a ServiceB ustawia wagę metryki na niski lub zero. Jaka jest rzeczywista waga, która jest używana do uzyskiwania informacji?

Istnieje wiele wag, które są śledzone dla każdej metryki. Pierwsza waga jest taka, którą zdefiniowano dla metryki podczas tworzenia usługi. Druga waga to globalna waga, która jest obliczana automatycznie. Klaster Menedżer zasobów używa obu tych wag podczas oceniania rozwiązań. Uwzględnianie obu wag do konta jest ważne. Dzięki temu klaster Menedżer zasobów zrównoważyć każdą usługę zgodnie z własnymi priorytetami, a także upewnić się, że klaster jako całość jest przypisywany prawidłowo.

Co się stanie, Jeśli klaster Menedżer zasobów nie zadziałała zarówno saldo globalne, jak i lokalne? Ponadto łatwo jest utworzyć rozwiązania, które są zrównoważone globalnie, ale co powoduje spadek równowagi zasobów dla poszczególnych usług. W poniższym przykładzie Przyjrzyjmy się usłudze z użyciem tylko metryk domyślnych i zobacz, co się dzieje, gdy zostanie uznane tylko saldo globalne:

<center>

![wpływ tylko na rozwiązanie globalne][Image4]
</center>

W górnym przykładzie opartym tylko na globalnym saldzie klaster jako całość jest zrównoważony. Wszystkie węzły mają tę samą liczbę Primaries i tę samą łączną liczbę replik. Jeśli jednak przeszukiwany jest rzeczywisty wpływ tego przydziału, nie jest to dobre: utrata dowolnego węzła wpływa na określone obciążenie nieproporcjonalnie, ponieważ wychodzi wszystkie jego Primaries. Na przykład jeśli pierwszy węzeł ulegnie awarii trzy Primaries dla trzech różnych partycji usługi Circle, wszystkie zostałyby utracone. Z drugiej strony, Trójkąty i sześciokąty mają traconą replikę. Nie powoduje to zakłócenia, poza koniecznością odzyskiwania repliki w dół.

W dolnym przykładzie klaster Menedżer zasobów rozłożyć repliki na podstawie zarówno globalnego, jak i za usługę. Podczas obliczania wyniku rozwiązania zapewnia większość wagi do rozwiązania globalnego i (konfigurowalne) część do poszczególnych usług. Saldo globalne dla metryki jest obliczane na podstawie średniej wagi metryk z każdej usługi. Każda usługa jest zrównoważona zgodnie ze zdefiniowanymi wagami metryk. Dzięki temu usługi są zrównoważone w zależności od potrzeb. W związku z tym, jeśli ten sam pierwszy węzeł ulegnie awarii, błąd jest dystrybuowany we wszystkich partycjach wszystkich usług. Wpływ na poszczególne są takie same.

## <a name="next-steps"></a>Następne kroki
- Aby uzyskać więcej informacji na temat konfigurowania usług, [Dowiedz się więcej o konfigurowaniu usług](service-fabric-cluster-resource-manager-configure-services.md)(Service-Fabric-Cluster-Resource-Manager-Configure-Services.MD)
- Definiowanie metryk defragmentacji jest jednym ze sposobów konsolidacji obciążenia węzłów zamiast rozproszenia go. Aby dowiedzieć się, jak skonfigurować defragmentację, zapoznaj się z [tym artykułem](service-fabric-cluster-resource-manager-defragmentation-metrics.md) .
- Aby dowiedzieć się, w jaki sposób klaster Menedżer zasobów zarządza i równoważenia obciążenia w klastrze, zapoznaj się z artykułem dotyczącym [równoważenia obciążenia](service-fabric-cluster-resource-manager-balancing.md)
- Zacznij od początku i [zapoznaj się z wprowadzeniem do klastra Service Fabric Menedżer zasobów](service-fabric-cluster-resource-manager-introduction.md)
- Koszt przenoszenia jest jednym ze sposobów sygnalizowania klastra Menedżer zasobów, że niektóre usługi są droższe do przeniesienia niż inne. Aby dowiedzieć się więcej o koszcie przenoszenia, zapoznaj się z [tym artykułem](service-fabric-cluster-resource-manager-movement-cost.md) .

[Image1]:./media/service-fabric-cluster-resource-manager-metrics/cluster-resource-manager-cluster-layout-with-default-metrics.png
[Image2]:./media/service-fabric-cluster-resource-manager-metrics/Service-Fabric-Resource-Manager-Dynamic-Load-Reports.png
[Image3]:./media/service-fabric-cluster-resource-manager-metrics/cluster-resource-manager-metric-weights-impact.png
[Image4]:./media/service-fabric-cluster-resource-manager-metrics/cluster-resource-manager-global-vs-local-balancing.png
