---
title: 'Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z usługą Litmos | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i aplikacją Litmos.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: cfaae4bb-e8e5-41d1-ac88-8cc369653036
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/26/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a935ef6d14b4de67964c555e0ffa610bbe992459
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/30/2019
ms.locfileid: "70171557"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-litmos"></a>Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z usługą Litmos

W tym samouczku dowiesz się, jak zintegrować usługę Litmos z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi Litmos z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do Litmos.
* Zezwól użytkownikom na automatyczne logowanie się do usługi Litmos przy użyciu kont w usłudze Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) Litmos.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Aplikacja Litmos obsługuje logowanie jednokrotne inicjowane przez **dostawcę tożsamości**
* Aplikacja Litmos obsługuje aprowizowanie użytkowników typu **Just In Time**

## <a name="adding-litmos-from-the-gallery"></a>Dodawanie aplikacji Litmos z galerii

Aby skonfigurować integrację aplikacji Litmos w usłudze Azure AD, należy dodać aplikację Litmos z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) przy użyciu służbowego lub osobistego konta Microsoft.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Litmos** w polu wyszukiwania.
1. Wybierz pozycję **Litmos** from panel wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-litmos"></a>Skonfiguruj i przetestuj Logowanie jednokrotne w usłudze Azure AD dla Litmos

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą Litmos przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w Litmos.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą Litmos, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj logowanie](#configure-litmos-sso)** jednokrotne w usłudze Litmos, aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz użytkownika testowego Litmos](#create-litmos-test-user)** , aby dysponować odpowiednikiem B. Simon w Litmos, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj logowanie](#test-sso)** jednokrotne — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie logowania jednokrotnego w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **Litmos** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie**jednokrotne.
1. Na stronie **Wybierz metodę logowania** jednokrotnego wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** wprowadź wartości dla następujących pól:

    a. W polu **Identyfikator** wpisz adres URL, korzystając z następującego wzorca: `https://<companyname>.litmos.com/account/Login`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<companyname>.litmos.com/integration/samllogin`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zastąp te wartości rzeczywistymi wartościami identyfikatora i adresu URL odpowiedzi, które zostały omówione w dalszej części samouczka, lub skontaktuj się z [zespołem pomocy technicznej klienta usługi Litmos](https://www.litmos.com/contact-us), aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **certyfikat (base64)** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link pobierania certyfikatu](common/certificatebase64.png)

1. W sekcji **Konfigurowanie Litmos** skopiuj odpowiednie adresy URL na podstawie wymagania.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w Azure Portal o nazwie B. Simon.

1. W lewym okienku w Azure Portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. Wybierz **nowego użytkownika** w górnej części ekranu.
1. We właściwościach **użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W polu **Nazwa użytkownika** wprowadź wartość username@companydomain.extension. Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi Litmos.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **Litmos**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link "Użytkownicy i grupy"](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

## <a name="configure-litmos-sso"></a>Konfigurowanie logowania jednokrotnego Litmos

1. W innym oknie przeglądarki zaloguj się do firmowej witryny aplikacji Litmos jako administrator.

2. Na pasku nawigacyjnym po lewej stronie kliknij pozycję **Accounts** (Konta).

    ![Sekcja Accounts (Konta) po stronie aplikacji][22]

3. Kliknij kartę **Integrations** (Integracje).

    ![Karta Integrations (Integracje)][23]

4. Na karcie **Integrations** (Integracje) przewiń w dół do pozycji **3rd Party Integrations** (Integracje innych firm), a następnie kliknij kartę **SAML 2.0**.

    ![Sekcja SAML 2.0][24]

5. Skopiuj wartość w obszarze **The SAML endpoint for litmos is:** (Punktem końcowym SAML dla aplikacji Litmos jest:) i wklej ją w polu tekstowym **Adres URL odpowiedzi** w sekcji **Domena i adresy URL aplikacji Litmos** w witrynie Azure Portal.

    ![Punkt końcowy SAML][26]

6. W aplikacji **Litmos** wykonaj następujące kroki:

    ![Aplikacja Litmos][25]

    a. Kliknij pozycję **Enable SAML** (Włącz protokół SAML).

    b. Otwórz w Notatniku swój certyfikat zakodowany w formacie base-64, skopiuj zawartość do Schowka, a następnie wklej ją w polu tekstowym **SAML X.509 Certificate** (Certyfikat X.509 SAML).

    c. Kliknij przycisk **Zapisz zmiany**.

### <a name="create-litmos-test-user"></a>Tworzenie użytkownika testowego aplikacji Litmos

Celem tej sekcji jest utworzenie użytkownika o nazwie Britta Simon w aplikacji Litmos. Aplikacja Litmos obsługuje aprowizację Just-in-Time. Oznacza to, że konto użytkownika jest tworzone automatycznie w zależności od potrzeb podczas próby uzyskania dostępu do aplikacji za pomocą panelu dostępu.

**Aby utworzyć użytkownika o nazwie Britta Simon w aplikacji Litmos, wykonaj następujące kroki:**

1. W innym oknie przeglądarki zaloguj się do firmowej witryny aplikacji Litmos jako administrator.

2. Na pasku nawigacyjnym po lewej stronie kliknij pozycję **Accounts** (Konta).

    ![Sekcja Accounts (Konta) po stronie aplikacji][22]

3. Kliknij kartę **Integrations** (Integracje).

    ![Karta Integrations (Integracje)][23]

4. Na karcie **Integrations** (Integracje) przewiń w dół do pozycji **3rd Party Integrations** (Integracje innych firm), a następnie kliknij kartę **SAML 2.0**.

    ![SAML 2.0][24]

5. Wybierz pozycję **Autogenerate Users** (Automatyczne generowanie użytkowników)
  
    ![Automatyczne generowanie użytkowników][27]

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Litmos w panelu dostępu powinno nastąpić automatyczne zalogowanie do aplikacji Litmos, dla której skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [ Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj Litmos z usługą Azure AD](https://aad.portal.azure.com/)

[21]: ./media/litmos-tutorial/tutorial_litmos_60.png
[22]: ./media/litmos-tutorial/tutorial_litmos_61.png
[23]: ./media/litmos-tutorial/tutorial_litmos_62.png
[24]: ./media/litmos-tutorial/tutorial_litmos_63.png
[25]: ./media/litmos-tutorial/tutorial_litmos_64.png
[26]: ./media/litmos-tutorial/tutorial_litmos_65.png
[27]: ./media/litmos-tutorial/tutorial_litmos_66.png