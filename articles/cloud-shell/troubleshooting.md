---
title: Rozwiązywanie problemów Azure Cloud Shell | Microsoft Docs
description: Azure Cloud Shell rozwiązywania problemów
services: azure
documentationcenter: ''
author: maertendMSFT
manager: hemantm
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/24/2018
ms.author: damaerte
ms.openlocfilehash: 1d244d7b62fcfefeec6f628f473274ae982bf4d8
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79272996"
---
# <a name="troubleshooting--limitations-of-azure-cloud-shell"></a>Rozwiązywanie problemów & ograniczenia Azure Cloud Shell

Znane rozwiązania dotyczące rozwiązywania problemów w Azure Cloud Shell obejmują:

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="general-troubleshooting"></a>Ogólne rozwiązywanie problemów

### <a name="error-running-azuread-cmdlets-in-powershell"></a>Błąd uruchamiania poleceń cmdlet AzureAD w programie PowerShell

- **Szczegóły**: po uruchomieniu poleceń cmdlet AzureAD, takich jak `Get-AzureADUser` w Cloud Shell, może zostać wyświetlony komunikat o błędzie: `You must call the Connect-AzureAD cmdlet before calling any other cmdlets`. 
- **Rozwiązanie**: Uruchom polecenie cmdlet `Connect-AzureAD`. Wcześniej polecenie cmdlet Cloud Shell uruchamiane automatycznie podczas uruchamiania programu PowerShell. Aby przyspieszyć czas rozpoczęcia, polecenie cmdlet nie jest już uruchamiane automatycznie. Można przywrócić poprzednie zachowanie, dodając `Connect-AzureAD` do pliku $PROFILE w programie PowerShell.

### <a name="early-timeouts-in-firefox"></a>Wczesne przekroczenia limitu czasu w programie FireFox

- **Szczegóły**: Cloud Shell korzysta z otwartego protokołu WebSocket do przekazywania danych wejściowych/wyjściowych do przeglądarki. W programie FireFox istnieją wstępnie zdefiniowane zasady, które mogą zamknąć protokół WebSocket przedwcześnie wydający wczesne przekroczenia limitu czasu w Cloud Shell.
- **Rozwiązanie**: Otwórz przeglądarkę Firefox i przejdź do strony "about: config" w polu adresu URL. Wyszukaj ciąg "Network. WebSocket. Timeout. ping. Request" i zmień wartość z 0 na 10.

### <a name="disabling-cloud-shell-in-a-locked-down-network-environment"></a>Wyłączanie Cloud Shell w zablokowanym środowisku sieciowym

- **Szczegóły**: Administratorzy mogą chcieć wyłączyć dostęp do Cloud Shell dla swoich użytkowników. Cloud Shell wykorzystuje dostęp do domeny `ux.console.azure.com`, którą można odmówić, zatrzymywać dowolny dostęp do punktu wejścia Cloud Shell, w tym portal.azure.com, shell.azure.com, Visual Studio Code rozszerzenia konta platformy Azure i docs.microsoft.com. W chmurze dla instytucji rządowych USA punkt wejścia jest `ux.console.azure.us`; nie ma odpowiednich shell.azure.us.
- **Rozwiązanie**: Ogranicz dostęp do `ux.console.azure.com` lub `ux.console.azure.us` za pośrednictwem ustawień sieciowych do środowiska. Ikona Cloud Shell nadal istnieje w Azure Portal, ale nie nawiąże połączenia z usługą.

### <a name="storage-dialog---error-403-requestdisallowedbypolicy"></a>Okno dialogowe magazynu — błąd: 403 RequestDisallowedByPolicy

- **Szczegóły**: podczas tworzenia konta magazynu za pomocą Cloud Shell, nie powiedzie się z powodu zasady platformy Azure umieszczonej przez administratora. Komunikat o błędzie będzie zawierać: `The resource action 'Microsoft.Storage/storageAccounts/write' is disallowed by one or more policies.`
- **Rozwiązanie**: skontaktuj się z administratorem platformy Azure, aby usunąć lub zaktualizować zasady platformy Azure odrzucające tworzenie magazynu.

### <a name="storage-dialog---error-400-disallowedoperation"></a>Okno dialogowe magazynu — błąd: 400 DisallowedOperation

