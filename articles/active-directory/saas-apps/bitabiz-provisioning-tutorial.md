---
title: 'Samouczek: Konfigurowanie BitaBIZ do automatycznego inicjowania obsługi administracyjnej za pomocą usługi Azure Active Directory | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować usługę Azure Active Directory do automatycznego inicjowania obsługi administracyjnej i usuwania z obsługi administracyjnej kont użytkowników bitabiz.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: d0d38abe-c041-482a-9d3f-ca340678c226
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: ad9176614c4a5235e5138444d4197286204a747f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77059242"
---
# <a name="tutorial-configure-bitabiz-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie BitaBIZ do automatycznego inicjowania obsługi administracyjnej przez użytkowników

Celem tego samouczka jest zademonstrowanie kroków, które należy wykonać w usłudze BitaBIZ i usłudze Azure Active Directory (Azure AD) w celu skonfigurowania usługi Azure AD w celu automatycznego aprowizowania i deekwowania użytkowników i/lub grup do BitaBIZ.

> [!NOTE]
> W tym samouczku opisano łącznik utworzony na podstawie usługi inicjowania obsługi administracyjnej użytkowników usługi Azure AD. Aby uzyskać ważne informacje na temat działania tej usługi, działania i często zadawanych pytań, zobacz [Automatyzacja inicjowania obsługi administracyjnej i usuwania obsługi administracyjnej aplikacji SaaS za pomocą usługi Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Ten łącznik jest obecnie w publicznej wersji zapoznawczej. Aby uzyskać więcej informacji na temat ogólnych warunków korzystania z platformy Microsoft Azure dla funkcji w wersji Zapoznawczej, zobacz [Dodatkowe warunki użytkowania w wersji Zapoznawczej platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku zakłada, że masz już następujące wymagania wstępne:

* Dzierżawa usługi Azure AD.
* [Najemca BitaBIZ](https://bitabiz.dk/en/price/).
* Konto użytkownika w BitaBIZ z uprawnieniami administratora.

## <a name="assigning-users-to-bitabiz"></a>Przypisywanie użytkowników do BitaBIZ

Usługa Azure Active Directory używa koncepcji o nazwie *przydziały,* aby określić, którzy użytkownicy powinni otrzymać dostęp do wybranych aplikacji. W kontekście automatycznego inicjowania obsługi administracyjnej użytkowników tylko użytkownicy i/lub grupy, które zostały przypisane do aplikacji w usłudze Azure AD są synchronizowane.

Przed skonfigurowaniem i włączeniem automatycznego inicjowania obsługi administracyjnej użytkowników należy zdecydować, którzy użytkownicy i/lub grupy w usłudze Azure AD potrzebują dostępu do BitaBIZ. Po podjęciu decyzji, można przypisać tych użytkowników i / lub grup do BitaBIZ, postępując zgodnie z instrukcjami tutaj:
* [Przypisywanie użytkownika lub grupy do aplikacji przedsiębiorstwa](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-bitabiz"></a>Ważne wskazówki dotyczące przypisywania użytkowników do BitaBIZ

* Zaleca się, że jeden użytkownik usługi Azure AD jest przypisany do BitaBIZ do testowania konfiguracji automatycznego inicjowania obsługi administracyjnej użytkownika. Dodatkowi użytkownicy i/lub grupy mogą być przypisane później.

* Podczas przypisywania użytkownika do BitaBIZ należy wybrać dowolną prawidłową rolę specyficzną dla aplikacji (jeśli jest dostępna) w oknie dialogowym przypisania. Użytkownicy z rolą **dostępu domyślnego** są wykluczeni z inicjowania obsługi administracyjnej.

## <a name="setup-bitabiz-for-provisioning"></a>Konfigurowanie BitaBIZ do inicjowania obsługi administracyjnej

Przed skonfigurowaniem BitaBIZ do automatycznego inicjowania obsługi administracyjnej za pomocą usługi Azure AD należy włączyć inicjowanie obsługi administracyjnej scim na BitaBIZ.

1. Zaloguj się do [konsoli administracyjnej BitaBIZ](https://www.bitabiz.com/login?lang=en). Kliknij pozycję **SETUP ADMIN** (ADMINISTRATOR KONFIGURACJI).

    ![Konsola administracyjna BitaBIZ](media/bitabiz-provisioning-tutorial/setup-admin.png)

2.  Przejdź do **integration**.

    ![Konsola administracyjna BitaBIZ](media/bitabiz-provisioning-tutorial/integration.png)

2.  Przejdź do usługi **Microsoft Azure AD Provisioning**.  Wybierz **pozycję Włączone** w obszarze Automatyczne inicjowanie obsługi administracyjnej użytkowników. Skopiuj wartości **adresu URL punktu końcowego inicjowania obsługi administracyjnej scim** i **tokenu nośnika**. Te wartości zostaną wprowadzone w adresie URL dzierżawy i tajne token pola na karcie inicjowania obsługi administracyjnej aplikacji BitaBIZ w witrynie Azure portal.

    ![BitaBIZ Dodaj SCIM](media/bitabiz-provisioning-tutorial/authentication.png)


## <a name="add-bitabiz-from-the-gallery"></a>Dodaj BitaBIZ z galerii

Aby skonfigurować BitaBIZ do automatycznego inicjowania obsługi administracyjnej za pomocą usługi Azure AD, należy dodać BitaBIZ z galerii aplikacji usługi Azure AD do listy zarządzanych aplikacji SaaS.

**Aby dodać BitaBIZ z galerii aplikacji usługi Azure AD, wykonaj następujące kroki:**

1. W **[witrynie Azure portal](https://portal.azure.com)** w lewym panelu nawigacyjnym wybierz pozycję **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do **aplikacji enterprise**, a następnie wybierz pozycję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, wybierz przycisk **Nowa aplikacja** u góry okienka.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wprowadź **BitaBIZ**, wybierz **BitaBIZ** w panelu wyników, a następnie kliknij przycisk **Dodaj,** aby dodać aplikację.

    ![Aplikacja BitaBIZ na liście wyników](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-bitabiz"></a>Konfigurowanie automatycznego inicjowania obsługi administracyjnej bitabiz 

W tej sekcji można przejść przez kroki konfigurowania usługi inicjowania obsługi administracyjnej usługi Azure AD do tworzenia, aktualizowania i wyłączania użytkowników i/lub grup w BitaBIZ na podstawie przypisania użytkowników i/lub grup w usłudze Azure AD.

> [!TIP]
> Możesz również włączyć samooceny jednokrotne oparte na SAML dla BitaBIZ, postępując zgodnie z instrukcjami podanymi w [samouczku jednostronnego logowania BitaBIZ.](BitaBIZ-tutorial.md) Logowanie jednokrotne można skonfigurować niezależnie od automatycznego inicjowania obsługi administracyjnej przez użytkowników, chociaż te dwie funkcje wzajemnie się uzupełniają

### <a name="to-configure-automatic-user-provisioning-for-bitabiz-in-azure-ad"></a>Aby skonfigurować automatyczne inicjowanie obsługi administracyjnej dla BitaBIZ w usłudze Azure AD:

1. Zaloguj się do [Portalu Azure](https://portal.azure.com). Wybierz pozycję **Aplikacje przedsiębiorstwa**, a następnie wybierz pozycję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **BitaBIZ**.

    ![Link aplikacji BitaBIZ na liście aplikacji](common/all-applications.png)

3. Wybierz kartę **Inicjowanie obsługi administracyjnej.**

    ![Karta Inicjowanie obsługi administracyjnej](common/provisioning.png)

4. Ustaw **tryb inicjowania obsługi administracyjnej** na **Automatyczny**.

    ![Karta Inicjowanie obsługi administracyjnej](common/provisioning-automatic.png)

5. W sekcji Poświadczenia administratora wprowadź **adres URL punktu końcowego inicjowania obsługi administracyjnej scim** i **tokenu nośnika** pobrane wcześniej odpowiednio w adresie URL dzierżawy i tokenie tajnym. Kliknij **przycisk Testuj połączenie,** aby upewnić się, że usługa Azure AD może łączyć się z BitaBIZ. Jeśli połączenie nie powiedzie się, upewnij się, że twoje konto BitaBIZ ma uprawnienia administratora i spróbuj ponownie.

    ![Adres URL dzierżawy + token](common/provisioning-testconnection-tenanturltoken.png)

6. W polu **Wiadomość e-mail z powiadomieniem** wprowadź adres e-mail osoby lub grupy, która powinna otrzymywać powiadomienia o błędach inicjowania obsługi administracyjnej, i zaznacz pole wyboru - **Wyślij powiadomienie e-mail, gdy wystąpi błąd.**

    ![Wiadomość e-mail z powiadomieniem](common/provisioning-notification-email.png)

7. Kliknij przycisk **Zapisz**.

8. W sekcji **Mapowania** wybierz pozycję **Synchronizuj użytkowników usługi Azure Active Directory z BitaBIZ**.

    ![Mapowania użytkowników BitaBIZ](media/bitabiz-provisioning-tutorial/usermapping.png)

9. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD do BitaBIZ w sekcji **Mapowanie atrybutów.** Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania kont użytkowników w BitaBIZ do operacji aktualizacji. Wybierz przycisk **Zapisz,** aby zatwierdzić wszelkie zmiany.

    ![Atrybuty użytkownika BitaBIZ](media/bitabiz-provisioning-tutorial/user-attribute.png)


10. Aby skonfigurować filtry zakresu, zapoznaj się z poniższymi instrukcjami podanymi w [samouczku filtru zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Aby włączyć usługę inicjowania obsługi administracyjnej usługi Azure AD dla BitaBIZ, zmień **stan inicjowania obsługi administracyjnej** **na Włączone** w sekcji **Ustawienia.**

    ![Stan inicjowania obsługi administracyjnej włączony](common/provisioning-toggle-on.png)

12. Zdefiniuj użytkowników i/lub grupy, które chcesz udostępnić BitaBIZ, wybierając żądane wartości w **zakresie** w sekcji **Ustawienia.**

    ![Zakres inicjowania obsługi administracyjnej](common/provisioning-scope.png)

13. Gdy będziesz gotowy do aprowienia, kliknij przycisk **Zapisz**.

    ![Zapisywanie konfiguracji inicjowania obsługi administracyjnej](common/provisioning-configuration-save.png)

Ta operacja rozpoczyna początkową synchronizację wszystkich użytkowników i/lub grup zdefiniowanych w **zakresie** w sekcji **Ustawienia.** Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które występują co około 40 minut, o ile jest uruchomiona usługa inicjowania obsługi administracyjnej usługi Azure AD. Można użyć szczegóły **synchronizacji** sekcji do monitorowania postępu i obserwowanie łączy do raportu aktywności inicjowania obsługi administracyjnej, który opisuje wszystkie akcje wykonywane przez usługę inicjowania obsługi administracyjnej usługi Azure AD w BitaBIZ.

Aby uzyskać więcej informacji na temat sposobu zapoznania się z dziennikami inicjowania obsługi administracyjnej usługi Azure AD, zobacz [Raportowanie automatycznego inicjowania obsługi administracyjnej konta użytkownika.](../app-provisioning/check-status-user-account-provisioning.md)

## <a name="connector-limitations"></a>Ograniczenia złącza

* BitaBIZ wymaga **userName**, **e-mail**, **firstName** i **lastName** jako atrybuty obowiązkowe. 
* BitaBIZ nie obsługuje twardych usuwa obecnie.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Zarządzanie inicjowanie obsługi administracyjnej kont użytkowników dla aplikacji dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md).
* [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i otrzymywać raporty dotyczące aktywności inicjowania obsługi administracyjnej.](../app-provisioning/check-status-user-account-provisioning.md)
