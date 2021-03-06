---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: notification-hubs
author: sethmanheim
ms.service: notification-hubs
ms.topic: include
ms.date: 02/10/2020
ms.author: sethm
ms.custom: include file
ms.openlocfilehash: bf2596f5a8e287799285f97f3d1be9f3fe10f644
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77123135"
---
## <a name="generate-the-certificate-signing-request-file"></a>Generowanie pliku żądania podpisywania certyfikatów

Usługa powiadomień push firmy Apple (APN) używa certyfikatów do uwierzytelniania powiadomień wypychanych. Wykonaj instrukcje, aby utworzyć niezbędny certyfikat powiadomień wypychanych umożliwiający ich wysyłanie i odbieranie. Więcej informacji dotyczących tych pojęć można znaleźć w oficjalnej dokumentacji usługi [Apple Push Notification Service](https://developer.apple.com/library/archive/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html).

Wygeneruj plik csr (Certificate Signing Request), którego firma Apple używa do generowania podpisanego certyfikatu wypychania.

1. Na komputerze Mac uruchom narzędzie Keychain Access. Można go otworzyć z folderu **Narzędzia** lub **innego** folderu na launchpadzie.

1. Wybierz **pozycję Dostęp do pęku kluczy**, rozwiń pozycję Asystent **certyfikatu**, a następnie wybierz pozycję **Żądaj certyfikatu od urzędu certyfikacji.**

    ![Żądanie nowego certyfikatu za pomocą narzędzia Keychain Access](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-request-cert-from-ca.png)

   > [!NOTE]
   > Domyślnie dostęp do pęku kluczy wybiera pierwszy element na liście. Może to być problem, jeśli jesteś w kategorii **Certyfikaty** i **Apple Worldwide Developer Relations Certification Authority** nie jest pierwszym elementem na liście. Przed wygenerowaniem csr (żądanie podpisania certyfikatu) upewnij się, że jest to element niebędący kluczem lub wybrany jest klucz **urzędu certyfikacji Apple Worldwide Developer Relations.**

1. Wybierz **adres e-mail użytkownika**, wprowadź wartość **Nazwa pospolitą,** upewnij się, że określono **zapisane na dysku,** a następnie wybierz pozycję **Kontynuuj**. Pozostaw **adres e-mail urzędu certyfikacji** pusty, ponieważ nie jest wymagany.

    ![Wymagane informacje o certyfikacie](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-csr-info.png)

1. Wprowadź nazwę pliku CSR w polu **Zapisz jako**, wybierz lokalizację w **obszarze Gdzie**, a następnie wybierz pozycję **Zapisz**.

    ![Wybieranie nazwy pliku certyfikatu](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-save-csr.png)

    Ta akcja zapisuje plik CSR w wybranej lokalizacji. Domyślną lokalizacją jest **Pulpit**. Zapamiętaj wybraną lokalizację pliku.

Następnie zarejestruj aplikację w usłudze Apple, włącz powiadomienia wypychane i przekaż wyeksportowany csr, aby utworzyć certyfikat wypychania.

## <a name="register-your-app-for-push-notifications"></a>Rejestrowanie aplikacji dla usługi powiadomień wypychanych

Aby wysyłać powiadomienia wypychane do aplikacji na iOS, zarejestruj aplikację w usłudze Apple, a także zarejestruj powiadomienia wypychane.  

1. Jeśli aplikacja nie została jeszcze zarejestrowana, przejdź do [portalu aprowizacji systemu iOS](https://go.microsoft.com/fwlink/p/?LinkId=272456) w Centrum deweloperów firmy Apple. Zaloguj się do portalu za pomocą konta Apple ID i wybierz **pozycję Identyfikatory**. Następnie **+** wybierz, aby zarejestrować nową aplikację.

    ![Strona identyfikatorów aplikacji w portalu aprowizowania dla systemu iOS](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-ios-appids.png)

2. Na ekranie **Zarejestruj nowy identyfikator** wybierz przycisk opcji **Identyfikatory aplikacji.** Następnie wybierz pozycję **Kontynuuj**.

    ![Portal inicjowania obsługi administracyjnej systemu iOS rejestruje nową stronę identyfikatora](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-ios-appids-new.png)

3. Zaktualizuj następujące trzy wartości dla nowej aplikacji, a następnie wybierz pozycję **Kontynuuj:**

   * **Opis:** Wpisz opisową nazwę aplikacji.

   * **Identyfikator pakietu:** Wprowadź identyfikator pakietu formularza **Identyfikator organizacji.Nazwa produktu,** jak wspomniano w [Przewodniku dystrybucji aplikacji](https://help.apple.com/xcode/mac/current/#/dev91fe7130a). Wartości *identyfikatora organizacji* i *nazwy produktu* muszą być zgodne z identyfikatorem organizacji i nazwą produktu używanym podczas tworzenia projektu Xcode. Na poniższym zrzucie ekranu wartość **NotificationHubs** jest używana jako identyfikator organizacji, a wartość **GetStarted** jest używana jako nazwa produktu. Upewnij się, że wartość **identyfikatora pakietu** jest zgodna z wartością w projekcie Xcode, dzięki czemu xcode używa poprawnego profilu publikowania.

      ![Strona identyfikatora rejestru portalu aprowizacji systemu iOS](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-appid-bundle.png)

   * **Powiadomienia wypychania:** Sprawdź opcję **Powiadomienia wypychania** w sekcji **Możliwości.**

      ![Formularz rejestracji nowego identyfikatora aplikacji](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-appid-push.png)

      Ta akcja generuje identyfikator aplikacji i żąda potwierdzenia informacji. Wybierz **przycisk Kontynuuj**, a następnie wybierz pozycję Zarejestruj **się,** aby potwierdzić nowy identyfikator aplikacji.

      ![Potwierdź nowy identyfikator aplikacji](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-appid-register.png)

      Po **wybraniu**opcji Zarejestruj się nowy identyfikator aplikacji zostanie wyświetlony jako element zamówienia na stronie **Certyfikaty, identyfikatory & profile.**

4. Na stronie **Certyfikaty identyfikatory & profile** w obszarze **Identyfikatory**znajdź utworzony właśnie element zamówienia identyfikator aplikacji i wybierz jego wiersz, aby wyświetlić ekran **Edytuj konfigurację identyfikatora aplikacji.**

5. Przewiń w dół do zaznaczonej opcji **Powiadomień wypychanych,** a następnie wybierz pozycję **Konfiguruj,** aby utworzyć certyfikat.

    ![Edytowanie strony identyfikatorów aplikacji](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-edit-appid.png)

6. Zostanie wyświetlone okno **Certyfikaty SSL usługi powiadomień wypychanych firmy Apple.** Wybierz przycisk **Utwórz certyfikat** w sekcji **Deweloperzy certyfikat SSL.**

    ![Przycisk tworzenia certyfikatu dla identyfikatora aplikacji](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-create-cert.png)

    Zostanie wyświetlony ekran **Utwórz nowy certyfikat.**

    > [!NOTE]
    > Instrukcje w tym samouczku obejmują użycie certyfikatu deweloperskiego. Ten sam proces jest używany podczas rejestrowania certyfikatu produkcyjnego. Należy po prostu pamiętać, aby używać tego samego typu certyfikatu podczas wysyłania powiadomień.

1. Wybierz **pozycję Wybierz plik**, przejdź do lokalizacji, w której plik CSR został zapisany z pierwszego zadania, a następnie kliknij dwukrotnie nazwę certyfikatu, aby go załadować. Następnie wybierz pozycję **Kontynuuj**.

1. Po tym, jak portal utworzy certyfikat, wybierz przycisk **Pobierz.** Zapisz certyfikat i zapamiętaj lokalizację, w której został zapisany.

    ![Strona pobierania wygenerowanego certyfikatu](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-download-cert.png)

    Certyfikat zostanie pobrany i zapisany na komputerze w folderze **Pobrane.**

    ![Lokalizowanie pliku certyfikatu w folderze Pobrane](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-downloaded.png)

    > [!NOTE]
    > Domyślnie pobrany certyfikat dewelopera nosi nazwę **aps_development.cer**.

1. Kliknij dwukrotnie pobrany certyfikat powiadomień wypychanych **aps_development.cer**. Ta akcja powoduje zainstalowanie nowego certyfikatu w narzędziu Keychain, jak przedstawiono na poniższym rysunku:

    ![Lista certyfikatów narzędzia Keychain Access z nowym certyfikatem](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-in-keychain.png)

    > [!NOTE]
    > Chociaż nazwa w certyfikacie może być inna, nazwa będzie poprzedzony **usługami push systemu Apple Development iOS**.

1. W narzędziu Keychain Access kliknij prawym przyciskiem myszy nowy certyfikat powiadomień wypychanych utworzony w kategorii **Certyfikaty**. Wybierz **pozycję Eksportuj**, nazwij plik, wybierz format **p12,** a następnie wybierz pozycję **Zapisz**.

    ![Eksportowanie certyfikatu w formacie p12](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-export-cert-p12.png)

    Można chronić certyfikat hasłem, ale jest to opcjonalne. Kliknij **przycisk OK,** jeśli chcesz pominąć tworzenie haseł. Zanotuj nazwę pliku i lokalizację wyeksportowanego certyfikatu .p12. Są one używane do włączania uwierzytelniania za pomocą sieci APN.

    > [!NOTE]
    > Nazwa i lokalizacja pliku p12 może się różnić od tego, co jest na zdjęciu w tym samouczku.

## <a name="create-a-provisioning-profile-for-the-app"></a>Tworzenie profilu inicjowania obsługi dla aplikacji

1. Wróć do [portalu inicjowania obsługi administracyjnej systemu iOS](https://go.microsoft.com/fwlink/p/?LinkId=272456), wybierz **pozycję Certyfikaty, Identyfikatory & profile**, wybierz pozycję **Profile** z lewego menu, a następnie wybierz pozycję, **+** aby utworzyć nowy profil. Zostanie wyświetlony ekran **Zarejestruj nowy profil inicjowania obsługi administracyjnej.**

1. Wybierz **pozycję Rozwój aplikacji systemu iOS** w obszarze **Rozwój** jako typ profilu inicjowania obsługi administracyjnej, a następnie wybierz pozycję **Kontynuuj**.

    ![Lista profili aprowizacji](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-provisioning-profile.png)

1. Następnie wybierz identyfikator aplikacji utworzony z listy rozwijanej **Identyfikator aplikacji** i wybierz pozycję **Kontynuuj**.

    ![Wybieranie identyfikatora aplikacji](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-select-appid-for-provisioning.png)

1. W oknie **Wybierz certyfikaty** wybierz certyfikat dewelopera używany do podpisywania kodu i wybierz pozycję **Kontynuuj**. Ten certyfikat nie jest utworzonym certyfikatem wypychania. Jeśli ktoś nie istnieje, należy go utworzyć. Jeśli certyfikat istnieje, przejdź do następnego kroku. Aby utworzyć certyfikat dewelopera, jeśli taki nie istnieje:

    1. Jeśli widzisz, **że nie są dostępne żadne certyfikaty,** wybierz pozycję **Utwórz certyfikat**.
    2. W sekcji **Oprogramowanie** wybierz pozycję **Apple Development**. Następnie wybierz pozycję **Kontynuuj**.
    3. Na ekranie **Utwórz nowy certyfikat** wybierz pozycję **Wybierz plik**.
    4. Przejdź do certyfikatu **żądanie podpisywania certyfikatu** utworzonego wcześniej, wybierz go, a następnie wybierz pozycję **Otwórz**.
    5. Wybierz przycisk **Kontynuuj**.
    6. Pobierz certyfikat dewelopera i zapamiętaj lokalizację, w której został zapisany.

1. Wróć do strony **Certyfikaty, Identyfikatory & Profile,** wybierz **profile** z lewego **+** menu, a następnie wybierz, aby utworzyć nowy profil. Zostanie wyświetlony ekran **Zarejestruj nowy profil inicjowania obsługi administracyjnej.**

1. W oknie **Wybierz certyfikaty** wybierz właśnie utworzony certyfikat dewelopera. Następnie wybierz pozycję **Kontynuuj**.

1. Następnie wybierz urządzenia do testowania i wybierz pozycję **Kontynuuj**.

1. Na koniec wybierz nazwę profilu w **obszarze Nazwa profilu inicjowania obsługi administracyjnej**i wybierz pozycję **Generuj**.

    ![Wybieranie nazwy profilu aprowizacji](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-name-profile.png)

1. Po utworzeniu nowego profilu inicjowania obsługi administracyjnej wybierz pozycję **Pobierz**. Zapamiętaj lokalizację, do której jest zapisana.

1. Przejdź do lokalizacji profilu inicjowania obsługi administracyjnej, a następnie kliknij go dwukrotnie, aby zainstalować go na komputerze deweloperskim Xcode.

## <a name="create-a-notification-hub"></a>Tworzenie centrum powiadomień

W tej sekcji utworzysz centrum powiadomień i konfigurujesz uwierzytelnianie za pomocą apn przy użyciu wcześniej utworzonego certyfikatu wypychania p12. Jeśli chcesz użyć utworzonego już centrum powiadomień, możesz przejść do kroku 5.

[!INCLUDE [notification-hubs-portal-create-new-hub](notification-hubs-portal-create-new-hub.md)]

## <a name="configure-your-notification-hub-with-apns-information"></a>Konfigurowanie centrum powiadomień za pomocą informacji o sieciach APN

1. W obszarze **Usługi powiadomień**wybierz pozycję **Apple (APNS)**.

1. Wybierz pozycję **Certyfikat**.

1. Wybierz ikonę pliku.

1. Wybierz wcześniej wyeksportowany plik p12, a następnie wybierz pozycję **Otwórz**.

1. W razie potrzeby określ poprawne hasło.

1. Wybierz tryb **Piaskownica**. Trybu **Produkcja** używaj wyłącznie wtedy, gdy chcesz wysyłać powiadomienia push do użytkowników, którzy kupili Twoją aplikację w sklepie.

    ![Konfigurowanie certyfikacji APNs w witrynie Azure Portal](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-apple-config-cert.png)

1. Wybierz **pozycję Zapisz**.

Centrum powiadomień zostało skonfigurowane za pomocą sieci APN. Masz również parametry połączenia, aby zarejestrować aplikację i wysyłać powiadomienia wypychane.
