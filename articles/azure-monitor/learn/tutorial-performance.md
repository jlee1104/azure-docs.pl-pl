---
title: Diagnozowanie problemów z wydajnością za pomocą usługi Azure Application Insights | Microsoft Docs
description: Samouczek omawiający znajdowanie i diagnozowanie problemów z wydajnością aplikacji za pomocą usługi Azure Application Insights.
ms.subservice: application-insights
ms.topic: tutorial
author: mrbullwinkle
ms.author: mbullwin
ms.date: 08/13/2019
ms.custom: mvc
ms.openlocfilehash: 98d7c1552a7b1f2b02ae4df1cad24e20f7ac76e1
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79239595"
---
# <a name="find-and-diagnose-performance-issues-with-azure-application-insights"></a>Znajdowanie i diagnozowanie problemów z wydajnością za pomocą usługi Azure Application Insights

Usługa Azure Application Insights gromadzi dane telemetryczne z Twojej aplikacji, aby pomóc w analizie jej działania i wydajności.  Tymi informacjami można się posłużyć przy identyfikowaniu stwierdzonych problemów albo identyfikowaniu usprawnień do aplikacji, które miałyby największy wpływ na użytkowników.  Ten samouczek przedstawia proces analizowania wydajności składników serwera aplikacji i perspektywy klienta.  Omawiane kwestie:

> [!div class="checklist"]
> * Identyfikowanie wydajności operacji po stronie serwera
> * Analizowanie operacji serwera w celu określenia głównej przyczyny niskiej wydajności
> * Identyfikowanie najwolniejszych operacji po stronie klienta
> * Analizowanie szczegółów wyświetleń stron przy użyciu języka zapytań


## <a name="prerequisites"></a>Wymagania wstępne

W celu ukończenia tego samouczka:

