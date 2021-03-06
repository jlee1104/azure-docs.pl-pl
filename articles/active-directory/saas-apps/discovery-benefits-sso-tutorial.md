---
title: 'Samouczek Azure Active Directory: integracja logowania jednokrotnego (SSO) z usługą Discovery Zalety rejestracji jednokrotnej | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i odnajdywanie w usłudze SSO.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a788cd07-0eed-4067-b79d-19b840e8836d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/03/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 64c4a6811ef5d7ed4f29c7dae89561616895a42a
ms.sourcegitcommit: b4665f444dcafccd74415fb6cc3d3b65746a1a31
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2019
ms.locfileid: "72266143"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-discovery-benefits-sso"></a>Samouczek: integracja z logowaniem jednokrotnym (SSO) Azure Active Directory z użyciem funkcji odnajdywania Logowanie jednokrotne

W ramach tego samouczka dowiesz się, jak zintegrować usługę odnajdowania z usługą logowania jednokrotnego w usłudze Azure Active Directory (Azure AD). Po zintegrowaniu usługi odnajdowania z logowaniem jednokrotnym w usłudze Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do funkcji odnajdywania Logowanie jednokrotne.
* Zezwól użytkownikom na automatyczne logowanie się w celu odnajdywania usług logowania jednokrotnego przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Funkcja odnajdywania korzysta z usługi rejestracji jednokrotnej logowania jednokrotnego (SSO).

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Usługa Discovery Zalety rejestracji jednokrotnej obsługuje **dostawcy tożsamości** zainicjowane Logowanie jednokrotne

> [!NOTE]
> Identyfikator tej aplikacji to stała wartość ciągu, dlatego można skonfigurować tylko jedno wystąpienie w jednej dzierżawie.

## <a name="adding-discovery-benefits-sso-from-the-gallery"></a>Dodawanie usługi Discovery Zalety rejestracji jednokrotnej z galerii

Aby skonfigurować integrację funkcji odnajdywania w usłudze Azure AD, należy dodać do listy aplikacji zarządzanych SaaS zalety funkcji odnajdywania Logowanie jednokrotne z galerii.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) przy użyciu służbowego lub osobistego konta Microsoft.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Discovery Zalety rejestracji jednokrotnej** w polu wyszukiwania.
1. Wybierz pozycję **odnajdywanie usługi Logowanie jednokrotne** z panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-discovery-benefits-sso"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD na potrzeby odnajdywania Logowanie jednokrotne

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą odnajdywania za pomocą logowania jednokrotnego dla użytkownika testowego o nazwie **B. Simon** Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w usłudze Discovery — Logowanie jednokrotne.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD z użyciem funkcji odnajdywania Logowanie jednokrotne, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj zalety odnajdywania](#configure-discovery-benefits-sso-sso)** Logowanie jednokrotne w usłudze, aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    1. **[Tworzenie usługi Discovery zalety użytkownika testowego rejestracji jednokrotnej](#create-discovery-benefits-sso-test-user)** w celu uzyskania odpowiedników B. Simon w usłudze Discovery, która jest powiązana z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie logowania jednokrotnego w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie **odnajdywanie aplikacji rejestracji jednokrotnej dotyczącej odnajdowania** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja SAML** aplikacja została wstępnie skonfigurowana w trybie inicjalizacji programu **dostawcy tożsamości** i wymagane adresy URL są już wstępnie wypełnione na platformie Azure. Użytkownik musi zapisać konfigurację, klikając przycisk **zapisz** .

1. Aplikacja do odnajdowania korzysta z funkcji rejestracji jednokrotnej w określonym formacie, co wymaga dodania mapowań atrybutów niestandardowych do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych. Kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe Atrybuty użytkownika.

    ![image](common/edit-attribute.png)

    a. Kliknij ikonę **Edytuj** , aby otworzyć okno dialogowe **unikatowy identyfikator użytkownika (identyfikator nazwy)** .

    ![Konfiguracja rejestracji jednokrotnej w usłudze Discovery](./media/discovery-benefits-sso-tutorial/attribute01.png)

    ![Konfiguracja rejestracji jednokrotnej w usłudze Discovery](./media/discovery-benefits-sso-tutorial/attribute02.png)

    b. Kliknij ikonę **Edytuj** , aby otworzyć okno dialogowe **Zarządzanie transformacjęm** .

    d. W polu tekstowym **transformacja** wpisz **ToUppercase ()** widoczny dla tego wiersza.

    d. W polu tekstowym **parametr 1** wpisz parametr, taki jak `<Name Identifier value>`.

    e. Kliknij pozycję **Add** (Dodaj).

    > [!NOTE]
    > Funkcja odnajdywania w ramach rejestracji jednokrotnej wymaga, aby stała wartość ciągu została przeniesiona w polu **unikatowy identyfikator użytkownika (identyfikator nazwy)** w celu uzyskania tej integracji. Usługa Azure AD obecnie nie obsługuje tej funkcji, więc w celu obejścia tego problemu można użyć **ToUpper** lub **ToLower** przekształceń NameID, aby ustawić ustaloną wartość ciągu, jak pokazano powyżej na zrzucie ekranu.

    f. Automatycznie wypełniono dodatkowe oświadczenia wymagane do konfiguracji rejestracji jednokrotnej (`SSOInstance` i `SSOID`). Użyj ikony **Edytuj** , aby zamapować wartości zgodnie z potrzebami organizacji.

    ![Konfiguracja rejestracji jednokrotnej w usłudze Discovery](./media/discovery-benefits-sso-tutorial/attribute03.png)

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **certyfikat (base64)** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link pobierania certyfikatu](common/certificatebase64.png)

1. W sekcji **Konfigurowanie zalet rejestracji Jednokrotnej dla odnajdywania** skopiuj odpowiednie adresy URL na podstawie wymagań.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

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

W tej sekcji zostanie włączone Logowanie jednokrotne w usłudze B. Simon, przyznając dostęp do usługi Discovery w celu skorzystania z usług rejestracji pojedynczych.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **odnajdywanie — usługa rejestracji jednokrotnej**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link "Użytkownicy i grupy"](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

## <a name="configure-discovery-benefits-sso-sso"></a>Skonfiguruj zalety odnajdywania SSO logowania jednokrotnego

Aby skonfigurować Logowanie jednokrotne za pomocą logowania **JEDNOkrotnego** w usłudze Discovery, musisz wysłać pobrany **certyfikat (base64)** i odpowiednie skopiowane adresy URL z usługi Azure Portal do [odnajdywania usługi rejestracji usługi SSO](mailto:Jsimpson@DiscoveryBenefits.com). Ustawiają to ustawienie, aby były prawidłowo po obu stronach połączenia logowania jednokrotnego SAML.

### <a name="create-discovery-benefits-sso-test-user"></a>Utwórz użytkownika testowego z użyciem funkcji odnajdywania

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w ramach odnajdywania Logowanie jednokrotne. Pracuj z usługą [Discovery zalety zespół pomocy technicznej rejestracji jednokrotnej](mailto:Jsimpson@DiscoveryBenefits.com) , aby dodać użytkowników w usłudze Discovery Zalety rejestracji jednokrotnej. Użytkownicy muszą być tworzone i aktywowana, aby używać logowania jednokrotnego.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Logowanie jednokrotne w panelu dostępu należy automatycznie zalogować się do usługi odnajdywania Logowanie jednokrotne, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [ Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj usługę Discovery — Logowanie jednokrotne w usłudze Azure AD](https://aad.portal.azure.com/)

