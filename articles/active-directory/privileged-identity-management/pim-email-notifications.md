---
title: Powiadomienia e-mail w usłudze PIM — usługa Azure Active Directory | Dokumenty firmy Microsoft
description: W tym artykule opisano powiadomienia e-mail w usłudze Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 01/05/2019
ms.author: curtand
ms.reviewer: hanki
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: ee5f2edbae28276f8485ae774a5b1c52e1af2fd1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72756400"
---
# <a name="email-notifications-in-pim"></a>Powiadomienia e-mail w pim

Zarządzanie tożsamościami uprzywilejowanymi (PIM) informuje, kiedy w organizacji usługi Azure Active Directory (Azure AD) występują ważne zdarzenia, na przykład gdy rola jest przypisywana lub aktywowana. Uprzywilejowane zarządzanie tożsamościami na bieżąco informuje cię, wysyłając powiadomienia e-mailowe dla Ciebie i innych uczestników. Te wiadomości e-mail mogą również zawierać łącza do odpowiednich zadań, takich jak aktywowanie lub odnawianie roli. W tym artykule opisano, jak wyglądają te wiadomości e-mail, kiedy są wysyłane i kto je odbiera.

## <a name="sender-email-address-and-subject-line"></a>Adres e-mail nadawcy i wiersz tematu

Wiadomości e-mail wysyłane z zarządzania tożsamościami uprzywilejowanymi zarówno dla usługi Azure AD, jak i ról zasobów platformy Azure mają następujący adres e-mail nadawcy:

- Adres e-mail: **\@azure-noreply microsoft.com**
- Nazwa wyświetlana: Microsoft Azure

Te wiadomości e-mail zawierają prefiks **PIM** w wierszu tematu. Oto przykład:

- PIM: Alain Charon został na stałe przypisany do roli czytnik kopii zapasowych

## <a name="notifications-for-azure-ad-roles"></a>Powiadomienia dla ról usługi Azure AD

Usługa Zarządzanie tożsamościami uprzywilejowanymi wysyła wiadomości e-mail, gdy wystąpią następujące zdarzenia dla ról usługi Azure AD:

- Gdy aktywacja roli uprzywilejowanej oczekuje na zatwierdzenie
- Po zakończeniu żądania aktywacji roli uprzywilejowanej
- Gdy włączona jest usługa Azure AD Privileged Identity Management

Kto odbiera te wiadomości e-mail dla ról usługi Azure AD zależy od roli, zdarzenia i ustawienia powiadomień:

| Użytkownik | Aktywacja roli oczekuje na zatwierdzenie | Żądanie aktywacji roli zostało zakończone | Funkcja PIM jest włączona |
| --- | --- | --- | --- |
| Administrator ról uprzywilejowanych</br>(Aktywowany/Kwalifikujący się) | Tak</br>(tylko wtedy, gdy nie określono jawnych osób zatwierdzających) | Tak* | Tak |
| Administrator zabezpieczeń</br>(Aktywowany/Kwalifikujący się) | Nie | Tak* | Tak |
| Administrator globalny</br>(Aktywowany/Kwalifikujący się) | Nie | Tak* | Tak |

