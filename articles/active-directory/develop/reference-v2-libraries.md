---
title: Biblioteki uwierzytelniania platformy tożsamości firmy Microsoft | Dokumenty firmy Microsoft
description: Zgodne biblioteki klienckie i biblioteki oprogramowania pośredniczącego serwera, wraz z powiązaną biblioteką, źródłem i przykładowymi łączami dla punktu końcowego platformy tożsamości firmy Microsoft.
services: active-directory
documentationcenter: ''
author: negoe
manager: CelesteDG
editor: ''
ms.assetid: 19cec615-e51f-4141-9f8c-aaf38ff9f746
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/25/2019
ms.author: negoe
ms.reviewer: jmprieur, saeeda
ms.custom: aaddev
ms.openlocfilehash: 9d2df175fa9d1ed33eb17ae85e01a4fd7a24e728
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77611940"
---
# <a name="microsoft-identity-platform-authentication-libraries"></a>Biblioteki uwierzytelniania platformy tożsamości firmy Microsoft

[Punkt końcowy platformy tożsamości firmy Microsoft](active-directory-v2-compare.md) obsługuje standardowe protokoły OAuth 2.0 i OpenID Connect 1.0. Biblioteka uwierzytelniania firmy Microsoft (MSAL) została zaprojektowana do pracy z punktem końcowym platformy tożsamości firmy Microsoft. Można również użyć bibliotek open source, które obsługują OAuth 2.0 i OpenID Connect 1.0.

Zaleca się używanie bibliotek napisanych przez ekspertów domeny protokołu, którzy stosują metodologię cyklu rozwoju zabezpieczeń (SDL). Takie metodologie obejmują [ten, który Microsoft następuje][Microsoft-SDL]. Jeśli ręcznie kod dla protokołów, należy postępować zgodnie z metodologią, takich jak Microsoft SDL. Należy zwrócić szczególną uwagę na kwestie bezpieczeństwa w specyfikacjach standardów dla każdego protokołu.

> [!NOTE]
> Szukasz biblioteki uwierzytelniania usługi Azure Active Directory (ADAL)? Zapoznaj się z [przewodnikiem po bibliotece ADAL](../azuread-dev/active-directory-authentication-libraries.md).

## <a name="types-of-libraries"></a>Typy bibliotek

Punkt końcowy platformy tożsamości firmy Microsoft współpracuje z dwoma typami bibliotek:

* **Biblioteki klientów:** Klienci natywni i serwery używają bibliotek klienckich do uzyskiwania tokenów dostępu do wywoływania zasobu, takiego jak Microsoft Graph.
* **Biblioteki oprogramowania pośredniczącego serwera:** Aplikacje sieci Web używają bibliotek oprogramowania pośredniczącego serwera do logowania się użytkownika. Interfejsy API sieci Web używają bibliotek oprogramowania pośredniczącego serwera do sprawdzania poprawności tokenów wysyłanych przez klientów natywnych lub przez inne serwery.

## <a name="library-support"></a>Obsługa biblioteki

Biblioteki są dostępne w dwóch kategoriach pomocy technicznej:

* **Microsoft obsługiwane:** Firma Microsoft zapewnia poprawki dla tych bibliotek i zrobił SDL należytej staranności na tych bibliotek.
* **Zgodność**: Firma Microsoft przetestowała te biblioteki w podstawowych scenariuszach i potwierdziła, że współpracuje z punktem końcowym platformy tożsamości firmy Microsoft. Firma Microsoft nie udostępnia poprawek dla tych bibliotek i nie dokonała przeglądu tych bibliotek. Problemy i żądania funkcji powinny być kierowane do projektu open source biblioteki.

Aby uzyskać listę bibliotek, które współpracują z punktem końcowym platformy tożsamości firmy Microsoft, zobacz następujące sekcje.

## <a name="microsoft-supported-client-libraries"></a>Biblioteki klienckie obsługiwane przez firmę Microsoft

Użyj bibliotek uwierzytelniania klienta, aby uzyskać token do wywoływania chronionego interfejsu API sieci web.

