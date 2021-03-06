---
title: Opis witryny Marketplace dla modułu Azure IoT Edge | Portal Azure Marketplace
description: Utwórz opis portalu Marketplace dla modułu IoT Edge.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: pabutler
ms.openlocfilehash: 8021b6fe40e006a95b3eeff1031606c38cc47b87
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76721629"
---
# <a name="iot-edge-module-marketplace-tab"></a>Karta Marketplace modułu IoT Edge Portal

Na karcie **Marketplace** na stronie **Nowa oferta** można zapewnić potencjalnym klientom potencjalną obsługę marketingową, sprzedażową i informacje prawne oraz umowy oraz zarządzać klientami wygenerowanymi w portalu Marketplace. Ten długi formularz jest podzielony na cztery sekcje: **Omówienie**, **artefakty marketingowe**, **Zarządzanie potencjalnymi klientami**i warunki **prawne**.


## <a name="overview"></a>Omówienie

W tej sekcji wprowadzasz ogólne informacje o ofercie portalu Azure Marketplace.  Gwiazdka (*) dołączona do nazwy pola wskazuje, że jest to wymagane.

![Sekcja Przegląd karty Marketplace w nowej formie dla modułów IoT Edge](./media/iot-edge-module-marketplace-tab-overview.png)

W poniższej tabeli opisano przeznaczenie i zawartość tych pól. Wymagane pola są indicted przez gwiazdkę (*).

