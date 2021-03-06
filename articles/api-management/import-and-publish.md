---
title: Importowanie i publikowanie pierwszego interfejsu API na platformie Azure API Management
description: Dowiedz się, jak zaimportować interfejs API specyfikacji OpenAPI do platformy Azure API Management i przetestować interfejs API w Azure Portal.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 02/27/2020
ms.author: apimpm
ms.openlocfilehash: 886063dcf886d79ac960814f20b3789e8e3b6839
ms.sourcegitcommit: 1f738a94b16f61e5dad0b29c98a6d355f724a2c7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/28/2020
ms.locfileid: "78163508"
---
# <a name="import-and-publish-your-first-api"></a>Importowanie i publikowanie pierwszego interfejsu API 

W tym samouczku przedstawiono sposób importowania interfejsu API zaplecza specyfikacji OpenAPI w formacie JSON do usługi Azure API Management. Firma Microsoft udostępnia interfejs API zaplecza i hostuje go na platformie Azure w [https://conferenceapi.azurewebsites.net?format=json](https://conferenceapi.azurewebsites.net?format=json).

Po zaimportowaniu interfejsu API zaplecza do API Management, interfejs API API Management stał się elewacją dla interfejsu API zaplecza. Możesz dostosować elewację do swoich potrzeb w API Management bez dotykania interfejsu API zaplecza. Aby uzyskać więcej informacji, zobacz [Przekształcanie i ochrona interfejsu API](transform-api.md). 

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Importowanie interfejsu API do API Management
> * Testowanie interfejsu API w witrynie Azure Portal

![Nowy interfejs API](./media/api-management-import-and-publish/created-api.png)

## <a name="prerequisites"></a>Wymagania wstępne

- Poznaj [terminologię dotyczącą API Management platformy Azure](api-management-terminology.md).
- [Tworzenie wystąpienia usługi Azure API Management](get-started-create-service-instance.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-api"> </a>Importowanie i publikowanie interfejsu API zaplecza

W tej sekcji przedstawiono sposób importowania i publikowania interfejsu API zaplecza specyfikacji OpenAPI.
 
1. W lewym panelu nawigacyjnym wystąpienia API Management wybierz opcję **interfejsy API** z sekcji **API Management** .
1. Wybierz kafelek **openapi** , a następnie na ekranie podręcznym wybierz pozycję **pełne** .
1. Na ekranie **Tworzenie ze specyfikacją openapi** Użyj wartości z poniższej tabeli, aby utworzyć interfejs API.
   
   Czerwona gwiazdka obok pola w formularzu wskazuje, że pole jest wymagane. Możesz ustawić wartości interfejsu API podczas tworzenia lub później, przechodząc do karty **Ustawienia** . 
   
   ![Tworzenie interfejsu API](./media/api-management-import-and-publish/create-api.png)
   
   |Ustawienie|Wartość|Opis|
   |-------|-----|-----------|
   |**Specyfikacja interfejsu OpenAPI**|*https:\//conferenceapi.azurewebsites.NET? format = JSON*|Usługa implementująca interfejs API. Usługa API Management przekazuje żądania na ten adres.|
   |**Nazwa wyświetlana**|Po wprowadzeniu powyższego adresu URL usługi API Management wypełnia to pole na podstawie kodu JSON.|Nazwa wyświetlana w portalu dla deweloperów.|
   |**Nazwa**|Po wprowadzeniu powyższego adresu URL usługi API Management wypełnia to pole na podstawie kodu JSON.|Unikatowa nazwa interfejsu API.|
   |**Opis**|Po wprowadzeniu powyższego adresu URL usługi API Management wypełnia to pole na podstawie kodu JSON.|Opcjonalny opis interfejsu API.|
   |**Schemat adresu URL**|**HTTPS**|Protokołów, które mogą być używane do uzyskiwania dostępu do interfejsu API.|
   |**Sufiks adresu URL interfejsu API**|*conference*|Sufiks dołączany do podstawowego adresu URL usługi API Management. API Management odróżnia interfejsy API według ich sufiksów, więc sufiks musi być unikatowy dla każdego interfejsu API dla danego wydawcy.|
   |**Produkty**|**Unlimited (nieograniczony)**|Skojarzenie jednego lub więcej interfejsów API. Każde wystąpienie API Management zawiera dwa przykładowe produkty: **Starter** i **nieograniczone**. Interfejs API jest publikowany przez skojarzenie interfejsu API z produktem, **nieograniczoną** w tym przykładzie.<br/>Możesz dołączyć kilka interfejsów API w produkcie i zaoferować je deweloperom za pomocą portalu dla deweloperów. Aby dodać ten interfejs API do innego produktu, wpisz lub wybierz nazwę produktu. Powtórz ten krok, aby dodać interfejs API do wielu produktów. Możesz również dodać interfejsy API do produktów później ze strony **ustawień** .<br/>Przed uzyskaniem dostępu do interfejsu API deweloperzy muszą najpierw zasubskrybować produkt. Podczas subskrybowania uzyskują klucz subskrypcji dobry dla każdego interfejsu API w tym produkcie. <br/>Jeśli utworzono wystąpienie API Management, jesteś już administratorem, więc subskrybujesz każdy produkt w wystąpieniu.|
   |**Tagi**| |Tagi służące do organizowania interfejsów API do wyszukiwania, grupowania lub filtrowania.|
   |**Czy chcesz utworzyć wersję tego interfejsu API?**|Zaznacz lub usuń zaznaczenie|Aby uzyskać więcej informacji na temat przechowywania wersji, zobacz [Publikowanie wielu wersji interfejsu API](api-management-get-started-publish-versions.md).|
   
   > [!NOTE]
   > Aby opublikować interfejs API, musisz skojarzyć go z produktem. Można to zrobić na stronie **Ustawienia** .
   
1. Wybierz pozycję **Utwórz**.

Jeśli masz problemy z importowaniem definicji interfejsu API, zobacz [listę znanych problemów i ograniczeń](api-management-api-import-restrictions.md).

## <a name="test-the-new-api-in-the-azure-portal"></a>Przetestuj nowy interfejs API w Azure Portal

Operacje interfejsu API można wywoływać bezpośrednio z Azure Portal, co zapewnia wygodny sposób wyświetlania i testowania operacji.

1. W lewym panelu nawigacyjnym wystąpienia API Management wybierz opcję **interfejsy API** z sekcji **API Management** , a następnie wybierz pozycję **interfejs API konferencji demonstracyjnej**.
1. Wybierz kartę **test** , a następnie wybierz pozycję **getgłośników**. Na stronie są wyświetlane parametry i **nagłówki** **zapytania** , jeśli istnieją. Wartość **OCP-APIM-Subscription-Key** jest automatycznie wypełniana dla klucza subskrypcji skojarzonego z tym interfejsem API.
1. Wybierz pozycję **Wyślij**.
   
   ![Mapa testowania interfejsu API](./media/api-management-import-and-publish/01-import-first-api-01.png)
   
   Zaplecze odpowiada wartością **200 OK** i pewnymi danymi.

## <a name="next-steps"> </a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Importowanie pierwszego interfejsu API
> * Testowanie interfejsu API w witrynie Azure Portal

Przejdź do następnego samouczka, aby dowiedzieć się, jak utworzyć i opublikować produkt:

> [!div class="nextstepaction"]
> [Tworzenie i publikowanie produktu](api-management-howto-add-products.md)
