---
title: 'Szybki start: aprowizuj symulowane urządzenie modułu TPM do usługi Azure IoT Hub przy użyciu języka C'
description: W tym przewodniku Szybki start używane są rejestracje indywidualne. W tym przewodniku Szybki start utworzysz i aprowizujesz symulowane urządzenie TPM przy użyciu zestawu SDK urządzenia C dla usługi inicjowania obsługi administracyjnej usługi azure IoT Hub (DPS).
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: d41c4757f0b81312cefa580c3a3263f87bccffa9
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79241703"
---
# <a name="quickstart-provision-a-simulated-tpm-device-using-the-azure-iot-c-sdk"></a>Szybki start: aprowizowanie symulowanego urządzenia TPM za pomocą zestawu SDK języka C usługi Azure IoT

[!INCLUDE [iot-dps-selector-quick-create-simulated-device-tpm](../../includes/iot-dps-selector-quick-create-simulated-device-tpm.md)]

Dzięki temu przewodnikowi Szybki start dowiesz się, jak utworzyć i uruchomić symulator urządzenia modułu TPM na maszynie deweloperskiej z systemem Windows. To symulowane urządzenie połączysz z usługą IoT Hub przy użyciu wystąpienia usługi Device Provisioning. Przykładowy kod z [zestawu SDK języka C usługi Azure IoT](https://github.com/Azure/azure-iot-sdk-c) będzie używany w celu sprawniejszego rejestrowania urządzenia z wystąpieniem usługi Device Provisioning i symulowania sekwencji rozruchu dla tego urządzenia.

Jeśli nie znasz procesu automatycznego obsługi administracyjnej, zapoznaj się z [pojęciami autoobceptowania.](concepts-auto-provisioning.md) Pamiętaj również, aby przed rozpoczęciem pracy z tym przewodnikiem Szybki start wykonać kroki przedstawione w części [Konfigurowanie usługi IoT Hub Device Provisioning za pomocą witryny Azure Portal](./quick-setup-auto-provision.md). 

Usługa Azure IoT Device Provisioning obsługuje dwa typy rejestracji:
- [Grupy rejestracji](concepts-service.md#enrollment-group): służą do rejestrowania wielu pokrewnych urządzeń.
- [Rejestracje indywidualne](concepts-service.md#individual-enrollment): służą do rejestrowania pojedynczych urządzeń.

W tym artykule przedstawiono rejestracje indywidualne.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Następujące wymagania wstępne są dla środowiska deweloperskiego systemu Windows. W przypadku systemu Linux lub macOS zobacz odpowiednią sekcję w [przygotowaniu środowiska programistycznego](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) w dokumentacji SDK.

* [Visual Studio](https://visualstudio.microsoft.com/vs/) 2019 z włączonym obciążeniem ["Tworzenie pulpitu z c++".](https://docs.microsoft.com/cpp/?view=vs-2019#pivot=workloads) Obsługiwane są również program visual studio 2015 i visual studio 2017.

* Zainstalowana najnowsza wersja usługi[Git](https://git-scm.com/download/).

<a id="setupdevbox"></a>

## <a name="prepare-a-development-environment-for-the-azure-iot-c-sdk"></a>Przygotowywanie środowiska deweloperskiego dla zestawu SDK języka C usługi Azure IoT

W tej sekcji przygotujesz środowisko deweloperskie używane do opracowania [zestawu SDK języka C usługi Azure IoT](https://github.com/Azure/azure-iot-sdk-c) i przykładowy symulator urządzenia [TPM](https://docs.microsoft.com/windows/device-security/tpm/trusted-platform-module-overview).

1. Pobierz [system kompilacji CMake](https://cmake.org/download/).

    Ważne jest, aby wstępnie wymagane składniki (program Visual Studio oraz pakiet roboczy „Programowanie aplikacji klasycznych w języku C++”) były zainstalowane na tym komputerze **przed** uruchomieniem `CMake` instalacji. Gdy wymagania wstępne zostaną spełnione, a pobrane pliki zweryfikowane, zainstaluj system kompilacji CMake.

2. Znajdź nazwę tagu [dla najnowszej wersji](https://github.com/Azure/azure-iot-sdk-c/releases/latest) SDK.

3. Otwórz wiersz polecenia lub powłokę Git Bash. Uruchom następujące polecenia, aby sklonować najnowszą wersję repozytorium GitHub [SDK usługi Azure IoT C.](https://github.com/Azure/azure-iot-sdk-c) Użyj znacznika znalezionego w poprzednim kroku `-b` jako wartości parametru:

    ```cmd/sh
    git clone -b <release-tag> https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Należy się spodziewać, że ukończenie operacji potrwa kilka minut.

4. Utwórz podkatalog `cmake` w katalogu głównym repozytorium Git, a następnie przejdź do tego folderu. Uruchom następujące polecenia z `azure-iot-sdk-c` katalogu:

    ```cmd/sh
    mkdir cmake
    cd cmake
    ```

## <a name="build-the-sdk-and-run-the-tpm-device-simulator"></a>Skompiluj zestaw SDK i uruchom symulator urządzenia modułu TPM

W tej sekcji skompilujesz zestaw SDK języka C usługi Azure IoT, który zawiera przykładowy kod symulatora urządzenia TPM. W tym przykładzie przedstawiono [mechanizm zaświadczania](concepts-security.md#attestation-mechanism) modułu TPM za pośrednictwem uwierzytelniania tokenu sygnatury dostępu współdzielonego (SAS).

1. Z podkatalogu `cmake` utworzonego w repozytorium Git azure-iot-sdk-c uruchom następujące polecenie, aby skompilować przykładowy kod. Rozwiązanie programu Visual Studio dla symulowanego urządzenia zostanie również wygenerowane przez to polecenie kompilacji.

    ```cmd/sh
    cmake -Duse_prov_client:BOOL=ON -Duse_tpm_simulator:BOOL=ON ..
    ```

    Jeśli program `cmake` nie znajdzie kompilatora języka C++, mogą występować błędy kompilacji podczas uruchamiania powyższego polecenia. Jeśli tak się stanie, spróbuj uruchomić to polecenie w [wierszu polecenia programu Visual Studio](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs). 

    Gdy kompilacja zakończy się powodzeniem, kilka ostatnich wierszy danych wyjściowych będzie wyglądać podobnie do następujących danych wyjściowych:

    ```cmd/sh
    $ cmake -Duse_prov_client:BOOL=ON -Duse_tpm_simulator:BOOL=ON ..
    -- Building for: Visual Studio 15 2017
    -- Selecting Windows SDK version 10.0.16299.0 to target Windows 10.0.17134.
    -- The C compiler identification is MSVC 19.12.25835.0
    -- The CXX compiler identification is MSVC 19.12.25835.0

    ...

    -- Configuring done
    -- Generating done
    -- Build files have been written to: E:/IoT Testing/azure-iot-sdk-c/cmake
    ```

2. Przejdź do folderu głównego sklonowanego repozytorium git i uruchom symulator [TPM](https://docs.microsoft.com/windows/device-security/tpm/trusted-platform-module-overview) przy użyciu ścieżki pokazanej poniżej. Ten symulator nasłuchuje przez gniazdo na portach 2321 i 2322. Nie zamykaj tego okna polecenia; będzie ono potrzebne, aby ten symulator działał do czasu zakończenia tego przewodnika Szybki start. 

   Jeśli znajdujesz się w folderze *cmake*, uruchom następujące polecenia:

    ```cmd/sh
    cd ..
    .\provisioning_client\deps\utpm\tools\tpm_simulator\Simulator.exe
    ```

    Nie będą widoczne żadne dane wyjściowe z symulatora. Pozwól mu kontynuować pracę i symulowanie urządzenia TPM.

<a id="simulatetpm"></a>

## <a name="read-cryptographic-keys-from-the-tpm-device"></a>Odczytywanie kluczy kryptograficznych z urządzenia TPM

W tej sekcji skompilujesz i wykonasz przykładowy kod, który odczyta klucz poręczenia i identyfikator rejestracji z symulatora modułu TPM, który pozostał uruchomiony i nasłuchuje na portach 2321 i 2322. Te wartości będą używane do rejestracji urządzenia z wystąpieniem usługi Device Provisioning Service.

1. Uruchom program Visual Studio i otwórz nowy plik rozwiązania o nazwie `azure_iot_sdks.sln`. Ten plik rozwiązania znajduje się w folderze `cmake` utworzonym wcześniej w katalogu głównym repozytorium Git azure-iot-sdk-c.

2. W menu Programu Visual Studio wybierz pozycję **Buduj** > **rozwiązanie kompilacji,** aby utworzyć wszystkie projekty w rozwiązaniu.

3. W oknie programu Visual Studio *Eksplorator rozwiązań* przejdź do folderu **Provision\_Tools**. Kliknij prawym przyciskiem myszy projekt **tpm_device_provision** i wybierz pozycję **Ustaw jako projekt startowy**. 

4. W menu Programu Visual Studio wybierz **debugowanie** > **start bez debugowania,** aby uruchomić rozwiązanie. Aplikacja odczytuje i wyświetla **_identyfikator rejestracji_** i **_klucz poręczenia_**. Zanotuj lub skopiuj te wartości. Będą one używane w następnej sekcji do rejestracji urządzenia. 


<a id="portalenrollment"></a>

## <a name="create-a-device-enrollment-entry-in-the-portal"></a>Tworzenie wpisu rejestracji urządzenia w portalu

1. Zaloguj się do witryny Azure portal, wybierz przycisk **Wszystkie zasoby** w menu po lewej stronie i otwórz usługę inicjowania obsługi administracyjnej urządzeń.

1. Wybierz kartę **Zarządzanie rejestracjami,** a następnie wybierz przycisk **Dodaj rejestrację indywidualną** u góry. 

1. W panelu **Dodawanie rejestracji** wprowadź następujące informacje:
   - Wybierz opcję **TPM** jako *Mechanizm* poświadczania tożsamości.
   - Wprowadź identyfikator *rejestracji* i *klucz poręczenia dla* urządzenia TPM na podstawie wartości, które zostały wcześniej odnotowane.
   - Wybierz centrum IoT połączone z Twoją usługą aprowizacji.
   - Opcjonalnie można podać następujące informacje:
       - Wprowadź unikatowy *identyfikator urządzenia* (możesz użyć sugerowanego **urządzenia testowego docs** lub podać własne). Nadając nazwę urządzeniu, unikaj korzystania z danych poufnych. Jeśli nie zdecydujesz się go podać, identyfikator rejestracji zostanie użyty do zidentyfikowania urządzenia.
       - Zaktualizuj pole **Początkowy stan bliźniaczej reprezentacji urządzenia** za pomocą wybranej konfiguracji początkowej dla urządzenia.
   - Po zakończeniu naciśnij przycisk **Zapisz.** 

      ![Wprowadzanie informacji o rejestracji urządzenia w portalu](./media/quick-create-simulated-device/enter-device-enrollment.png)  

      Po pomyślnej rejestracji *Identyfikator rejestracji* Twojego urządzenia pojawi się na liście na karcie *Indywidualne rejestracje*. 


<a id="firstbootsequence"></a>

## <a name="simulate-first-boot-sequence-for-the-device"></a>Symulowanie sekwencji pierwszego uruchamiania dla urządzenia

W tej sekcji skonfigurujesz przykładowy kod w celu używania protokołu [AMQP (Advanced Message Queuing Protocol)](https://wikipedia.org/wiki/Advanced_Message_Queuing_Protocol) do wysyłania sekwencji rozruchu urządzenia do wystąpienia usługi Device Provisioning Service. Ta sekwencja uruchamiania spowoduje, że urządzenie zostanie rozpoznane i przypisane do centrum IoT Hub połączonego z wystąpieniem usługi Device Provisioning Service.

1. W witrynie Azure Portal wybierz kartę **Przegląd** dla swojej usługi Device Provisioning, a następnie skopiuj wartość **_Identyfikator zakresu_**.

    ![Wyodrębnianie informacji o punkcie końcowym usługi Device Provisioning Service z portalu](./media/quick-create-simulated-device/extract-dps-endpoints.png) 

2. W oknie *Eksplorator rozwiązań* programu Visual Studio przejdź do folderu **Provision\_Samples**. Rozwiń przykładowy projekt o nazwie **prov\_dev\_client\_sample**. Rozwiń pozycję **Pliki źródłowe**, a następnie otwórz plik **prov\_dev\_client\_sample.c**.

3. W górnej części pliku znajdź instrukcje `#define` dla każdego protokołu urządzenia, jak pokazano poniżej. Upewnij się, że tylko `SAMPLE_AMQP` jest pozbawiony komentarza.

    Obecnie [protokół MQTT nie jest obsługiwana w przypadku rejestracji indywidualnej urządzenia TPM](https://github.com/Azure/azure-iot-sdk-c#provisioning-client-sdk).

    ```c
    //
    // The protocol you wish to use should be uncommented
    //
    //#define SAMPLE_MQTT
    //#define SAMPLE_MQTT_OVER_WEBSOCKETS
    #define SAMPLE_AMQP
    //#define SAMPLE_AMQP_OVER_WEBSOCKETS
    //#define SAMPLE_HTTP
    ```

4. Znajdź stałą `id_scope` i zastąp jej wartość wcześniej skopiowaną wartością **Zakres identyfikatorów**. 

    ```c
    static const char* id_scope = "0ne00002193";
    ```

5. Znajdź definicję funkcji `main()` w tym samym pliku. Upewnij się, że zmienna `hsm_type` jest ustawiona na `SECURE_DEVICE_TYPE_TPM`, a nie `SECURE_DEVICE_TYPE_X509`, jak pokazano poniżej.

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    hsm_type = SECURE_DEVICE_TYPE_TPM;
    //hsm_type = SECURE_DEVICE_TYPE_X509;
    ```

6. Kliknij prawym przyciskiem myszy projekt **prov\_dev\_client\_sample**, a następnie wybierz pozycję **Ustaw jako projekt startowy**. 

7. W menu Programu Visual Studio wybierz **debugowanie** > **start bez debugowania,** aby uruchomić rozwiązanie. W wierszu o przebudowie projektu wybierz opcję **Tak**, aby odbudować projekt przed uruchomieniem.

    Następujące dane wyjściowe to przykład pomyślnego uruchomienia aprowizowanego urządzenia klienta oraz podłączenia do aprowizowanego wystąpienia usługi Device Provisioning Service w celu uzyskania informacji na temat usługi IoT Hub i jej rejestracji:

    ```cmd
    Provisioning API Version: 1.2.7
    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED

    Registering... Press enter key to interrupt.

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service:
    test-docs-hub.azure-devices.net, deviceId: test-docs-device
    ```

8. Gdy symulowane urządzenie jest aprowizowana do centrum IoT hub przez usługę inicjowania obsługi administracyjnej, identyfikator urządzenia pojawia się z **urządzeniami IoT**koncentratora . 

    ![Urządzenie jest rejestrowane w centrum IoT](./media/quick-create-simulated-device/hub-registration.png) 


## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli planujesz kontynuować pracę i eksplorowanie przykładu klienta urządzenia, nie czyścić zasobów utworzonych w tym przewodniku Szybki start. Jeśli nie zamierzasz kontynuować, wykonaj następujące kroki, aby usunąć wszystkie zasoby utworzone przez ten przewodnik Szybki start.

1. Zamknij okno danych wyjściowych przykładu klienta urządzenia na swojej maszynie.
2. Zamknij okno symulatora modułu TPM na swojej maszynie.
3. Z menu po lewej stronie w witrynie Azure portal wybierz **pozycję Wszystkie zasoby,** a następnie wybierz usługę inicjowania obsługi urządzeń. Otwórz **okno Zarządzaj rejestracjami** dla usługi, a następnie wybierz kartę **Rejestracje indywidualne.** Zaznacz pole wyboru obok *identyfikatora REJESTRACJI* urządzenia zarejestrowanego w tym przewodniku Szybki start, a następnie naciśnij przycisk **Usuń** u góry okienka. 
4. Z menu po lewej stronie w witrynie Azure portal wybierz **pozycję Wszystkie zasoby,** a następnie wybierz centrum IoT Hub. Otwórz **urządzenia IoT** dla centrum, zaznacz pole wyboru obok *identyfikatora URZĄDZENIA* urządzenia zarejestrowanego w tym przewodniku Szybki start, a następnie naciśnij przycisk **Usuń** u góry okienka.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzono symulowane urządzenie modułu TPM na komputerze i zainicjowano jego obsługę w centrum IoT przy użyciu usługi inicjowania obsługi administracyjnej urządzeń usługi IoT Hub. Aby dowiedzieć się, jak programowo zarejestrować urządzenie modułU TPM, przejdź do szybkiego startu w celu uzyskania programowej rejestracji urządzenia modułu TPM. 

> [!div class="nextstepaction"]
> [Szybki start platformy Azure — rejestrowanie urządzenia modułu TPM w usłudze inicjowania obsługi administracyjnej urządzeń usługi Azure IoT Hub](quick-enroll-device-tpm-java.md)
