---
title: Tworzenie aplikacji mobilnej, która wywołuje internetowe interfejsy API | Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak utworzyć aplikację mobilną, która wywołuje internetowe interfejsy API (omówienie)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviwer: brandwe
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 6675d67299091325fcc3e12572a906716bf5b88d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77132419"
---
# <a name="scenario-mobile-application-that-calls-web-apis"></a>Scenariusz: Aplikacja mobilna, która wywołuje internetowe interfejsy API

Dowiedz się, jak utworzyć aplikację mobilną, która wywołuje internetowe interfejsy API.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>Wprowadzenie

Utwórz pierwszą aplikację mobilną i wypróbuj szybki start.

> [!div class="nextstepaction"]
> [Szybki start: uzyskiwanie tokenu i wywoływanie interfejsu API programu Microsoft Graph z aplikacji dla systemu Android](./quickstart-v2-android.md)
>
> [Szybki start: uzyskiwanie tokenu i wywoływanie interfejsu API programu Microsoft Graph z aplikacji dla systemu iOS](./quickstart-v2-ios.md)
>
> [Szybki start: uzyskiwanie tokenu i wywoływanie interfejsu API programu Microsoft Graph z aplikacji xamarin iOS i Android](https://github.com/Azure-Samples/active-directory-xamarin-native-v2)

## <a name="overview"></a>Omówienie

Spersonalizowane, bezproblemowe środowisko użytkownika ma zasadnicze znaczenie dla aplikacji mobilnych.  Platforma tożsamości firmy Microsoft umożliwia deweloperom mobilnym tworzenie tego środowiska dla użytkowników systemów iOS i Android. Aplikacja może logować się w usłudze Azure Active Directory (Azure AD) użytkowników, użytkowników osobistych kont Microsoft i użytkowników usługi Azure AD B2C. Można również uzyskać tokeny do wywoływania internetowego interfejsu API w ich imieniu. Aby zaimplementować te przepływy, użyjemy biblioteki uwierzytelniania firmy Microsoft (MSAL). MSAL implementuje standardowy [przepływ kodu autoryzacji OAuth2.0](v2-oauth2-auth-code-flow.md).

![Aplikacje demona](./media/scenarios/mobile-app.svg)

Zagadnienia dotyczące aplikacji mobilnych:

- **Środowisko użytkownika jest kluczem:** zezwalaj użytkownikom na wyświetlanie wartości aplikacji, zanim poprosisz o zalogowanie się. Żądaj tylko wymaganych uprawnień.
- **Obsługa wszystkich konfiguracji użytkowników:** wielu użytkowników biznesowych urządzeń przenośnych musi przestrzegać zasad dostępu warunkowego i zasad zgodności z urządzeniami. Pamiętaj, aby obsługiwać te kluczowe scenariusze.
- **Implementowanie logowania jednokrotnego (Logowanie jednokrotne)**: Za pomocą platformy MSAL i microsoft tożsamości, można włączyć logowanie jednokrotne za pośrednictwem przeglądarki urządzenia lub Microsoft Authenticator (i Intune Company Portal w systemie Android).

## <a name="specifics"></a>Specyfiki

Podczas tworzenia aplikacji mobilnej na platformie tożsamości firmy Microsoft należy pamiętać o następujących zagadnieniach:

- W zależności od platformy niektóre interakcje z użytkownikiem mogą być wymagane przy pierwszym logowanie użytkowników. Na przykład system iOS wymaga aplikacji, aby pokazać interakcję użytkownika, gdy używają sytuowania po raz pierwszy za pośrednictwem microsoft authenticator (i intune company portal w systemie Android).
- W systemach iOS i Android msal może używać zewnętrznej przeglądarki do logowania użytkowników. Zewnętrzna przeglądarka może pojawić się na górze aplikacji. Można dostosować konfigurację do korzystania z webviews w aplikacji zamiast.
- Nigdy nie używaj tajemnicy w aplikacji mobilnej. W tych aplikacjach wpisy tajne są dostępne dla wszystkich użytkowników.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Rejestrowanie aplikacji](scenario-mobile-app-registration.md)
