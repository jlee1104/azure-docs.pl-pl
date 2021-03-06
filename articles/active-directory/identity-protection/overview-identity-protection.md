---
title: Co to jest Azure Active Directory Identity Protection?
description: Wykrywaj, Koryguj, badaj i Analizuj ryzyko przy użyciu Azure AD Identity Protection
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: overview
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7e928d67ba7102df3d342e77705ea895f9230ff3
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/24/2019
ms.locfileid: "72887660"
---
# <a name="what-is-azure-active-directory-identity-protection"></a>Co to jest Azure Active Directory Identity Protection?

Program Identity Protection to narzędzie, które umożliwia organizacjom wykonywanie trzech najważniejszych zadań:

- Automatyzowanie wykrywania i korygowania zagrożeń opartych na tożsamościach.
- Zbadaj ryzyko przy użyciu danych w portalu.
- Eksportuj dane wykrywania ryzyka do narzędzi innych firm w celu dalszej analizy.

Usługa Identity Protection korzysta z informacji uzyskanych od firmy Microsoft w organizacjach z usługą Azure AD, przestrzenią użytkownika z kontami Microsoft oraz w grach z konsolą Xbox w celu ochrony użytkowników. Microsoft analizuje 6 500 000 000 000 sygnałów dziennie w celu identyfikowania i ochrony klientów przed zagrożeniami.

Sygnały generowane przez i przekazywane do usługi Identity Protection mogą być dodatkowo wprowadzane do narzędzi takich jak dostęp warunkowy w celu podejmowania decyzji dotyczących dostępu lub powracania do narzędzia do zarządzania informacjami i zdarzeniami zabezpieczeń (SIEM) w celu dalszej analizy na podstawie wymuszone zasady.

## <a name="why-is-automation-important"></a>Dlaczego Automatyzacja jest ważna?

W swoim [wpisie w blogu w październiku 2018](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Eight-essentials-for-hybrid-identity-3-Securing-your-identity/ba-p/275843) Weinert, który prowadzi do zespołu ds. zabezpieczeń i ochrony tożsamości firmy Microsoft, wyjaśnia, dlaczego Automatyzacja jest ważna w przypadku rozpatrzenia ilości zdarzeń:

> Codziennie nasze systemy uczenia maszynowego i heurystycznego zapewniają oceny ryzyka dla 18 000 000 000 prób logowania dla ponad 800 000 000 odrębnych kont, 300 000 000 z discernibly przez źródłami ataków (jednostki takie jak: aktory karne, hakerzy).
>
> Przy zapłonie w ubiegłym roku mam do 3 najlepszych ataków na nasze systemy tożsamości. Poniżej przedstawiono ostatnią ilość tych ataków
>   
>   - **Powtarzanie naruszenia**: w maju 2018 wykryto ataki mld USD z 4.6
>   - **Rozpylanie hasła**: 350K w kwietniu 2018
>   - **Phishing**: jest to trudne do wypróbowania dokładnie, ale 23M zdarzenia o podwyższonym ryzyku w marcu 2018, wiele z nich jest związanych z phishingiem

## <a name="risk-detection-and-remediation"></a>Wykrywanie ryzyka i korygowanie

Ochrona tożsamości identyfikuje ryzyko w następujących klasyfikacjach:

| Typ wykrywania ryzyka | Opis |
| --- | --- |
| Nietypowe podróże | Zaloguj się z nietypowej lokalizacji na podstawie ostatnich logowań użytkownika. |
| Anonimowy adres IP | Zaloguj się przy użyciu anonimowego adresu IP (na przykład: przeglądarki tor, sieci VPN Anonymizer). |
| Nieznane właściwości logowania | Zaloguj się przy użyciu właściwości, które nie były ostatnio widziane dla danego użytkownika. |
| Połączony adres IP złośliwego oprogramowania | Zaloguj się przy użyciu połączonego adresu IP złośliwego oprogramowania |
| Nieujawnione poświadczenia | To wykrywanie ryzyka wskazuje, że nieprawidłowe poświadczenia użytkownika zostały ujawnione |
| Analiza zagrożeń usługi Azure AD | Wewnętrzne i zewnętrzne źródła analizy zagrożeń firmy Microsoft określiły znany wzorzec ataku |

