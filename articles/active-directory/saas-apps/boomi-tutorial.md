---
title: 'Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z usługą Boomi | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i platformą Boomi.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 40d034ff-7394-4713-923d-1f8f2ed8bf36
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 02/07/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a22a36d5e6c36008c3a574cbcf9be8ec4f52b82b
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/08/2020
ms.locfileid: "77086458"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-boomi"></a>Samouczek: Azure Active Directory integracji logowania jednokrotnego (SSO) z usługą Boomi

W tym samouczku dowiesz się, jak zintegrować usługę Boomi z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi Boomi z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do Boomi.
* Zezwól użytkownikom na automatyczne logowanie się do usługi Boomi przy użyciu kont w usłudze Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) Boomi.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Platforma Boomi obsługuje logowanie jednokrotne inicjowane przez **dostawcę tożsamości**
* Po skonfigurowaniu Boomi można wymusić kontrolki sesji, które chronią eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolki sesji wykraczają poza dostęp warunkowy. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-boomi-from-the-gallery"></a>Dodawanie platformy Boomi z galerii

Aby skonfigurować integrację platformy Boomi z usługą Azure AD, musisz dodać platformę Boomi z galerii do swojej listy zarządzanych aplikacji SaaS.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) przy użyciu służbowego lub osobistego konta Microsoft.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Boomi** w polu wyszukiwania.
1. Wybierz pozycję **Boomi** from panel wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.