|  **Pole**                |     **Opis**                                                          |
|  ---------                |     ---------------                                                          |
| **\* tytułu**                 | Tytuł oferty. Zostanie ona wyświetlona w widocznym miejscu w portalu Marketplace.  Maksymalna długość 50 znaków. <!--ADD PICTURE IN ACTION-->|
| **\* podsumowania**               | Krótkie podsumowanie oferty. Maksymalna długość 100 znaków. <!--ADD PICTURE IN ACTION-->|
| **Długi\* podsumowujący**          | Dłuższe podsumowanie oferty (chociaż może być takie samo, jak w przypadku **podsumowania**).  Maksymalna długość 256 znaków. <!--ADD PICTURE IN ACTION-->|
| **\* opisu**           | Opis oferty.  Maksymalna długość 3000 znaków, obsługuje proste formatowanie HTML.<br/> W dolnej części musi znajdować się akapit o *minimalnych wymaganiach dotyczących sprzętu* . Na przykład: <br/> <p><u>Minimalne wymagania sprzętowe:</u> Linux x64 i arm32 OS, 1 GB pamięci RAM, 500 MB magazynu</p>
| **\* identyfikatora marketingu**  | Unikatowy adres URL do skojarzenia z ofertą zwykle zawiera nazwę organizacji i rozwiązania, maksymalną długość 50 znaków.  Na przykład: <br/> `https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sampleIoTEdgeModule`  |
| **Podgląd identyfikatorów subskrypcji** | Dodaj jeden do 100 identyfikatorów subskrypcji dla recenzentów. Te białe subskrypcje będą miały dostęp do oferty po jej opublikowaniu. |
| **Przydatne linki**          | Można z nich najlepiej skojarzyć wiele wybranych kategorii branżowych i technicznych.  Maksymalnie 10 dozwolonych. Upewnij się, że dodano co najmniej jeden link do dokumentacji i jeden link do zgodnych urządzeń IoT Edge z [wykazu urządzeń usługi Azure IoT](https://catalog.azureiotsolutions.com/). |
| **Sugerowane kategorie\*** | Wybierz maksymalnie pięć kategorii. Zostaną one wyświetlone na stronie szczegółów produktu. Na stronach przeglądania wszystkie moduły IoT Edge są wyświetlane w kategorii *moduł Internet rzeczy \> IoT Edge* .|
|  |  |


### <a name="offer-example"></a>Przykład oferty

 W poniższych przykładach pokazano, jak pola **tytuł**oferty, **Podsumowanie**, **Opis**, **logo**i **zrzuty ekranu** pojawiają się w różnych widokach.

 
#### <a name="on-the-azure-marketplace-website"></a>W witrynie sieci Web portalu Azure Marketplace:

- Podczas przeglądania oferty:

    ![Jak są wyświetlane oferty z witryny Marketplace w witrynie sieci Web portalu Azure Marketplace — przeglądanie](./media/iot-edge-module-ampdotcom-card.png)

- Podczas przeglądania szczegółów oferty:

    ![Jak moduł IoT Edge jest wyświetlany podczas wyszukiwania szczegółów produktu w witrynie sieci Web](./media/iot-edge-module-ampdotcom-pdp.png)


#### <a name="on-the-azure-portal-website"></a>W witrynie Azure Portal:

- Podczas przeglądania oferty:

    ![Jak moduł IoT Edge jest wyświetlany podczas przeglądania Azure Portal #1](./media/iot-edge-module-portal-browse.png)

    ![Jak moduł IoT Edge jest wyświetlany podczas przeglądania Azure Portal #2](./media/iot-edge-module-portal-product-picker.png)

- Podczas wyszukiwania oferty:

    ![Jak moduł IoT Edge jest wyświetlany podczas wyszukiwania Azure Portal](./media/iot-edge-module-portal-search.png)

- Podczas przeglądania szczegółów oferty:

    ![Jak moduł IoT Edge jest wyświetlany podczas wyszukiwania szczegółów produktu w portalu](./media/iot-edge-module-portal-pdp.png)


## <a name="marketing-artifacts"></a>Artefakty marketingowe

Ta sekcja zawiera następujące podsekcje: **logo**, **zrzut ekranu**i **wideo**. 

>[!Note]
>Logo są jedynymi wymaganymi artefaktami marketingowymi, ale wszystkie są zdecydowanie zalecane w przypadku najlepszych odwołań klientów.

![Sekcja dotycząca artefaktów marketingowych na karcie Marketplace w nowej postaci dla maszyn wirtualnych](./media/publishvm_009.png)

|  **Pole**                |     **Opis**                                                          |
|  ---------                |     ---------------                                                          |
| *Graficznych*  | Zobacz poprzednie przechwycenia ekranu, aby zobaczyć, jak i gdzie będą używane logo.  |
| **Małe\***                 | Format PNG 40x40 pikseli                                                     |
| **Średni\***                | Format PNG 90x90 pikseli                                                     |
| **Duże\***                 | Format PNG 115x115 pikseli                                                  |
| **Szerokie\***                  | Format PNG 255x115 pikseli                                                   |
| **Hero**                  | Format PNG 815x290 pikseli.  Opcjonalne, ale nie można usunąć ikony Hero. |
| *Zrzutów*  | Zrzuty ekranu są wyświetlane na stronie ze szczegółowymi informacjami o produkcie. Są one dobrym sposobem na wizualne komunikowanie się modułu IoT Edge i sposobu jego działania. Na przykład można wyświetlić diagramy architektury lub ilustracje przypadków użycia. Opcjonalne, ale maksymalnie pięć zrzutów ekranu na jednostkę SKU. |
| **Nazwa**                  | Nazwa lub tytuł. Maksymalna długość 100 znaków.                             |
| **Obraz**                 | Obraz przechwytywania ekranu, format 533x324 pikseli                               |
| *Wideo*  | Filmy wideo są wyświetlane na stronie szczegółów produktu. Są one dobrym sposobem na wizualne komunikowanie się modułu IoT Edge i sposobu jego działania. |
| **Nazwa**                  | Nazwa lub tytuł. Maksymalna długość 100 znaków.                             |
| **Link**                  | Adres URL wideo, hostowany w serwisie YouTube lub Vimeo                                        |
| **Miniaturk**             | Format PNG 533x324 pikseli                                                     |
|  |  |


### <a name="logo-guidelines"></a>Wytyczne dotyczące logo

<!-- It seems like this section could be better located in some common area, maybe an AMP Marketing/Design section 
+1 this should all be in a common area and referenced from here to that location.-->

Wszystkie logo przekazane do portal Cloud Partner powinny postępować zgodnie z wytycznymi:

*  Projekt platformy Azure ma prostą paletę kolorów. Zachowaj niewielką liczbę kolorów podstawowych i pomocniczych w logo.
*  Kolory motywu w witrynie Azure Portal są białe i czarne. Należy unikać używania tych kolorów jako koloru tła logo. Użyj koloru, który spowoduje, że logo zostanie uwidocznione w Azure Portal. Zalecamy proste kolory podstawowe. Jeśli używasz przezroczystego tła, upewnij się, że logo/tekst nie są białe, czarne lub niebieskie.
*  Nie używaj tła gradientu w logo.
*  Unikaj umieszczania tekstu — nawet nazwy firmy lub marki — w logo. Wygląd i zachowanie logo powinny być "płaskie" i nie powinno się unikać gradientów.
*  Nie rozciągaj logo.


#### <a name="hero-logo"></a>Logo Duży obraz

Logo Hero jest opcjonalne.

>[!Important]
>Po przekazaniu logo Hero nie można go usunąć.

W przypadku logo Hero należy stosować następujące wytyczne: 

*  Czarne, białe i przezroczyste tła są niedozwolone.
*  Należy unikać używania żadnego jasnego koloru jako tła logo.  Nazwa wyświetlana wydawcy, tytuł planu i długi podsumowanie oferty są wyświetlane w białym kolorze czcionki i muszą zostać wystawione w tle.
*  Unikaj używania większości tekstu podczas projektowania logo. Nazwa wydawcy, tytuł planu, podsumowanie oferty długiej i przycisk Utwórz są osadzane programowo w logo, gdy zostanie wyświetlona. 
* Uwzględnij nieużywane prostokątne miejsce po prawej stronie logo Hero. Puste miejsce to 415x100 pikseli i przesunięcie od lewej do 370 pikseli.  

<!-- P2: would be nice to also have an picture int he context of iot edge of the hero image
As an example, the following hero icon is for the Azure Container Service.

![Example hero icon for Azure Container Service](./media/publishvm_010.png)
-->


## <a name="lead-management"></a>Zarządzanie potencjalnymi klientami

Ta sekcja umożliwia skonfigurowanie opcji zbierania potencjalnych klientów wygenerowanych z ofert portalu Azure Marketplace. Z listy rozwijanej można wybrać następujące opcje magazynu.

* **Brak** — nie są zbierane informacje o kliencie domyślnym.
* Tabela Azure — zapisywana w tabeli platformy Azure określona przez parametry połączenia.
* Dynamics CRM Online — Zapisano w wystąpieniu usługi [Microsoft Dynamics 365 online](https://dynamics.microsoft.com/) , określonym przez adres URL i poświadczenia uwierzytelniania.
* Punkt końcowy HTTPS — zapisany w określonym punkcie końcowym HTTPS jako ładunek JSON.
* Program Market — zapisany w określonym wystąpieniu programu [Marketo](https://www.marketo.com/) , określony przez identyfikator serwera, identyfikator Munchkin i identyfikator formularza.
* Salesforce — zapisywana w bazie danych [usług Salesforce](https://www.salesforce.com/) , określona przez identyfikator obiektu.

Po pomyślnym opublikowaniu oferty zostanie zweryfikowane połączenie z liderem i klient testowy zostanie automatycznie wysłany do skonfigurowanego miejsca docelowego. 

>[!Note]
>Informacje o potencjalnych klientach powinny być ciągle zarządzane, a te ustawienia należy natychmiast zaktualizować, gdy zostaną wprowadzone zmiany w architekturze zarządzania klientami.

<!-- there is missing some marketing imagess
1. inside azure portal the wide logo/regular logo
2. inside azure portal the top curation section
3. amp.com the pricing tab
4. amp.com the tile -->


## <a name="legal"></a>Prawidłowe

Ta sekcja pozwala udostępnić dwa dokumenty prawne wymagane dla każdej oferty: zasady ochrony prywatności i warunki użytkowania.

|  **Pole**                    |     **Opis**                                                          |
|  ---------                    |     ---------------                                                          |
| **Adres URL zasad ochrony prywatności\***      | Adres URL dla opublikowanych zasad ochrony prywatności                                            |
| **Użyj standardowego Constract\***  | Czy używać standardowego szablonu umowy Microsoft.  Aby uzyskać więcej informacji, zobacz temat [Enterprise Contract](https://docs.microsoft.com/azure/marketplace/standard-contract).   |
| **Warunki użytkowania\***            | *Warunki użytkowania* jako wbudowany prosty kod HTML lub łącze do strony ogłoszonych warunków użytkowania     |
|  |  |


## <a name="next-steps"></a>Następne kroki

Skorzystaj z karty [Pomoc techniczna](./cpp-support-tab.md) , aby udostępnić zasoby techniczne i pomoc techniczną dla swojej oferty.