Bardziej szczegółowe informacje o tych zagrożeniach i sposobie ich obliczenia można znaleźć w artykule, [co jest ryzykowne](concept-identity-protection-risks.md).

Sygnały ryzyka mogą wyzwolić działania naprawcze, takie jak wymaganie od użytkowników: wykonywania Multi-Factor Authentication Azure, resetowania hasła przy użyciu funkcji samoobsługowego resetowania hasła lub blokowania do momentu podjęcia działania przez administratora.

## <a name="risk-investigation"></a>Badanie ryzyka

Administratorzy mogą przeglądać wykryte wykrywania i podejmować działania ręczne w razie konieczności. Istnieją trzy główne raporty używane przez administratorów do badań w ramach usługi Identity Protection:

- Ryzykowni użytkownicy
- Ryzykowne logowania
- Wykrycia ryzyka

Więcej informacji można znaleźć w artykule, [jak: badanie ryzyka](howto-identity-protection-investigate-risk.md).

## <a name="exporting-risk-data"></a>Eksportowanie danych ryzyka

Dane z usługi Identity Protection można eksportować do innych narzędzi w celu archiwizacji i dalszych badań i współpracy. Interfejsy API oparte na Microsoft Graph umożliwiają organizacjom zbieranie tych danych do dalszej obróbki w narzędziu, takim jak SIEM. Informacje o sposobach uzyskiwania dostępu do interfejsu API usługi Identity Protection można znaleźć w artykule [wprowadzenie do Azure Active Directory Identity Protection i Microsoft Graph](howto-identity-protection-graph-api.md)

Informacje o integrowaniu informacji o ochronie tożsamości ze wskaźnikiem kontrolnym platformy Azure można znaleźć w artykule [łączenie danych z Azure AD Identity Protection](../../sentinel/connect-azure-ad-identity-protection.md).

## <a name="permissions"></a>Uprawnienia

Aby uzyskać dostęp do programu, Ochrona tożsamości wymaga, aby użytkownicy mieli zabezpieczenia czytnika zabezpieczeń, operatora zabezpieczeń, administratora zabezpieczeń, czytnika globalnego lub administratora globalnego.

## <a name="license-requirements"></a>Wymagania licencyjne

[!INCLUDE [Active Directory P2 license](../../../includes/active-directory-p2-license.md)]

| Możliwość | Szczegóły | Usługa Azure AD — warstwa Premium P2 | Usługa Azure AD — warstwa Premium P1 | Azure AD — wersja Podstawowa/bezpłatnie |
| --- | --- | --- | --- | --- |
| Zasady dotyczące ryzyka | Zasady ryzyka użytkownika (za pośrednictwem ochrony tożsamości) | Tak | Nie | Nie |
| Zasady dotyczące ryzyka | Zasady dotyczące ryzyka związanego z logowaniem (za pośrednictwem funkcji ochrony tożsamości lub dostępu warunkowego) | Tak | Nie | Nie |
| Raporty zabezpieczeń | Przegląd | Tak | Nie | Nie |
| Raporty zabezpieczeń | Ryzykowni użytkownicy | Pełny dostęp | Ograniczone informacje | Ograniczone informacje |
| Raporty zabezpieczeń | Ryzykowne logowania | Pełny dostęp | Ograniczone informacje | Ograniczone informacje |
| Raporty zabezpieczeń | Wykrycia ryzyka | Pełny dostęp | Ograniczone informacje | Nie |
| Powiadomienia | Użytkownicy zagrożeni wykrytymi alertami | Tak | Nie | Nie |
| Powiadomienia | Podsumowanie tygodniowe | Tak | Nie | Nie |
| | Zasady rejestracji usługi MFA | Tak | Nie | Nie |

## <a name="next-steps"></a>Następne kroki

- [Omówienie zabezpieczeń](concept-identity-protection-security-overview.md)

- [Co to jest ryzyko](concept-identity-protection-risks.md)

- [Zasady dostępne w celu ograniczenia ryzyka](concept-identity-protection-policies.md)