- **Szczegóły**: w przypadku korzystania z subskrypcji Azure Active Directory nie można utworzyć magazynu.
- **Rozwiązanie**: Użyj subskrypcji platformy Azure, która może tworzyć zasoby magazynu. Subskrypcje usługi Azure AD nie mogą tworzyć zasobów platformy Azure.

### <a name="terminal-output---error-failed-to-connect-terminal-websocket-cannot-be-established-press-enter-to-reconnect"></a>Dane wyjściowe terminalu — błąd: nie można nawiązać połączenia z terminalem: nie można ustanowić protokołu WebSocket. Naciśnij `Enter`, aby ponownie nawiązać połączenie.
- **Szczegóły**: Cloud Shell wymaga możliwości nawiązania połączenia protokołu WebSocket z infrastrukturą Cloud Shell.
- **Rozwiązanie**: Sprawdź, czy ustawienia sieciowe zostały skonfigurowane w taki sposób, aby umożliwiały wysyłanie żądań HTTPS i żądań protokołu WebSocket do domen w lokalizacji *. Console.Azure.com.

### <a name="set-your-cloud-shell-connection-to-support-using-tls-12"></a>Ustaw połączenie Cloud Shell, aby obsługiwało korzystanie z protokołu TLS 1,2
 - **Szczegóły**: Aby zdefiniować wersję protokołu TLS dla połączenia do Cloud Shell, należy ustawić ustawienia specyficzne dla przeglądarki.
 - **Rozwiązanie**: Przejdź do ustawień zabezpieczeń przeglądarki i zaznacz pole wyboru obok pozycji "Użyj protokołu TLS 1,2".

## <a name="bash-troubleshooting"></a>Rozwiązywanie problemów z bash

### <a name="cannot-run-the-docker-daemon"></a>Nie można uruchomić demona platformy Docker

