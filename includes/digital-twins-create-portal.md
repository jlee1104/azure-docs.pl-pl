---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
ms.topic: include
ms.date: 01/07/2020
ms.custom: include file
ms.openlocfilehash: 9ccdc7a438d1dade534d39dc97a39a3bdae37dc4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75895400"
---
1. Zaloguj się do [Portalu Azure](https://portal.azure.com).

1. Wybierz pasek boczny strony głównej, a następnie **+ Utwórz zasób**. 

   [![Rozwiń pasek boczny domu, a następnie wybierz + Utwórz zasób](./media/create-digital-twins-portal/azure-portal-create-a-resource.png)](./media/create-digital-twins-portal/azure-portal-create-a-resource.png#lightbox)

1. Wyszukaj **pozycję Cyfrowe bliźnięta**i wybierz pozycję Cyfrowe **bliźnięta**. 

   [![Opcje podczas tworzenia nowego wystąpienia usługi Digital Twins](./media/create-digital-twins-portal/azure-portal-create-digital-twins.png)](./media/create-digital-twins-portal/azure-portal-create-digital-twins.png#lightbox)

   Alternatywnie wybierz **Internet rzeczy**i wybierz opcję Cyfrowe bliźnięta **(wersja zapoznawcza)**.

1. Wybierz przycisk **Utwórz**, aby rozpocząć proces wdrażania.

   [![Tworzenie i potwierdzanie wdrożenia zasobu](./media/create-digital-twins-portal/azure-create-and-confirm-resource.png)](./media/create-digital-twins-portal/azure-create-and-confirm-resource.png#lightbox)

1. W okienku **Digital Twins** wprowadź następujące informacje:
   * **Nazwa zasobu**: utwórz unikatową nazwę dla wystąpienia usługi Digital Twins.
   * **Subskrypcja**: wybierz subskrypcję, która ma zostać użyta do utworzenia tego wystąpienia usługi Digital Twins. 
   * **Grupa zasobów**: wybierz lub utwórz [grupę zasobów](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups) dla wystąpienia usługi Digital Twins.
   * **Lokalizacja**: wybierz lokalizację znajdującą się najbliżej Twoich urządzeń.

     [![Okienko usługi Digital Twins z wprowadzonymi informacjami](./media/create-digital-twins-portal/create-digital-twins-param.png)](./media/create-digital-twins-portal/create-digital-twins-param.png#lightbox)

1. Przejrzyj informacje dotyczące usługi Digital Twins, a następnie wybierz pozycję **Utwórz**. Tworzenie wystąpienia usługi Digital Twins może potrwać kilka minut. Postępy możesz monitorować w okienku **Powiadomienia**.

1. Otwórz okienko **Przegląd** wystąpienia usługi Digital Twins. Zwróć uwagę na link w obszarze **Interfejs API zarządzania**. Adres URL **interfejsu API zarządzania** jest sformatowany w następujący sposób: 
   
   ```URL
   https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/swagger
   ```
   
   Ten adres URL prowadzi do dokumentacji interfejsu API REST usługi Azure Digital Twins dotyczącej Twojego wystąpienia. Przeczytaj artykuł [Jak używać struktury Swagger w usłudze Digital Twins](../articles/digital-twins/how-to-use-swagger.md), aby dowiedzieć się, jak odczytać tę dokumentację interfejsu API i korzystać z niej. Skopiuj i zmodyfikuj adres URL **interfejsu API zarządzania** do tego formatu: 
    
   ```URL
   https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/api/v1.0/
   ```
    
   Aplikacja uzyska dostęp do Twojego wystąpienia, używając zmodyfikowanego adresu URL jako podstawowego adresu URL. Skopiuj ten zmodyfikowany adres URL do pliku tymczasowego. Będzie potrzebny w następnej sekcji.

   [![Omówienie interfejsu API zarządzania](./media/create-digital-twins-portal/digital-twins-management-api.png)](./media/create-digital-twins-portal/digital-twins-management-api.png#lightbox)