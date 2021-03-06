---
title: Samouczek — dodawanie domeny niestandardowej do konfiguracji drzwi frontowych platformy Azure
description: W tym samouczku dowiesz się, jak dodać domenę niestandardową do usługi Azure Front Door.
services: frontdoor
documentationcenter: ''
author: sharad4u
editor: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: 5ffa85a2a681bfd064bfeade77d9ae7b85b1f723
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79471765"
---
# <a name="tutorial-add-a-custom-domain-to-your-front-door"></a>Samouczek: dodawanie domeny niestandardowej do usługi Front Door
W tym Samouczku opisano, jak dodać domenę niestandardową do usługi Front Door. Gdy używasz usługi Azure Front Door do dostarczania aplikacji, domena niestandardowa jest niezbędna, jeśli chcesz, aby twoja nazwa domeny była widoczna w żądaniu użytkownika końcowego. Widoczna nazwa domeny może być wygodna dla klientów i przydatna dla celów związanych ze znakowaniem.

Po utworzeniu usługi Front Door domyślny host frontonu, który jest domeną podrzędną domeny `azurefd.net`, zostaje dołączony do adresu URL na potrzeby domyślnego dostarczania zawartości usługi Front Door z wewnętrznej bazy danych (na przykład https:\//contoso.azurefd.net/activeusers.htm). Dla Twojej wygody usługa Azure Front Door udostępnia opcję kojarzenia domeny niestandardowej z hostem domyślnym. Ta opcja umożliwia dostarczanie zawartości przy użyciu domeny niestandardowej w adresie URL zamiast korzystania z nazwy domeny należącej do usługi Front Door (np. https:\//www.contoso.com/photo.png). 

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> - Tworzenie rekordu DNS CNAME.
> - Kojarzenie domeny niestandardowej z usługą Front Door.
> - Weryfikowanie domeny niestandardowej.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

> [!NOTE]
> Drzwi frontowe **nie** obsługuje domen niestandardowych ze znakami [punycode.](https://en.wikipedia.org/wiki/Punycode) 

## <a name="prerequisites"></a>Wymagania wstępne

Przed wykonaniem kroków opisanych w tym samouczku należy utworzyć usługę Front Door. Aby uzyskać więcej informacji, zobacz temat [Quickstart: Create a Front Door for a highly available global web application](quickstart-create-front-door.md) (Szybki start: tworzenie usługi Front Door na potrzeby łatwo dostępnej globalnej aplikacji internetowej).

Jeśli nie masz jeszcze domeny niestandardowej, musisz ją najpierw kupić od dostawcy domeny. Zobacz na przykład temat [Buy a custom domain name (Kupowanie nazwy domeny niestandardowej)](https://docs.microsoft.com/azure/app-service/manage-custom-dns-buy-domain).

Jeśli używasz platformy Azure do hostowania [domen DNS](https://docs.microsoft.com/azure/dns/dns-overview), musisz delegować system nazw domen (DNS) dostawcy domen do usługi Azure DNS. Aby uzyskać więcej informacji, zobacz [Delegowanie domeny do usługi Azure DNS](https://docs.microsoft.com/azure/dns/dns-delegate-domain-azure-dns). W przeciwnym razie, jeśli używasz dostawcy domeny do obsługi domeny DNS, przejdź do sekcji [Tworzenie rekordu DNS CNAME](#create-a-cname-dns-record).


## <a name="create-a-cname-dns-record"></a>Tworzenie rekordu DNS CNAME

Przed rozpoczęciem używania domeny niestandardowej z usługą Front Door należy najpierw utworzyć rekord nazwy kanonicznej (CNAME) z pomocą dostawcy domeny, aby wskazać na domyślny host frontonu usługi Front Door (powiedzmy contoso.azurefd.net). Rekord CNAME jest typem rekordu DNS, który mapuje nazwę domeny źródłowej na nazwę domeny docelowej. W przypadku drzwi frontowych platformy Azure nazwa domeny źródłowej jest nazwą domeny niestandardowej, a nazwa domeny docelowej jest domyślną nazwą hosta drzwi frontowych. Po weryfikacji przez drzwi wejściowe rekordu CNAME ruch adresowany do źródłowej domeny niestandardowej (na przykład www\.contoso.com) jest kierowany do określonego docelowego domyślnego hosta frontendu drzwi frontowych (takiego jak contoso.azurefd.net). 

Domena niestandardowa i jej poddomena mogą być skojarzone tylko z jednymi drzwiami frontowymi naraz. Można jednak użyć różnych poddomen z tej samej domeny niestandardowej dla różnych drzwi przednich przy użyciu wielu rekordów CNAME. Można również mapować domenę niestandardową z różnymi poddomenami na te same drzwi frontowe.


## <a name="map-the-temporary-afdverify-subdomain"></a>Mapowanie tymczasowej poddomeny afdverify

W przypadku mapowania istniejącej domeny, która znajduje się w środowisku produkcyjnym, należy wziąć pod uwagę zagadnienia specjalne. W przypadku rejestrowania domeny niestandardowej w witrynie Azure Portal może wystąpić krótki przestój domeny. Aby uniknąć przerw w ruchu sieci web, najpierw zamapuj domenę niestandardową na domyślny host frontendu drzwi frontowych z poddomeną afdverify platformy Azure, aby utworzyć tymczasowe mapowanie CNAME. Przy użyciu tej metody użytkownicy mogą uzyskiwać dostęp do domeny bez zakłóceń w trakcie mapowania DNS.

W przeciwnym razie, jeśli używasz domeny niestandardowej po raz pierwszy i nie ma w niej żadnego ruchu związanego ze środowiskiem produkcyjnym, domenę niestandardową można mapować bezpośrednio do usługi Front Door. Przejdź do sekcji [Mapowanie trwałej domeny niestandardowej](#map-the-permanent-custom-domain).

Aby utworzyć rekord CNAME z poziomu domeny podrzędnej afdverify:

1. Zaloguj się w witrynie internetowej dostawcy domeny niestandardowej.

2. Znajdź stronę zarządzania rekordami DNS, sprawdzając dokumentację dostawcy lub wyszukując obszary witryny internetowej z etykietą **Nazwa domeny**, **DNS** lub **Zarządzanie serwerem nazw**. 

3. Utwórz wpis rekordu CNAME dla domeny niestandardowej, a następnie wypełnij pola, tak jak pokazano w poniższej tabeli (nazwy pól mogą być inne):

    | Element źródłowy                    | Typ  | Element docelowy                     |
    |---------------------------|-------|---------------------------------|
    | afdverify.www.contoso.com | CNAME | afdverify.contoso.azurefd.net |

    - Źródło: Wprowadź niestandardową nazwę domeny, w tym poddomenę afdverify, w następującym formacie: afdverify. _niestandardowa&gt;nazwa domeny . &lt;_ Na przykład afdverify.www.contoso.com.

    - Typ: wprowadź wartość *CNAME*.

    - Miejsce docelowe: wprowadź domyślny host frontendu drzwi frontowych, w tym poddomenę afdverify, w następującym formacie: afdverify. nazwa punktu końcowego .azurefd.net. _ &lt;&gt;_ Na przykład afdverify.contoso.azurefd.net.

4. Zapisz zmiany.

Na przykład procedura rejestratora domen GoDaddy wygląda następująco:

1. Zaloguj się i wybierz domenę niestandardową do użycia.

2. W sekcji Domeny wybierz pozycję **Zarządzaj wszystkimi**, a następnie wybierz kolejno pozycje **DNS** | **Zarządzaj strefami**.

3. W obszarze **Nazwa domeny** wprowadź domenę niestandardową, a następnie wybierz pozycję **Wyszukaj**.

4. Na stronie **Zarządzanie DNS** wybierz pozycję **Dodaj**, a następnie wybierz pozycję **CNAME** na liście **Typ**.

5. Wypełnij następujące pola wpisu CNAME:

    - Typ: pozostaw wybraną pozycję *CNAME*.

    - Host: wprowadź domenę podrzędną domeny niestandardowej do użycia, z uwzględnieniem nazwy domeny podrzędnej afdverify. Na przykład afdverify.www.

    - Wskazuje na: wprowadź nazwę hosta domyślnego hosta frontonu usługi Front Door, z uwzględnieniem nazwy domeny podrzędnej afdverify. Na przykład afdverify.contoso.azurefd.net. 

    - TTL: Pozostaw *jedną godzinę* zaznaczoną.

6. Wybierz **pozycję Zapisz**.
 
    Wpis CNAME jest dodawany do tabeli rekordów DNS.


## <a name="associate-the-custom-domain-with-your-front-door"></a>Kojarzenie domeny niestandardowej z usługą Front Door

Po zarejestrowaniu domeny niestandardowej można dodać ją do usługi Front Door.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/) i przejdź do usługi Front Door zawierającej hosta frontonu, który ma być mapowany na domenę niestandardową.
    
2. Na stronie **projektanta usługi Front Door** kliknij „+”, aby dodać domenę niestandardową.
    
3. Wybierz opcję **Domena niestandardowa**. 

4. Dla opcji **Host frontonu** wstępnie wypełniony jest host frontonu do użycia jako domena docelowa rekordu CNAME, utworzony na podstawie usługi Front Door: *&lt;domyślna nazwa hosta&gt;*.azurefd.net. Nie można zmienić tej nazwy.

5. W obszarze **Niestandardowa nazwa hosta** wprowadź domenę niestandardową (razem z poddomeną), która ma być używana jako domena źródłowa rekordu CNAME. Na przykład\.www contoso.com lub cdn.contoso.com. Nie używaj nazwy domeny podrzędnej afdverify.

6. Wybierz pozycję **Dodaj**.

   Platforma Azure sprawdzi, czy dla wprowadzonej nazwy domeny niestandardowej istnieje rekord CNAME. Jeśli rekord CNAME jest poprawny, domena niestandardowa zostanie zweryfikowana.

>[!WARNING]
> **Musisz** upewnić się, że każdy z hostów frontonu (z uwzględnieniem domen niestandardowych) w usłudze Front Door ma regułę rozsyłania ze skojarzoną z nim domyślną ścieżką („/\*”). Oznacza to, że wśród wszystkich reguł rozsyłania musi być co najmniej jedna reguła rozsyłania dla każdego z hostów frontonu zdefiniowanych w ścieżce domyślnej („/\*”). Jeśli tak nie będzie, ruch użytkowników końcowych może nie być kierowany prawidłowo.

## <a name="verify-the-custom-domain"></a>Weryfikowanie domeny niestandardowej

Po zakończeniu rejestracji domeny niestandardowej sprawdź, czy odwołuje się ona do domyślnego hosta frontonu usługi Front Door.
 
W przeglądarce przejdź do adresu pliku przy użyciu domeny niestandardowej. Jeśli na przykład domena niestandardowa to robotics.contoso.com, adres URL buforowanego pliku powinien być podobny do następującego: http:\//robotics.contoso.com/my-public-container/my-file.jpg. Sprawdź, czy wynik jest taki sam, jak podczas uzyskiwania dostępu do drzwi wejściowych bezpośrednio na * &lt;hosta drzwi yjdące&gt;*.azurefd.net.


## <a name="map-the-permanent-custom-domain"></a>Mapowanie trwałej domeny niestandardowej

Jeśli sprawdzono, że domena podrzędna afdverify została pomyślnie mapowana do usługi Front Door (lub jeśli używasz nowej domeny niestandardowej, która nie znajduje się w środowisku produkcyjnym), można mapować domenę niestandardową bezpośrednio do domyślnego hosta frontonu usługi Front Door.

Aby utworzyć rekord CNAME dla domeny niestandardowej:

1. Zaloguj się w witrynie internetowej dostawcy domeny niestandardowej.

2. Znajdź stronę do zarządzania rekordami DNS, korzystając z dokumentacji dostawcy lub wyszukując obszary witryny sieci Web oznaczone jako **Domain Name**, **DNS**lub Name **Server Management**. 

3. Utwórz wpis rekordu CNAME dla domeny niestandardowej, a następnie wypełnij pola, tak jak pokazano w poniższej tabeli (nazwy pól mogą być inne):

    | Element źródłowy          | Typ  | Element docelowy           |
    |-----------------|-------|-----------------------|
    | <www.contoso.com> | CNAME | contoso.azurefd.net |

   - Źródło: Wprowadź niestandardową nazwę domeny\.(na przykład www contoso.com).

   - Typ: wprowadź wartość *CNAME*.

   - Element docelowy: wprowadź domyślny host frontonu usługi Front Door. Musi być w następującym formacie:_&lt;nazwa hosta&gt;_.azurefd.net. Na przykład contoso.azurefd.net.

4. Zapisz zmiany.

5. Jeśli wcześniej utworzono rekord CNAME tymczasowej domeny podrzędnej afdverify, usuń go. 

6. Jeśli używasz tej domeny niestandardowej w środowisku produkcyjnym po raz pierwszy, wykonaj kroki z sekcji [Kojarzenie domeny niestandardowej z usługą Front Door](#associate-the-custom-domain-with-your-front-door) i [Weryfikowanie domeny niestandardowej](#verify-the-custom-domain).

Na przykład procedura rejestratora domen GoDaddy wygląda następująco:

1. Zaloguj się i wybierz domenę niestandardową do użycia.

2. W sekcji Domeny wybierz pozycję **Zarządzaj wszystkimi**, a następnie wybierz kolejno pozycje **DNS** | **Zarządzaj strefami**.

3. W obszarze **Nazwa domeny** wprowadź domenę niestandardową, a następnie wybierz pozycję **Wyszukaj**.

4. Na stronie **Zarządzanie DNS** wybierz pozycję **Dodaj**, a następnie wybierz pozycję **CNAME** na liście **Typ**.

5. Wypełnij pola wpisu CNAME:

    - Typ: pozostaw wybraną pozycję *CNAME*.

    - Host: wprowadź domenę podrzędną domeny niestandardowej do użycia. Na przykład www lub profil.

    - Wskazuje na: wprowadź domyślną nazwę hosta usługi Front Door. Na przykład contoso.azurefd.net. 

    - TTL: Pozostaw *jedną godzinę* zaznaczoną.

6. Wybierz **pozycję Zapisz**.
 
    Wpis CNAME jest dodawany do tabeli rekordów DNS.

7. Jeśli masz rekord CNAME domeny afdverify, wybierz ikonę ołówka obok niego, a następnie wybierz ikonę kosza.

8. Wybierz pozycję **Usuń**, aby usunąć rekord CNAME.


## <a name="clean-up-resources"></a>Oczyszczanie zasobów

W poprzednich krokach dodano domenę niestandardową do usługi Front Door. Jeśli nie chcesz skojarzyć usługi Front Door z domeną niestandardową, możesz usunąć domenę niestandardową, wykonując następujące kroki:
 
1. W projektancie usługi Front Door wybierz domenę niestandardową, która ma zostać usunięta.

2. Kliknij przycisk Usuń z menu kontekstowego dla domeny niestandardowej.  

   Skojarzenie domeny niestandardowej z punktem końcowym zostanie usunięte.


## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> - Tworzenie rekordu DNS CNAME.
> - Kojarzenie domeny niestandardowej z usługą Front Door.
> - Weryfikowanie domeny niestandardowej.