- **Szczegóły**: Cloud Shell korzysta z kontenera do hostowania środowiska powłoki, w związku z czym demon jest niedozwolony.
- **Rozwiązanie**: Użyj [maszyny platformy Docker](https://docs.docker.com/machine/overview/), która jest instalowana domyślnie, aby zarządzać kontenerami platformy Docker ze zdalnego hosta platformy Docker.

## <a name="powershell-troubleshooting"></a>Rozwiązywanie problemów z programem PowerShell

### <a name="gui-applications-are-not-supported"></a>Aplikacje graficznego interfejsu użytkownika nie są obsługiwane

- **Szczegóły**: Jeśli użytkownik uruchamia aplikację z graficznym interfejsem użytkownika, monit nie zostanie zwrócony. Na przykład, gdy jeden klonuje prywatne repozytorium GitHub z włączonym dwuskładnikowym uwierzytelnianiem, zostanie wyświetlone okno dialogowe umożliwiające ukończenie uwierzytelniania dwuskładnikowego.
- **Rozwiązanie**: Zamknij i ponownie Otwórz powłokę.

### <a name="troubleshooting-remote-management-of-azure-vms"></a>Rozwiązywanie problemów z zdalne zarządzanie maszynami wirtualnymi platformy Azure
> [!NOTE]
> Maszyny wirtualne platformy Azure muszą mieć publiczny adres IP.

- **Szczegóły**: ze względu na domyślne ustawienia zapory systemu Windows dla usługi WinRM użytkownik może zobaczyć następujący błąd: `Ensure the WinRM service is running. Remote Desktop into the VM for the first time and ensure it can be discovered.`
- **Rozwiązanie**: Uruchom `Enable-AzVMPSRemoting`, aby włączyć wszystkie aspekty komunikacji zdalnej programu PowerShell na maszynie docelowej.

### <a name="dir-does-not-update-the-result-in-azure-drive"></a>`dir` nie aktualizuje wyniku na dysku platformy Azure

- **Szczegóły**: domyślnie aby zoptymalizować środowisko użytkownika, wyniki `dir` są przechowywane w pamięci podręcznej na dysku platformy Azure.
- **Rozwiązanie**: po utworzeniu, aktualizacji lub usunięciu zasobu platformy azure Uruchom `dir -force`, aby zaktualizować wyniki na dysku platformy Azure.

## <a name="general-limitations"></a>Ogólne ograniczenia

Azure Cloud Shell ma następujące znane ograniczenia:

### <a name="quota-limitations"></a>Ograniczenia limitu przydziału

Azure Cloud Shell ma limit 20 współbieżnych użytkowników na dzierżawcę na region. Jeśli spróbujesz otworzyć więcej równoczesnych sesji niż limit, zobaczysz błąd "użytkownik dzierżawy przekroczył limit przydziału". Jeśli masz uzasadnioną potrzebę posiadania większej liczby sesji (na przykład w przypadku sesji szkoleniowych), skontaktuj się z pomocą techniczną z wyprzedzeniem, aby zażądać zwiększenia limitu przydziału.

Cloud Shell jest świadczona jako bezpłatna usługa i jest przeznaczona do użycia w celu konfigurowania środowiska platformy Azure, a nie jako platformy obliczeniowej ogólnego przeznaczenia. Nadmierne użycie może być brane pod uwagę w przypadku warunków świadczenia usługi na platformie Azure i może prowadzić do zablokowania dostępu Cloud Shell.

### <a name="system-state-and-persistence"></a>Stan i trwałość systemu

Komputer, na którym jest dostępna sesja Cloud Shell, jest tymczasowy i jest odtwarzany po upływie 20 minut aktywności. Cloud Shell wymaga zainstalowania udziału plików platformy Azure. W związku z tym subskrypcja musi mieć możliwość skonfigurowania zasobów magazynu w celu uzyskania dostępu do Cloud Shell. Inne zagadnienia obejmują:

- W przypadku zainstalowanego magazynu utrwalane są tylko modyfikacje znajdujące się w katalogu `clouddrive`. W programie bash katalog `$HOME` również został utrwalony.
- Udziały plików platformy Azure można instalować tylko z poziomu [przypisanego regionu](persisting-shell-storage.md#mount-a-new-clouddrive).
  - W bash Uruchom `env`, aby znaleźć swój region ustawiony jako `ACC_LOCATION`.
- Azure Files obsługuje tylko Magazyn lokalnie nadmiarowy i konta magazynu geograficznie nadmiarowego.

### <a name="browser-support"></a>Obsługa przeglądarki

Cloud Shell obsługuje najnowsze wersje następujących przeglądarek:

- Microsoft Edge
- Microsoft Internet Explorer
- Google Chrome
- Mozilla Firefox
- Apple Safari
  - Przeglądarka Safari w trybie prywatnym nie jest obsługiwana.

### <a name="copy-and-paste"></a>Kopiowanie i wklejanie

[!INCLUDE [copy-paste](../../includes/cloud-shell-copy-paste.md)]

### <a name="usage-limits"></a>Limity użycia

Cloud Shell jest przeznaczony dla interaktywnych przypadków użycia. W efekcie wszystkie długotrwałe sesje nieinterakcyjne zostaną zakończone bez ostrzeżenia.

### <a name="user-permissions"></a>Uprawnienia użytkowników

Uprawnienia są ustawiane jako regularni użytkownicy bez dostępu sudo. Jakakolwiek instalacja poza katalogiem `$Home` nie jest utrwalona.

## <a name="bash-limitations"></a>Ograniczenia bash

### <a name="editing-bashrc"></a>Edytowanie. bashrc

Należy zachować ostrożność podczas edytowania. bashrc, co może spowodować nieoczekiwane błędy w Cloud Shell.

## <a name="powershell-limitations"></a>Ograniczenia programu PowerShell

### <a name="preview-version-of-azuread-module"></a>Wersja zapoznawcza modułu AzureAD

Obecnie dostępna jest wersja zapoznawcza modułu opartego na .NET Standard, `AzureAD.Standard.Preview`. Ten moduł zapewnia te same funkcje co `AzureAD`.

### <a name="sqlserver-module-functionality"></a>funkcje modułu `SqlServer`

Moduł `SqlServer` objęty Cloud Shell ma tylko wstępne wsparcie dla programu PowerShell Core. W szczególności `Invoke-SqlCmd` nie jest jeszcze dostępna.

### <a name="default-file-location-when-created-from-azure-drive"></a>Domyślna lokalizacja pliku podczas tworzenia z dysku platformy Azure

Przy użyciu poleceń cmdlet programu PowerShell użytkownicy nie mogą tworzyć plików na dysku platformy Azure. Gdy użytkownicy tworzą nowe pliki przy użyciu innych narzędzi, takich jak vim lub nano, pliki są domyślnie zapisywane do `$HOME`.

### <a name="tab-completion-can-throw-psreadline-exception"></a>Ukończenie karty może zgłosić wyjątek PSReadline

Jeśli użytkownik PSReadline EditMode ma ustawioną wartość Emacs:, użytkownik próbuje wyświetlić wszystkie możliwości za pośrednictwem karty, a rozmiar okna jest zbyt mały, aby wyświetlić wszystkie możliwości, PSReadline zgłosi nieobsłużony wyjątek.

### <a name="large-gap-after-displaying-progress-bar"></a>Duża przerwa po wyświetleniu paska postępu

Jeśli akcja polecenia lub użytkownika wyświetla pasek postępu, na przykład kartę kończącą się na dysku `Azure:`, istnieje możliwość, że kursor nie jest ustawiony prawidłowo i pojawia się odstęp, gdzie pasek postępu był wcześniej.

### <a name="random-characters-appear-inline"></a>Znaki losowe pojawiają się w tekście

Kody sekwencji położenia kursora, na przykład `5;13R`, mogą pojawić się w danych wejściowych użytkownika. Znaki można usunąć ręcznie.

## <a name="personal-data-in-cloud-shell"></a>Dane osobowe w Cloud Shell

Azure Cloud Shell poważnie potraktuje dane osobowe, dane przechwycone i przechowywane przez usługę Azure Cloud Shell są używane do zapewnienia ustawień domyślnych dla środowiska, takich jak ostatnio używane powłoki, preferowany rozmiar czcionki, preferowany typ czcionki oraz szczegóły dotyczące udziału plików Ten dysk w chmurze. Czy chcesz wyeksportować lub usunąć te dane, wykonaj następujące instrukcje.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

### <a name="export"></a>Eksportowanie
W celu **wyeksportowania** ustawień użytkownika Cloud Shell zapisywanych danych, takich jak preferowana powłoka, rozmiar czcionki i typ czcionki, uruchom następujące polecenia.

1. [![](https://shell.azure.com/images/launchcloudshell.png "Launch Azure Cloud Shell")](https://shell.azure.com)
2. Uruchom następujące polecenia w programie bash lub PowerShell:

Bash

  ```
  token="Bearer $(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true -s | jq -r ".access_token")"
  curl https://management.azure.com/providers/Microsoft.Portal/usersettings/cloudconsole?api-version=2017-12-01-preview -H Authorization:"$token" -s | jq
  ```

Program PowerShell:

  ```powershell
  $token= ((Invoke-WebRequest -Uri "$env:MSI_ENDPOINT`?resource=https://management.core.windows.net/" -Headers @{Metadata='true'}).content |  ConvertFrom-Json).access_token
  ((Invoke-WebRequest -Uri https://management.azure.com/providers/Microsoft.Portal/usersettings/cloudconsole?api-version=2017-12-01-preview -Headers @{Authorization = "Bearer $token"}).Content | ConvertFrom-Json).properties | Format-List
```

### <a name="delete"></a>Usuń
Aby **usunąć** ustawienia użytkownika Cloud Shell zapisywanych danych, takich jak preferowana powłoka, rozmiar czcionki i typ czcionki, uruchom następujące polecenia. Przy następnym uruchomieniu Cloud Shell zostanie wyświetlony monit o ponowne dołączenie udziału plików. 

>[!Note]
> Po usunięciu ustawień użytkownika rzeczywisty udział Azure Files nie zostanie usunięty. Przejdź do Azure Files, aby zakończyć tę akcję.

1. [![](https://shell.azure.com/images/launchcloudshell.png "Launch Azure Cloud Shell")](https://shell.azure.com)
2. Uruchom następujące polecenia w programie bash lub PowerShell:

Bash

  ```
  token="Bearer $(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true -s | jq -r ".access_token")"
  curl -X DELETE https://management.azure.com/providers/Microsoft.Portal/usersettings/cloudconsole?api-version=2017-12-01-preview -H Authorization:"$token"
  ```

Program PowerShell:

  ```powershell
  $token= ((Invoke-WebRequest -Uri "$env:MSI_ENDPOINT`?resource=https://management.core.windows.net/" -Headers @{Metadata='true'}).content |  ConvertFrom-Json).access_token
  Invoke-WebRequest -Method Delete -Uri https://management.azure.com/providers/Microsoft.Portal/usersettings/cloudconsole?api-version=2017-12-01-preview -Headers @{Authorization = "Bearer $token"}
  ```
## <a name="azure-government-limitations"></a>Ograniczenia Azure Government
Azure Cloud Shell w Azure Government jest dostępny tylko w Azure Portal.
