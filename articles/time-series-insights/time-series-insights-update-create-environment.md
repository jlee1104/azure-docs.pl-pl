---
title: 'Samouczek: Konfigurowanie środowiska w wersji zapoznawczej — Azure Time Series Insights | Microsoft Docs'
description: 'Samouczek: informacje dotyczące konfigurowania środowiska w wersji zapoznawczej programu Azure Time Series Insights.'
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: tutorial
ms.date: 02/20/2020
ms.custom: seodec18
ms.openlocfilehash: af15a7366fd07cecb376ff76ad383f784202a887
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/21/2020
ms.locfileid: "77526822"
---
# <a name="tutorial-set-up-an-azure-time-series-insights-preview-environment"></a>Samouczek: Konfigurowanie środowiska Azure Time Series Insights w wersji zapoznawczej

Ten samouczek przeprowadzi Cię przez proces tworzenia środowiska Azure Time Series Insights w wersji zapoznawczej *płatność zgodnie z rzeczywistym* użyciem.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
>
> * Tworzenie środowiska wersji zapoznawczej usługi Azure Time Series Insights.
> * Połącz środowisko Azure Time Series Insights w wersji zapoznawczej z IoT Hub.
> * Uruchamianie przykładowego akceleratora rozwiązań umożliwiającego strumieniowe przesyłanie danych do środowiska wersji zapoznawczej usługi Azure Time Series Insights.
> * Wykonywanie podstawowej analizy danych.
> * Definiowanie hierarchii oraz typu modelu szeregów czasowych i kojarzenie ich z wystąpieniami.
> * Użyj łącznika Power BI i Wizualizuj dane w Power BI.

