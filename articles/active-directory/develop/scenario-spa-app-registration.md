---
title: Rejestrowanie aplikacji jednostronicowych — platforma tożsamości firmy Microsoft | Azure
description: Dowiedz się, jak utworzyć aplikację jednostronicową (rejestracja aplikacji)
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 5b18b10748e0587920c6965f1d235376da928469
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76701896"
---
# <a name="single-page-application-app-registration"></a>Aplikacja jednostronicowa: rejestracja aplikacji

Na tej stronie opisano szczegóły rejestracji aplikacji dla aplikacji jednostronicowej (SPA).

Wykonaj kroki, aby [zarejestrować nową aplikację na platformie tożsamości firmy Microsoft](quickstart-register-app.md)i wybierz obsługiwane konta dla aplikacji. Scenariusz SPA może obsługiwać uwierzytelnianie przy użyciu kont w organizacji lub dowolnej organizacji i osobistych kont Microsoft.

Następnie zapoznaj się z konkretnymi aspektami rejestracji aplikacji, które mają zastosowanie do aplikacji jednostronicowych.

## <a name="register-a-redirect-uri"></a>Rejestrowanie identyfikatora URI przekierowania

Przepływ niejawny wysyła tokeny w przekierowaniu do aplikacji jednostronicowej uruchomionej w przeglądarce sieci web. Dlatego ważne jest, aby zarejestrować identyfikator URI przekierowania, gdzie aplikacja może odbierać tokeny. Upewnij się, że identyfikator URI przekierowania dokładnie pasuje do identyfikatora URI dla aplikacji.

W [witrynie Azure portal](https://go.microsoft.com/fwlink/?linkid=2083908)przejdź do zarejestrowanej aplikacji. Na stronie **Uwierzytelnianie** aplikacji wybierz platformę **sieci Web.** Wprowadź wartość identyfikatora URI przekierowania dla aplikacji w polu **Przekierowanie identyfikatora URI.**

## <a name="enable-the-implicit-flow"></a>Włączanie przepływu niejawnego

Na tej samej stronie **uwierzytelniania** w obszarze **Ustawienia zaawansowane**należy również włączyć **niejawne przyznanie**. Jeśli aplikacja loguje się tylko do użytkowników i otrzymuje tokeny identyfikatorów, wystarczy zaznaczyć pole wyboru **tokeny identyfikatorów.**

Jeśli aplikacja musi również uzyskać tokeny dostępu do wywoływania interfejsów API, upewnij się, aby **zaznaczyć tokeny dostępu** pole wyboru, jak również. Aby uzyskać więcej informacji, zobacz [tokeny identyfikatorów](./id-tokens.md) i [tokeny dostępu](./access-tokens.md).

## <a name="api-permissions"></a>Uprawnienia aplikacji

Aplikacje jednostronicowe mogą wywoływać interfejsy API w imieniu zalogowanego użytkownika. Muszą zażądać delegowanych uprawnień. Aby uzyskać szczegółowe informacje, zobacz [Dodawanie uprawnień dostępu do internetowych interfejsów API](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis).

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Konfiguracja kodu aplikacji](scenario-spa-app-configuration.md)
