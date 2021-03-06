---
title: Szybki start aplikacji java dla użytkowników platformy tożsamości firmy Microsoft | Azure
description: Dowiedz się, jak zaimplementować logowanie microsoftu w aplikacji Java Web App przy użyciu funkcji OpenID Connect
services: active-directory
author: sangonzal
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/09/2019
ms.author: sagonzal
ms.custom: aaddev, scenarios:getting-started, languages:Java
ms.openlocfilehash: c2f3272beb463a16f9488139b6c3a45febae6493
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79529638"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-a-java-web-app"></a>Szybki start: dodawanie logowania za pomocą firmy Microsoft do aplikacji sieci Web Java

W tym przewodniku Szybki start dowiesz się, jak zintegrować aplikację sieci Web Java z platformą tożsamości firmy Microsoft. Aplikacja zaloguje się do użytkownika, pobierze token dostępu, aby wywołać interfejs API programu Microsoft Graph i zażąda interfejsu API programu Microsoft Graph.

Po zakończeniu tego przewodnika Szybki start aplikacja będzie akceptować logowania osobistych kont Microsoft (w tym outlook.com, live.com i innych) oraz kont służbowych lub szkolnych od dowolnej firmy lub organizacji korzystającej z usługi Azure Active Directory. (Zobacz [Jak działa przykład](#how-the-sample-works) dla ilustracji).

## <a name="prerequisites"></a>Wymagania wstępne

Aby uruchomić ten przykład, potrzebujesz:

- [Java Development Kit (JDK)](https://openjdk.java.net/) 8 lub więcej, i [Maven](https://maven.apache.org/).

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>Rejestrowanie i pobieranie aplikacji Szybki start
> Dostępne są dwie opcje uruchomienia aplikacji szybki start: ekspresowa (opcja 1) lub ręczna (opcja 2)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Opcja 1. Zarejestrowanie i automatyczne skonfigurowanie aplikacji, a następnie pobranie przykładowego kodu
>
> 1. Przejdź do [witryny Azure portal — rejestracje aplikacji](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps).
> 1. Wprowadź nazwę aplikacji i wybierz pozycję **Zarejestruj**.
> 1. Postępuj zgodnie z instrukcjami, aby pobrać i automatycznie skonfigurować nową aplikację.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Opcja 2. Zarejestrowanie i ręczne skonfigurowanie aplikacji oraz przykładowego kodu
>
> #### <a name="step-1-register-your-application"></a>Krok 1. Rejestrowanie aplikacji
>
> Aby zarejestrować aplikację i ręcznie dodać informacje na temat rejestracji aplikacji do rozwiązania, wykonaj następujące czynności:
>
> 1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft.
> 1. Jeśli Twoje konto umożliwia dostęp do więcej niż jednej dzierżawy, wybierz konto w prawym górnym rogu, a następnie ustaw sesję portalu na odpowiednią dzierżawę usługi Azure AD.
>
> 1. Przejdź do platformy tożsamości firmy Microsoft dla deweloperów [Rejestracje aplikacji.](/azure/active-directory/develop/)
> 1. Wybierz **pozycję Nowa rejestracja**.
> 1. Po wyświetleniu strony **Rejestrowanie aplikacji** podaj informacje dotyczące rejestracji aplikacji:
>    - W sekcji **Nazwa** podaj znaczącą nazwę aplikacji, która będzie wyświetlana użytkownikom aplikacji, na przykład `java-webapp`.
>    - Pozostaw **przekierowanie identyfikatora URI** puste na razie i wybierz pozycję **Zarejestruj**.
> 1. Na **stronie Przegląd** znajdź **identyfikator aplikacji (klienta)** i wartości **identyfikatora katalogu (dzierżawy)** aplikacji. Skopiuj te wartości na później.
> 1. Wybierz **uwierzytelnianie** z menu, a następnie dodaj następujące informacje:
>    - W **przekieruj identyfikatory URI**, dodaj `https://localhost:8080/msal4jsample/secure/aad` i `https://localhost:8080/msal4jsample/graph/me`.
>    - Wybierz **pozycję Zapisz**.
> 1. Wybierz **certyfikaty & wpisy tajne** z menu i w sekcji **Wpisy tajne klienta,** kliknij **nowy klucz tajny klienta:**
>
>    - Wpisz opis klucza (na przykład klucz tajny aplikacji).
>    - Wybierz czas trwania **klucza W ciągu 1 roku**.
>    - Wartość klawisza zostanie wyświetlone po **wybraniu**opcji Dodaj .
>    - Skopiuj wartość klucza na później. Ta wartość klucza nie będą wyświetlane ponownie, ani można pobrać za pomocą innych środków, więc należy go zarejestrować tak szybko, jak to jest widoczne z witryny Azure portal.
>
> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Krok 1. Konfigurowanie aplikacji w witrynie Azure Portal
>
> Aby przykładowy kod dla tego szybkiego startu działał, musisz:
>
> 1. Dodaj adresy `https://localhost:8080/msal4jsample/secure/aad` URL `https://localhost:8080/msal4jsample/graph/me`odpowiedzi jako i .
> 1. Utwórz klucz tajny klienta.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Wprowadź zmiany automatycznie]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Już skonfigurowano](media/quickstart-v2-aspnet-webapp/green-check.png) Twoja aplikacja została skonfigurowana za pomocą tych atrybutów.

#### <a name="step-2-download-the-code-sample"></a>Krok 2: Pobierz przykładowy kod
> [!div renderon="docs"]
> [Pobierz przykład kodu](https://github.com/Azure-Samples/ms-identity-java-webapp/archive/master.zip)

> [!div class="sxs-lookup" renderon="portal"]
> Pobierz projekt i wyodrębnij plik zip do folderu lokalnego bliżej folderu głównego - na przykład **C:\Azure-Samples**
> 
> Aby użyć https z localhost, wprowadź właściwości server.ssl.key. Aby wygenerować certyfikat z podpisem własnym, użyj narzędzia keytool (zawartego w środowiskou JRE).
>
>  ```
>   Example:
>   keytool -genkeypair -alias testCert -keyalg RSA -storetype PKCS12 -keystore keystore.p12 -storepass password
>
>   server.ssl.key-store-type=PKCS12  
>   server.ssl.key-store=classpath:keystore.p12  
>   server.ssl.key-store-password=password  
>   server.ssl.key-alias=testCert
>   ```
>   Umieść wygenerowany plik magazynu kluczy w folderze "zasoby".
   
> [!div renderon="portal" id="autoupdate" class="nextstepaction"]
> [Pobierz przykładowy kod](https://github.com/Azure-Samples/ms-identity-java-webapp/archive/master.zip)

> [!div renderon="docs"]
> #### <a name="step-3-configure-the-code-sample"></a>Krok 3: Konfigurowanie przykładu kodu
> 1. Wyodrębnij plik zip do folderu lokalnego.
> 1. Jeśli używasz zintegrowanego środowiska programistycznego, otwórz próbkę w swoim ulubionym ŚRODOWISKU IDE (opcjonalnie).
> 1. Otwórz plik application.properties, który można znaleźć w folderze src/main/resources/ i zastąp wartość pól *aad.clientId*, *aad.authority* i *aad.secretKey* odpowiednimi wartościami **Identyfikator aplikacji,** **Identyfikator dzierżawy** i **klucz tajny klienta** jako następujący:
>
>    ```file
>    aad.clientId=Enter_the_Application_Id_here
>    aad.authority=https://login.microsoftonline.com/Enter_the_Tenant_Info_Here/
>    aad.secretKey=Enter_the_Client_Secret_Here
>    aad.redirectUriSignin=https://localhost:8080/msal4jsample/secure/aad
>    aad.redirectUriGraph=https://localhost:8080/msal4jsample/graph/me
>    aad.msGraphEndpointHost="https://graph.microsoft.com/"
>    ```
> Gdzie:
>
> - `Enter_the_Application_Id_here` jest identyfikatorem dla zarejestrowanej aplikacji.
> - `Enter_the_Client_Secret_Here`- jest **kluczem tajnym klienta** utworzonym w **certyfikatach & tajemnic** dla zarejestrowanej aplikacji.
> - `Enter_the_Tenant_Info_Here`- jest wartością **identyfikatora katalogu (dzierżawy)** zarejestrowanej aplikacji.
> 1. Aby użyć https z localhost, wprowadź właściwości server.ssl.key. Aby wygenerować certyfikat z podpisem własnym, użyj narzędzia keytool (zawartego w środowiskou JRE).
>
>  ```
>   Example:
>   keytool -genkeypair -alias testCert -keyalg RSA -storetype PKCS12 -keystore keystore.p12 -storepass password
>
>   server.ssl.key-store-type=PKCS12  
>   server.ssl.key-store=classpath:keystore.p12  
>   server.ssl.key-store-password=password  
>   server.ssl.key-alias=testCert
>   ```
>   Umieść wygenerowany plik magazynu kluczy w folderze "zasoby".


> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-run-the-code-sample"></a>Krok 3: Uruchom przykładowy kod
> [!div renderon="docs"]
> #### <a name="step-4-run-the-code-sample"></a>Krok 4: Uruchom przykładowy kod

Aby uruchomić projekt, można:

Uruchom go bezpośrednio z IDE za pomocą wbudowanego serwera rozruchowego sprężyny lub spakować go do pliku WAR za pomocą [maven](https://maven.apache.org/plugins/maven-war-plugin/usage.html) i wdrożyć go do rozwiązania kontenera J2EE, takich jak [Apache Tomcat](http://tomcat.apache.org/).

##### <a name="running-from-ide"></a>Uruchamianie z IDE

Jeśli korzystasz z aplikacji sieci web z IDE, kliknij uruchom, a następnie przejdź do strony głównej projektu. W tym przykładzie standardowy https://localhost:8080adres URL strony głównej to .

1. Na pierwszej stronie wybierz przycisk **Zaloguj,** aby przekierować do usługi Azure Active Directory i monitować użytkownika o ich poświadczenia.

1. Po uwierzytelnienie użytkownika są one przekierowywane do *https://localhost:8080/msal4jsample/secure/aad*pliku . Są teraz zalogowane, a na stronie zostaną wyświetlona informacje o zalogowaniu się. Przykładowy interfejs użytkownika ma następujące przyciski:
    - *Wyloguj :* wylogowuje bieżącego użytkownika z aplikacji i przekierowuje go na stronę główną.
    - *Pokaż informacje o użytkowniku:* Uzyskuje token dla programu Microsoft Graph i wywołuje program Microsoft Graph z żądaniem zawierającym token, który zwraca podstawowe informacje o zalogowanym użytkowniku.


   
> [!IMPORTANT]
> Aplikacja w tym przewodniku Szybki start używa klucza tajnego klienta do identyfikowania się jako klienta poufnego. Ponieważ klucz tajny klienta jest dodawany jako zwykły tekst do plików projektu, ze względów bezpieczeństwa zaleca się użycie certyfikatu zamiast klucza tajnego klienta przed uznaniem aplikacji za aplikację produkcyjną. Aby uzyskać więcej informacji na temat używania certyfikatu, zobacz [Poświadczenia certyfikatu do uwierzytelniania aplikacji](https://docs.microsoft.com/azure/active-directory/develop/active-directory-certificate-credentials).

## <a name="more-information"></a>Więcej informacji

### <a name="how-the-sample-works"></a>Jak działa próbka
![Pokazuje, jak działa przykładowa aplikacja generowana przez ten szybki start](media/quickstart-v2-java-webapp/java-quickstart.svg)

### <a name="getting-msal"></a>Uzyskiwanie msal

MSAL for Java (MSAL4J) to biblioteka Java używana do logowania użytkowników i żądania tokenów używanych do uzyskiwania dostępu do interfejsu API chronionego przez platformę tożsamości firmy Microsoft.

Dodaj MSAL4J do aplikacji za pomocą Maven lub Gradle do zarządzania zależnościami, wszedzając następujące zmiany w pliku pom.xml (Maven) lub build.gradle (Gradle).

```XML
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>msal4j</artifactId>
    <version>1.0.0</version>
</dependency>
```

```$xslt
compile group: 'com.microsoft.azure', name: 'msal4j', version: '1.0.0'
```

### <a name="msal-initialization"></a>Inicjowanie biblioteki MSAL

Dodaj odwołanie do msal dla języka Java, dodając następujący kod do górnej części pliku, w którym będzie używany MSAL4J:

```Java
import com.microsoft.aad.msal4j.*;
```

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat uprawnień i wyrażania zgody:

> [!div class="nextstepaction"]
> [Uprawnienia i wyrażanie zgody](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent)

Aby dowiedzieć się więcej na temat przepływu eru dla tego scenariusza, zobacz Przepływ kodu autoryzacji Oauth 2.0:

> [!div class="nextstepaction"]
> [Kod autoryzacyjny Przepływ Oauth](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-auth-code-flow)

Pomóż nam ulepszyć platformę tożsamości firmy Microsoft. Powiedz nam, co myślisz, wypełniając krótką ankietę z dwoma pytaniami.

> [!div class="nextstepaction"]
> [Ankieta na temat platformy tożsamości firmy Microsoft](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
