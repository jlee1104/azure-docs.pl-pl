---
title: 'Samouczek: Azure Active Directory Integracja z logowaniem jednokrotnym (SSO) przy użyciu podróży i wydatków Concur | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory a kosztami podróży i wydatków Concur.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 6401ace4-71c0-4778-8b15-900f2f5f0c4c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/10/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9dddd9f6904aa5ef7840850792aeabf04666dddc
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2019
ms.locfileid: "72373415"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-concur-travel-and-expense"></a>Samouczek: Azure Active Directory Integracja z logowaniem jednokrotnym (SSO) przy użyciu podróży i wydatków Concur

W tym samouczku dowiesz się, jak zintegrować Concure i koszty za pomocą usługi Azure Active Directory (Azure AD). Po zintegrowaniu Concur podróży i wydatków z usługą Azure AD możesz:

* Kontrolka w usłudze Azure AD, która ma dostęp do podróży i wydatków usługi Concur.
* Zezwól użytkownikom na automatyczne logowanie do Concur podróży i wydatków przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja podróży i wydatków usługi Concur.
* Rola "administrator firmy" w ramach konta użytkownika Concur. Możesz sprawdzić, czy masz odpowiednie uprawnienia dostępu, przechodząc do narzędzia samoobsługowego [logowania jednokrotnego Concur](https://www.concursolutions.com/nui/authadmin/ssoadmin). Jeśli nie masz dostępu, skontaktuj się z pomocą techniczną Concur lub zaimplementowanie menedżera projektu. 

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD.

* Concur podróże i wydatki obsługują Logowanie jednokrotne w **dostawcy tożsamości** i **SP**
* Concur podróże i wydatki obsługują testowanie logowania jednokrotnego w środowisku produkcyjnym i implementacji 

> [!NOTE]
> Identyfikator tej aplikacji to stała wartość ciągu dla każdego z trzech regionów: US, EMEA i Chiny. Można skonfigurować tylko jedno wystąpienie dla każdego regionu w jednej dzierżawie. 

## <a name="adding-concur-travel-and-expense-from-the-gallery"></a>Dodawanie podróży i wydatków usługi Concur z galerii

Aby skonfigurować integrację usługi Concur w podróży i obchodzić z usługą Azure AD, musisz dodać przemieszczenie Concur i wydatki z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) przy użyciu służbowego lub osobistego konta Microsoft.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Concur podróże i wydatki** w polu wyszukiwania.
1. Wybierz pozycję **Concur podróże i wydatki** z panelu wyniki, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-concur-travel-and-expense"></a>Skonfiguruj i przetestuj Logowanie jednokrotne w usłudze Azure AD na potrzeby podróży i wydatków usługi Concur

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą Concurego ruchu i wydatków przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w programie Concur podróże i wydatki.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą Concurego ruchu i wydatków, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj podróże i wydatki logowania jednokrotnego w usłudze Concur,](#configure-concur-travel-and-expense-sso)** aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz użytkownika testowego podróży i wydatków usługi Concur,](#create-concur-travel-and-expense-test-user)** aby dysponować odpowiednikiem B. Simon w Concur podróży i wydatków, które są połączone z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie logowania jednokrotnego w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji do **podróży i wydatków Concur** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja SAML** aplikacja została wstępnie skonfigurowana w trybie inicjalizacji **dostawcy tożsamości** , a wymagane adresy URL są już wstępnie wypełnione na platformie Azure. Użytkownik musi zapisać konfigurację, klikając przycisk **Zapisz** .

    > [!NOTE]
    > Identyfikator (identyfikator jednostki) i adres URL odpowiedzi (adres URL usługi Konsumenckej potwierdzenia) są specyficzne dla regionu. Wybierz pozycję na podstawie centrum danych jednostki Concur. Jeśli nie znasz centrum danych jednostki Concur, skontaktuj się z pomocą techniczną Concur. 

5. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióra, aby zmienić ustawienia **atrybutu użytkownika** . Unikatowy identyfikator użytkownika musi pasować do login_id użytkownika Concur. Zwykle należy zmienić **User. userPrincipalName** na **User. mail**.

    ![Edytowanie atrybutu użytkownika](common/edit-attribute.png)

6. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu protokołu SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **plik XML metadanych Federacji** i wybierz pozycję **Pobierz** , aby pobrać metadane i zapisać je na komputerze.

    ![Link pobierania certyfikatu](common/metadataxml.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w Azure Portal o nazwie B. Simon.

1. W lewym okienku w Azure Portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. Wybierz **nowego użytkownika** w górnej części ekranu.
1. We właściwościach **użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W polu **Nazwa użytkownika** wprowadź username@companydomain.extension. Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do Concur podróży i wydatków.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **Concur podróże i wydatki**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link "Użytkownicy i grupy"](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

## <a name="configure-concur-travel-and-expense-sso"></a>Konfigurowanie Concur podróży i wydatków logowania jednokrotnego

1. Aby skonfigurować Logowanie jednokrotne na stronie **Concur podróże i wydatki** , musisz przekazać pobrany **XML metadanych Federacji** do narzędzia samoobsługowego logowania [jednokrotnego Concur](https://www.concursolutions.com/nui/authadmin/ssoadmin) i zalogować się przy użyciu konta z rolą "administrator firmy". 

1. Kliknij pozycję **Add** (Dodaj).
1. Wprowadź niestandardową nazwę dostawcy tożsamości, na przykład "Azure AD (US)". 
1. Kliknij pozycję **Przekaż plik XML** i Dołącz wcześniej pobrany **kod XML metadanych Federacji** .
1. Kliknij przycisk **Dodaj metadane** , aby zapisać zmiany.

    ![Zrzut ekranu narzędzia samoobsługowego logowania jednokrotnego Concur](./media/concur-travel-and-expense-tutorial/add-metadata-concur-self-service-tool.png)

### <a name="create-concur-travel-and-expense-test-user"></a>Utwórz użytkownika testowego podróży i wydatków Concur

W tej sekcji utworzysz użytkownika o nazwie B. Simon w Concur podróży i obrachunku. Współpracuj z zespołem pomocy technicznej Concur, aby dodać użytkowników z platformy podróży i wydatków Concur. Użytkownicy muszą być tworzone i aktywowana, aby używać logowania jednokrotnego. 

> [!NOTE]
> B. identyfikator logowania Concur Simon musi pasować do unikatowego identyfikatora B. Simon w usłudze Azure AD. Na przykład jeśli Simon usługi Azure AD `B.Simon@contoso.com`identyfikator. B. Simon identyfikator logowania Concur musi być również `B.Simon@contoso.com`. 

## <a name="configure-concur-mobile-sso"></a>Konfigurowanie logowania jednokrotnego w usłudze Concur Mobile
Aby włączyć logowanie jednokrotne w usłudze Concur Mobile, należy przyznać Concur obsługę **adresu URL dostępu użytkownika**zespołu. Wykonaj poniższe kroki, aby uzyskać **adres URL dostępu użytkownika** z usługi Azure AD:
1. Przejdź do **aplikacji dla przedsiębiorstw**
1. Kliknij pozycję **Concur podróże i wydatki**
1. Kliknij pozycję **Właściwości**
1. Kopiuj **adres URL dostępu użytkowników** i nadaj temu adresowi URL obsługę Concur

> [!NOTE]
> Opcja samoobsługowego konfigurowania logowania jednokrotnego jest niedostępna, aby współpraca z zespołem pomocy technicznej Concur mógł włączyć logowanie jednokrotne dla urządzeń przenośnych. 

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka podróży i wydatków usługi Concur w panelu dostępu należy automatycznie zalogować się do Concur podróży i wydatków, dla których skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [ Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj Concur podróży i wydatków dzięki usłudze Azure AD](https://aad.portal.azure.com/)

