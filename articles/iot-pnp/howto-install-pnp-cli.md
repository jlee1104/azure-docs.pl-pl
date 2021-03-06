---
title: Użyj rozszerzenia Azure IoT dla interfejsu wiersza polecenia platformy Azure, aby korzystać z usługi IoT Plug and Play w wersji zapoznawczej | Microsoft Docs
description: Zainstaluj rozszerzenie Azure IoT dla interfejsu wiersza polecenia platformy Azure i użyj go do współpracy z urządzeniami Plug and Play IoT podłączonymi do centrum IoT Hub.
author: ChrisGMsft
ms.author: chrisgre
ms.date: 12/26/2019
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 8dead08017f15a7429655b4bf17b6e8c8e481114
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2020
ms.locfileid: "78251011"
---
# <a name="install-and-use-the-azure-iot-extension-for-the-azure-cli"></a>Instalowanie i używanie rozszerzenia Azure IoT dla interfejsu wiersza polecenia platformy Azure

[Interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) to narzędzie wielodostępnej do obsługi wielu platform i zarządzania zasobami platformy Azure, takimi jak IoT Hub. Interfejs wiersza polecenia platformy Azure jest dostępny w systemach Windows, Linux i MacOS. Interfejs wiersza polecenia platformy Azure jest również wstępnie zainstalowany w [Azure Cloud Shell](https://shell.azure.com). Interfejs wiersza polecenia platformy Azure umożliwia zarządzanie zasobami IoT Hub platformy Azure, wystąpieniami usługi Device Provisioning Service i połączonymi centrami bez instalowania żadnych rozszerzeń.

Rozszerzenie Azure IoT dla interfejsu wiersza polecenia platformy Azure to narzędzie wiersza poleceń umożliwiające współdziałanie z urządzeniami w wersji zapoznawczej IoT Plug and Play i testowanie ich. Możesz użyć rozszerzenia, aby:

- Nawiąż połączenie z urządzeniem.
- Wyświetl dane telemetryczne wysyłane przez urządzenie.
- Pracuj z właściwościami urządzeń.
- Wywoływanie poleceń dotyczących urządzeń.

W tym artykule pokazano, jak:

- Zainstaluj i skonfiguruj rozszerzenie Azure IoT dla interfejsu wiersza polecenia platformy Azure.
- Użyj rozszerzenia, aby korzystać z i testować urządzenia.
- Użyj rozszerzenia, aby zarządzać interfejsami w repozytorium modeli.

## <a name="install-azure-iot-extension-for-the-azure-cli"></a>Zainstaluj rozszerzenie Azure IoT dla interfejsu wiersza polecenia platformy Azure

### <a name="step-1---install-the-azure-cli"></a>Krok 1. Instalowanie interfejsu wiersza polecenia platformy Azure

Postępuj zgodnie z [instrukcjami instalacji](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) , aby skonfigurować interfejs wiersza polecenia platformy Azure w Twoim środowisku. Aby użyć wszystkich poniższych poleceń, wersja interfejsu wiersza polecenia platformy Azure musi być w wersji 2.0.73 lub nowszej. Użyj polecenia `az -–version` w celu przeprowadzenia weryfikacji.

### <a name="step-2---install-iot-extension"></a>Krok 2. Instalowanie rozszerzenia IoT

[Plik readme rozszerzenia IoT](https://github.com/Azure/azure-iot-cli-extension) opisuje kilka sposobów instalowania rozszerzenia. Najprostszym sposobem jest uruchomienie polecenia `az extension add --name azure-iot`. Po zakończeniu instalacji można użyć polecenia `az extension list` w celu zweryfikowania aktualnie zainstalowanych rozszerzeń lub polecenia `az extension show --name azure-iot` w celu wyświetlenia szczegółów rozszerzenia IoT. Aby usunąć rozszerzenie, można użyć polecenia `az extension remove --name azure-iot`.

## <a name="use-azure-iot-extension-for-the-azure-cli"></a>Korzystanie z rozszerzenia Azure IoT dla interfejsu wiersza polecenia platformy Azure

### <a name="prerequisites"></a>Wymagania wstępne

Aby zalogować się do subskrypcji platformy Azure, uruchom następujące polecenie:

```cmd/sh
az login
```

> [!NOTE]
> Jeśli używasz usługi Azure Cloud Shell, nastąpi automatyczne zalogowanie i nie trzeba uruchamiać poprzedniego polecenia.

Aby korzystać z rozszerzenia Azure IoT dla interfejsu wiersza polecenia platformy Azure, potrzebne są:

- Usługa Azure IoT Hub. Istnieje wiele sposobów dodawania Centrum IoT Hub do subskrypcji platformy Azure, np. [Tworzenie Centrum IoT Hub przy użyciu interfejsu wiersza polecenia platformy Azure](../iot-hub/iot-hub-create-using-cli.md). Do uruchamiania poleceń rozszerzenia usługi Azure IoT wymagane są parametry połączenia Centrum IoT Hub. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

    > [!NOTE]
    > W publicznej wersji zapoznawczej funkcje Plug and Play IoT są dostępne tylko w centrach IoT, które zostały utworzone w regionach **środkowe stany USA**, **Europa Północna**i **Japonia Wschodnia** .

- Urządzenie zarejestrowane w usłudze IoT Hub. Aby zarejestrować urządzenie, możesz użyć poniższego polecenia interfejsu CLI platformy Azure, pamiętaj, aby zastąpić `{YourIoTHubName}` i `{YourDeviceID}` symbole zastępcze wartościami:

    ```cmd/sh
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id {YourDeviceID}
    ```

- Niektóre polecenia wymagają parametrów połączenia dla repozytorium modelu firmy. Repozytorium modelu dla firmy jest tworzone podczas pierwszej dołączenia [do portalu usługi Azure Certified for IoT](howto-onboard-portal.md). Osoby trzecie mogą współdzielić swoje parametry połączenia repozytorium modelu z ty, aby zapewnić dostęp do ich interfejsów i modeli.

### <a name="interact-with-a-device"></a>Korzystanie z urządzenia

Za pomocą rozszerzenia można wyświetlać i korzystać z urządzeń Plug and Play IoT podłączonych do centrum IoT Hub. Rozszerzenie współpracuje z dwucyfrowym sznurem, który reprezentuje urządzenie Plug and Play IoT.

#### <a name="list-devices-and-interfaces"></a>Wyświetlanie listy urządzeń i interfejsów

Wyświetl listę wszystkich urządzeń na IoT Hub:

```cmd/sh
az iot hub device-identity list --hub-name {YourIoTHubName}
```

Wyświetl listę wszystkich interfejsów zarejestrowanych przez urządzenie Plug and Play IoT:

```cmd/sh
az iot dt list-interfaces --hub-name {YourIoTHubName} --device-id {YourDeviceID}
```

#### <a name="properties"></a>Właściwości

Wyświetl listę wszystkich właściwości i wartości właściwości dla interfejsu na urządzeniu:

```cmd/sh
az iot dt list-properties --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID} --source private --repo-login "{YourCompanyModelRepoConnectionString}"
```

Ustaw wartość właściwości do odczytu i zapisu:

```cmd/sh
az iot dt update-property --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface-payload {JSONPayload or FilePath}
```

Przykładowy plik ładunku służący do ustawiania właściwości **name** w interfejsie **czujnika** urządzenia do firmy **contoso** wygląda następująco:

```json
{
  "sensor": {
    "properties": {
      "name": {
        "desired": {
          "value": "Contoso"
        }
      }
    }
  }
}
```

#### <a name="commands"></a>Polecenia

Wyświetl listę wszystkich poleceń dla interfejsu na urządzeniu:

```cmd/sh
az iot dt list-commands --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID} --source private --repo-login {YourCompanyModelRepoConnectionString}
```

Bez parametru `--repo-login` to polecenie używa repozytorium modelu publicznego.

Wywołaj polecenie:

```cmd/sh
az iot dt invoke-command --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID} --cn {CommandName} --command-payload {CommandPayload or FilePath}
```

#### <a name="digital-twin-events"></a>Cyfrowe wydarzenia bliźniaczye

Monitoruj wszystkie usługi IoT Plug and Play zdarzenia cyfrowego przędzy z określonego urządzenia i interfejsu przechodzącego do **$default** grupy odbiorców centrum zdarzeń:

```cmd/sh
az iot dt monitor-events --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID}
```

Monitoruj wszystkie usługi IoT Plug and Play zdarzenia cyfrowego przędzy z określonego urządzenia i interfejsu przechodzącego do określonej grupy odbiorców:

```cmd/sh
az iot dt monitor-events --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID} --consumer-group {YourConsumerGroup}
```

### <a name="manage-interfaces-in-a-model-repository"></a>Zarządzanie interfejsami w repozytorium modelu

Następujące polecenia używają publicznego repozytorium modeli Plug and Play IoT. Aby użyć repozytorium modelu firmy, należy dodać argument `--login` z parametrami połączenia repozytorium modeli.

Wyświetl listę interfejsów w publicznym repozytorium modeli Plug and Play IoT:

```cmd/sh
az iot pnp interface list
```

Pokaż interfejs w publicznym repozytorium modeli Plug and Play IoT:

```cmd/sh
az iot pnp interface show --interface {YourInterfaceId}
```

Utwórz interfejs w repozytorium modelu firmy IoT Plug and Play:

```cmd/sh
az iot pnp interface create --definition {JSONPayload or FilePath} --login {YourCompanyModelRepoConnectionString}
```

Nie można bezpośrednio utworzyć interfejsu w repozytorium modelu publicznego.

Zaktualizuj interfejs w repozytorium modelu firmy IoT Plug and Play:

```cmd/sh
az iot pnp interface update --definition {JSONPayload or FilePath} --login {YourCompanyModelRepoConnectionString}
```

Nie można bezpośrednio zaktualizować interfejsu w repozytorium modelu publicznego.

Opublikuj interfejs z repozytorium modelu firmy IoT Plug and Play w repozytorium modelu publicznego. Ta operacja sprawia, że interfejs jest niezmienny:

```cmd/sh
az iot pnp interface publish --interface {YourInterfaceID} --login {YourCompanyModelRepoConnectionString}
```

Tylko partnerzy firmy Microsoft mogą publikować interfejsy w repozytorium modelu publicznego.

### <a name="manage-device-capability-models-in-a-model-repository"></a>Zarządzanie modelami możliwości urządzeń w repozytorium modelu

Następujące polecenia używają publicznego repozytorium modeli Plug and Play IoT. Aby użyć repozytorium modelu firmy, należy dodać argument `--login` z parametrami połączenia repozytorium modeli.

Wyświetl listę modeli możliwości urządzeń w repozytorium modelu publicznego Plug and Play IoT:

```cmd/sh
az iot pnp capability-model list
```

Pokaż model możliwości urządzenia w repozytorium modelu publicznego Plug and Play IoT:

```cmd/sh
az iot pnp capability-model show --model {YourModelID}
```

Utwórz model możliwości urządzenia w repozytorium modelu firmy IoT Plug and Play:

```cmd/sh
az iot pnp capability-model create --definition {JSONPayload or FilePath} --login {YourCompanyModelRepoConnectionString}
```

Nie można bezpośrednio utworzyć modelu w repozytorium modelu publicznego.

Zaktualizuj model możliwości urządzenia w repozytorium modelu firmy IoT Plug and Play:

```cmd/sh
az iot pnp capability-model update --definition {JSONPayload or FilePath} --login {YourCompanyModelRepoConnectionString}
```

Nie można bezpośrednio zaktualizować modelu w repozytorium modelu publicznego.

Opublikuj model możliwości urządzenia z repozytorium modelu firmy IoT Plug and Play w repozytorium modelu publicznego. Ta operacja sprawia, że model jest niezmienny:

```cmd/sh
az iot pnp capability-model publish --model {YourModelID} --login {YourCompanyModelRepoConnectionString}
```

Tylko partnerzy firmy Microsoft mogą publikować modele w repozytorium modelu publicznego.

## <a name="next-steps"></a>Następne kroki

W tym artykule poznasz sposób instalowania i używania rozszerzenia Azure IoT dla interfejsu wiersza polecenia platformy Azure w celu współdziałania z urządzeniami Plug and Play. Sugerowany następny krok to Dowiedz się, jak [zarządzać modelami](./howto-manage-models.md).