## <a name="configure-and-test-azure-ad-single-sign-on-for-boomi"></a>Skonfiguruj i przetestuj Logowanie jednokrotne w usłudze Azure AD dla Boomi

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą Boomi przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w Boomi.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą Boomi, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    * **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    * **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj Logowanie jednokrotne](#configure-boomi-sso)** w usłudze Boomi, aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    * **[Utwórz użytkownika testowego Boomi](#create-boomi-test-user)** , aby dysponować odpowiednikiem B. Simon w Boomi, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie logowania jednokrotnego w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **Boomi** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja protokołu SAML** , jeśli masz **plik metadanych dostawcy usług** i chcesz skonfigurować w trybie zainicjowania programu **dostawcy tożsamości** , wykonaj następujące czynności:

    a. Kliknij pozycję **Przekaż plik metadanych**.

    ![Przekazywanie pliku metadanych](common/upload-metadata.png)

    b. Kliknij **logo folderu**, aby wybrać plik metadanych, a następnie kliknij pozycję **Przekaż**.

    ![wybierz plik metadanych](common/browse-upload-metadata.png)

    c. Po pomyślnym przekazaniu pliku metadanych wartości **identyfikatorów** i **adresów URL odpowiedzi** są automatycznie wypełniane w sekcji Podstawowa konfiguracja SAML.

    ![image](common/idp-intiated.png)

    > [!Note]
    > **Plik metadanych dostawcy usług** zostanie pobrany z sekcji **Konfigurowanie logowania jednokrotnego Boomi** , która została omówiona w dalszej części tego samouczka. Jeśli wartości **Identyfikator** i **Adres URL odpowiedzi** nie zostaną automatycznie wypełnione, wpisz te wartości ręcznie zgodnie z wymaganiami.

1. Aplikacja Boomi oczekuje potwierdzeń SAML w określonym formacie, co wymaga dodania niestandardowych mapowań atrybutów do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych.

    ![image](common/default-attributes.png)

1. Oprócz powyższych, aplikacja Boomi oczekuje kilku atrybutów do przekazania z powrotem w odpowiedzi SAML, które przedstawiono poniżej. Te atrybuty są również wstępnie wypełnione, ale można je sprawdzić zgodnie z wymaganiami.

    | Name (Nazwa) |  Atrybut źródłowy|
    | ---------------|  --------- |
    | FEDERATION_ID | user.mail |

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **certyfikat (base64)** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. W sekcji **Konfigurowanie Boomi** skopiuj odpowiednie adresy URL na podstawie wymagania.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w Azure Portal o nazwie B. Simon.

1. W lewym okienku w Azure Portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. Wybierz przycisk **Nowy użytkownik** w górnej części ekranu.
1. We właściwościach **użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W polu **Nazwa użytkownika** wprowadź username@companydomain.extension. Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi Boomi.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **Boomi**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

## <a name="configure-boomi-sso"></a>Konfigurowanie logowania jednokrotnego Boomi

1. W innym oknie przeglądarki sieci Web Zaloguj się do firmowej witryny Boomi jako administrator.

1. Przejdź do pozycji **Nazwa firmy** i przejdź do pozycji **Set up** (Konfigurowanie).

1. Kliknij kartę **SSO Options** (Opcje logowania jednokrotnego), a następnie wykonaj poniższe kroki.

    ![Konfigurowanie logowania jednokrotnego po stronie aplikacji](./media/boomi-tutorial/tutorial_boomi_11.png)

    a. Zaznacz pole wyboru **Enable SAML Single Sign-On** (Włącz logowanie jednokrotne SAML).

    b. Kliknij przycisk **Import** (Importuj), aby przekazać pobrany certyfikat z usługi Azure AD do pola **Identity Provider Certificate** (Certyfikat dostawcy tożsamości).

    c. W polu tekstowym **Identity Provider Login URL** (Adres URL logowania dostawcy tożsamości) wstaw wartość**adresu URL logowania** z okna konfiguracji aplikacji usługi Azure AD.

    d. W polu **Lokalizacja identyfikatora federacyjnego**wybierz pozycję **identyfikator Federacji w FEDERATION_ID** przycisk radiowy elementu atrybutu.

    e. Skopiuj **adres URL metadanych AtomSphere**, przejdź do **adresu URL metadanych** za pomocą wybranej przeglądarki i Zapisz dane wyjściowe do pliku. Przekaż **adres URL metadanych** w sekcji **podstawowe konfiguracje języka SAML** w Azure Portal.

    f. Kliknij przycisk **Save** (Zapisz).

### <a name="create-boomi-test-user"></a>Tworzenie użytkownika testowego platformy Boomi

Aby umożliwić użytkownikom usługi Azure AD logowanie się w usłudze Boomi, muszą one być obsługiwane w Boomi. W przypadku platformy Boomi aprowizowanie jest zadaniem ręcznym.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Aby aprowizować konto użytkownika, wykonaj następujące kroki:

1. Zaloguj się do firmowej witryny Boomi jako administrator.

1. Po zalogowaniu się przejdź do pozycji **User Management** (Zarządzanie użytkownikami) i wybierz pozycję **Users** (Użytkownicy).

    ![Użytkownicy](./media/boomi-tutorial/tutorial_boomi_001.png "Użytkownicy")

1. Kliknij ikonę **+** , aby otworzyć okno dialogowe **Add/Maintain User Roles** (Dodawanie/obsługa ról użytkowników).

    ![Użytkownicy](./media/boomi-tutorial/tutorial_boomi_002.png "Użytkownicy")

    ![Użytkownicy](./media/boomi-tutorial/tutorial_boomi_003.png "Użytkownicy")

    a. W polu tekstowym **User e-mail address** (Adres e-mail użytkownika) wpisz adres e-mail użytkownika, taki jak B.Simon@contoso.com.

    b. W polu tekstowym **imię i nazwisko** wpisz imię użytkownika jak B.

    c. W polu tekstowym **Last Name** (Nazwisko) wpisz nazwisko użytkownika, takie jak Simon.

    d. Wprowadź **identyfikator federacyjny** użytkownika. Każdy użytkownik musi mieć identyfikator federacyjny, który w sposób unikatowy identyfikuje użytkownika w ramach konta.

    e. Przypisz do użytkownika rolę **Standard User** (Użytkownik standardowy). Nie przypisuj roli administratora, ponieważ będzie ona mieć dostęp do normalnego dostępu do atmosfery, a także dostępu do logowania jednokrotnego.

    f. Kliknij przycisk **OK**.

    > [!NOTE]
    > Użytkownik nie otrzyma wiadomości e-mail z powiadomieniem powitalnym zawierającym hasło, które może służyć do logowania się do konta AtomSphere, ponieważ ich hasło jest zarządzane przez dostawcę tożsamości. Do aprowizowania kont użytkowników usługi Azure Active Directory możesz użyć dowolnych innych interfejsów API lub narzędzi do tworzenia kont użytkowników platformy Boomi oferowanych przez tę platformę.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Boomi w panelu dostępu powinno nastąpić automatyczne zalogowanie do platformy Boomi, dla której skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [ Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Co to jest kontrola sesji w Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Wypróbuj Boomi z usługą Azure AD](https://aad.portal.azure.com/)