| Platforma | Biblioteka | Pobierz | Kod źródłowy | Sample | Tematy pomocy | Doc koncepcyjny | Harmonogram działania |
| --- | --- | --- | --- | --- | --- | --- | --- |
| ![JavaScript](media/sample-v2-code/logo_js.png) | MSAL.js  | [Npm](https://www.npmjs.com/package/msal) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/README.md) |  [Aplikacja jednostronicowa](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2) | [Tematy pomocy](https://azuread.github.io/microsoft-authentication-library-for-js/docs/msal/) | [Dokumenty koncepcyjne](msal-overview.md)| [Harmonogram działania](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki#roadmap)
|![Angular JS](media/sample-v2-code/logo_angular.png) | MSAL Kątowy JS | [Npm](https://www.npmjs.com/package/@azure/msal-angularjs) | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angularjs/README.md) |  |  | |
![Angular](media/sample-v2-code/logo_angular.png) | MSAL Kątowy (wersja zapoznawcza) | [Npm](https://www.npmjs.com/package/@azure/msal-angular) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/README.md) | | | |
| ![.NET Framework](media/sample-v2-code/logo_NET.png) ![Platforma UWP](media/sample-v2-code/logo_windows.png) ![Xamarin](media/sample-v2-code/logo_xamarin.png) | MSAL.NET  |[NuGet](https://www.nuget.org/packages/Microsoft.Identity.Client) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) | [Aplikacja klasyczna](guidedsetups/active-directory-mobileanddesktopapp-windowsdesktop-intro.md) | [MSAL.NET](https://docs.microsoft.com/dotnet/api/microsoft.identity.client?view=azure-dotnet-preview) |[Dokumenty koncepcyjne](msal-overview.md) | [Harmonogram działania](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki#roadmap)
| ![Python](media/sample-v2-code/logo_python.png) | MĘTÓW MSAL | [PyPI](https://pypi.org/project/msal) | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-python) | [Próbki](https://github.com/AzureAD/microsoft-authentication-library-for-python/tree/dev/sample) | [ReadTheDocs (Niem.](https://msal-python.rtfd.io/) | [Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki) | [Harmonogram działania](https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki/Roadmap)
| ![Java](media/sample-v2-code/logo_java.png) | MSAL Java | [Maven](https://mvnrepository.com/artifact/com.microsoft.azure/msal4j) | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-java) | [Próbki](https://github.com/AzureAD/microsoft-authentication-library-for-java/tree/dev/src/samples) | [Tematy pomocy](https://javadoc.io/doc/com.microsoft.azure/msal4j/latest/index.html) | [Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-java/wiki) | [Harmonogram działania](https://github.com/AzureAD/microsoft-authentication-library-for-java/wiki)
| system macOS & systemu iOS | MSAL iOS i macOS | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-objc) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-objc) | [Aplikacja na iOS](https://github.com/Azure-Samples/ms-identity-mobile-apple-swift-objc), [aplikacja macOS](https://github.com/Azure-Samples/ms-identity-macOS-swift-objc) | [Tematy pomocy](https://azuread.github.io/microsoft-authentication-library-for-objc/index.html)  | [Dokumenty koncepcyjne](msal-overview.md) | |
|![Android / Java](media/sample-v2-code/logo_Android.png) | MSAL Android | [Centralne repozytorium](https://repo1.maven.org/maven2/com/microsoft/identity/client/msal/) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-android) | [Aplikacja na Androida](quickstart-v2-android.md) | [JavaDocs ( JavaDocs )](https://javadoc.io/doc/com.microsoft.identity.client/msal) | [Dokumenty koncepcyjne](msal-overview.md) |[Harmonogram działania](https://github.com/AzureAD/microsoft-authentication-library-for-android/wiki/Roadmap)

## <a name="microsoft-supported-server-middleware-libraries"></a>Biblioteki oprogramowania pośredniczącego serwerów obsługiwane przez firmę Microsoft

Biblioteki oprogramowania pośredniczącego pomagają chronić aplikacje sieci Web i internetowe interfejsy API. Aplikacje sieci Web lub internetowe interfejsy API napisane za pomocą ASP.NET lub ASP.NET Core używają bibliotek oprogramowania pośredniczącego.

| Platforma | Biblioteka | Pobierz | Kod źródłowy | Sample | Tematy pomocy
| --- | --- | --- | --- | --- | --- |
| ![.NET](media/sample-v2-code/logo_NET.png) ![.NET Core](media/sample-v2-code/logo_NETcore.png) | Bezpieczeństwo ASP.NET |[NuGet](https://www.nuget.org/packages/Microsoft.AspNet.Mvc/) |[GitHub](https://github.com/aspnet/AspNetCore) |[Aplikacja MVC](quickstart-v2-aspnet-webapp.md) |[Wykaz interfejsów API platformy ASP.NET](https://docs.microsoft.com/dotnet/api/?view=aspnetcore-2.0) |
| ![.NET](media/sample-v2-code/logo_NET.png)| Rozszerzenia Model tożsamości dla platformy .NET| |[GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | [Aplikacja MVC](quickstart-v2-aspnet-webapp.md) |[Tematy pomocy](https://docs.microsoft.com/dotnet/api/overview/azure/activedirectory/client?view=azure-dotnet) |
| ![Node.js](media/sample-v2-code/logo_nodejs.png) | Azure AD Passport |[Npm](https://www.npmjs.com/package/passport-azure-ad) |[GitHub](https://github.com/AzureAD/passport-azure-ad) | [Aplikacja internetowa](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs) | |

## <a name="microsoft-supported-libraries-by-os--language"></a>Biblioteki obsługiwane przez firmę Microsoft według systemu operacyjnego / język

W zakresie obsługiwanych systemów operacyjnych w porównaniu z językami mapowanie jest następujące:

|             | Windows    | Linux      | macOS      | iOS | Android    |
|-------------|------------|------------|------------|------------|------------|
| ![JavaScript](media/sample-v2-code/logo_js.png)  |  MSAL.js | MSAL.js | MSAL.js | MSAL.js |  MSAL.js |
| <img alt="C#" src="../../cognitive-services/speech-service/media/index/logo_csharp.svg" width="64px" height="64px" /> | ASP.NET, ASP.NET rdzeń, MSAL.Net (.NET FW, rdzeń, platformy uniwersalne systemu| ASP.NET rdzeń, MSAL.Net (.NET Core) | ASP.NET rdzeń, MSAL.Net (MacOS)       | MSAL.Net (Xamarin.iOS) | MSAL.Net (Xamarin.Android)|
| Swift <br> Obiektowy C |            |            | [Biblioteka MSAL dla systemów iOS i macOS](msal-overview.md) | [Biblioteka MSAL dla systemów iOS i macOS](msal-overview.md) |            |
| ![Java](media/sample-v2-code/logo_java.png) Java | msal4j ( msal4j ) | msal4j ( msal4j ) | msal4j ( msal4j ) | | MSAL Android |
| ![Python](media/sample-v2-code/logo_python.png) Python | MĘTÓW MSAL | MĘTÓW MSAL | MĘTÓW MSAL |
| ![Node.js](media/sample-v2-code/logo_nodejs.png) Node.JS | Passport.node | Passport.node | Passport.node |

Zobacz też [Scenariusze obsługiwanych platform i języków](authentication-flows-app-scenarios.md#scenarios-and-supported-platforms-and-languages)

## <a name="compatible-client-libraries"></a>Zgodne biblioteki klienckie

| Platforma | Nazwa biblioteki | Przetestowana wersja | Kod źródłowy | Sample |
|:---:|:---:|:---:|:---:|:---:|
|![JavaScript](media/sample-v2-code/logo_js.png)|[Hello.js](https://adodson.com/hello.js/) | Wersja 1.13.5 |[Hello.js](https://github.com/MrSwitch/hello.js) |[Spa](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2) |
| ![Java](media/sample-v2-code/logo_java.png) | [Skryba Java](https://github.com/scribejava/scribejava) | [Wersja 3.2.0](https://github.com/scribejava/scribejava/releases/tag/scribejava-3.2.0) | [ScribeJava](https://github.com/scribejava/scribejava/) | |
| ![Java](media/sample-v2-code/logo_java.png) | [Biblioteka Gluu OpenID Connect](https://github.com/GluuFederation/oxAuth) | [Wersja 3.0.2](https://github.com/GluuFederation/oxAuth/releases/tag/3.0.2) | [Biblioteka Gluu OpenID Connect](https://github.com/GluuFederation/oxAuth) | |
| ![Python](media/sample-v2-code/logo_python.png) | [Wnioski-OAuthlib](https://github.com/requests/requests-oauthlib) | [Wersja 1.2.0](https://github.com/requests/requests-oauthlib/releases/tag/v1.2.0) | [Wnioski-OAuthlib](https://github.com/requests/requests-oauthlib) | |
| ![Node.js](media/sample-v2-code/logo_nodejs.png) | [openid-klient](https://github.com/panva/node-openid-client) | [Wersja 2.4.5](https://github.com/panva/node-openid-client/releases/tag/v2.4.5) | [openid-klient](https://github.com/panva/node-openid-client) | |
| ![PHP](media/sample-v2-code/logo_php.png) | [Php League oauth2-client](https://github.com/thephpleague/oauth2-client) | [Wersja 1.4.2](https://github.com/thephpleague/oauth2-client/releases/tag/1.4.2) | [oauth2-klient](https://github.com/thephpleague/oauth2-client/) | |
| ![Ruby](media/sample-v2-code/logo_ruby.png) |[OmniAuth ( OmniAuth )](https://github.com/omniauth/omniauth/wiki) |omniauth: 1.3.1<br />omniauth-oauth2: 1.4.0 |[OmniAuth ( OmniAuth )](https://github.com/omniauth/omniauth)<br />[OmniAuth OAuth2](https://github.com/intridea/omniauth-oauth2) |  |
| iOS, macOS, & Android  | [Reaguj natywną aplikację Auth](https://github.com/FormidableLabs/react-native-app-auth) | [Wersja 4.2.0](https://github.com/FormidableLabs/react-native-app-auth/releases/tag/v4.2.0) | [Reaguj natywną aplikację Auth](https://github.com/FormidableLabs/react-native-app-auth) | |

W przypadku dowolnej biblioteki zgodnej ze standardami można użyć punktu końcowego platformy tożsamości firmy Microsoft. Ważne jest, aby wiedzieć, gdzie szukać pomocy technicznej:

* W przypadku problemów i nowych żądań funkcji w kodzie biblioteki skontaktuj się z właścicielem biblioteki.
* W przypadku problemów i nowych żądań funkcji w implementacji protokołu po stronie usługi skontaktuj się z firmą Microsoft.
* [Złóż żądanie funkcji](https://feedback.azure.com/forums/169401-azure-active-directory) dla dodatkowych funkcji, które chcesz wyświetlić w protokole.
* [Utwórz żądanie pomocy technicznej,](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) jeśli znajdziesz problem, w którym punkt końcowy platformy tożsamości firmy Microsoft nie jest zgodny z OAuth 2.0 lub OpenID Connect 1.0.

## <a name="related-content"></a>Zawartość pokrewna

Aby uzyskać więcej informacji na temat punktu końcowego platformy tożsamości firmy Microsoft, zobacz [omówienie platformy tożsamości firmy Microsoft][AAD-App-Model-V2-Overview].

<!--Image references-->

<!--Reference style links -->
[AAD-App-Model-V2-Overview]: v2-overview.md
[ClientLib-NET-Lib]: https://www.nuget.org/packages/Microsoft.Identity.Client
[ClientLib-NET-Repo]: https://github.com/AzureAD/microsoft-authentication-library-for-dotnet
[ClientLib-NET-Sample]: active-directory-v2-devquickstarts-wpf.md
[ClientLib-Node-Lib]: https://www.npmjs.com/package/passport-azure-ad
[ClientLib-Node-Repo]: https://github.com/AzureAD/passport-azure-ad
[ClientLib-Node-Sample]:/
[ClientLib-Iosmac-Lib]:/
[ClientLib-Iosmac-Repo]:/
[ClientLib-Iosmac-Sample]:/
[ClientLib-Android-Lib]:/
[ClientLib-Android-Repo]:/
[ClientLib-Android-Sample]:/
[ClientLib-Js-Lib]:/
[ClientLib-Js-Repo]:/
[ClientLib-Js-Sample]:/

[Microsoft-SDL]: https://www.microsoft.com/sdl/default.aspx
[ServerLib-Net4-Owin-Oidc-Lib]: https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect/
[ServerLib-Net4-Owin-Oidc-Repo]: https://katanaproject.codeplex.com/
[ServerLib-Net4-Owin-Oidc-Sample]: active-directory-v2-devquickstarts-dotnet-web.md
[ServerLib-Net4-Owin-Oauth-Lib]: https://www.nuget.org/packages/Microsoft.Owin.Security.OAuth/
[ServerLib-Net4-Owin-Oauth-Repo]: https://katanaproject.codeplex.com/
[ServerLib-Net4-Owin-Oauth-Sample]: https://azure.microsoft.com/documentation/articles/active-directory-v2-devquickstarts-dotnet-api/
[ServerLib-Net-Jwt-Lib]: https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt
[ServerLib-Net-Jwt-Repo]: https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet
[ServerLib-Net-Jwt-Sample]:/
[ServerLib-NetCore-Owin-Oidc-Lib]: https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.OpenIdConnect/
[ServerLib-NetCore-Owin-Oidc-Repo]: https://github.com/aspnet/Security
[ServerLib-NetCore-Owin-Oidc-Sample]: https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-aspnetcore-v2
[ServerLib-NetCore-Owin-Oauth-Lib]: https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.OAuth/
[ServerLib-NetCore-Owin-Oauth-Repo]: https://github.com/aspnet/Security
[ServerLib-NetCore-Owin-Oauth-Sample]:/
[ServerLib-Node-Lib]: https://www.npmjs.com/package/passport-azure-ad
[ServerLib-Node-Repo]: https://github.com/AzureAD/passport-azure-ad/
[ServerLib-Node-Sample]: https://azure.microsoft.com/documentation/articles/active-directory-v2-devquickstarts-node-web/
