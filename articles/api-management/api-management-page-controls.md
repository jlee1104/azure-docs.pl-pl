---
title: Kontrolki strony API Management platformy Azure | Microsoft Docs
description: Dowiedz się więcej na temat kontrolek strony dostępnych do użycia w szablonach portalu dla deweloperów w usłudze Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: ff608cac9786ebc0d3908ce4d88fb501e621be6c
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79244019"
---
# <a name="azure-api-management-page-controls"></a>Kontrolki strony API Management platformy Azure
Usługa Azure API Management udostępnia następujące kontrolki do użycia w szablonach portalu dla deweloperów.  
  
Aby użyć kontrolki, umieść ją w odpowiedniej lokalizacji w szablonie portalu dla deweloperów. Niektóre kontrolki, takie jak kontrolka [akcje aplikacji](#app-actions) , mają parametry, jak pokazano w następującym przykładzie:  
  
```xml  
<app-actions params="{ appId: '{{app.id}}' }"></app-actions>  
```  
  
Wartości parametrów są przenoszone w ramach modelu danych dla szablonu. W większości przypadków można po prostu wkleić w podanym przykładzie dla każdej kontrolki, aby działała poprawnie. Aby uzyskać więcej informacji na temat wartości parametrów, można wyświetlić sekcję model danych dla każdego szablonu, w którym można użyć kontrolki.  

Aby uzyskać więcej informacji na temat pracy z szablonami, zobacz [How to dostosowywanie portalu deweloperów API Management przy użyciu szablonów](https://azure.microsoft.com/documentation/articles/api-management-developer-portal-templates/).  

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]
  
## <a name="developer-portal-template-page-controls"></a>Kontrolki strony szablonu portalu dla deweloperów  
  
-   [aplikacje — akcje](#app-actions)  
-   [Logowanie Podstawowe](#basic-signin)  
-   [Kontrola stronicowania](#paging-control)  
-   [udostępnia](#providers)  
-   [Kontrolka wyszukiwania](#search-control)  
-   [Utwórz konto](#sign-up)  
-   [subskrypcja — przycisk](#subscribe-button)  
-   [subskrypcja — Anuluj](#subscription-cancel)  
  
##  <a name="app-actions"></a>aplikacje — akcje  
 Formant `app-actions` udostępnia interfejs użytkownika służący do korzystania z aplikacji na stronie profilu użytkownika w portalu dla deweloperów.  
  
 ![Kontrola&#45;akcji aplikacji](./media/api-management-page-controls/APIM-app-actions-control.png "Aplikacja APIM — kontrola akcji")  
  
### <a name="usage"></a>Sposób użycia  
  
```xml  
<app-actions params="{ appId: '{{app.id}}' }"></app-actions>  
```  
  
### <a name="parameters"></a>Parametry  
  
|Parametr|Opis|  
|---------------|-----------------|  
|appId|Identyfikator aplikacji.|  
  
### <a name="developer-portal-templates"></a>Szablony portalu dla deweloperów  
 Kontrolka `app-actions` może być używana w następujących szablonach portalu dla deweloperów:  
  
-   [Aplikacje](api-management-user-profile-templates.md#Applications)  
  
##  <a name="basic-signin"></a>Logowanie Podstawowe  
 Kontrolka `basic-signin` udostępnia formant do zbierania informacji logowania użytkownika na stronie logowania w portalu dla deweloperów.  
  
 ![podstawowa&#45;kontrola logowania](./media/api-management-page-controls/APIM-basic-signin-control.png "APIM — kontrola logowania")  
  
### <a name="usage"></a>Sposób użycia  
  
```xml  
<basic-SignIn></basic-SignIn>  
```  
  
### <a name="parameters"></a>Parametry  
 Brak.  
  
### <a name="developer-portal-templates"></a>Szablony portalu dla deweloperów  
 Kontrolka `basic-signin` może być używana w następujących szablonach portalu dla deweloperów:  
  
-   [Rejestrowanie](api-management-page-templates.md#SignIn)  
  
##  <a name="paging-control"></a>Kontrola stronicowania  
 `paging-control` udostępnia funkcje stronicowania na stronach portalu dla deweloperów, które wyświetlają listę elementów.  
  
 ![Kontrolka stronicowania](./media/api-management-page-controls/APIM-paging-control.png "APIM — formant stronicowania")  
  
### <a name="usage"></a>Sposób użycia  
  
```xml  
<paging-control></paging-control>  
```  
  
### <a name="parameters"></a>Parametry  
 Brak.  
  
### <a name="developer-portal-templates"></a>Szablony portalu dla deweloperów  
 Kontrolka `paging-control` może być używana w następujących szablonach portalu dla deweloperów:  
  
-   [Lista interfejsów API](api-management-api-templates.md#APIList)  
  
-   [Lista problemów](api-management-issue-templates.md#IssueList)  
  
-   [Lista produktów](api-management-product-templates.md#ProductList)  
  
##  <a name="providers"></a>udostępnia  
 Formant `providers` zapewnia kontrolę wyboru dostawców uwierzytelniania na stronie logowania w portalu dla deweloperów.  
  
 ![Sterowanie dostawcami](./media/api-management-page-controls/APIM-providers-control.png "Kontrolka dostawcy APIM")  
  
### <a name="usage"></a>Sposób użycia  
  
```xml  
<providers></providers>  
```  
  
### <a name="parameters"></a>Parametry  
 Brak.  
  
### <a name="developer-portal-templates"></a>Szablony portalu dla deweloperów  
 Kontrolka `providers` może być używana w następujących szablonach portalu dla deweloperów:  
  
-   [Rejestrowanie](api-management-page-templates.md#SignIn)  
  
##  <a name="search-control"></a>Kontrolka wyszukiwania  
 `search-control` udostępnia funkcje wyszukiwania na stronach portalu dla deweloperów, które wyświetlają listę elementów.  
  
 ![Kontrolka wyszukiwania](./media/api-management-page-controls/APIM-search-control.png "APIM — formant wyszukiwania")  
  
### <a name="usage"></a>Sposób użycia  
  
```xml  
<search-control></search-control>  
```  
  
### <a name="parameters"></a>Parametry  
 Brak.  
  
### <a name="developer-portal-templates"></a>Szablony portalu dla deweloperów  
 Kontrolka `search-control` może być używana w następujących szablonach portalu dla deweloperów:  
  
-   [Lista interfejsów API](api-management-api-templates.md#APIList)  
  
-   [Lista produktów](api-management-product-templates.md#ProductList)  
  
##  <a name="sign-up"></a>Utwórz konto  
 Formant `sign-up` zapewnia kontrolę zbierania informacji o profilu użytkownika na stronie tworzenia konta w portalu dla deweloperów.  
  
 ![Kontrola&#45;rejestracji](./media/api-management-page-controls/APIM-sign-up-control.png "APIM — formant rejestracji")  
  
### <a name="usage"></a>Sposób użycia  
  
```xml  
<sign-up></sign-up>  
```  
  
### <a name="parameters"></a>Parametry  
 Brak.  
  
### <a name="developer-portal-templates"></a>Szablony portalu dla deweloperów  
 Kontrolka `sign-up` może być używana w następujących szablonach portalu dla deweloperów:  
  
-   [Tworzenie konta](api-management-page-templates.md#SignUp)  
  
##  <a name="subscribe-button"></a>subskrypcja — przycisk  
 `subscribe-button` zapewnia kontrolę subskrybowania użytkownika w produkcie.  
  
 ![przycisk&#45;subskrybowania — formant](./media/api-management-page-controls/APIM-subscribe-button-control.png "APIM — formant subskrypcji przycisku")  
  
### <a name="usage"></a>Sposób użycia  
  
```xml  
<subscribe-button></subscribe-button>  
```  
  
### <a name="parameters"></a>Parametry  
 Brak.  
  
### <a name="developer-portal-templates"></a>Szablony portalu dla deweloperów  
 Kontrolka `subscribe-button` może być używana w następujących szablonach portalu dla deweloperów:  
  
-   [Iloczyn](api-management-product-templates.md#Product)  
  
##  <a name="subscription-cancel"></a>subskrypcja — Anuluj  
 Formant `subscription-cancel` zapewnia kontrolkę anulowania subskrypcji produktu na stronie profilu użytkownika w portalu dla deweloperów.  
  
 ![Kontrola&#45;anulowania subskrypcji](./media/api-management-page-controls/APIM-subscription-cancel-control.png "Subskrypcja APIM — anulowanie kontroli")  
  
### <a name="usage"></a>Sposób użycia  
  
```xml  
<subscription-cancel params="{ subscriptionId: '{{subscription.id}}', cancelUrl: '{{subscription.cancelUrl}}' }">  
</subscription-cancel>  
  
```  
  
### <a name="parameters"></a>Parametry  
  
|Parametr|Opis|  
|---------------|-----------------|  
|subscriptionId|Identyfikator subskrypcji do anulowania.|  
|cancelUrl|Subskrypcja anuluje adres URL.|  
  
### <a name="developer-portal-templates"></a>Szablony portalu dla deweloperów  
 Kontrolka `subscription-cancel` może być używana w następujących szablonach portalu dla deweloperów:  
  
-   [Iloczyn](api-management-product-templates.md#Product)

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat pracy z szablonami, zobacz [How to dostosowywanie portalu deweloperów API Management przy użyciu szablonów](api-management-developer-portal-templates.md).
