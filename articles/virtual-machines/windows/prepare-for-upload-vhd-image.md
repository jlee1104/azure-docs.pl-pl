---
title: Przygotowywanie dysku VHD systemu Windows do przekazania na platformę Azure
description: Dowiedz się, jak przygotować dysk VHD lub VHDX systemu Windows do przekazania go na platformę Azure
services: virtual-machines-windows
documentationcenter: ''
author: glimoli
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 7802489d-33ec-4302-82a4-91463d03887a
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 05/11/2019
ms.author: genli
ms.openlocfilehash: 719a1985aeb0db7b0cf7f55a10762bf3ebb3e045
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250194"
---
# <a name="prepare-a-windows-vhd-or-vhdx-to-upload-to-azure"></a>Przygotowywanie dysku VHD lub VHDX systemu Windows do przekazania na platformę Azure

Przed przekazaniem maszyny wirtualnej systemu Windows (VM) z lokalnego na platformę Azure, należy przygotować wirtualny dysk twardy (VHD lub VHDX). Platforma Azure obsługuje maszyny wirtualne generacji 1 i 2 generacji, które są w formacie VHD i mają dysk o stałym rozmiarze. Maksymalny rozmiar dla VHD wynosi 1023 GB. 

Na maszynie wirtualnej generacji 1 można przekonwertować system plików VHDX na dysk VHD. Można również przekonwertować dynamicznie rozwijający się dysk na dysk o stałym rozmiarze. Ale nie można zmienić generacji maszyny Wirtualnej. Aby uzyskać więcej informacji, zobacz [Czy utworzyć maszynę wirtualną generacji 1 lub 2 w funkcji Hyper-V?](https://technet.microsoft.com/windows-server-docs/compute/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v) [Azure support for generation 2 VMs (preview)](generation-2.md)

Aby uzyskać informacje na temat zasad pomocy technicznej dla maszyn wirtualnych platformy Azure, zobacz [Pomoc techniczna oprogramowania serwera firmy Microsoft dla maszyn wirtualnych platformy Azure.](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)

> [!NOTE]
> Instrukcje w tym artykule odnoszą się do:
>1. 64-bitowa wersja systemów operacyjnych Windows Server 2008 R2 i nowszych systemach operacyjnych Windows Server. Aby uzyskać informacje na temat uruchamiania 32-bitowego systemu operacyjnego na platformie Azure, zobacz [Obsługa 32-bitowych systemów operacyjnych na maszynach wirtualnych platformy Azure](https://support.microsoft.com/help/4021388/support-for-32-bit-operating-systems-in-azure-virtual-machines).
>2. Jeśli do migracji obciążenia, takiego jak usługa Azure Site Recovery lub Azure Migrate, narzędzie do odzyskiwania po awarii będzie używane do migracji, proces ten jest nadal wymagany do wykonania i obserwowany w systemie operacyjnym gościa w celu przygotowania obrazu przed migracją.

## <a name="system-file-checker-sfc-command"></a>Polecenie Sprawdzanie plików systemowych (SFC)

### <a name="run-windows-system-file-checker-utility-run-sfc-scannow-on-os-prior-to-generalization-step-of-creating-customer-os-image"></a>Uruchom narzędzie Windows System File Checker (uruchom sfc /scannow) w systemie operacyjnym przed etapem uogólnienia tworzenia obrazu systemu operacyjnego klienta

Polecenie System File Checker (SFC) służy do weryfikacji i zastępowania plików systemowych Systemu Windows.

Aby uruchomić polecenie SFC:

1. Otwórz monit CMD z podwyższonym poziomem uprawnień jako administrator.
1. Wpisz `sfc /scannow` i wybierz **enter**.

    ![ narzędzie sprawdzania plików systemowych](media/prepare-for-upload-vhd-image/system-file-checker.png)


Po zakończeniu skanowania SFC spróbuj zainstalować aktualizacje systemu Windows i ponownie uruchomić komputer.

## <a name="convert-the-virtual-disk-to-a-fixed-size-and-to-vhd"></a>Konwertowanie dysku wirtualnego na stały rozmiar i dysk VHD

Jeśli chcesz przekonwertować dysk wirtualny na wymagany format platformy Azure, użyj jednej z metod w tej sekcji:

1. Przed uruchomieniem procesu konwersji dysku wirtualnego należy przeprowadzić utworzenie kopii zapasowej maszyny Wirtualnej.

1. Upewnij się, że dysk VHD systemu Windows działa poprawnie na serwerze lokalnym. Rozwiąż wszelkie błędy w samej maszynie wirtualnej przed podjęciem próby konwersji lub przekazania jej na platformę Azure.

1. Jeśli chodzi o rozmiar VHD:

   1. Wszystkie dyski VHD na platformie Azure muszą mieć rozmiar wirtualny wyrównany do 1 MB. Podczas konwersji z dysku nieprzetworzonego na dysk wirtualny należy upewnić się, że rozmiar dysku nieprzetworzonego jest wielokrotnością 1 MB przed konwersją. Ułamki megabajta spowoduje błędy podczas tworzenia obrazów z przesłanego dysku VHD.

   2. Maksymalny rozmiar dozwolony dla OS VHD wynosi 2 TB.


Po przekonwertowaniu dysku utwórz maszynę wirtualną, która używa dysku. Uruchom i zaloguj się do maszyny Wirtualnej, aby zakończyć przygotowywanie jej do przesłania.

### <a name="use-hyper-v-manager-to-convert-the-disk"></a>Konwertowanie dysku za pomocą Menedżera funkcji Hyper-V 
1. Otwórz Menedżera funkcji Hyper-V i wybierz komputer lokalny po lewej stronie. W menu nad listą komputerów wybierz pozycję **Akcja** > **edytuj dysk**.
2. Na stronie **Lokalizowanie wirtualnego dysku twardego** wybierz dysk wirtualny.
3. Na stronie **Wybierz akcję** wybierz pozycję **Konwertuj** > **dalej**.
4. Jeśli chcesz przekonwertować z VHDX, wybierz **VHD** > **Next**.
5. Jeśli chcesz przekonwertować z dynamicznie rozwijającego się dysku, wybierz **opcję Stały rozmiar** > **Dalej**.
6. Znajdź i wybierz ścieżkę, w którą chcesz zapisać nowy plik VHD.
7. Wybierz **pozycję Zakończ**.

> [!NOTE]
> Użyj podwyższonej liczby sesji programu PowerShell, aby uruchomić polecenia w tym artykule.

### <a name="use-powershell-to-convert-the-disk"></a>Konwertowanie dysku za pomocą programu PowerShell 
Dysk wirtualny można przekonwertować za pomocą polecenia [Konwertuj vhd](https://technet.microsoft.com/library/hh848454.aspx) w programie Windows PowerShell. Po uruchomieniu programu PowerShell wybierz **pozycję Uruchom jako administrator.** 

Poniższe przykładowe polecenie konwertuje dysk z VHDX na VHD. Polecenie konwertuje również dysk z dynamicznie rozszerzającego się dysku na dysk o stałym rozmiarze.

```Powershell
Convert-VHD –Path c:\test\MY-VM.vhdx –DestinationPath c:\test\MY-NEW-VM.vhd -VHDType Fixed
```

W tym poleceniu `-Path` zastąp wartość ścieżką do wirtualnego dysku twardego, który chcesz przekonwertować. Zastąp `-DestinationPath` wartość nowej ścieżki i nazwy przekonwertowanego dysku.

### <a name="convert-from-vmware-vmdk-disk-format"></a>Konwersja z formatu dysku VMware VMDK
Jeśli masz obraz maszyny Wirtualnej systemu Windows w [formacie VMDK,](https://en.wikipedia.org/wiki/VMDK)użyj [konwertera maszyn wirtualnych firmy Microsoft,](https://www.microsoft.com/download/details.aspx?id=42497) aby przekonwertować go na format VHD. Aby uzyskać więcej informacji, zobacz [Jak przekonwertować VMware VMDK na dysk VHD funkcji Hyper-V](https://blogs.msdn.com/b/timomta/archive/2015/06/11/how-to-convert-a-vmware-vmdk-to-hyper-v-vhd.aspx).

## <a name="set-windows-configurations-for-azure"></a>Ustawianie konfiguracji systemu Windows na platformie Azure

> [!NOTE]
> Platforma Azure montuje plik ISO do dysku DVD-ROM, gdy maszyna wirtualna systemu Windows jest tworzona na podstawie uogólnionego obrazu.
> Z tego powodu dysk DVD-ROM musi być włączony w os w obrazie uogólnionym. Jeśli jest wyłączona, maszyna wirtualna systemu Windows zostanie zablokowana w OOBE.

Na maszynie wirtualnej, którą zamierzasz przekazać na platformę Azure, uruchom następujące polecenia z okna wiersza polecenia z [podwyższonym poziomem uprawnień:](https://technet.microsoft.com/library/cc947813.aspx)

1. Usuń wszystkie statyczne trasy trwałe w tabeli routingu:
   
   * Aby wyświetlić tabelę `route print` tras, uruchom w wierszu polecenia.
   * Sprawdź `Persistence Routes` sekcje. Jeśli istnieje trasa trwała, użyj `route delete` polecenia, aby ją usunąć.
2. Usuń serwer proxy WinHTTP:
   
    ```PowerShell
    netsh winhttp reset proxy
    ```

    Jeśli maszyna wirtualna musi pracować z określonym serwerem proxy, dodaj wyjątek serwera proxy do adresu IP platformy Azure[(168.63.129.16](https://blogs.msdn.microsoft.com/mast/2015/05/18/what-is-the-ip-address-168-63-129-16/
)), aby maszyna wirtualna mogła połączyć się z platformą Azure:
    ```
    $proxyAddress="<your proxy server>"
    $proxyBypassList="<your list of bypasses>;168.63.129.16"

    netsh winhttp set proxy $proxyAddress $proxyBypassList
    ```

3. Ustaw zasady sieci [`Onlineall`](https://technet.microsoft.com/library/gg252636.aspx)SAN dysku na:
   
    ```PowerShell
    diskpart 
    ```
    W otwartym oknie wiersza polecenia wpisz następujące polecenia:

     ```DISKPART
    san policy=onlineall
    exit   
    ```

4. Ustaw czas skoordynowanego czasu uniwersalnego (UTC) dla systemu Windows. Ustaw również typ uruchamiania usługi`w32time`czasu `Automatic`systemu Windows ( ) na :
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\TimeZoneInformation' -Name "RealTimeIsUniversal" -Value 1 -Type DWord -Force

    Set-Service -Name w32time -StartupType Automatic
    ```
5. Ustaw profil mocy na wysoką wydajność:

    ```PowerShell
    powercfg /setactive SCHEME_MIN
    ```
6. Upewnij się, że `TEMP` `TMP` zmienne środowiskowe są ustawione na ich wartości domyślne:

    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Environment' -Name "TEMP" -Value "%SystemRoot%\TEMP" -Type ExpandString -Force

    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Environment' -Name "TMP" -Value "%SystemRoot%\TEMP" -Type ExpandString -Force
    ```

## <a name="check-the-windows-services"></a>Sprawdzanie usług systemu Windows
Upewnij się, że każda z następujących usług systemu Windows jest ustawiona na wartości domyślne systemu Windows. Te usługi są minimum, które muszą być skonfigurowane w celu zapewnienia łączności maszyny Wirtualnej. Aby zresetować ustawienia uruchamiania, uruchom następujące polecenia:
   
```PowerShell
Get-Service -Name bfe | Where-Object { $_.StartType -ne 'Automatic' } | Set-Service -StartupType 'Automatic'
Get-Service -Name dhcp | Where-Object { $_.StartType -ne 'Automatic' } | Set-Service -StartupType 'Automatic'
Get-Service -Name dnscache | Where-Object { $_.StartType -ne 'Automatic' } | Set-Service -StartupType 'Automatic'
Get-Service -Name IKEEXT | Where-Object { $_.StartType -ne 'Automatic' } | Set-Service -StartupType 'Automatic'
Get-Service -Name iphlpsvc | Where-Object { $_.StartType -ne 'Automatic' } | Set-Service -StartupType 'Automatic'
Get-Service -Name netlogon | Where-Object { $_.StartType -ne 'Manual' } | Set-Service -StartupType 'Manual'
Get-Service -Name netman | Where-Object { $_.StartType -ne 'Manual' } | Set-Service -StartupType 'Manual'
Get-Service -Name nsi | Where-Object { $_.StartType -ne 'Automatic' } | Set-Service -StartupType 'Automatic'
Get-Service -Name TermService | Where-Object { $_.StartType -ne 'Manual' } | Set-Service -StartupType 'Manual'
Get-Service -Name MpsSvc | Where-Object { $_.StartType -ne 'Automatic' } | Set-Service -StartupType 'Automatic'
Get-Service -Name RemoteRegistry | Where-Object { $_.StartType -ne 'Automatic' } | Set-Service -StartupType 'Automatic'
```
## <a name="update-remote-desktop-registry-settings"></a>Aktualizowanie ustawień rejestru pulpitu zdalnego
Upewnij się, że następujące ustawienia są poprawnie skonfigurowane dla dostępu zdalnego:

>[!NOTE] 
>Po uruchomieniu `Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services -Name <object name> -Value <value>`może pojawić się komunikat o błędzie . Możesz bezpiecznie zignorować ten komunikat. Oznacza to tylko, że domena nie wypycha tej konfiguracji za pośrednictwem obiektu zasad grupy.

1. Protokół RDP (Remote Desktop Protocol) jest włączony:
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server' -Name "fDenyTSConnections" -Value 0 -Type DWord -Force

    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -Name "fDenyTSConnections" -Value 0 -Type DWord -Force
    ```
   
2. Port RDP jest poprawnie skonfigurowany. Domyślny port to 3389:
   
    ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name "PortNumber" -Value 3389 -Type DWord -Force
    ```
    Podczas wdrażania maszyny Wirtualnej reguły domyślne są tworzone dla portu 3389. Jeśli chcesz zmienić numer portu, zrób to po wdrożeniu maszyny wirtualnej na platformie Azure.

3. Odbiornik nasłuchuje w każdym interfejsie sieciowym:
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name "LanAdapter" -Value 0 -Type DWord -Force
   ```
4. Skonfiguruj tryb uwierzytelniania na poziomie sieci (NLA) dla połączeń RDP:
   
    ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -Name "UserAuthentication" -Value 1 -Type DWord -Force

    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -Name "SecurityLayer" -Value 1 -Type DWord -Force

    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -Name "fAllowSecProtocolNegotiation" -Value 1 -Type DWord -Force
     ```

5. Ustaw wartość keep-alive:
    
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -Name "KeepAliveEnable" -Value 1  -Type DWord -Force
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -Name "KeepAliveInterval" -Value 1  -Type DWord -Force
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name "KeepAliveTimeout" -Value 1 -Type DWord -Force
    ```
6. Ponownie:
    
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -Name "fDisableAutoReconnect" -Value 0 -Type DWord -Force
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name "fInheritReconnectSame" -Value 1 -Type DWord -Force
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name "fReconnectSame" -Value 0 -Type DWord -Force
    ```
7. Ogranicz liczbę równoczesnych połączeń:
    
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name "MaxInstanceCount" -Value 4294967295 -Type DWord -Force
    ```
8. Usuń wszystkie certyfikaty z podpisem własnym powiązane ze odbiornikiem RDP:
    
    ```PowerShell
    if ((Get-Item -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp').Property -contains "SSLCertificateSHA1Hash")
    {
        Remove-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -Name "SSLCertificateSHA1Hash" -Force
    }
    ```
    Ten kod zapewnia, że można połączyć się na początku podczas wdrażania maszyny Wirtualnej. Jeśli chcesz przejrzeć to później, możesz to zrobić po wdrożeniu maszyny wirtualnej na platformie Azure.

9. Jeśli maszyna wirtualna będzie częścią domeny, sprawdź następujące zasady, aby upewnić się, że poprzednie ustawienia nie są przywracane. 
    
    | Cel                                     | Zasady                                                                                                                                                       | Wartość                                                                                    |
    |------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------|
    | RDP jest włączone                           | Konfiguracja komputera\Zasady\Ustawienia systemu Windows\Szablony administracyjne\Składniki\Usługi pulpitu zdalnego\Host sesji usług pulpitu zdalnego\Połączenia         | Zezwalaj użytkownikom na zdalne łączenie się przy użyciu pulpitu zdalnego                                  |
    | Zasady grupy NLA                         | Ustawienia\Szablony administracyjne\Składniki\Usługi pulpitu zdalnego\Host sesji usług pulpitu zdalnego\Zabezpieczenia                                                    | Wymagaj uwierzytelniania użytkownika dla dostępu zdalnego przy użyciu NLA |
    | Ustawienia keep-alive                      | Konfiguracja komputera\Zasady\Ustawienia systemu Windows\Szablony administracyjne\Składniki systemu Windows\Usługi pulpitu zdalnego\Host sesji usług pulpitu zdalnego\Połączenia | Konfigurowanie interwału połączenia keep-alive                                                 |
    | Ustawienia ponownego połączenia                       | Konfiguracja komputera\Zasady\Ustawienia systemu Windows\Szablony administracyjne\Składniki systemu Windows\Usługi pulpitu zdalnego\Host sesji usług pulpitu zdalnego\Połączenia | Automatyczne łączenie się ponownie                                                                   |
    | Ograniczona liczba ustawień połączenia | Konfiguracja komputera\Zasady\Ustawienia systemu Windows\Szablony administracyjne\Składniki systemu Windows\Usługi pulpitu zdalnego\Host sesji usług pulpitu zdalnego\Połączenia | Ogranicz liczbę połączeń                                                              |

## <a name="configure-windows-firewall-rules"></a>Konfigurowanie reguł Zapory systemu Windows
1. Włącz Zaporę systemu Windows w trzech profilach (domena, standard i publiczny):

   ```PowerShell
    Set-NetFirewallProfile -Profile Domain,Public,Private -Enabled True
   ```

2. Uruchom następujące polecenie w programie PowerShell, aby zezwolić na usługę WinRM za pośrednictwem trzech profilów zapory (domeny, prywatnej i publicznej) i włączyć usługę zdalną programu PowerShell:
   
   ```PowerShell
    Enable-PSRemoting -Force

    Set-NetFirewallRule -DisplayName "Windows Remote Management (HTTP-In)" -Enabled True
   ```
3. Włącz następujące reguły zapory, aby zezwolić na ruch RDP:

   ```PowerShell
    Set-NetFirewallRule -DisplayGroup "Remote Desktop" -Enabled True
   ```   
4. Włącz regułę udostępniania plików i drukarek, aby maszyna wirtualna mogła odpowiedzieć na polecenie ping wewnątrz sieci wirtualnej:

   ```PowerShell
   Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -Enabled True
   ``` 
5. Utwórz regułę dla sieci platformy Azure:

   ```PowerShell
    New-NetFirewallRule -DisplayName "AzurePlatform" -Direction Inbound -RemoteAddress 168.63.129.16 -Profile Any -Action Allow -EdgeTraversalPolicy Allow
    New-NetFirewallRule -DisplayName "AzurePlatform" -Direction Outbound -RemoteAddress 168.63.129.16 -Profile Any -Action Allow
   ``` 
6. Jeśli maszyna wirtualna będzie częścią domeny, sprawdź następujące zasady usługi Azure AD, aby upewnić się, że poprzednie ustawienia nie są przywracane. 

    | Cel                                 | Zasady                                                                                                                                                  | Wartość                                   |
    |--------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------|
    | Włączanie profilów Zapory systemu Windows | Konfiguracja komputera\Zasady\Ustawienia systemu Windows\Szablony administracyjne\Sieć\Połączenie sieciowe\Zapora systemu Windows\Profil domeny\Zapora systemu Windows   | Ochrona wszystkich połączeń sieciowych         |
    | Włączanie protokołu RDP                           | Konfiguracja komputera\Zasady\Ustawienia systemu Windows\Szablony administracyjne\Sieć\Połączenie sieciowe\Zapora systemu Windows\Profil domeny\Zapora systemu Windows   | Zezwalaj na wyjątki przychodzącego pulpitu zdalnego |
    |                                      | Konfiguracja komputera\Zasady\Ustawienia systemu Windows\Szablony administracyjne\Sieć\Połączenie sieciowe\Zapora systemu Windows\Profil standardowy\Zapora systemu Windows | Zezwalaj na wyjątki przychodzącego pulpitu zdalnego |
    | Włączanie ICMP-V4                       | Konfiguracja komputera\Zasady\Ustawienia systemu Windows\Szablony administracyjne\Sieć\Połączenie sieciowe\Zapora systemu Windows\Profil domeny\Zapora systemu Windows   | Zezwalaj na wyjątki ICMP                   |
    |                                      | Konfiguracja komputera\Zasady\Ustawienia systemu Windows\Szablony administracyjne\Sieć\Połączenie sieciowe\Zapora systemu Windows\Profil standardowy\Zapora systemu Windows | Zezwalaj na wyjątki ICMP                   |

## <a name="verify-the-vm"></a>Sprawdź maszynę wirtualną 

Upewnij się, że maszyna wirtualna jest w dobrej kondycji, bezpieczna i rdp dostępne: 

1. Aby upewnić się, że dysk jest w dobrej kondycji i spójny, sprawdź dysk przy następnym ponownym uruchomieniu maszyny Wirtualnej:

    ```PowerShell
    Chkdsk /f
    ```
    Upewnij się, że raport pokazuje czysty i zdrowy dysk.

2. Ustawianie ustawień danych konfiguracji rozruchu (BCD). 

    > [!NOTE]
    > Użyj podwyższonego poziomu okna programu PowerShell, aby uruchomić te polecenia.
   
   ```powershell
    bcdedit /set "{bootmgr}" integrityservices enable
    bcdedit /set "{default}" device partition=C:
    bcdedit /set "{default}" integrityservices enable
    bcdedit /set "{default}" recoveryenabled Off
    bcdedit /set "{default}" osdevice partition=C:
    bcdedit /set "{default}" bootstatuspolicy IgnoreAllFailures

    #Enable Serial Console Feature
    bcdedit /set "{bootmgr}" displaybootmenu yes
    bcdedit /set "{bootmgr}" timeout 5
    bcdedit /set "{bootmgr}" bootems yes
    bcdedit /ems "{current}" ON
    bcdedit /emssettings EMSPORT:1 EMSBAUDRATE:115200
   ```
3. Dziennik zrzutu może być pomocny w rozwiązywaniu problemów z awariami systemu Windows. Włącz kolekcję dziennika zrzutu:

    ```powershell
    # Set up the guest OS to collect a kernel dump on an OS crash event
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -Name CrashDumpEnabled -Type DWord -Force -Value 2
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -Name DumpFile -Type ExpandString -Force -Value "%SystemRoot%\MEMORY.DMP"
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -Name NMICrashDump -Type DWord -Force -Value 1

    # Set up the guest OS to collect user mode dumps on a service crash event
    $key = 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps'
    if ((Test-Path -Path $key) -eq $false) {(New-Item -Path 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting' -Name LocalDumps)}
    New-ItemProperty -Path $key -Name DumpFolder -Type ExpandString -Force -Value "c:\CrashDumps"
    New-ItemProperty -Path $key -Name CrashCount -Type DWord -Force -Value 10
    New-ItemProperty -Path $key -Name DumpType -Type DWord -Force -Value 2
    Set-Service -Name WerSvc -StartupType Manual
    ```
4. Sprawdź, czy repozytorium Instrumentacja zarządzania windowsem (WMI) jest spójne:

    ```PowerShell
    winmgmt /verifyrepository
    ```
    Jeśli repozytorium jest uszkodzone, zobacz [WMI: Uszkodzenie repozytorium lub nie](https://blogs.technet.microsoft.com/askperf/2014/08/08/wmi-repository-corruption-or-not).

5. Upewnij się, że żadna inna aplikacja nie używa portu 3389. Ten port jest używany dla usługi RDP na platformie Azure. Aby zobaczyć, które porty są `netstat -anob`używane na maszynie Wirtualnej, uruchom:

    ```PowerShell
    netstat -anob
    ```

6. Aby przekazać dysk VHD systemu Windows, który jest kontrolerem domeny:

   * Wykonaj [te dodatkowe kroki,](https://support.microsoft.com/kb/2904015) aby przygotować dysk.

   * Upewnij się, że znasz hasło trybu przywracania usług katalogowych (DSRM) w przypadku, gdy trzeba uruchomić maszynę wirtualną w DSRM w pewnym momencie. Aby uzyskać więcej informacji, zobacz [Ustawianie hasła DSRM](https://technet.microsoft.com/library/cc754363(v=ws.11).aspx).

7. Upewnij się, że znasz wbudowane konto administratora i hasło. Możesz zresetować bieżące hasło administratora lokalnego i upewnić się, że możesz użyć tego konta do logowania się do systemu Windows za pośrednictwem połączenia RDP. To uprawnienie dostępu jest kontrolowane przez obiekt zasad grupy "Zezwalaj na logowanie za pośrednictwem usług pulpitu zdalnego". Zobacz ten obiekt w Edytorze lokalnych zasad grupy tutaj:

    Konfiguracja komputera\Ustawienia systemu Windows\Ustawienia zabezpieczeń\Zasady lokalne\Przypisanie praw użytkownika

8. Sprawdź następujące zasady usługi Azure AD, aby upewnić się, że nie blokujesz dostępu do protokołu RDP za pośrednictwem protokołu RDP lub z sieci:

    - Konfiguracja komputera\Ustawienia systemu Windows\Ustawienia zabezpieczeń\Zasady lokalne\Przypisanie praw użytkownika\Odmów dostępu do tego komputera z sieci

    - Konfiguracja komputera\Ustawienia systemu Windows\Ustawienia zabezpieczeń\Zasady lokalne\Przypisywanie praw użytkownika\Odmów logowanie za pośrednictwem usług pulpitu zdalnego


9. Sprawdź następujące zasady usługi Azure AD, aby upewnić się, że nie usuwasz żadnych wymaganych kont dostępu:

   - Konfiguracja komputera\Ustawienia systemu Windows\Ustawienia zabezpieczeń\Zasady lokalne\Przypisanie praw użytkownika\Dostęp do tego komputera z sieci

   Zasady powinny wymieniać następujące grupy:

   - Administratorzy

   - Operatorzy kopii zapasowych

   - Wszyscy

   - Użytkownicy

10. Uruchom ponownie maszynę wirtualną, aby upewnić się, że system Windows jest nadal w dobrej kondycji i można uzyskać kontakt za pośrednictwem połączenia RDP. W tym momencie można utworzyć maszynę wirtualną w lokalnym funkcji Hyper-V, aby upewnić się, że maszyna wirtualna uruchamia się całkowicie. Następnie przetestuj, aby upewnić się, że możesz dotrzeć do maszyny Wirtualnej za pośrednictwem protokołu RDP.

11. Usuń wszystkie dodatkowe filtry TDI (Transport Driver Interface). Na przykład usuń oprogramowanie, które analizuje pakiety TCP lub dodatkowe zapory. Jeśli chcesz przejrzeć to później, możesz to zrobić po wdrożeniu maszyny wirtualnej na platformie Azure.

12. Odinstaluj inne oprogramowanie lub sterownik innych firm, które są związane ze składnikami fizycznymi lub inną technologią wirtualizacji.

### <a name="install-windows-updates"></a>Instalowanie aktualizacji systemu Windows
Najlepiej, jeśli urządzenie powinno być aktualizowane na *poziomie plastra.* Jeśli nie jest to możliwe, upewnij się, że są zainstalowane następujące aktualizacje. Aby uzyskać najnowsze aktualizacje, zobacz strony historii aktualizacji systemu Windows: [Windows 10 i Windows Server 2019](https://support.microsoft.com/help/4000825), [Windows 8.1 i Windows Server 2012 R2](https://support.microsoft.com/help/4009470) oraz [Windows 7 z SP1 i Windows Server 2008 R2 z znp.](https://support.microsoft.com/help/4009469)

| Składnik               | plików binarnych         | Windows 7 z dodatku SP1, Windows Server 2008 R2 zw. | Windows 8, Windows Server 2012               | Windows 8.1, Windows Server 2012 R2 | Windows 10 v1607, Windows Server 2016 w wersji 1607 | Windows 10 w wersji 1703    | Windows 10 v1709, Windows Server 2016 v1709 | Windows 10 v1803, Windows Server 2016 v1803 |
|-------------------------|----------------|-------------------------------------------|---------------------------------------------|------------------------------------|---------------------------------------------------------|----------------------------|-------------------------------------------------|-------------------------------------------------|
| Magazyn                 | Disk.sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.17638 / 6.2.9200.21757 - KB3137061 | 6.3.9600.18203 - KB3137061         | -                                                       | -                          | -                                               | -                                               |
|                         | Storport.sys   | 6.1.7601.23403 - KB3125574                | 6.2.9200.17188 / 6.2.9200.21306 - KB3018489 | 6.3.9600.18573 - KB4022726         | 10.0.14393.1358 - KB4022715                             | 10.0.15063.332             | -                                               | -                                               |
|                         | Ntfs.sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.17623 / 6.2.9200.21743 - KB3121255 | 6.3.9600.18654 - KB4022726         | 10.0.14393.1198 - KB4022715                             | 10.0.15063.447             | -                                               | -                                               |
|                         | Plik Iologmsg.dll   | 6.1.7601.23403 - KB3125574                | 6.2.9200.16384 - KB2995387                  | -                                  | -                                                       | -                          | -                                               | -                                               |
|                         | Classpnp.sys   | 6.1.7601.23403 - KB3125574                | 6.2.9200.17061 / 6.2.9200.21180 - KB2995387 | 6.3.9600.18334 - KB3172614         | 10.0.14393.953 - KB4022715                              | -                          | -                                               | -                                               |
|                         | Volsnap.sys    | 6.1.7601.23403 - KB3125574                | 6.2.9200.17047 / 6.2.9200.21165 - KB2975331 | 6.3.9600.18265 - KB3145384         | -                                                       | 10.0.15063.0               | -                                               | -                                               |
|                         | Partmgr.sys    | 6.1.7601.23403 - KB3125574                | 6.2.9200.16681 - KB2877114                  | 6.3.9600.17401 - KB3000850         | 10.0.14393.953 - KB4022715                              | 10.0.15063.0               | -                                               | -                                               |
|                         | volmgr.sys     |                                           |                                             |                                    |                                                         | 10.0.15063.0               | -                                               | -                                               |
|                         | Volmgrx.sys    | 6.1.7601.23403 - KB3125574                | -                                           | -                                  | -                                                       | 10.0.15063.0               | -                                               | -                                               |
|                         | Msiscsi.sys    | 6.1.7601.23403 - KB3125574                | 6.2.9200.21006 - KB2955163                  | 6.3.9600.18624 - KB4022726         | 10.0.14393.1066 - KB4022715                             | 10.0.15063.447             | -                                               | -                                               |
|                         | Msdsm.sys      | 6.1.7601.23403 - KB3125574                | 6.2.9200.21474 - KB3046101                  | 6.3.9600.18592 - KB4022726         | -                                                       | -                          | -                                               | -                                               |
|                         | Mpio.sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.21190 - KB3046101                  | 6.3.9600.18616 - KB4022726         | 10.0.14393.1198 - KB4022715                             | -                          | -                                               | -                                               |
|                         | vmstorfl.sys   | 6.3.9600.18907 - KB4072650                | 6.3.9600.18080 - KB3063109                  | 6.3.9600.18907 - KB4072650         | 10.0.14393.2007 - KB4345418                             | 10.0.15063.850 - KB4345419 | 10.0.16299.371 - KB4345420                      | -                                               |
|                         | Plik Fveapi.dll     | 6.1.7601.23311 - KB3125574                | 6.2.9200.20930 - KB2930244                  | 6.3.9600.18294 - KB3172614         | 10.0.14393.576 - KB4022715                              | -                          | -                                               | -                                               |
|                         | Plik Fveapibase.dll | 6.1.7601.23403 - KB3125574                | 6.2.9200.20930 - KB2930244                  | 6.3.9600.17415 - KB3172614         | 10.0.14393.206 - KB4022715                              | -                          | -                                               | -                                               |
| Network (Sieć)                 | netvsc.sys     | -                                         | -                                           | -                                  | 10.0.14393.1198 - KB4022715                             | 10.0.15063.250 - KB4020001 | -                                               | -                                               |
|                         | mrxsmb10.sys   | 6.1.7601.23816 - KB4022722                | 6.2.9200.22108 - KB4022724                  | 6.3.9600.18603 - KB4022726         | 10.0.14393.479 - KB4022715                              | 10.0.15063.483             | -                                               | -                                               |
|                         | mrxsmb20.sys   | 6.1.7601.23816 - KB4022722                | 6.2.9200.21548 - KB4022724                  | 6.3.9600.18586 - KB4022726         | 10.0.14393.953 - KB4022715                              | 10.0.15063.483             | -                                               | -                                               |
|                         | mrxsmb.sys     | 6.1.7601.23816 - KB4022722                | 6.2.9200.22074 - KB4022724                  | 6.3.9600.18586 - KB4022726         | 10.0.14393.953 - KB4022715                              | 10.0.15063.0               | -                                               | -                                               |
|                         | Tcpip.sys      | 6.1.7601.23761 - KB4022722                | 6.2.9200.22070 - KB4022724                  | 6.3.9600.18478 - KB4022726         | 10.0.14393.1358 - KB4022715                             | 10.0.15063.447             | -                                               | -                                               |
|                         | http.sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.17285 - KB3042553                  | 6.3.9600.18574 - KB4022726         | 10.0.14393.251 - KB4022715                              | 10.0.15063.483             | -                                               | -                                               |
|                         | plik vmswitch.sys   | 6.1.7601.23727 - KB4022719                | 6.2.9200.22117 - KB4022724                  | 6.3.9600.18654 - KB4022726         | 10.0.14393.1358 - KB4022715                             | 10.0.15063.138             | -                                               | -                                               |
| Podstawowe                    | Ntoskrnl.exe   | 6.1.7601.23807 - KB4022719                | 6.2.9200.22170 - KB4022718                  | 6.3.9600.18696 - KB4022726         | 10.0.14393.1358 - KB4022715                             | 10.0.15063.483             | -                                               | -                                               |
| Usługi pulpitu zdalnego | plik rdpcorets.dll  | 6.2.9200.21506 - KB4022719                | 6.2.9200.22104 - KB4022724                  | 6.3.9600.18619 - KB4022726         | 10.0.14393.1198 - KB4022715                             | 10.0.15063.0               | -                                               | -                                               |
|                         | Termsrv.dll    | 6.1.7601.23403 - KB3125574                | 6.2.9200.17048 - KB2973501                  | 6.3.9600.17415 - KB3000850         | 10.0.14393.0 - KB4022715                                | 10.0.15063.0               | -                                               | -                                               |
|                         | termdd.sys     | 6.1.7601.23403 - KB3125574                | -                                           | -                                  | -                                                       | -                          | -                                               | -                                               |
|                         | win32k.sys     | 6.1.7601.23807 - KB4022719                | 6.2.9200.22168 - KB4022718                  | 6.3.9600.18698 - KB4022726         | 10.0.14393.594 - KB4022715                              | -                          | -                                               | -                                               |
|                         | plik rdpdd.dll      | 6.1.7601.23403 - KB3125574                | -                                           | -                                  | -                                                       | -                          | -                                               | -                                               |
|                         | rdpwd.sys      | 6.1.7601.23403 - KB3125574                | -                                           | -                                  | -                                                       | -                          | -                                               | -                                               |
| Zabezpieczenia                | MS17-010       | aktualizacja KB4012212                                 | aktualizacja KB4012213                                   | aktualizacja KB4012213                          | KB4012606                                               | KB4012606                  | -                                               | -                                               |
|                         |                |                                           | KB4012216                                   |                                    | KB4013198                                               | KB4013198                  | -                                               | -                                               |
|                         |                | KB4012215                                 | KB4012214                                   | KB4012216                          | aktualizacja KB4013429                                               | aktualizacja KB4013429                  | -                                               | -                                               |
|                         |                |                                           | aktualizacja KB4012217                                   |                                    | aktualizacja KB4013429                                               | aktualizacja KB4013429                  | -                                               | -                                               |
|                         | CVE-2018-0886  | aktualizacja KB4103718               | aktualizacja KB4103730                | aktualizacja KB4103725       | aktualizacja KB4103723                                               | aktualizacja KB4103731                  | aktualizacja KB4103727                                       | aktualizacja KB4103721                                       |
|                         |                | aktualizacja KB4103712          | aktualizacja KB4103726          | aktualizacja KB4103715|                                                         |                            |                                                 |                                                 |
       
> [!NOTE]
> Aby uniknąć przypadkowego ponownego uruchomienia podczas inicjowania obsługi administracyjnej maszyny Wirtualnej, zaleca się upewnienie się, że wszystkie instalacje usługi Windows Update są zakończone i że nie są oczekujące żadne aktualizacje. Jednym ze sposobów, aby to zrobić, jest zainstalowanie wszystkich możliwych aktualizacji systemu Windows i ponowne uruchomienie raz przed uruchomieniem polecenia Sysprep.

### <a name="determine-when-to-use-sysprep"></a>Określanie, kiedy należy używać programu Sysprep<a id="step23"></a>    

Narzędzie do przygotowania systemu (Sysprep) to proces, który można uruchomić w celu zresetowania instalacji systemu Windows. Sysprep zapewnia środowisko "po wyjęciu z pudełka", usuwając wszystkie dane osobowe i resetując kilka składników. 

Zazwyczaj uruchamiasz narzędzie Sysprep, aby utworzyć szablon, z którego można wdrożyć kilka innych maszyn wirtualnych, które mają określoną konfigurację. Szablon jest nazywany *obrazem uogólnionym*.

Jeśli chcesz utworzyć tylko jedną maszynę wirtualną z jednego dysku, nie musisz używać narzędzia Sysprep. Zamiast tego można utworzyć maszynę wirtualną na podstawie *wyspecjalizowanego obrazu*. Aby uzyskać informacje dotyczące tworzenia maszyny Wirtualnej na dysku specjalistycznym, zobacz:

- [Tworzenie maszyny wirtualnej na podstawie wyspecjalizowanego dysku](create-vm-specialized.md)
- [Tworzenie maszyny wirtualnej na specjalistycznym dysku VHD](https://docs.microsoft.com/azure/virtual-machines/windows/create-vm-specialized-portal?branch=master)

Jeśli chcesz utworzyć obraz uogólniony, musisz uruchomić sysprep. Aby uzyskać więcej informacji, zobacz [Jak używać Sysprep: Wprowadzenie](https://technet.microsoft.com/library/bb457073.aspx). 

Nie każda rola lub aplikacja zainstalowana na komputerze z systemem Windows obsługuje obrazy uogólnione. Dlatego przed uruchomieniem tej procedury upewnij się, że program Sysprep obsługuje rolę komputera. Aby uzyskać więcej informacji, zobacz [Obsługa programu Sysprep dla ról serwera](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles).

### <a name="generalize-a-vhd"></a>Uogólnianie dysku VHD

>[!NOTE]
> Po uruchomieniu `sysprep.exe` w poniższych krokach należy wyłączyć maszynę wirtualną. Nie włączaj go z powrotem, dopóki nie utworzysz obrazu na platformie Azure.

1. Zaloguj się do maszyny Wirtualnej systemu Windows.
1. Uruchom **wiersz polecenia** jako administrator. 
1. Zmień katalog na `%windir%\system32\sysprep`. Następnie należy uruchomić polecenie `sysprep.exe`.
1. W oknie dialogowym **Narzędzie przygotowywania systemu** wybierz pozycję **Włącz systemowy tryb OOBE** i upewnij się, że pole wyboru **Uogólnij** jest zaznaczone.

    ![Narzędzie do przygotowania systemu](media/prepare-for-upload-vhd-image/syspre.png)
1. W **obszarze Opcje zamykania**wybierz pozycję **Zamknięcie**.
1. Kliknij przycisk **OK**.
1. Po zakończeniu sysprep, zamknij maszynę wirtualną. Nie używaj **ponownego uruchamiania,** aby zamknąć maszynę wirtualną.

Teraz VHD jest gotowy do przesłania. Aby uzyskać więcej informacji na temat tworzenia maszyny Wirtualnej z dysku uogólnionego, zobacz [Przekazywanie uogólnionego dysku wirtualnego i używanie jej do tworzenia nowej maszyny wirtualnej na platformie Azure.](sa-upload-generalized.md)


>[!NOTE]
> Niestandardowy plik *unattend.xml* nie jest obsługiwany. Mimo że obsługujemy `additionalUnattendContent` właściwość, która zapewnia tylko ograniczoną obsługę dodawania opcji konfiguracji [powłoki systemu Microsoft Windows](https://docs.microsoft.com/windows-hardware/customize/desktop/unattend/microsoft-windows-shell-setup) do pliku *unattend.xml* używanego przez agenta inicjowania obsługi administracyjnej platformy Azure. Można użyć, na przykład, [additionalUnattendContent](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.compute.models.additionalunattendcontent?view=azure-dotnet) dodać FirstLogonCommands i LognCommands. Aby uzyskać więcej informacji, zobacz [additionalUnattendContent FirstLogonCommands przykład](https://github.com/Azure/azure-quickstart-templates/issues/1407).


## <a name="complete-the-recommended-configurations"></a>Uzupełnij zalecane konfiguracje
Poniższe ustawienia nie mają wpływu na przesyłanie dysków VHD. Jednak zdecydowanie zaleca się, aby skonfigurować je.

* Zainstaluj [agenta maszyny wirtualnej platformy Azure](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Następnie można włączyć rozszerzenia maszyn wirtualnych. Rozszerzenia maszyn wirtualnych implementują większość funkcji krytycznych, które można użyć z maszynami wirtualnymi. Rozszerzenia będą potrzebne na przykład do resetowania haseł lub konfigurowania protokołu RDP. Aby uzyskać więcej informacji, zobacz [omówienie usługi Azure Virtual Machine Agent](../extensions/agent-windows.md).
* Po utworzeniu maszyny Wirtualnej na platformie Azure zaleca się umieszczenie pliku stronicowa na *woluminie dysku czasowego* w celu zwiększenia wydajności. Umiejscowienie pliku można skonfigurować w następujący sposób:

   ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Memory Management' -Name "PagingFiles" -Value "D:\pagefile.sys" -Type MultiString -Force
   ```
  Jeśli dysk danych jest dołączony do maszyny Wirtualnej, litera woluminu dysku czasowego to zazwyczaj *D*. To oznaczenie może być różne, w zależności od ustawień i liczby dostępnych dysków.
  * Zalecamy wyłączenie blokowania skryptów, które mogą być dostarczane przez oprogramowanie antywirusowe. Mogą one zakłócać i blokować skrypty agenta aprowizacji systemu Windows wykonywane podczas wdrażania nowej maszyny Wirtualnej z obrazu.
  
## <a name="next-steps"></a>Następne kroki
* [Przekazywanie obrazu maszyny Wirtualnej systemu Windows do platformy Azure dla wdrożeń usługi Resource Manager](upload-generalized-managed.md)
* [Rozwiązywanie problemów z aktywacją maszyny Wirtualnej systemu Windows platformy Azure](troubleshoot-activation-problems.md)