\*Jeśli [ustawienie **Powiadomienia** ](pim-how-to-change-default-settings.md#notifications) jest ustawione na **Włącz**.

Poniżej przedstawiono przykładową wiadomość e-mail, która jest wysyłana, gdy użytkownik aktywuje rolę usługi Azure AD dla fikcyjnej organizacji Contoso.

![Nowy adres e-mail usługi Azure AD dla zarządzania tożsamościami uprzywilejowanymi](./media/pim-email-notifications/email-directory-new.png)

### <a name="weekly-privileged-identity-management-digest-email-for-azure-ad-roles"></a>Cotygodniowa usługa zarządzania tożsamościami uprzywilejowana — poczta e-mail dla ról usługi Azure AD

Cotygodniowa wiadomość e-mail podsumowania zarządzania tożsamościami uprzywilejowanymi dla ról usługi Azure AD jest wysyłana do administratorów ról uprzywilejowanych, administratorów zabezpieczeń i administratorów globalnych, którzy włączyli zarządzanie tożsamościami uprzywilejowanymi. Ten cotygodniowy adres e-mail zawiera migawkę działań zarządzania tożsamościami uprzywilejowanymi w tygodniu, a także uprzywilejowanych przypisań ról. Jest on dostępny tylko dla dzierżawców w chmurze publicznej. Oto przykładowy adres e-mail:

![Cotygodniowa usługa zarządzania tożsamościami uprzywilejowana — poczta e-mail dla ról usługi Azure AD](./media/pim-email-notifications/email-directory-weekly.png)

Wiadomość e-mail zawiera cztery kafelki:

| Kafelek | Opis |
| --- | --- |
| **Użytkownicy aktywowani** | Ile razy użytkownicy aktywowali swoją kwalifikującą się rolę wewnątrz dzierżawy. |
| **Użytkownicy stali** | Liczba stałych użytkowników z kwalifikującym się przydziałem. |
| **Przypisania ról w zarządzania tożsamościami uprzywilejowanymi** | Ile razy użytkownikom przypisano kwalifikującą się rolę w zarządzaniu tożsamościami uprzywilejowanymi. |
| **Przypisania ról poza usługą PIM** | Ile razy użytkownikom przypisano stałą rolę poza zarządzaniem tożsamościami uprzywilejowanymi (wewnątrz usługi Azure AD). |

Omówienie **najlepszych ról** sekcji zawiera listę pięciu pierwszych ról w dzierżawie na podstawie całkowitej liczby stałych i kwalifikujących się administratorów dla każdej roli. Łącze **Podejmij akcję** otwiera [kreatora usługi PIM,](pim-security-wizard.md) w którym można konwertować stałych administratorów na uprawnionych administratorów w partiach.

## <a name="pim-emails-for-azure-resource-roles"></a>Wiadomości e-mail usługi PIM dla ról zasobów platformy Azure

Zarządzanie tożsamościami uprzywilejowanymi wysyła wiadomości e-mail do właścicieli i administratorów dostępu użytkowników, gdy wystąpią następujące zdarzenia dla ról zasobów platformy Azure:

- Gdy przypisanie roli oczekuje na zatwierdzenie
- Po przypisaniu roli
- Gdy rola wkrótce wygaśnie
- Gdy rola kwalifikuje się do rozszerzenia
- Po odnowieniu roli przez użytkownika końcowego
- Po zakończeniu żądania aktywacji roli

Usługa Zarządzanie tożsamościami uprzywilejowanymi wysyła wiadomości e-mail do użytkowników końcowych, gdy wystąpią następujące zdarzenia dla ról zasobów platformy Azure:

- Gdy rola jest przypisana do użytkownika
- Gdy rola użytkownika wygasła
- Po rozszerknięciu roli użytkownika
- Po zakończeniu żądania aktywacji roli użytkownika

Poniżej przedstawiono przykładową wiadomość e-mail, która jest wysyłana, gdy użytkownikowi jest przypisywana rola zasobu platformy Azure dla fikcyjnej organizacji Contoso.

![Nowy adres e-mail zarządzania tożsamościami uprzywilejowanymi dla ról zasobów platformy Azure](./media/pim-email-notifications/email-resources-new.png)

## <a name="next-steps"></a>Następne kroki

- [Konfigurowanie ustawień roli usługi Azure AD w zarządzania tożsamościami uprzywilejowanymi](pim-how-to-change-default-settings.md)
- [Zatwierdzanie lub odrzucanie żądań dla ról usługi Azure AD w zarządzania tożsamościami uprzywilejowanymi](azure-ad-pim-approval-workflow.md)
