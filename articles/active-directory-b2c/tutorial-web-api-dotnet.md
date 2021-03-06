---
title: 'Samouczek: Udzielanie dostępu do ASP.NET interfejsu API sieci Web'
titleSuffix: Azure AD B2C
description: Samouczek dotyczący używania usługi Active Directory B2C do chronienia internetowego interfejsu API platformy ASP.NET i wywoływania go z aplikacji internetowej platformy ASP.NET.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 10/14/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 23531bd4c53dc2fc4851a1e4718fca0e9c3bfc1c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "78187427"
---
# <a name="tutorial-grant-access-to-an-aspnet-web-api-using-azure-active-directory-b2c"></a>Samouczek: Udzielanie dostępu do ASP.NET interfejsu API sieci Web przy użyciu usługi Azure Active Directory B2C

W tym samouczku pokazano, jak wywołać chroniony zasób interfejsu API sieci Web w usłudze Azure Active Directory B2C (Azure AD B2C) z aplikacji sieci web ASP.NET.

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Dodawanie aplikacji internetowego interfejsu API
> * Konfigurowanie zakresów dla internetowego interfejsu API
> * Udzielanie uprawnień do internetowego interfejsu API
> * Konfigurowanie przykładu korzystania z aplikacji

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Wykonaj kroki i wymagania wstępne w [samouczku: Włącz uwierzytelnienie w aplikacji sieci web przy użyciu usługi Azure Active Directory B2C](tutorial-web-app-dotnet.md).

## <a name="add-a-web-api-application"></a>Dodawanie aplikacji internetowego interfejsu API

Należy zarejestrować zasoby internetowego interfejsu API w dzierżawie, zanim będzie on mógł akceptować i odpowiadać na żądania chronionych zasobów wysyłane przez aplikacje klienckie przedstawiające token dostępu.

