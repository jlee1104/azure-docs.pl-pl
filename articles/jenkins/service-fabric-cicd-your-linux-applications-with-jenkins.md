---
title: Ciągła kompilacja dla aplikacji systemu Linux przy użyciu Jenkins
description: Ciągła kompilacja i integracja aplikacji Service Fabric Linux przy użyciu Jenkins
keywords: Jenkins, Azure, DevOps, cicd, Linux
ms.topic: tutorial
ms.date: 07/31/2018
ms.openlocfilehash: 96e8c3721b5849b874878ea6a846271fb6760ffd
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/27/2020
ms.locfileid: "77675239"
---
# <a name="use-jenkins-to-build-and-deploy-your-linux-applications"></a>Korzystanie z Jenkins do kompilowania i wdrażania aplikacji systemu Linux

Ten samouczek obejmuje kilka możliwych sposobów konfigurowania środowiska Jenkins oraz różne sposoby wdrażania aplikacji w klastrze Service Fabric po jego skompilowaniu. Postępuj zgodnie z tymi ogólnymi krokami, aby pomyślnie skonfigurować Jenkins, ściągnąć zmiany z usługi GitHub, skompilować aplikację i wdrożyć ją w klastrze:

1. Upewnij się, że zainstalowano [wymagania wstępne](#prerequisites).
1. Następnie postępuj zgodnie z instrukcjami w jednej z poniższych sekcji, aby skonfigurować Jenkins:
   * [Skonfiguruj Jenkins w klastrze Service Fabric](#set-up-jenkins-inside-a-service-fabric-cluster), 
   * [Skonfiguruj Jenkins poza klastrem Service Fabric](#set-up-jenkins-outside-a-service-fabric-cluster)lub
   * [Zainstaluj wtyczkę Service Fabric w istniejącym środowisku Jenkins](#install-service-fabric-plugin-in-an-existing-jenkins-environment).
1. Po skonfigurowaniu usługi Jenkins wykonaj kroki opisane w temacie [Tworzenie i Konfigurowanie zadania Jenkins](#create-and-configure-a-jenkins-job) , aby skonfigurować witrynę GitHub na potrzeby wyzwalania Jenkins w przypadku wprowadzania zmian w aplikacji, a następnie skonfigurować potok zadań Jenkins za pomocą kroku kompilacji, aby ściągnąć zmiany z usługi GitHub i skompilować aplikację. 
1. Na koniec Skonfiguruj krok Jenkins zadania po kompilacji, aby wdrożyć aplikację w klastrze Service Fabric. Istnieją dwa sposoby konfigurowania Jenkins do wdrażania aplikacji w klastrze:    
   * W przypadku środowisk deweloperskich i testowych Użyj opcji [Konfiguruj wdrażanie za pomocą punktu końcowego zarządzania klastrem](#configure-deployment-using-cluster-management-endpoint). Jest to najprostsza metoda wdrażania do skonfigurowania.
   * W przypadku środowisk produkcyjnych Użyj opcji [Konfiguruj wdrożenie przy użyciu poświadczeń platformy Azure](#configure-deployment-using-azure-credentials). Firma Microsoft zaleca tę metodę dla środowisk produkcyjnych, ponieważ przy użyciu poświadczeń platformy Azure można ograniczyć dostęp do zasobów platformy Azure w ramach zadania Jenkins. 

## <a name="prerequisites"></a>Wymagania wstępne

- Upewnij się, że narzędzie git jest zainstalowane lokalnie. Odpowiednią wersję narzędzia Git można zainstalować ze [strony plików do pobrania](https://git-scm.com/downloads) w usłudze Git w oparciu o system operacyjny. Jeśli jesteś nowym narzędziem git, Dowiedz się więcej na jego temat w [dokumentacji usługi git](https://git-scm.com/docs).
- W tym artykule jest wykorzystywany *przykład Service Fabric wprowadzenie* w witrynie GitHub: [https://github.com/Azure-Samples/service-fabric-java-getting-started](https://github.com/Azure-Samples/service-fabric-java-getting-started) do kompilowania i wdrażania aplikacji. Można utworzyć rozwidlenie tego repozytorium w taki sposób, aby były zgodne, lub, z modyfikacją instrukcji, korzystać z własnego projektu GitHub.


## <a name="install-service-fabric-plugin-in-an-existing-jenkins-environment"></a>Zainstaluj wtyczkę Service Fabric w istniejącym środowisku Jenkins

Jeśli dodajesz wtyczkę Service Fabric do istniejącego środowiska Jenkins, musisz wykonać następujące czynności:

- [Interfejs wiersza polecenia Service Fabric (sfctl)](../service-fabric/service-fabric-cli.md). Zainstaluj interfejs wiersza polecenia na poziomie systemu, a nie na poziomie użytkownika, tak aby Jenkins można było uruchamiać polecenia interfejsu CLI. 
- Aby wdrażać aplikacje Java, zainstaluj zarówno [Gradle, jak i Open JDK 8,0](../service-fabric/service-fabric-get-started-linux.md#set-up-java-development). 
- Aby wdrożyć aplikacje platformy .NET Core 2,0, zainstaluj [zestaw SDK programu .net core 2,0](../service-fabric/service-fabric-get-started-linux.md#set-up-net-core-20-development). 

Po zainstalowaniu wymagań wstępnych dotyczących środowiska można wyszukać wtyczkę Service Fabric platformy Azure w witrynie Jenkins Marketplace i zainstalować ją.

Po zainstalowaniu wtyczki przejdź dalej, aby [utworzyć i skonfigurować zadanie Jenkins](#create-and-configure-a-jenkins-job).

## <a name="set-up-jenkins-inside-a-service-fabric-cluster"></a>Konfigurowanie narzędzia Jenkins w ramach klastra usługi Service Fabric

Narzędzie Jenkins możesz skonfigurować wewnątrz klastra usługi Service Fabric lub poza nim. W poniższych sekcjach pokazano, jak skonfigurować ją w klastrze podczas korzystania z konta usługi Azure Storage w celu zapisania stanu wystąpienia kontenera.

1. Upewnij się, że masz klaster z systemem Service Fabric Linux z zainstalowaną platformą Docker. W klastrach Service Fabric uruchomionych na platformie Azure już zainstalowano platformę Docker. Jeśli klaster jest uruchamiany lokalnie (jednopunktowy dev Environment), sprawdź, czy platforma Docker jest zainstalowana na maszynie za pomocą polecenia `docker info`. Jeśli nie jest zainstalowana, zainstaluj ją za pomocą następujących poleceń:

   ```sh
   sudo apt-get install wget
   wget -qO- https://get.docker.io/ | sh
   ``` 

   > [!NOTE]
   > Upewnij się, że port 8081 jest określony jako niestandardowy punkt końcowy w klastrze. W przypadku korzystania z klastra lokalnego upewnij się, że na komputerze hosta jest otwarty port 8081 i że ma on publiczny adres IP.
   >

1. Klonowanie aplikacji przy użyciu następujących poleceń:
   ```sh
   git clone https://github.com/suhuruli/jenkins-container-application.git
   cd jenkins-container-application
   ```

1. Utrwalaj stan kontenera Jenkins w udziale plików:
   1. Utwórz konto usługi Azure Storage w **tym samym regionie** , w którym znajduje się klaster o takiej samej nazwie jak `sfjenkinsstorage1`.
   1. Utwórz **udział plików** w ramach konta magazynu o nazwie takiej jak `sfjenkins`.
   1. Kliknij pozycję **Połącz** dla udziału plików i zanotuj wartości, które są wyświetlane w obszarze **łączenie z systemu Linux**. wartość powinna wyglądać podobnie do przedstawionego poniżej:

      ```sh
      sudo mount -t cifs //sfjenkinsstorage1.file.core.windows.net/sfjenkins [mount point] -o vers=3.0,username=sfjenkinsstorage1,password=<storage_key>,dir_mode=0777,file_mode=0777
      ```

   > [!NOTE]
   > Aby zainstalować udziały CIFS, należy dysponować pakietem narzędzia CIFS-narzędzia zainstalowane w węzłach klastra.      
   >

1. Zaktualizuj wartości zastępcze w skrypcie `setupentrypoint.sh` przy użyciu szczegółów dotyczących usługi Azure Storage w kroku 2.
   ```sh
   vi JenkinsSF/JenkinsOnSF/Code/setupentrypoint.sh
   ```
   * Zastąp `[REMOTE_FILE_SHARE_LOCATION]` wartością `//sfjenkinsstorage1.file.core.windows.net/sfjenkins` z danych wyjściowych połączenia w kroku 2 powyżej.
   * Zastąp `[FILE_SHARE_CONNECT_OPTIONS_STRING]` wartością `vers=3.0,username=sfjenkinsstorage1,password=GB2NPUCQY9LDGeG9Bci5dJV91T6SrA7OxrYBUsFHyueR62viMrC6NIzyQLCKNz0o7pepGfGY+vTa9gxzEtfZHw==,dir_mode=0777,file_mode=0777` z kroku 2 powyżej.

1. **Tylko bezpieczny klaster:** 
   
   Aby skonfigurować wdrażanie aplikacji w bezpiecznym klastrze z Jenkins, certyfikat klastra musi być dostępny w kontenerze Jenkins. W pliku *ApplicationManifest. XML* , w tagu **ContainerHostPolicies** Dodaj to odwołanie do certyfikatu i zaktualizuj wartość odcisku palca przy użyciu certyfikatu klastra.

   ```xml
   <CertificateRef Name="MyCert" X509FindValue="[Thumbprint]"/>
   ```

   Dodatkowo Dodaj poniższe wiersze pod tagiem **ApplicationManifest** (root) w pliku *ApplicationManifest. XML* i zaktualizuj wartość odcisku palca przy użyciu certyfikatu klastra.

   ```xml
   <Certificates>
     <SecretsCertificate X509FindType="FindByThumbprint" X509FindValue="[Thumbprint]" />
   </Certificates> 
   ```

1. Połącz się z klastrem i zainstaluj aplikację kontenera.

   **Bezpieczny klaster**
   ```sh
   sfctl cluster select --endpoint https://PublicIPorFQDN:19080  --pem [Pem] --no-verify # cluster connect command
   bash Scripts/install.sh
   ```
   Poprzednie polecenie przyjmuje certyfikat w formacie PEM. Jeśli certyfikat jest w formacie PFX, można go przekonwertować przy użyciu następującego polecenia. Jeśli plik PFX nie jest chroniony hasłem, określ parametr **Pass** w jako `-passin pass:`.
   ```sh
   openssl pkcs12 -in cert.pfx -out cert.pem -nodes -passin pass:MyPassword1234!
   ``` 
   
   **Niezabezpieczony klaster**
   ```sh
   sfctl cluster select --endpoint http://PublicIPorFQDN:19080 # cluster connect command
   bash Scripts/install.sh
   ```

   To spowoduje zainstalowanie kontenera narzędzia Jenkins w klastrze i umożliwi monitorowanie za pomocą narzędzia Service Fabric Explorer.

   > [!NOTE]
   > Pobranie obrazu Jenkins w klastrze może potrwać kilka minut.
   >

1. W przeglądarce przejdź na adres `http://PublicIPorFQDN:8081`. Tam jest dostępna ścieżka do początkowego hasła administratora wymaganego do zalogowania się. 
1. Przyjrzyj się Service Fabric Explorer, aby określić węzeł, w którym jest uruchomiony kontener Jenkins. Secure Shell (SSH) Zaloguj się do tego węzła.
   ```sh
   ssh user@PublicIPorFQDN -p [port]
   ``` 
1. Uzyskaj identyfikator wystąpienia kontenera za pomocą polecenia `docker ps -a`.
1. Secure Shell (SSH) Zaloguj się do kontenera i wklej ścieżkę wyświetlaną w portalu Jenkins. Na przykład jeśli w portalu jest wyświetlana ścieżka `PATH_TO_INITIAL_ADMIN_PASSWORD`, uruchom następujące polecenia:

   ```sh
   docker exec -t -i [first-four-digits-of-container-ID] /bin/bash   # This takes you inside Docker shell
   ```
   ```sh
   cat PATH_TO_INITIAL_ADMIN_PASSWORD # This displays the password value
   ```
1. Na stronie Jenkins Wprowadzenie wybierz opcję Wybierz wtyczki do zainstalowania, zaznacz pole wyboru **Brak** , a następnie kliknij przycisk Instaluj.
1. Utwórz użytkownika lub wybierz opcję, aby kontynuować jako administrator.

Po skonfigurowaniu Jenkins przejdź dalej, aby [utworzyć i skonfigurować zadanie Jenkins](#create-and-configure-a-jenkins-job).  

## <a name="set-up-jenkins-outside-a-service-fabric-cluster"></a>Konfigurowanie narzędzia Jenkins poza klastrem usługi Service Fabric

Narzędzie Jenkins możesz skonfigurować wewnątrz klastra usługi Service Fabric lub poza nim. W poniższych sekcjach opisano sposób konfiguracji narzędzia poza klastrem.

1. Upewnij się, że na maszynie jest zainstalowany program Docker, uruchamiając `docker info` w terminalu. Dane wyjściowe wskazują, czy usługa platformy Docker jest uruchomiona.

1. Jeśli nie zainstalowano platformy Docker, uruchom następujące polecenia:

    ```sh
    sudo apt-get install wget
    wget -qO- https://get.docker.io/ | sh
    ```

1. Ściągnij obraz kontenera narzędzia Jenkins usługi Service Fabric: `docker pull rapatchi/jenkins:latest`. Ten obraz jest dostarczany ze wstępnie zainstalowaną wtyczką narzędzia Jenkins usługi Service Fabric.
1. Uruchom obraz kontenera: `docker run -itd -p 8080:8080 rapatchi/jenkins:latest`
1. Uzyskaj identyfikator wystąpienia obrazu kontenera. Listę wszystkich kontenerów platformy Docker można wyświetlić za pomocą polecenia `docker ps –a`
1. Zaloguj się do portalu Jenkins, wykonując następujące czynności:

   1. Zaloguj się do powłoki Jenkins z hosta. Użyj pierwszych czterech cyfr identyfikatora kontenera. Na przykład jeśli identyfikator kontenera jest `2d24a73b5964`, użyj `2d24`.

      ```sh
      docker exec -it [first-four-digits-of-container-ID] /bin/bash
      ```
   1. Z poziomu powłoki Jenkins Pobierz hasło administratora wystąpienia kontenera:

      ```sh
      cat /var/jenkins_home/secrets/initialAdminPassword
      ```      
   1. Aby zalogować się do pulpitu nawigacyjnego Jenkins, Otwórz następujący adres URL w przeglądarce sieci Web: `http://<HOST-IP>:8080`. Użyj hasła z poprzedniego kroku, aby odblokować Jenkins.
   1. (Opcjonalnie). Po zalogowaniu się po raz pierwszy możesz utworzyć własne konto użytkownika i użyć go w celu wykonania poniższych kroków lub można nadal korzystać z konta administratora. W przypadku utworzenia użytkownika należy kontynuować pracę z tym użytkownikiem.
1. Skonfiguruj witrynę GitHub do pracy z usługą Jenkins, wykonując czynności opisane w temacie [generowanie nowego klucza SSH i dodawanie go do agenta SSH](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/).
   * Korzystając z instrukcji udostępnionych przez usługę GitHub, wygeneruj klucz SSH i dodaj go do konta usługi GitHub, które hostuje repozytorium.
   * Uruchom polecenia wymienione pod powyższym linkiem w powłoce platformy Docker narzędzia Jenkins (a nie na hoście).
   * Aby zalogować się do powłoki narzędzia Jenkins z poziomu hosta, użyj następującego polecenia:

      ```sh
      docker exec -t -i [first-four-digits-of-container-ID] /bin/bash
      ```

Upewnij się, że klaster lub maszyna, na której znajduje się obraz kontenera Jenkins, ma publiczny adres IP. Dzięki temu wystąpienie narzędzia Jenkins może otrzymywać powiadomienia z usługi GitHub.

Po skonfigurowaniu Jenkins przejdź do następnej sekcji, [Utwórz i skonfiguruj zadanie Jenkins](#create-and-configure-a-jenkins-job).

## <a name="create-and-configure-a-jenkins-job"></a>Tworzenie i konfigurowanie zadania narzędzia Jenkins

Kroki opisane w tej sekcji przedstawiają sposób konfigurowania zadania Jenkins w celu reagowania na zmiany w repozytorium GitHub, pobierania zmian i kompilowania ich. Na końcu tej sekcji nastąpi przekierowanie do ostatniej procedury w celu skonfigurowania zadania do wdrożenia aplikacji na podstawie tego, czy wdrażasz w środowisku deweloperskim/testowym, czy też w środowisku produkcyjnym. 

1. Na pulpicie nawigacyjnym Jenkins kliknij pozycję **nowy element**.
1. Wprowadź nazwę elementu (na przykład **MyJob**). Wybierz pozycję **free-style project** (projekt w stylu dowolnym) i kliknij przycisk **OK**.
1. Zostanie otwarta strona Konfiguracja zadania. (Aby uzyskać dostęp do konfiguracji z poziomu pulpitu nawigacyjnego Jenkins, kliknij zadanie, a następnie kliknij przycisk **Konfiguruj**).

1. Na karcie **Ogólne** zaznacz pole wyboru **projekt GitHub**i określ adres URL projektu GitHub. Pod tym adresem URL jest hostowana aplikacja Java usługi Service Fabric, którą chcesz zintegrować z przepływem ciągłej integracji/ciągłego wdrażania narzędzia Jenkins (na przykład `https://github.com/{your-github-account}/service-fabric-java-getting-started`).

1. Na karcie **Zarządzanie kodem źródłowym** wybierz pozycję **git**. Określ adres URL repozytorium hostującego aplikację Java usługi Service Fabric, którą chcesz zintegrować z przepływem ciągłej integracji/ciągłego wdrażania narzędzia Jenkins (na przykład `https://github.com/{your-github-account}/service-fabric-java-getting-started`). Możesz również określić gałąź do skompilowania (na przykład `/master`).
1. Skonfiguruj repozytorium *GitHub* , aby komunikować się z usługą Jenkins:

   a. Na stronie repozytorium GitHub przejdź do pozycji **ustawienia** > **integracji i usług**.

   b. Wybierz pozycję **Add Service** (Dodaj usługę), wpisz ciąg **Jenkins** i wybierz pozycję **Jenkins-Github plugin** (Wtyczka Jenkins-Github).

   c. Wprowadź adres URL elementu webhook narzędzia Jenkins (domyślnie adres URL powinien być następujący: `http://<PublicIPorFQDN>:8081/github-webhook/`). Kliknij pozycję **add/update service** (dodaj/aktualizuj usługę).

   d. Do wystąpienia narzędzia Jenkins zostanie wysłane zdarzenie testowe. W usłudze GitHub powinien zostać wyświetlony zielony znacznik wyboru obok elementu webhook i projekt zostanie skompilowany.

1. Na karcie **kompilacje wyzwalaczy** w Jenkins wybierz opcję kompilacji, którą chcesz. Na potrzeby tego przykładu chcesz wyzwolić kompilację za każdym razem, gdy wystąpi wypchnięcie do repozytorium, więc wybierz **wyzwalacz punktu zaczepienia GitHub na potrzeby sondowania GITScm**. Wcześniej ta opcja miała nazwę **Build when a change is pushed to GitHub** (Kompiluj po wypchnięciu zmiany do usługi GitHub).
1. Na karcie **kompilacja** wykonaj jedną z następujących czynności, w zależności od tego, czy tworzysz aplikację Java, czy aplikację platformy .NET Core:

   * **W przypadku aplikacji Java:** Z listy rozwijanej **Dodaj krok kompilacji** wybierz pozycję **Wywołaj skrypt Gradle**. Kliknij pozycję **Advanced** (Zaawansowane). W menu Zaawansowane określ ścieżkę do **głównego skryptu kompilacji** dla aplikacji. Spowoduje to wybranie elementu build.gradle z określonej ścieżki i wykonanie odpowiedniego działania. W przypadku [aplikacji ActorCounter](https://github.com/Azure-Samples/service-fabric-java-getting-started/tree/master/reliable-services-actor-sample/Actors/ActorCounter)jest to: `${WORKSPACE}/reliable-services-actor-sample/Actors/ActorCounter`.

     ![Akcja kompilacji narzędzia Jenkins w usłudze Service Fabric](./media/service-fabric-cicd-your-linux-application-with-jenkins/build-step.png)

   * **W przypadku aplikacji .NET Core:** Z listy rozwijanej **Dodaj krok kompilacji** wybierz pozycję **Wykonaj powłokę**. W wyświetlonym oknie polecenia najpierw należy zmienić katalog na ścieżkę, w której znajduje się plik build.sh. Po zmianie katalogu można uruchomić skrypt build.sh i skompilować aplikację.

      ```sh
      cd /var/jenkins_home/workspace/[Job Name]/[Path to build.sh]  # change directory to location of build.sh file
      ./build.sh
      ```

     Poniższy zrzut ekranu przedstawia przykład poleceń, które są używane do budowania przykładu [usługi licznika](https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started/tree/master/Services/CounterService) z Jenkins nazwą zadania CounterServiceApplication.

      ![Akcja kompilacji narzędzia Jenkins w usłudze Service Fabric](./media/service-fabric-cicd-your-linux-application-with-jenkins/build-step-dotnet.png)

1. Aby skonfigurować Jenkins do wdrażania aplikacji w klastrze Service Fabric w akcjach po kompilacji, potrzebna jest lokalizacja certyfikatu tego klastra w kontenerze Jenkins. Wybierz jedną z następujących opcji, w zależności od tego, czy kontener Jenkins jest uruchomiony w klastrze, czy poza nim, i zanotuj lokalizację certyfikatu klastra:

   * **W przypadku usługi Jenkins uruchomionej w klastrze:** Ścieżkę do certyfikatu można znaleźć, wyświetlając wartość zmiennej środowiskowej *Certificates_JenkinsOnSF_Code_MyCert_PEM* z poziomu kontenera.

      ```sh
      echo $Certificates_JenkinsOnSF_Code_MyCert_PEM
      ```
   
   * **Dla Jenkins uruchomionych poza klastrem:** Wykonaj następujące kroki, aby skopiować certyfikat klastra do kontenera:
     1. Certyfikat musi być w formacie PEM. Jeśli nie masz pliku PEM, możesz utworzyć go z pliku PFX certyfikatu. Jeśli plik PFX nie jest chroniony hasłem, uruchom następujące polecenie z hosta:

        ```sh
        openssl pkcs12 -in clustercert.pfx -out clustercert.pem -nodes -passin pass:
        ``` 

        Jeśli plik PFX jest chroniony hasłem, należy podać hasło w parametrze `-passin`. Na przykład:

        ```sh
        openssl pkcs12 -in clustercert.pfx -out clustercert.pem -nodes -passin pass:MyPassword1234!
        ``` 

     1. Aby uzyskać identyfikator kontenera dla kontenera Jenkins, uruchom `docker ps` z hosta.
     1. Skopiuj plik PEM do swojego kontenera za pomocą następującego polecenia platformy Docker:
    
        ```sh
        docker cp clustercert.pem [first-four-digits-of-container-ID]:/var/jenkins_home
        ``` 

Prawie gotowe! Pozostaw otwarte zadanie Jenkins. Jedynym pozostałym zadaniem jest skonfigurowanie kroków po kompilacji, aby wdrożyć aplikację w klastrze Service Fabric:

* Aby wdrożyć program w środowisku deweloperskim lub testowym, wykonaj kroki opisane w temacie [Konfigurowanie wdrożenia przy użyciu punktu końcowego zarządzania klastrem](#configure-deployment-using-cluster-management-endpoint).
* Aby wdrożyć program w środowisku produkcyjnym, wykonaj kroki opisane w temacie [Konfigurowanie wdrożenia przy użyciu poświadczeń platformy Azure](#configure-deployment-using-azure-credentials).

## <a name="configure-deployment-using-cluster-management-endpoint"></a>Konfigurowanie wdrożenia przy użyciu punktu końcowego zarządzania klastrem

W przypadku środowisk deweloperskich i testowych można użyć punktu końcowego zarządzania klastrami do wdrożenia aplikacji. Skonfigurowanie akcji po kompilacji w punkcie końcowym zarządzania klastrem w celu wdrożenia aplikacji wymaga najmniejszej ilości skonfigurowanej. W przypadku wdrażania w środowisku produkcyjnym przejdź do [konfiguracji wdrożenia przy użyciu poświadczeń platformy Azure](#configure-deployment-using-azure-credentials) , aby skonfigurować jednostkę usługi Azure Active Directory do użycia podczas wdrażania.    

1. W zadaniu Jenkins kliknij kartę **Akcje po kompilacji** . 
1. Z listy rozwijanej **Post-Build Actions** (Akcje po kompilacji) wybierz pozycję **Deploy Service Fabric Project** (Wdróż projekt usługi Service Fabric). 
1. W obszarze **Konfiguracja klastra Service Fabric**wybierz przycisk radiowy **Wypełnij punkt końcowy zarządzania Service Fabric** .
1. Dla **hosta zarządzania**wprowadź punkt końcowy połączenia dla klastra; na przykład `{your-cluster}.eastus.cloudapp.azure.com`.
1. W polu **klucz klienta** i **certyfikat klienta**wprowadź lokalizację pliku PEM w kontenerze Jenkins; na przykład `/var/jenkins_home/clustercert.pem`. (Podczas kopiowania lokalizacji certyfikatu ostatni krok [tworzenia i konfigurowania zadania Jenkins](#create-and-configure-a-jenkins-job)).
1. W obszarze **Konfiguracja aplikacji**Skonfiguruj **nazwę aplikacji**, **Typ aplikacji**oraz ścieżkę (względną) do pól **manifestu aplikacji** .

   ![Akcja po kompilacji Service Fabric Jenkins — punkt końcowy zarządzania](./media/service-fabric-cicd-your-linux-application-with-jenkins/post-build-endpoint.png)

1. Kliknij przycisk **Weryfikuj konfigurację**. Po pomyślnej weryfikacji kliknij przycisk **Zapisz**. Potok zadań Jenkins jest teraz w pełni skonfigurowany. Przejdź do [następnych kroków](#next-steps) w celu przetestowania wdrożenia.

## <a name="configure-deployment-using-azure-credentials"></a>Konfigurowanie wdrożenia przy użyciu poświadczeń platformy Azure

W przypadku środowisk produkcyjnych zdecydowanie zaleca się skonfigurowanie poświadczeń platformy Azure do wdrażania aplikacji. W tej sekcji pokazano, jak skonfigurować jednostkę usługi Azure Active Directory, która będzie używana do wdrażania aplikacji w akcji po kompilacji. Do ról w katalogu można przypisywać jednostki usługi, aby ograniczyć uprawnienia do zadania Jenkins. 

W przypadku środowisk deweloperskich i testowych można skonfigurować poświadczenia platformy Azure lub punkt końcowy zarządzania klastrami, aby wdrożyć aplikację. Aby uzyskać szczegółowe informacje o sposobie konfigurowania punktu końcowego zarządzania klastrem, zobacz [Konfigurowanie wdrożenia przy użyciu punktu końcowego zarządzania klastrem](#configure-deployment-using-cluster-management-endpoint).   

1. Aby utworzyć nazwę główną usługi Azure Active Directory i przypisać uprawnienia IT do subskrypcji platformy Azure, wykonaj kroki opisane w temacie [Korzystanie z portalu w celu utworzenia Azure Active Directory aplikacji i nazwy głównej usługi](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal). Zwróć uwagę na następujące kwestie:

   * Wykonując czynności opisane w temacie, pamiętaj o skopiowaniu i zapisaniu następujących wartości: *Identyfikator aplikacji*, *klucz aplikacji*, *Identyfikator katalogu (identyfikator dzierżawy)* i *Identyfikator subskrypcji*. Są one potrzebne do skonfigurowania poświadczeń platformy Azure w programie Jenkins.
   * Jeśli nie masz [wymaganych uprawnień](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#required-permissions) do katalogu, musisz polecić administratorowi udzielenie uprawnień lub utworzenie jednostki usługi dla Ciebie lub trzeba skonfigurować punkt końcowy zarządzania dla klastra w **akcjach po kompilacji** dla zadania w Jenkins.
   * W sekcji [Tworzenie aplikacji Azure Active Directory](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#create-an-azure-active-directory-application) można wprowadzić dowolny poprawnie SFORMUŁOWANy adres URL dla **adresu URL logowania**.
   * W sekcji [przypisywanie aplikacji do roli](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal) można przypisać aplikację do roli *czytelnik* w grupie zasobów klastra.

1. Wróć do zadania Jenkins, a następnie kliknij kartę **Akcje po kompilacji** .
1. Z listy rozwijanej **Post-Build Actions** (Akcje po kompilacji) wybierz pozycję **Deploy Service Fabric Project** (Wdróż projekt usługi Service Fabric). 
1. W obszarze **Konfiguracja klastra Service Fabric**kliknij pozycję **Wybierz klaster Service Fabric**. Kliknij przycisk **Dodaj** obok pozycji **poświadczenia platformy Azure**. Kliknij pozycję **Jenkins** , aby wybrać dostawcę poświadczeń Jenkins.
1. W dostawcy poświadczeń Jenkins Microsoft Azure wybierz z listy rozwijanej **typ** **nazwę główną usługi** .
1. Użyj wartości zapisanych podczas konfigurowania nazwy głównej usługi w kroku 1, aby ustawić następujące pola:

   * **Identyfikator klienta**: *Identyfikator aplikacji*
   * **Wpis tajny klienta**: *klucz aplikacji*
   * **Identyfikator dzierżawy**: *Identyfikator katalogu*
   * **Identyfikator subskrypcji**: *Identyfikator subskrypcji*
1. Wprowadź opisowy **Identyfikator** używany do wybierania poświadczenia w Jenkins i krótki **Opis**. Następnie kliknij przycisk **weryfikuj nazwę główną usługi**. Jeśli weryfikacja zakończy się pomyślnie, kliknij przycisk **Dodaj**.

   ![Service Fabric Jenkins wprowadź poświadczenia platformy Azure](./media/service-fabric-cicd-your-linux-application-with-jenkins/enter-azure-credentials.png)
1. W obszarze **Konfiguracja klastra Service Fabric**upewnij się, że nowe poświadczenie jest wybrane dla **poświadczeń platformy Azure**. 
1. Z listy rozwijanej **Grupa zasobów** wybierz grupę zasobów klastra, w którym ma zostać wdrożona aplikacja.
1. Z listy rozwijanej **Service Fabric** wybierz klaster, do którego chcesz wdrożyć aplikację.
1. W polu **klucz klienta** i **certyfikat klienta**wprowadź lokalizację pliku PEM w kontenerze Jenkins. Na przykład: `/var/jenkins_home/clustercert.pem`. 
1. W obszarze **Konfiguracja aplikacji**Skonfiguruj **nazwę aplikacji**, **Typ aplikacji**oraz ścieżkę (względną) do pól **manifestu aplikacji** .
    Akcja po kompilacji ![Service Fabric Jenkins — Skonfiguruj poświadczenia platformy Azure](./media/service-fabric-cicd-your-linux-application-with-jenkins/post-build-credentials.png)
1. Kliknij przycisk **Weryfikuj konfigurację**. Po pomyślnej weryfikacji kliknij przycisk **Zapisz**. Potok zadań Jenkins jest teraz w pełni skonfigurowany. Przejdź do [kolejnych kroków](#next-steps) w celu przetestowania wdrożenia.

## <a name="troubleshooting-the-jenkins-plugin"></a>Rozwiązywanie problemów z wtyczką narzędzia Jenkins

Jeśli napotkasz jakiekolwiek usterki we wtyczkach narzędzia Jenkins, prześlij zgłoszenie za pomocą narzędzia [Jenkins JIRA](https://issues.jenkins-ci.org/) dla określonego składnika.

## <a name="ideas-to-try"></a>Pomysły do wypróbowania

Usługa GitHub i narzędzie Jenkins są teraz skonfigurowane. Należy rozważyć wprowadzenie przykładowej zmiany w projekcie `reliable-services-actor-sample/Actors/ActorCounter` w rozwidleniu repozytorium, https://github.com/Azure-Samples/service-fabric-java-getting-started. Wypchnij zmiany do zdalnej gałęzi `master` (lub dowolnej gałęzi, która została skonfigurowana do pracy). Spowoduje to wyzwolenie skonfigurowanego zadania narzędzia Jenkins `MyJob`. Pobiera zmiany z usługi GitHub, kompiluje je i wdraża aplikację w klastrze określonym w akcjach po kompilacji.  

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Usługa Jenkins na platformie Azure](/azure/Jenkins/)