- Zainstaluj [program Visual Studio 2019](https://www.visualstudio.com/downloads/) z następującymi obciążeniami:
    - Tworzenie aplikacji na platformie ASP.NET i aplikacji internetowych
    - Tworzenie aplikacji na platformie Azure
- Wdróż aplikację .NET na platformie Azure i [włącz zestaw Application Insights SDK](../../azure-monitor/app/asp-net.md).
- [Włącz profiler usługi Application Insights](../../azure-monitor/app/profiler.md#installation) dla swojej aplikacji.

## <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure.
Zaloguj się do portalu [https://portal.azure.com](https://portal.azure.com)Azure w .

## <a name="identify-slow-server-operations"></a>Identyfikowanie wolnych operacji serwera
Usługa Application Insights zbiera informacje o wydajności różnych operacji w aplikacji. Identyfikując operacje o najdłuższym czasie trwania, możesz zdiagnozować potencjalne problemy albo najlepiej ukierunkować trwające prace programistyczne w celu podniesienia ogólnej wydajności aplikacji.

1. Wybierz pozycję **Application Insights**, a następnie wybierz swoją subskrypcję.  
1. Aby otworzyć panel **Wydajność**, wybierz pozycję **Wydajność** z menu **Zbadaj** albo kliknij wykres**Czas odpowiedzi serwera**.

    ![Wydajność](media/tutorial-performance/1-overview.png)

2. Panel **Wydajność** przedstawia wydajność i średni czas trwania każdej operacji dla aplikacji.  Te informacje mogą posłużyć do zidentyfikowania tych operacji, które mają największy wpływ na użytkowników. W tym przykładzie dobrymi kandydatami do przyjrzenia się bliżej są operacje **GET Customers/Details** i **GET Home/Index** z powodu ich względnie długiego czasu trwania i względnie dużej liczby wywołań.  Inne operacje mogą mieć dłuższy czas trwania, ale są rzadko wywoływane, więc efekt ich poprawienia będzie niewielki.  

    ![Panel serwera wydajności](media/tutorial-performance/2-server-operations.png)

3. Wykres przedstawia obecnie średni czas trwania wybranych operacji w czasie. Możesz przełączyć się na 95. percentyl, aby znaleźć problemy z wydajnością. Dodaj operacje, które Cię interesują, przypinając je do wykresu.  To pokazuje, że istnieją wzrosty wartości warte zbadania.  Wyizoluj je jeszcze bardziej, zmniejszając przedział czasu wykresu.

    ![Przypinanie wartości](media/tutorial-performance/3-server-operations-95th.png)

4.  Panel wydajności po prawej stronie pokazuje rozkład czasów trwania różnych żądań dla wybranej operacji.  Zmniejsz okno, aby rozpocząć w okolicy 95. percentyla. Z karty ze szczegółowymi informacjami „3 najważniejsze zależności” można szybko odczytać, że zależności zewnętrzne najprawdopodobniej wpływają na spowolnienie transakcji.  Kliknij przycisk z liczbą próbek, aby wyświetlić listę próbek. Następnie możesz wybrać dowolną próbkę, aby wyświetlić szczegóły transakcji.

5.  Na pierwszy rzut oka zobaczysz, że wywołanie tabeli Fabrikamaccount Azure Table najbardziej wpływa na całkowity czas trwania transakcji. Zobaczysz też, że wyjątek spowodował jego niepowodzenia. Możesz kliknąć dowolną pozycję na liście, aby wyświetlić jej szczegóły po prawej stronie. [Dowiedz się więcej o środowisku diagnostyki transakcji](../../azure-monitor/app/transaction-diagnostics.md)

    ![Szczegóły kompleksowej operacji](media/tutorial-performance/4-end-to-end.png)
    

6.  Narzędzie **Profiler** pomaga zagłębić się w diagnostykę na poziomie kodu, wyświetlając rzeczywisty kod uruchamiany dla operacji i czas wymagany przez każdy z kroków. Niektóre operacje mogą nie mieć śladu, ponieważ profiler jest uruchamiany okresowo.  Z upływem czasu coraz więcej operacji powinno mieć ślady.  Aby uruchomić profiler dla operacji, kliknij pozycję **Ślady narzędzia Profiler**.
5.  Ślad pokazuje indywidualne zdarzenia dla każdej operacji, więc można zdiagnozować główną przyczynę obecnego czasu trwania całej operacji.  Kliknij jeden z przykładów u góry, które mają najdłuższy czas trwania.
6.  Kliknij **gorącą ścieżkę,** aby wyróżnić określoną ścieżkę zdarzeń, które najbardziej przyczyniają się do całkowitego czasu trwania operacji.  W tym przykładzie widać, że najwolniejsze wywołanie pochodzi z metody *FabrikamFiberAzureStorage.GetStorageTableData*. Częścią zabierającą najwięcej czasu jest metoda *CloudTable.CreateIfNotExist*. Jeśli ten wiersz kodu jest wywoływany po każdym wywołaniu funkcji, niepotrzebnie używane będą wywołanie sieciowe i zasób procesora CPU. Najlepszym sposobem poprawienia kodu jest umieszczenie tego wiersza w jakiejś metodzie startowej, która jest wykonywana tylko raz.

    ![Szczegóły profilera](media/tutorial-performance/5-hot-path.png)

7.  Obszar **Porada dotycząca wydajności** u góry ekranu potwierdza ocenę, że nadmierny czas trwania wynika z oczekiwania.  Kliknij link **oczekiwanie**, aby otworzyć dokumentację omawiającą interpretowanie różnych typów zdarzeń.

    ![Porada dotycząca wydajności](media/tutorial-performance/6-perf-tip.png)

8.   Aby uzyskać dalszą analizę, można kliknąć **pobierz śledzenie,** aby pobrać śledzenie. Można wyświetlić te dane za pomocą [PerfView](https://github.com/Microsoft/perfview#perfview-overview).

## <a name="use-logs-data-for-server"></a>Używanie danych dzienników dla serwera
 Dzienniki zawiera bogaty język zapytań, który umożliwia analizowanie wszystkich danych zebranych przez usługa Application Insights. Możesz jej używać do wykonywania głębokiej analizy danych żądań i wydajności.

1. Powrót do panelu szczegółów ![operacji i kliknij pozycję Widok ikony dzienników](media/tutorial-performance/app-viewinlogs-icon.png)**w dziennikach (Analytics)**

2. Dzienniki są otwierane z zapytaniem dla każdego z widoków w panelu.  Zapytania te można uruchomić w proponowanej formie lub dostosować do własnych wymagań.  Pierwsze zapytanie pokazuje czas trwania operacji w miarę upływu czasu.

    ![loguje kwerendę](media/tutorial-performance/7-request-time-logs.png)


## <a name="identify-slow-client-operations"></a>Identyfikowanie wolnych operacji klienta
Oprócz identyfikowania procesów serwera do zoptymalizowania, usługa Application Insights może analizować perspektywę przeglądarek klienta.  To może pomóc zidentyfikować potencjalne ulepszenia do składników klienta, a nawet zidentyfikować problemy z różnymi przeglądarkami i lokalizacjami.

1. Wybierz **pozycję Przeglądarka** w obszarze **Zbadaj,** a następnie kliknij pozycję **Wydajność przeglądarki** lub wybierz pozycję **Wydajność** w obszarze **Zbadaj** i przełącz się na kartę **Przeglądarka,** klikając przycisk przełączania serwer/przeglądarka w prawym górnym rogu, aby otworzyć podsumowanie wydajności przeglądarki. Ma ono postać wizualnego podsumowania różnych danych telemetrycznych dla Twojej aplikacji z perspektywy przeglądarki.

    ![Podsumowanie informacji o przeglądarce](media/tutorial-performance/8-browser.png)

2. Wybierz jedną z nazw operacji, a następnie kliknij niebieski przycisk próbek w prawym dolnym rogu i wybierz operację. Spowoduje to wyświetlenie szczegółów transakcji end-to-end, a po prawej stronie można wyświetlić **właściwości widoku strony**. Dzięki temu można wyświetlić szczegóły klienta żądającego strony, w tym typ przeglądarki i jej lokalizację. Te informacje mogą pomóc w określeniu, czy występują problemy z wydajnością powiązane z konkretnymi typami klientów.

    ![Wyświetlenie strony](media/tutorial-performance/9-page-view-properties.png)

## <a name="use-logs-data-for-client"></a>Używanie danych dzienników dla klienta
Podobnie jak dane zebrane pod kątem wydajności serwera, usługa Application Insights udostępnia wszystkie dane klienta do głębokiej analizy przy użyciu dzienników.

1. Powrót do podsumowania ![przeglądarki i](media/tutorial-performance/app-viewinlogs-icon.png) kliknij pozycję Widok ikony dzienników **w dziennikach (Analytics)**

2. Dzienniki są otwierane z zapytaniem dla każdego z widoków w panelu. Pierwsze zapytanie pokazuje czas trwania dla różnych wyświetleń stron w miarę upływu czasu.

    ![Kwerenda dzienników](media/tutorial-performance/10-page-view-logs.png)

3.  Inteligentna diagnostyka jest funkcją Dzienniki identyfikuje unikatowe wzorce w danych. Po kliknięciu na wykresie liniowym kropki funkcji Inteligentna diagnostyka uruchamiane jest to samo zapytanie, ale bez rekordów będących przyczyną anomalii. Szczegółowe informacje o tych rekordach są pokazywane w sekcji komentarza zapytania, więc można zidentyfikować właściwości tych wyświetleń strony, które powodują za długie czasy trwania.

    ![Dzienniki z inteligentną diagnostyką](media/tutorial-performance/11-page-view-logs-dsmart.png)


## <a name="next-steps"></a>Następne kroki
Teraz, gdy już wiesz, jak identyfikować wyjątki czasu wykonywania, przejdź do następnego samouczka, aby dowiedzieć się, jak tworzyć alerty w odpowiedzi na błędy.

> [!div class="nextstepaction"]
> [Alerty dotyczące kondycji aplikacji](../../azure-monitor/learn/tutorial-alert.md)