Aby zarejestrować aplikację w dzierżawie usługi Azure AD B2C, można użyć **bieżącego środowiska aplikacji** lub naszego nowego środowiska rejestracji aplikacji ujednoliconej **(w wersji zapoznawczej).** [Dowiedz się więcej na temat nowego środowiska](https://aka.ms/b2cappregintro).

#### <a name="applications"></a>[Aplikacje](#tab/applications/)

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
2. Upewnij się, że używasz katalogu, który zawiera dzierżawę usługi Azure AD B2C, wybierając filtr **subskrypcja katalog +** w górnym menu i wybierając katalog zawierający dzierżawę.
3. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
4. Wybierz **pozycję Aplikacje**, a następnie wybierz pozycję **Dodaj**.
5. Wprowadź nazwę aplikacji. Na przykład *webapi1*.
6. W obszarze **Dołącz aplikację internetową/ interfejs API sieci Web**wybierz pozycję **Tak**.
7. Dla pozycji **Adres URL odpowiedzi** wprowadź punkt końcowy, w którym usługa Azure AD B2C powinna zwracać wszelkie tokeny żądane przez Twoją aplikację. W tym samouczku przykładowa aplikacja jest uruchamiana lokalnie i nasłuchuje na `https://localhost:44332`.
8. Dla pozycji **Identyfikator URI identyfikatora aplikacji** wprowadź identyfikator używany na potrzeby internetowego interfejsu API. Zostanie wygenerowany pełny identyfikator URI łącznie z domeną. Na przykład `https://contosotenant.onmicrosoft.com/api`.
9. Kliknij przycisk **Utwórz**.
10. Na stronie właściwości zapisz identyfikator aplikacji, który będzie używany podczas konfigurowania aplikacji internetowej.

#### <a name="app-registrations-preview"></a>[Rejestracje aplikacji (wersja zapoznawcza)](#tab/app-reg-preview/)

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
1. Wybierz filtr **subskrypcja katalog +** w górnym menu, a następnie wybierz katalog zawierający dzierżawę usługi Azure AD B2C.
1. W menu po lewej stronie wybierz pozycję **Azure AD B2C**. Możesz też wybrać **wszystkie usługi** i wyszukać i wybrać pozycję Azure **AD B2C**.
1. Wybierz **pozycję Rejestracje aplikacji (Wersja zapoznawcza),** a następnie wybierz pozycję **Nowa rejestracja**.
1. Wprowadź **nazwę** aplikacji. Na przykład *webapi1*.
1. W obszarze **Przekierowanie identyfikatora URI**wybierz pozycję **Sieć Web**, a następnie wprowadź punkt końcowy, w którym usługa Azure AD B2C powinna zwracać wszystkie tokeny żądane przez aplikację. W tym samouczku przykładowa aplikacja jest uruchamiana lokalnie i nasłuchuje na `https://localhost:44332`.
1. Wybierz pozycję **Zarejestruj**.
1. Zarejestruj **identyfikator aplikacji (klienta)** do użycia w późniejszym kroku.

* * *

## <a name="configure-scopes"></a>Konfigurowanie zakresów

Zakresy umożliwiają zarządzanie dostępem do chronionych zasobów. Zakresy są używane przez internetowy interfejs API w celu implementowania kontroli dostępu opartej na zakresach. Na przykład użytkownicy internetowego interfejsu API mogą mieć dostęp zarówno do odczytu, jak i zapisu lub tylko dostęp do odczytu. Korzystając z zakresów, w tym samouczku zdefiniujesz uprawnienia do odczytu i zapisu dla internetowego interfejsu API.

[!INCLUDE [active-directory-b2c-scopes](../../includes/active-directory-b2c-scopes.md)]

## <a name="grant-permissions"></a>Udzielenie uprawnień

Aby wywoływać chroniony internetowy interfejs API z aplikacji, należy udzielić aplikacji uprawnień do tego interfejsu. W ramach samouczka wymagań wstępnych w usłudze Azure AD B2C została utworzona aplikacja internetowa o nazwie *webapp1*. Tej aplikacji użyjesz do wywołania internetowego interfejsu API.

[!INCLUDE [active-directory-b2c-permissions-api](../../includes/active-directory-b2c-permissions-api.md)]

Twoja aplikacja została zarejestrowana do wywoływania chronionego internetowego interfejsu API. Użytkownik uwierzytelnia się w usłudze Azure AD B2C, aby korzystać z aplikacji. Aplikacja uzyskuje autoryzację z usługi Azure AD B2C w celu uzyskiwania dostępu do chronionego internetowego interfejsu API.

## <a name="configure-the-sample"></a>Konfigurowanie przykładu

Po zarejestrowaniu internetowego interfejsu API i zdefiniowaniu zakresów konfigurujesz internetowy interfejs API w celu skorzystania ze swojej dzierżawy usługi Azure AD B2C. W tym samouczku skonfigurujesz przykładowy internetowy interfejs API. Przykładowy internetowy interfejs API jest częścią projektu, który pobrano w samouczku wskazanym w wymaganiach wstępnych.

Przykładowe rozwiązanie zawiera dwa projekty:

* **TaskWebApp** — tworzenie i edytowanie listy zadań. W przykładowej aplikacji jest używany przepływ użytkownika dotyczący **tworzenia konta lub logowania** na potrzeby tworzenia kont lub logowania użytkowników.
* **TaskService** — obsługuje następujące funkcje listy zadań: tworzenie, odczytywanie, aktualizowanie i usuwanie. Interfejs API jest chroniony przez usługę Azure AD B2C i wywoływany przez aplikację TaskWebApp.

### <a name="configure-the-web-application"></a>Konfigurowanie aplikacji internetowej

1. Otwórz rozwiązanie **B2C-WebAPI-DotNet** w programie Visual Studio.
1. W projekcie **TaskWebApp** otwórz witrynę **Web.config**.
1. Aby uruchomić interfejs API lokalnie, użyj ustawienia „localhost” dla wartości **api:TaskServiceUrl**. Zmień plik Web.config w następujący sposób:

    ```csharp
    <add key="api:TaskServiceUrl" value="https://localhost:44332/"/>
    ```

1. Skonfiguruj identyfikator URI interfejsu API. Ten identyfikator URI jest używany przez aplikację internetową w celu wysyłania żądań do interfejsu API. Ponadto skonfiguruj żądane uprawnienia.

    ```csharp
    <add key="api:ApiIdentifier" value="https://<Your tenant name>.onmicrosoft.com/api/" />
    <add key="api:ReadScope" value="demo.read" />
    <add key="api:WriteScope" value="demo.write" />
    ```

### <a name="configure-the-web-api"></a>Konfigurowanie internetowego interfejsu API

1. W projekcie **TaskService** otwórz plik **Web.config**.
1. Skonfiguruj interfejs API w celu korzystania z dzierżawy.

    ```csharp
    <add key="ida:AadInstance" value="https://<Your tenant name>.b2clogin.com/{0}/{1}/v2.0/.well-known/openid-configuration" />
    <add key="ida:Tenant" value="<Your tenant name>.onmicrosoft.com" />
    ```

1. Ustaw identyfikator klienta na identyfikator aplikacji zarejestrowanej aplikacji interfejsu API sieci web, *webapi1*.

    ```csharp
    <add key="ida:ClientId" value="<application-ID>"/>
    ```

1. Zaktualizuj ustawienie przepływu użytkownika o nazwę przepływu użytkownika rejestracji i logowania, *B2C_1_signupsignin1*.

    ```csharp
    <add key="ida:SignUpSignInPolicyId" value="B2C_1_signupsignin1" />
    ```

1. Skonfiguruj ustawienie zakresów, aby pasowały do tych utworzonych w portalu.

    ```csharp
    <add key="api:ReadScope" value="demo.read" />
    <add key="api:WriteScope" value="demo.write" />
    ```

## <a name="run-the-sample"></a>Uruchamianie aplikacji przykładowej

Należy uruchomić zarówno projekt **TaskWebApp**, jak i **TaskService**.

1. W Eksploratorze rozwiązań kliknij rozwiązanie prawym przyciskiem myszy i wybierz pozycję **Ustaw projekty startowe...**.
1. Wybierz pozycję **Ustawianie wielu projektów startowych**.
1. Zmień ustawienie **Akcja** dla obu projektów na wartość **Uruchom**.
1. Kliknij **przycisk OK,** aby zapisać konfigurację.
1. Naciśnij klawisz **F5**, aby uruchomić obie aplikacje. Każda aplikacja otwiera się we własnym oknie przeglądarki.
    * `https://localhost:44316/`to aplikacja internetowa.
    * `https://localhost:44332/` to internetowy interfejs API.

1. W aplikacji internetowej wybierz **pozycję Zarejestruj się / zaloguj się,** aby zalogować się do aplikacji internetowej. Użyj konta, które zostało wcześniej utworzone.
1. Po zalogowaniu się wybierz **pozycję Lista zadań do wykonania** i utwórz element listy zadań do wykonania.

Podczas tworzenia elementu listy zadań do wykonania aplikacja internetowa wysyła żądanie do internetowego interfejsu API, aby wygenerować element listy zadań do wykonania. Chroniona aplikacja sieci web wywołuje internetowy interfejs API chroniony przez usługę Azure AD B2C.

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Dodawanie aplikacji internetowego interfejsu API
> * Konfigurowanie zakresów dla internetowego interfejsu API
> * Udzielanie uprawnień do internetowego interfejsu API
> * Konfigurowanie przykładu korzystania z aplikacji

> [!div class="nextstepaction"]
> [Samouczek: Dodawanie dostawców tożsamości do aplikacji w usłudze Azure Active Directory B2C](tutorial-add-identity-providers.md)
