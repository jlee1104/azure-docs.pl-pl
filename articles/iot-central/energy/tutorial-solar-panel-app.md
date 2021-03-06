---
title: 'Samouczek: Tworzenie aplikacji do monitorowania słonecznego za pomocą IoT Central'
description: 'Samouczek: informacje na temat tworzenia aplikacji panelu słonecznego przy użyciu szablonów aplikacji IoT Central platformy Azure.'
author: op-ravi
ms.author: omravi
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: d5ea3d3420cb598693ccaede7ee10d2f8c4fd839
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025779"
---
# <a name="tutorial-create-and-walk-through-the-solar-panel-monitoring-app-template"></a>Samouczek: Tworzenie i przechodzenie przez szablon aplikacji monitorującej panel słoneczny 



Ten samouczek przeprowadzi Cię przez proces tworzenia aplikacji monitorującej panel słoneczny, która obejmuje przykładowy model urządzenia z symulowanymi danymi. Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:


> [!div class="checklist"]
> * Utwórz bezpłatnie aplikację panelu słonecznego
> * Przewodnik po aplikacji
> * Oczyszczanie zasobów


Jeśli nie masz subskrypcji, [Utwórz konto bezpłatnej wersji próbnej](https://azure.microsoft.com/free)

## <a name="prerequisites"></a>Wymagania wstępne
- Brak
- Zalecana jest subskrypcja platformy Azure, ale nie jest wymagana do wypróbowania


## <a name="create-a-solar-panel-monitoring-app"></a>Tworzenie aplikacji do monitorowania paneli słonecznych 

Tę aplikację można utworzyć w trzech prostych krokach:

1. Otwórz [stronę główną usługi Azure IoT Central](https://apps.azureiotcentral.com) i kliknij przycisk **Kompiluj** , aby utworzyć nową aplikację. 

2. Wybierz kartę **energia** , a następnie kliknij pozycję **Utwórz aplikację** w obszarze kafelek **monitorowanie** aplikacji. 

    > [!div class="mx-imgBorder"]
    > ![](media/tutorial-iot-central-solar-panel/solar-panel-build.png) aplikacji kompilacji
  
3. **Utworzenie aplikacji** spowoduje otwarcie **nowej aplikacji** . Wypełnij żądane szczegóły, jak pokazano na poniższej ilustracji:
    * **Nazwa aplikacji**: wybierz nazwę aplikacji IoT Central. 
    * **Adres URL**: Wybierz adres URL IoT Central, platforma sprawdzi swoją unikatowość.
    * **7-dniowa bezpłatna wersja próbna**: Jeśli masz już subskrypcję platformy Azure, zalecane jest ustawienie domyślne. Jeśli nie masz subskrypcji platformy Azure, Zacznij od bezpłatnej wersji próbnej.
    * **Informacje o rozliczeniach**: sama aplikacja jest bezpłatna. Szczegóły katalogu, subskrypcji platformy Azure i regionu są wymagane do aprowizacji zasobów aplikacji.
    * Kliknij przycisk **Utwórz** w dolnej części strony, a Twoja aplikacja zostanie utworzona na minutę lub w ten sposób.
        ![](media/tutorial-iot-central-solar-panel/solar-panel-create-app.png) formularz nowej aplikacji
        
        ![Nowe informacje o rozliczeniu formularza aplikacji](media/tutorial-iot-central-solar-panel/solar-panel-create-app-billinginfo.png)


### <a name="verify-the-application-and-simulated-data"></a>Weryfikowanie aplikacji i danych symulowanych

Nowo utworzona aplikacja panelu słonecznyego jest aplikacją i można ją zmodyfikować w dowolnym momencie. Upewnij się, że aplikacja została wdrożona i działa zgodnie z oczekiwaniami przed jej zmodyfikowaniem.

Aby sprawdzić Tworzenie aplikacji i symulację danych, przejdź do **pulpitu nawigacyjnego**. Jeśli widzisz kafelki z niektórymi danymi, wdrożenie aplikacji zakończyło się pomyślnie. Generowanie danych przez symulację danych może potrwać kilka minut, więc Zapewnij 1-2 minut. 

## <a name="application-walk-through"></a>Przewodnik po aplikacji
Po pomyślnym wdrożeniu szablonu aplikacji jest on dostępny przy użyciu przykładowego urządzenia miernika inteligentnego, modelu urządzenia i pulpitu nawigacyjnego.

Adatum to fikcyjna firma energetyczna, która monitoruje panele słoneczne i zarządza nimi. Na pulpicie nawigacyjnym monitorowania panelu słonecznym widoczne są właściwości, dane i przykładowe polecenia panelu. Umożliwia operatorom i zespołom pomocy technicznej przeprowadzenie aktywnego wykonywania następujących działań przed uwzględnieniem zdarzeń pomocy technicznej:
* Przejrzyj najnowsze informacje o panelu i jego lokalizację instalacji na mapie
* Aktywnie Sprawdź stan panelu i stan połączenia
* Przejrzyj trendy generacji energii i temperatury, aby przechwycić wszystkie nietypowe wzorce
* Śledź łączną generację energii na potrzeby planowania i rozliczeń
* Operacje związane z poleceniami i kontrolkami, takie jak panel Uaktywnij i aktualizują wersję oprogramowania układowego. W szablonie przyciski poleceń pokazują możliwe funkcje i nie wysyłają prawdziwych poleceń.

> [!div class="mx-imgBorder"]
> ![pulpitu nawigacyjnego monitorowania panelu słonecznego](media/tutorial-iot-central-solar-panel/solar-panel-dashboard.png)

### <a name="devices"></a>Urządzenia
Aplikacja jest dostępna z przykładowym urządzeniem panelu słonecznym. Aby wyświetlić szczegóły urządzenia, kliknij kartę **urządzenia** .

> [!div class="mx-imgBorder"]
> ![urządzeń z panelem słonecznym](media/tutorial-iot-central-solar-panel/solar-panel-device.png)


Kliknij link przykładowe urządzenie **SP0123456789** , aby wyświetlić szczegóły urządzenia. Na stronie **Właściwości aktualizacji** można zaktualizować właściwości z możliwością zapisu urządzenia i wizualizować zaktualizowane wartości na pulpicie nawigacyjnym. 

> [!div class="mx-imgBorder"]
> ![właściwości panelu słonecznego](media/tutorial-iot-central-solar-panel/solar-panel-device-properties.png)


### <a name="device-template"></a>Szablon urządzenia
Kliknij kartę **Szablony urządzeń** , aby wyświetlić model urządzenia w panelu. Model zawiera wstępnie zdefiniowane interfejsy dla danych, właściwości, poleceń i widoków.

> [!div class="mx-imgBorder"]
> ](media/tutorial-iot-central-solar-panel/solar-panel-device-templates.png) szablon ![urządzeń z panelem słonecznym


## <a name="clean-up-resources"></a>Oczyszczanie zasobów
Jeśli zdecydujesz, aby nie używać tej aplikacji, Usuń aplikację, wykonując następujące czynności:

1. W okienku po lewej stronie Otwórz kartę Administracja
2. Wybierz pozycję Ustawienia aplikacji, a następnie kliknij przycisk Usuń u dołu strony. 

    > [!div class="mx-imgBorder"]
    > ![usunąć aplikacji](media/tutorial-iot-central-solar-panel/solar-panel-delete-app.png)


## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o architekturze aplikacji panelu słonecznym zapoznaj się z [artykułem koncepcji](https://docs.microsoft.com/azure/iot-central/energy/concept-iot-central-solar-panel-app)
* Utwórz bezpłatnie szablony aplikacji panelu słonecznego: [aplikacja panelu słonecznych](https://apps.azureiotcentral.com/build/new/solar-panel-monitoring)
* Dowiedz się więcej na temat IoT Central, zobacz [omówienie IoT Central](https://docs.microsoft.com/azure/iot-central/)

