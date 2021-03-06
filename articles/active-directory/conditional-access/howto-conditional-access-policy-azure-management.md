---
title: Dostęp warunkowy — wymagaj usługi MFA do zarządzania platformą Azure — usługa Azure Active Directory
description: Tworzenie niestandardowych zasad dostępu warunkowego wymagających uwierzytelniania wieloskładnikowego dla zadań zarządzania platformy Azure
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: c90566006868c817d977699c35f2213895f3fe70
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295244"
---
# <a name="conditional-access-require-mfa-for-azure-management"></a>Dostęp warunkowy: wymagaj usługi MFA do zarządzania platformą Azure

Organizacje korzystają z różnych usług platformy Azure i zarządzają nimi za pomocą narzędzi opartych na usłudze Azure Resource Manager, takich jak:

* Portal Azure
* Azure PowerShell
* Interfejs wiersza polecenia platformy Azure

Narzędzia te mogą zapewnić wysoce uprzywilejowany dostęp do zasobów, które mogą zmieniać konfiguracje obejmujące całą subskrypcję, ustawienia usługi i rozliczenia subskrypcji. Aby chronić te uprzywilejowane zasoby, firma Microsoft zaleca wymaganie uwierzytelniania wieloskładnikowego dla każdego użytkownika uzyskującego dostęp do tych zasobów.

## <a name="user-exclusions"></a>Wykluczenia użytkowników

Zasady dostępu warunkowego są zaawansowanymi narzędziami, zalecamy wyłączenie następujących kont z zasad:

* **Dostęp awaryjny** lub **break-glass** kont, aby zapobiec blokady konta całej dzierżawcy. W mało prawdopodobnym scenariuszu wszyscy administratorzy są zablokowane z dzierżawy, konto administracyjne dostępu awaryjnego może służyć do logowania się do dzierżawy podjąć kroki w celu odzyskania dostępu.
   * Więcej informacji można znaleźć w artykule [Zarządzanie kontami dostępu awaryjnego w usłudze Azure AD](../users-groups-roles/directory-emergency-access.md).
* **Konta usług** i **zasady usługi,** takie jak konto usługi Azure AD Connect Sync. Konta usług są kontami nieinterakcyjnymi, które nie są powiązane z żadnym konkretnym użytkownikiem. Są one zwykle używane przez usługi zaplecza i umożliwiają programowy dostęp do aplikacji. Konta usług powinny być wykluczone, ponieważ nie można ukończyć programowo.
   * Jeśli organizacja ma te konta w użyciu w skryptach lub kod, należy rozważyć zastąpienie ich [tożsamościami zarządzanymi](../managed-identities-azure-resources/overview.md). Jako tymczasowe obejście można wykluczyć te określone konta z zasad linii bazowej.

## <a name="create-a-conditional-access-policy"></a>Tworzenie zasad dostępu warunkowego

Poniższe kroki pomogą utworzyć zasady dostępu warunkowego, aby wymagać od tych przypisanych ról administracyjnych do wykonywania uwierzytelniania wieloskładnikowego.

1. Zaloguj się do **witryny Azure portal** jako administrator globalny, administrator zabezpieczeń lub administrator dostępu warunkowego.
1. Przejdź do **usługi Azure Active Directory** > **Security** > **Conditional Access**.
1. Wybierz **pozycję Nowa zasada**.
1. Nadaj polityce nazwę. Zaleca się, aby organizacje tworzyły znaczący standard nazw swoich zasad.
1. W obszarze **Przydziały**wybierz **pozycję Użytkownicy i grupy**
   1. W **obszarze Uwzględnij**wybierz pozycję **Wszyscy użytkownicy**.
   1. W obszarze **Wyklucz**wybierz **pozycję Użytkownicy i grupy** i wybierz dostęp awaryjny w organizacji lub konta typu break-glass. 
   1. Wybierz pozycję **Done** (Gotowe).
1. W obszarze **Aplikacje lub akcje w** > chmurze**Uwzględnij**wybierz pozycję **Wybierz aplikacje**, wybierz pozycję **Microsoft Azure Management**i wybierz pozycję **Wybierz,** a następnie **gotowe**.
1. W **obszarze Warunki** > **aplikacje klienckie (Wersja zapoznawcza)** ustaw **pozycję Konfiguruj** na **Tak**i wybierz opcję **Gotowe**.
1. W obszarze **Kontrola** > dostępu**Przyznanie**wybierz pozycję **Udzielić dostępu**, **Wymagaj uwierzytelniania wieloskładnikowego**i wybierz pozycję **Wybierz**.
1. Potwierdź ustawienia i ustaw **włącz zasadę** **na Włącz**.
1. Wybierz **pozycję Utwórz,** aby utworzyć, aby włączyć zasady.

## <a name="next-steps"></a>Następne kroki

[Wspólne zasady dostępu warunkowego](concept-conditional-access-policy-common.md)

[Określanie wpływu przy użyciu trybu tylko dla dostępu warunkowego](howto-conditional-access-report-only.md)

[Symulowanie zachowania logowania za pomocą narzędzia Co jeśli dostęp warunkowy](troubleshoot-conditional-access-what-if.md)