>[!TIP]
> [Akceleratory rozwiązań IoT](https://www.azureiotsolutions.com/Accelerators) zapewniają wstępnie skonfigurowane rozwiązania klasy korporacyjnej, których można użyć do przyspieszenia tworzenia niestandardowych rozwiązań IoT.

Utwórz konto [bezpłatnej subskrypcji platformy Azure](https://azure.microsoft.com/free/) , jeśli jeszcze jej nie masz.

## <a name="prerequisites"></a>Wymagania wstępne

* Co najmniej musisz mieć rolę **współautor** dla subskrypcji platformy Azure. Aby uzyskać więcej informacji, zapoznaj się z tematem [Zarządzanie dostępem przy użyciu kontroli dostępu opartej na rolach i Azure Portal](../role-based-access-control/role-assignments-portal.md).

## <a name="create-a-device-simulation"></a>Tworzenie symulacji urządzeń

W tej sekcji utworzysz trzy symulowane urządzenia, które wysyłają dane do wystąpienia usługi Azure IoT Hub.

1. Przejdź do [strony akceleratorów rozwiązań Azure IoT](https://www.azureiotsolutions.com/Accelerators). Na stronie będzie widocznych kilka gotowych przykładów. Zaloguj się przy użyciu konta platformy Azure. Następnie wybierz pozycję **Symulacja urządzenia**.

   [![strony akceleratorów rozwiązań usługi Azure IoT.](media/v2-update-provision/iot-solution-accelerators-landing-page.png)](media/v2-update-provision/iot-solution-accelerators-landing-page.png#lightbox)

1. Na następnej stronie wybierz pozycję **Wypróbuj teraz**. Następnie wprowadź wymagane parametry na stronie **Tworzenie rozwiązania symulacji urządzenia** .

   Parametr|Opis
   ---|---
   **Nazwa wdrożenia** | Ta unikatowa wartość jest używana do tworzenia nowej grupy zasobów. Znajdujące się na liście zasoby platformy Azure są tworzone i przypisywane do grupy zasobów.
   **Subskrypcja platformy Azure** | Określ tę samą subskrypcję, która została użyta do utworzenia środowiska Time Series Insights w poprzedniej sekcji.
   **Opcje wdrażania** | Wybierz pozycję **Udostępnij nowe IoT Hub** , aby utworzyć nowe centrum IoT Hub specyficzne dla tego samouczka.
   **Lokalizacja platformy Azure** | Określ ten sam region, który został użyty do utworzenia środowiska Time Series Insights w poprzedniej sekcji.

   Po zakończeniu wybierz pozycję **Utwórz** , aby udostępnić zasoby platformy Azure rozwiązania. Ukończenie tego procesu może potrwać do 20 minut.

   [![zainicjować obsługę rozwiązania do symulacji urządzeń.](media/v2-update-provision/iot-solution-accelerators-configuration.png)](media/v2-update-provision/iot-solution-accelerators-configuration.png#lightbox)

1. Po zakończeniu aprowizacji zostaną wyświetlone dwa powiadomienia informujące o tym, że stan wdrożenia został przeniesiony z **aprowizacji** do **gotowe**. 

   >[!IMPORTANT]
   > Nie wprowadzaj jeszcze akceleratora rozwiązania. Pozostaw Tę stronę sieci Web otwartą, ponieważ wrócisz do niej później.

   [Zakończono Inicjowanie obsługi rozwiązań symulacji urządzenia ![.](media/v2-update-provision/iot-solution-accelerator-ready.png)](media/v2-update-provision/iot-solution-accelerator-ready.png#lightbox)

1. Teraz Sprawdź nowo utworzone zasoby w Azure Portal. Na stronie **grupy zasobów** Zwróć uwagę, że nowa grupa zasobów została utworzona przy użyciu **nazwy rozwiązania** podanej w ostatnim kroku. Zanotuj zasoby, które zostały utworzone dla symulacji urządzenia.

   [zasoby symulacji urządzenia ![.](media/v2-update-provision/tsi-device-sim-solution-resources.png)](media/v2-update-provision/tsi-device-sim-solution-resources.png#lightbox)

## <a name="create-a-preview-payg-environment"></a>Tworzenie środowiska w wersji zapoznawczej PAYG

W tej sekcji opisano sposób tworzenia środowiska Azure Time Series Insights w wersji zapoznawczej i łączenia go z usługą IoT Hub utworzoną przez Akcelerator rozwiązania IoT przy użyciu [Azure Portal](https://portal.azure.com/).

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta subskrypcji platformy Azure. 
1. W menu u góry po lewej stronie wybierz pozycję **+ Utwórz zasób**. 
1. Wybierz kategorię **Internet rzeczy**, a następnie wybierz pozycję **Time Series Insights**. 

   [![wybrać Time Series Insights zasób środowiska.](media/v2-update-provision/tsi-create-new-environment.png)](media/v2-update-provision/tsi-create-new-environment.png#lightbox)

1. W okienku **Tworzenie środowiska Time Series Insights** na karcie **podstawowe** ustaw następujące parametry:

    | Parametr | Akcja |
    | --- | ---|
    | **Nazwa środowiska** | Wprowadź unikatową nazwę środowiska Azure Time Series Insights w wersji zapoznawczej. |
    | **Subskrypcja** | Wprowadź subskrypcję, w której chcesz utworzyć środowisko Azure Time Series Insights w wersji zapoznawczej. Najlepszym rozwiązaniem jest użycie tej samej subskrypcji jako reszty zasobów IoT, które są tworzone przez symulatora urządzeń. |
    | **Grupa zasobów** | Wybierz istniejącą grupę zasobów lub Utwórz nową grupę zasobów dla zasobu środowiska Azure Time Series Insights wersji zapoznawczej. Grupa zasobów jest kontenerem zasobów platformy Azure. Najlepszym rozwiązaniem jest użycie tej samej grupy zasobów co inne zasoby IoT, które są tworzone przez symulatora urządzeń. |
    | **Lokalizacja** | Wybierz region centrum danych dla środowiska Azure Time Series Insights w wersji zapoznawczej. Aby uniknąć dodatkowego opóźnienia, najlepszym rozwiązaniem jest utworzenie środowiska Azure Time Series Insights w wersji zapoznawczej w tym samym regionie, w którym znajduje się centrum IoT utworzone w symulatorze urządzeń. |
    | **Warstwa** |  Wybierz pozycję **PAYG** (*płatność zgodnie z rzeczywistym*użyciem). Jest to jednostka SKU produktu Azure Time Series Insights w wersji zapoznawczej. |
    | **Nazwa właściwości** | Wprowadź wartość, która jednoznacznie identyfikuje wystąpienie szeregów czasowych. Nie można później zmienić wartości wprowadzonej w polu **ID właściwości** . Na potrzeby tego samouczka wprowadź ***iothub-Connection-Device-ID***. Aby dowiedzieć się więcej na temat identyfikatora szeregów czasowych, Przeczytaj [najlepsze rozwiązania dotyczące wybierania identyfikatora szeregów czasowych](./time-series-insights-update-how-to-id.md). |
    | **Nazwa konta magazynu** | Wprowadź globalnie unikatową nazwę nowego konta magazynu.|
    |**Włącz sklep ciepły**|Wybierz pozycję **tak** , aby włączyć sklep ciepły. Można wrócić później i włączyć to ustawienie. |
    |**Przechowywanie danych (w dniach)**|Wybierz opcję domyślną 7 dni. |

    Wybierz pozycję **Dalej: Źródło zdarzenia**.

   [![nową konfigurację środowiska Time Series Insights.](media/v2-update-provision/tsi-environment-configuration.png)](media/v2-update-provision/tsi-environment-configuration.png#lightbox)

   [![skonfigurować identyfikator szeregów czasowych dla środowiska.](media/v2-update-provision/tsi-time-series-id-selection.png)](media/v2-update-provision/tsi-time-series-id-selection.png#lightbox)

1. Na karcie **Źródło zdarzenia** ustaw następujące parametry:

   | Parametr | Akcja |
   | --- | --- |
   | **Utworzyć źródło zdarzeń?** | Wybierz pozycję **Tak**.|
   | **Nazwa** | Wprowadź unikatową wartość dla nazwy źródła zdarzenia. |
   | **Typ źródła** | Wybierz **IoT Hub**. |
   | **Wybierz centrum** | Wybierz **pozycję Wybierz istniejące**. |
   | **Subskrypcja** | Wybierz subskrypcję używaną dla symulatora urządzeń. |
   | **Nazwa centrum IoT** | Wybierz nazwę usługi IoT Hub utworzoną dla symulatora urządzeń. |
   | **Zasady dostępu do centrum IoT Hub** | Wybierz pozycję **iothubowner**. |
   | **Grupa konsumentów IoT Hub** | Wybierz pozycję **Nowy**, wprowadź unikatową nazwę, a następnie wybierz pozycję **+ Dodaj**. Grupa konsumentów musi być unikatowa w wersji zapoznawczej Azure Time Series Insights. |
   | **Właściwość znacznika czasu** | Ta wartość służy do identyfikowania właściwości **timestamp** w przychodzących danych telemetrycznych. Na potrzeby tego samouczka pozostaw to pole puste. Ten symulator używa sygnatury czasowej pochodzącej z usługi IoT Hub, domyślnej wartości dla usługi Time Series Insights. |

   Wybierz pozycję **Przegląd + utwórz**.

   [![skonfigurować utworzone Centrum IoT Hub jako źródło zdarzenia.](media/v2-update-provision/tsi-configure-event-source.png)](media/v2-update-provision/tsi-configure-event-source.png#lightbox)

1. Wybierz pozycję **Utwórz**.

    [![przegląd + Tworzenie strony z przyciskiem Utwórz.](media/v2-update-provision/tsi-environment-confirmation.png)](media/v2-update-provision/tsi-environment-confirmation.png#lightbox)

    Stan wdrożenia można sprawdzić:

    [![powiadomienia o zakończeniu wdrażania.](media/v2-update-provision/tsi-deployment-notification.png)](media/v2-update-provision/tsi-deployment-notification.png#lightbox)

1. Masz dostęp do środowiska w wersji zapoznawczej Azure Time Series Insights domyślnie, jeśli jesteś właścicielem subskrypcji platformy Azure. Sprawdź, czy masz dostęp:

   1. Wyszukaj grupę zasobów, a następnie wybierz nowo utworzone środowisko Azure Time Series Insights w wersji zapoznawczej. 

      [![zaznaczania i wyświetlania środowiska.](media/v2-update-provision/verify-tsi-resource-in-group.png)](media/v2-update-provision/verify-tsi-resource-in-group.png#lightbox)

   1. Na stronie Podgląd Azure Time Series Insights wybierz pozycję **zasady dostępu do danych**:

      [![sprawdzić zasady dostępu do danych.](media/v2-update-provision/tsi-data-access-panel.png)](media/v2-update-provision/tsi-data-access-panel.png#lightbox)

   1. Sprawdź, czy na liście znajdują się Twoje poświadczenia:

      Jeśli Twoje poświadczenia nie znajdują się na liście, musisz udzielić sobie uprawnień dostępu do środowiska, wybierając pozycję Dodaj i wyszukuj poświadczenia. Aby dowiedzieć się więcej o ustawianiu uprawnień, zobacz [Grant data access (Przyznawanie dostępu do danych)](./time-series-insights-data-access.md).

## <a name="stream-data"></a>Strumieniowe przesyłanie danych

Teraz, gdy wdrożono środowisko Time Series Insights, Rozpocznij przesyłanie strumieniowe danych na potrzeby analizy.

1. Wróć do swojego [Pulpitu nawigacyjnego akceleratorów rozwiązania](https://www.azureiotsolutions.com/Accelerators#dashboard). Zaloguj się ponownie, w razie potrzeby, przy użyciu tego samego konta platformy Azure, którego używasz w tym samouczku. Wybierz swoje urządzenie, a następnie **Przejdź do akceleratora rozwiązania** , aby uruchomić wdrożone rozwiązanie.

   [Pulpit nawigacyjny akceleratora rozwiązań ![.](media/v2-update-provision/iot-solution-accelerator-ready.png)](media/v2-update-provision/iot-solution-accelerator-ready.png#lightbox)

1. Aplikacja sieci Web symulacji urządzenia rozpoczyna się od wyświetlenia monitu o przyznanie aplikacji sieci Web **i zalogowanie się i odczytanie** uprawnienia do profilu. To uprawnienie umożliwia aplikacji pobranie informacji o profilu użytkownika niezbędnych do obsługi aplikacji.

   [![zgodę aplikacji sieci Web symulacji urządzenia.](media/v2-update-provision/sawa-signin-consent.png)](media/v2-update-provision/sawa-signin-consent.png#lightbox)

1. Wybierz pozycję **+ Nowa symulacja**. Po załadowaniu strony **konfiguracji symulacji** wprowadź wymagane parametry.

    | Parametr | Akcja |
    | --- | --- |
    | **Nazwa** | Wprowadź unikatową nazwę symulatora. |
    | **Opis** | Wprowadź definicję. |
    | **Czas trwania symulacji** | Wybierz opcję **Uruchamianie bez ograniczeń**. |
    | **Model urządzenia** | Kliknij pozycję + **Dodaj typ urządzenia** <br />**Nazwa**: wprowadź **windę**. <br />**Kwota**: wprowadź wartość **3**. <br /> Pozostaw pozostałe wartości domyślne |
    | **Docelowa usługa IoT Hub** | Wybierz pozycję **Użyj dostarczonej wstępnie usługi IoT Hub**. |

    [![skonfigurować parametry i uruchomienia.](media/v2-update-provision/tsi-launch-solution-accelerator.png)](media/v2-update-provision/tsi-launch-solution-accelerator.png#lightbox)

    Wybierz pozycję **Rozpocznij symulację**.

    Na pulpicie nawigacyjnym symulacji urządzenia są wyświetlane **aktywne urządzenia** i **całkowita liczba komunikatów** .

    [![pulpitu nawigacyjnego symulacji usługi Azure IoT.](media/v2-update-provision/tsi-see-active-devices-and-messages.png)](media/v2-update-provision/tsi-see-active-devices-and-messages.png#lightbox)

## <a name="analyze-data"></a>Analizowanie danych

W tej sekcji przeprowadzimy podstawową analizę danych szeregów czasowych przy użyciu [eksploratora wersji zapoznawczej usługi Azure Time Series Insights](./time-series-insights-update-explorer.md).

1. Przejdź do eksploratora wersji zapoznawczej usługi Azure Time Series Insights, wybierając adres URL na stronie zasobu w witrynie [Azure Portal](https://portal.azure.com/).

    [![adres URL Eksploratora Time Series Insights Preview.](media/v2-update-provision/tsi-select-explorer-url.png)](media/v2-update-provision/tsi-select-explorer-url.png#lightbox)

1. W Eksploratorze Time Series Insights zostanie wyświetlony pasek z górną częścią ekranu. Jest to Twój selektor dostępności. Upewnij się, że wybrano co najmniej dwa 2 m i jeśli to konieczne, rozwiń przedział czasu, zaznaczając i przeciągając uchwyty selektora w lewo i w prawo.

1. **Wystąpienia szeregów czasowych** będą wyświetlane po lewej stronie.

    [![listę wystąpień nienadrzędnych.](media/v2-update-provision/tsi-explorer-unparented-instances.png)](media/v2-update-provision/tsi-explorer-unparented-instances.png#lightbox)

1. Wybierz wystąpienie serii pierwszy-czas. Następnie wybierz pozycję **Pokaż temperaturę**.

    [![wybrane wystąpienie szeregów czasowych z poleceniem menu, aby wyświetlić średnią temperaturę.](media/v2-update-provision/select-instance-and-temperature.png)](media/v2-update-provision/select-instance-and-temperature.png#lightbox)

    Zostanie wyświetlony wykres szeregów czasowych. Zmień **Interwał** na **30 s**.

1. Powtórz poprzedni krok z innymi dwoma wystąpieniami szeregów czasowych, aby wyświetlić wszystkie trzy, jak pokazano na tym wykresie:

    [![wykres dla wszystkich szeregów czasowych.](media/v2-update-provision/tsi-explorer-add-three-instances.png)](media/v2-update-provision/tsi-explorer-add-three-instances.png#lightbox)

1. Wybierz selektor przedziałów czasu w prawym górnym rogu. W tym miejscu możesz wybrać określone czasy rozpoczęcia i zakończenia w milisekundach lub wybrać spośród wstępnie skonfigurowanych opcji, takich jak **ostatnie 30 minut**. Możesz również zmienić domyślną strefę czasową.

    [![Ustaw zakres czasu na 30 minut.](media/v2-update-provision/tsi-explorer-thirty-minute-time-range.png)](media/v2-update-provision/tsi-explorer-thirty-minute-time-range.png#lightbox)

    W Eksploratorze Time Series Insights zostanie wyświetlony postęp akceleratora rozwiązania w ciągu **ostatnich 30 minut** .

## <a name="define-and-apply-a-model"></a>Definiowanie i stosowanie modelu

W tej sekcji zastosujesz model, aby ustrukturyzować dane. Do utworzenia modelu potrzebne są definicje typów, hierarchii i wystąpień. Aby dowiedzieć się więcej na temat modelowania danych, zobacz [model szeregów czasowych](./time-series-insights-update-tsm.md).

1. W eksploratorze wybierz kartę **Model**:

   [![wyświetlić kartę Model w Eksploratorze.](media/v2-update-provision/tsi-select-model-view.png)](media/v2-update-provision/tsi-select-model-view.png#lightbox)

   Na karcie **typy** wybierz pozycję **+ Dodaj**.

1. Wprowadź następujące parametry:

    | Parametr | Akcja |
    | --- | ---|
    | **Nazwa** | Wprowadź **windę** |
    | **Opis** | Wprowadź **tę definicję typu dla wind** |

1. Następnie wybierz kartę **zmienne** . 

   Wybierz pozycję **+ Dodaj zmienną** i wypełnij następujące wartości dla pierwszej zmiennej typu Wind. W sumie są tworzone trzy zmienne.

    | Parametr | Akcja |
    | --- | --- |
    | **Nazwa** | Wpisz **Avg Temperature** (Temperatura średnia). |
    | **Natur** | Wybierz wartość **numeryczną** |
    | **Wartość** | Wybierz jedną z ustawień predefiniowanych: wybierz pozycję **temperatura (Podwójna precyzja)** . <br /> Uwaga: Ta **wartość** może potrwać kilka minut, jeśli funkcja Azure Time Series Insights Preview zacznie otrzymywać zdarzenia.|
    | **Operacja agregacji** | Rozwiń **Opcje zaawansowane**. <br /> Wybierz opcję **AVG** (średnia). |

    Wybierz przycisk **Zastosuj**. Następnie ponownie **Dodaj zmienną** i ustaw następujące wartości:

    | Parametr | Akcja |
    | --- | --- |
    | **Nazwa** | Wprowadź **średnie wibracje**. |
    | **Natur** | Wybierz wartość **numeryczną** |
    | **Wartość** | Wybierz spośród ustawień predefiniowanych: wybierz pozycję **drgania (Double)** . <br /> Uwaga: Ta **wartość** może potrwać kilka minut, jeśli funkcja Azure Time Series Insights Preview zacznie otrzymywać zdarzenia.|
    | **Operacja agregacji** | Rozwiń **Opcje zaawansowane**. <br /> Wybierz opcję **AVG** (średnia). |

    Wybierz przycisk **Zastosuj**. Następnie ponownie **Dodaj zmienną** i ustaw następujące wartości dla zmiennej trzeciej i końcowej:

    | Parametr | Akcja |
    | --- | --- |
    | **Nazwa** | Wprowadź **piętro**. |
    | **Natur** | Wybierz **kategorii** |
    | **Wartość** | Wybierz z ustawień predefiniowanych: wybierz pozycję **piętro (Double)** . <br /> Uwaga: Ta **wartość** może potrwać kilka minut, jeśli funkcja Azure Time Series Insights Preview zacznie otrzymywać zdarzenia.|
    | **Rodzaj** | <span style="text-decoration: underline">Etykiety</span>  - <span style="text-decoration: underline">wartości</span> <br /> Niższy: 1, 2, 3, 4 <br /> Środek: 5, 6, 7, 8, 9 <br /> Upper: 10, 11, 12, 13, 14, 15 |
    | **Kategoria domyślna** | Wprowadź **nieznany** |

    [![dodać zmiennych typu.](media/v2-update-provision/tsi-add-type-variables.png)](media/v2-update-provision/tsi-add-type-variables.png#lightbox)

    Wybierz przycisk **Zastosuj**.

1. Wybierz pozycję **Zapisz**. Zostaną utworzone i wyświetlone trzy zmienne.

    [![po dodaniu typu, przejrzyj go w widoku model.](media/v2-update-provision/tsi-add-type-and-view.png)](media/v2-update-provision/tsi-add-type-and-view.png#lightbox)

1. Wybierz kartę **hierarchie** . Następnie wybierz pozycję **+ Dodaj**.
   
   W okienku **Edytuj hierarchię** ustaw następujące parametry:

   | Parametr | Akcja |
   | --- | ---|
   | **Nazwa** | Wpisz **Location Hierarchy** (Hierarchia lokalizacji). |
   |**Poziomy**| Wprowadź **kraj** jako nazwę pierwszego poziomu <br> Wybierz pozycję **+ Dodaj poziom** <br> Wprowadź **miasto** dla drugiego poziomu, a następnie wybierz pozycję **+ Dodaj poziom** <br> Wprowadź wartość **konstrukcyjną** jako nazwę trzeciego i końcowego poziomu |

   Wybierz pozycję **Zapisz**.

   [![wyświetlić nową hierarchię w widoku model.](media/v2-update-provision/tsi-add-hierarchy-and-view.png)](media/v2-update-provision/tsi-add-hierarchy-and-view.png#lightbox)

1. Przejdź do **wystąpień**. W obszarze **Akcje** po prawej stronie i wybierz ikonę ołówka, aby zmodyfikować pierwsze wystąpienie o następujące wartości:

    | Parametr | Akcja |
    | --- | --- |
    | **Typ** | Wybierz pozycję **windy**. |
    | **Nazwa** | Wprowadź **windę 1**|
    | **Opis** | Wprowadź **wystąpienie dla wind 1** |

    Przejdź do **pól wystąpień** i wprowadź następujące wartości:

    | Parametr | Akcja |
    | --- | --- |
    | **Hierarchies** | Wybierz **hierarchię lokalizacji** |
    | **Country** | Wprowadź **Stan USA** |
    | **City** | Wprowadź **Seattle** |
    | **Building** | Wprowadź **wskazówkę miejsca** |

    Wybierz pozycję **Zapisz**.

1. Powtórz poprzedni krok z innymi dwoma wystąpieniami przy użyciu następujących wartości:

    **Dla wind 2:**

    | Parametr | Akcja |
    | --- | --- |
    | **Typ** | Wybierz pozycję **windy**. |
    | **Nazwa** | Wprowadź **windę 2**|
    | **Opis** | Wprowadź **wystąpienie dla wind 2** |
    | **Hierarchies** | Wybierz **hierarchię lokalizacji** |
    | **Country** | Wprowadź **Stan USA** |
    | **City** | Wprowadź **Seattle** |
    | **Building** | Wprowadź **centrum nauki Pacyfiku** |

    **Dla windy 3:**

    | Parametr | Akcja |
    | --- | --- |
    | **Typ** | Wybierz pozycję **windy**. |
    | **Nazwa** | Wprowadź **windę 3**|
    | **Opis** | Wprowadź **wystąpienie dla wind 3** |
    | **Hierarchies** | Wybierz **hierarchię lokalizacji** |
    | **Country** | Wprowadź **Stan USA** |
    | **City** | Wprowadź **Nowy Jork** |
    | **Building** | Wprowadź **kompilację stanu Empire** |

    [![wyświetlić zaktualizowane wystąpienia.](media/v2-update-provision/iot-solution-accelerator-instances.png)](media/v2-update-provision/iot-solution-accelerator-instances.png#lightbox)

1. Przejdź z powrotem do karty **Analiza** , aby wyświetlić okienko wykresów. W obszarze **Hierarchia lokalizacji**rozwiń węzeł wszystkie poziomy hierarchii, aby wyświetlić wystąpienia szeregów czasowych:

    [![wyświetlić wszystkie hierarchie w widoku wykresu.](media/v2-update-provision/iot-solution-accelerator-view-hierarchies.png)](media/v2-update-provision/iot-solution-accelerator-view-hierarchies.png#lightbox)

1. W obszarze **centrum nauki pacyficznego**wybierz pozycję przenośnik szeregów czasowych **2**, a następnie wybierz pozycję **Pokaż średnią temperaturę**.

1. Dla tego samego wystąpienia, **Wind 2**, wybierz pozycję **Pokaż piętro**.

    Za pomocą zmiennej kategorii można określić, jak długo winda zajmuje się górną, niższą i środkową podłogą.

    [![wizualizuje windę 2 z hierarchią i danymi.](media/v2-update-provision/iot-solution-accelerator-elevator-two.png)](media/v2-update-provision/iot-solution-accelerator-elevator-two.png#lightbox)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po ukończeniu samouczka Wyczyść utworzone zasoby:

1. W menu po lewej stronie w [Azure Portal](https://portal.azure.com)wybierz pozycję **wszystkie zasoby**, Znajdź Azure Time Series Insights grupę zasobów.
1. Usuń całą grupę zasobów (i wszystkie znajdujące się w niej zasoby), wybierając pozycję **Usuń** lub Usuń osobno każdy zasób.

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:  

* Tworzenie i używanie akceleratora symulacji urządzenia.
* Tworzenie środowiska wersji zapoznawczej usługi Azure Time Series Insights dostępnej w modelu PAYG.
* Podłącz środowisko Azure Time Series Insights w wersji zapoznawczej do centrum IoT Hub.
* Uruchamianie przykładowego akceleratora rozwiązań umożliwiającego strumieniowe przesyłanie danych do środowiska wersji zapoznawczej usługi Azure Time Series Insights.
* Wykonywanie podstawowej analizy danych.
* Definiowanie hierarchii oraz typu modelu szeregów czasowych i kojarzenie ich z wystąpieniami.

Wiesz już, jak utworzyć własne środowisko wersji zapoznawczej usługi Azure Time Series Insights. Teraz możesz dowiedzieć się więcej na temat kluczowych pojęć związanych z usługą Azure Time Series Insights.

Zapoznaj się z konfiguracją magazynu usługi Azure Time Series Insights:

> [!div class="nextstepaction"]
> [Magazyn i ruch przychodzący usługi Azure Time Series Insights w wersji zapoznawczej](./time-series-insights-update-storage-ingress.md)

Dowiedz się więcej o modelach szeregów czasowych:

> [!div class="nextstepaction"]
> [Modelowanie danych w usłudze Azure Time Series Insights w wersji zapoznawczej](./time-series-insights-update-tsm.md)
