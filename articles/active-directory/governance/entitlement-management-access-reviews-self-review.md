---
title: Samoobsługowe przeglądanie pakietu dostępu w usłudze Azure AD uprawnień zarządzania
description: Dowiedz się, jak przeglądać dostęp użytkowników do pakietów dostępu do zarządzania prawami w Azure Active Directory przeglądy dostępu (wersja zapoznawcza).
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 11/01/2019
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 45da1170705bab1206a98c59e02c7616c25ce502
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2020
ms.locfileid: "78967772"
---
# <a name="self-review-of-an-access-package-in-azure-ad-entitlement-management"></a>Samoobsługowe przeglądanie pakietu dostępu w usłudze Azure AD uprawnień zarządzania

Zarządzanie prawami usługi Azure AD upraszcza zarządzanie dostępem do grup, aplikacji i witryn programu SharePoint przez przedsiębiorstwa. W tym artykule opisano sposób, w jaki użytkownik wykonuje własne przeglądy przypisanych pakietów dostępu.

## <a name="open-the-access-review"></a>Otwórz przegląd dostępu

Aby przeprowadzić przegląd dostępu, musisz najpierw otworzyć przegląd dostępu. Aby znaleźć i otworzyć przegląd dostępu, wykonaj czynności opisane w poniższej procedurze:

1. Otrzymasz od firmy Microsoft wiadomość e-mail z prośbą o sprawdzenie dostępu. Znajdź wiadomość e-mail, aby otworzyć przegląd dostępu. Oto przykład wiadomości e-mail żądającej przeglądu dostępu: 
    
    ![Adres e-mail recenzenta przeglądu dostępu](./media/entitlement-management-access-reviews-review-access/self-review-reviewer-email.png)

1. Kliknij link **Przejrzyj dostęp** .

1. Możesz też przejść bezpośrednio do https://myaccess.microsoft.com, aby znaleźć oczekujące przeglądy dostępu, jeśli nie otrzymasz wiadomości e-mail.  (W przypadku instytucji rządowych USA Użyj `https://myaccess.microsoft.us` zamiast tego).

1. Kliknij przycisk **przeglądy dostępu** na lewym pasku nawigacyjnym, aby wyświetlić listę oczekujących przeglądów dostępu przypisanych do użytkownika.


1.  Kliknij przegląd, którego chcesz zacząć.

## <a name="perform-the-access-review"></a>Wykonaj przegląd dostępu

Po otwarciu przeglądu dostępu zobaczysz Twój dostęp. Aby zapoznać się z przeglądem dostępu, wykonaj czynności opisane w poniższej procedurze:

1.  Zdecyduj, czy nadal potrzebujesz dostępu do pakietu dostępu. Na przykład projekt, nad którym pracujesz, nie jest gotowy, więc nadal potrzebujesz dostępu, aby kontynuować pracę nad projektem.

1.  Kliknij przycisk **tak** , aby zachować dostęp, lub kliknij przycisk **nie** , aby usunąć dostęp.
    >[!NOTE]
    >Jeśli użytkownik stwierdził, że nie potrzebujesz już dostępu, nie zostanie natychmiast usunięty z pakietu dostępu. Użytkownik zostanie usunięty z pakietu dostępu po zakończeniu przeglądu lub jeśli administrator zatrzyma przegląd.

1.  Jeśli klikniesz **przycisk tak**, może być konieczne dołączenie instrukcji justowania do pola **Przyczyna** .

1.  Kliknij przycisk **Prześlij**.

Możesz powrócić do przeglądu, jeśli zmienisz zdanie i zdecydujesz się zmienić odpowiedź przed końcem przeglądu.

## <a name="next-steps"></a>Następne kroki

- [Przejrzyj dostęp do pakietów dostępu](entitlement-management-access-reviews-review-access.md) 
