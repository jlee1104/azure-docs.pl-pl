---
title: Używanie programu PowerShell do tworzenia kopii zapasowych obciążeń programu DPM
description: Dowiedz się, jak wdrażać Azure Backup dla Data Protection Manager (DPM) przy użyciu programu PowerShell i zarządzać nimi
ms.topic: conceptual
ms.date: 01/23/2017
ms.openlocfilehash: 06c138a4015a0b730369e091fc57a34d2190051d
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/26/2020
ms.locfileid: "77616730"
---
# <a name="deploy-and-manage-backup-to-azure-for-data-protection-manager-dpm-servers-using-powershell"></a>Wdrażanie kopii zapasowych serwerów Data Protection Manager (DPM) na platformie Azure i zarządzanie nimi przy użyciu programu PowerShell

W tym artykule opisano sposób użycia programu PowerShell w celu skonfigurowania Azure Backup na serwerze programu DPM oraz zarządzania kopiami zapasowymi i odzyskiwaniem.

## <a name="setting-up-the-powershell-environment"></a>Konfigurowanie środowiska programu PowerShell

Aby móc używać programu PowerShell do zarządzania kopiami zapasowymi z programu Data Protection Manager na platformie Azure, musisz mieć odpowiednie środowisko w programie PowerShell. Na początku sesji programu PowerShell upewnij się, że zostało uruchomione następujące polecenie w celu zaimportowania właściwych modułów i poprawnego odwoływania się do poleceń cmdlet programu DPM:

```powershell
& "C:\Program Files\Microsoft System Center 2012 R2\DPM\DPM\bin\DpmCliInitScript.ps1"
```

```Output
Welcome to the DPM Management Shell!

Full list of cmdlets: Get-Command
Only DPM cmdlets: Get-DPMCommand
Get general help: help
Get help for a cmdlet: help <cmdlet-name> or <cmdlet-name> -?
Get definition of a cmdlet: Get-Command <cmdlet-name> -Syntax
Sample DPM scripts: Get-DPMSampleScript
```

## <a name="setup-and-registration"></a>Instalacja i rejestracja

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Aby rozpocząć, [Pobierz najnowszą Azure PowerShell](/powershell/azure/install-az-ps).

Następujące zadania konfiguracji i rejestracji można zautomatyzować za pomocą programu PowerShell:

* Tworzenie magazynu Usług odzyskiwania
* Instalowanie agenta Azure Backup
* Rejestrowanie w usłudze Azure Backup
* Ustawienia sieci
* Ustawienia szyfrowania

## <a name="create-a-recovery-services-vault"></a>Tworzenie magazynu usługi Recovery Services

Poniższe kroki umożliwiają utworzenie magazynu Recovery Services. Magazyn Recovery Services jest inny niż magazyn kopii zapasowych.

1. Jeśli używasz Azure Backup po raz pierwszy, musisz użyć polecenia cmdlet **register-AzResourceProvider** , aby zarejestrować dostawcę usługi Azure Recovery Service w ramach subskrypcji.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

2. Magazyn Recovery Services jest zasobem ARM, więc należy go umieścić w grupie zasobów. Możesz użyć istniejącej grupy zasobów lub utworzyć nową. Podczas tworzenia nowej grupy zasobów należy określić nazwę i lokalizację grupy zasobów.

    ```powershell
    New-AzResourceGroup –Name "test-rg" –Location "West US"
    ```

3. Użyj polecenia cmdlet **New-AzRecoveryServicesVault** , aby utworzyć nowy magazyn. Należy określić tę samą lokalizację dla magazynu, która została użyta dla grupy zasobów.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName " test-rg" -Location "West US"
    ```

4. Określ typ nadmiarowości magazynu do użycia; można użyć [magazynu lokalnie nadmiarowego (LRS)](../storage/common/storage-redundancy-lrs.md) lub [magazynu geograficznie nadmiarowego (GRS)](../storage/common/storage-redundancy-grs.md). W poniższym przykładzie pokazano opcję-BackupStorageRedundancy dla testVault jest ustawiona na wartość geomiarowa.

   > [!TIP]
   > Wiele poleceń cmdlet usługi Azure Backup wymaga obiektu magazynu usługi Recovery Services jako danych wejściowych. Z tego powodu wygodne jest przechowywanie obiektu magazynu usługi Backup Recovery Services w zmiennej.
   >
   >

    ```powershell
    $vault1 = Get-AzRecoveryServicesVault –Name "testVault"
    Set-AzRecoveryServicesBackupProperties  -vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

## <a name="view-the-vaults-in-a-subscription"></a>Wyświetlanie magazynów w ramach subskrypcji

Użyj **Get-AzRecoveryServicesVault** , aby wyświetlić listę wszystkich magazynów w bieżącej subskrypcji. Możesz użyć tego polecenia, aby sprawdzić, czy został utworzony nowy magazyn, lub zobaczyć, jakie magazyny są dostępne w ramach subskrypcji.

Uruchom polecenie Get-AzRecoveryServicesVault i wszystkie magazyny w subskrypcji zostaną wyświetlone na liście.

```powershell
Get-AzRecoveryServicesVault
```

```Output
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```

## <a name="installing-the-azure-backup-agent-on-a-dpm-server"></a>Instalowanie agenta Azure Backup na serwerze programu DPM

Przed zainstalowaniem agenta Azure Backup należy pobrać Instalatora i zaprezentować go w systemie Windows Server. Najnowszą wersję Instalatora można pobrać z [Centrum pobierania Microsoft](https://aka.ms/azurebackup_agent) lub ze strony pulpitu nawigacyjnego magazynu Recovery Services. Zapisz Instalatora w łatwo dostępnej lokalizacji, takiej jak * C:\Downloads\*.

Aby zainstalować agenta, uruchom następujące polecenie w konsoli programu PowerShell z podwyższonym poziomem uprawnień **na serwerze programu DPM**:

```powershell
MARSAgentInstaller.exe /q
```

Spowoduje to zainstalowanie agenta ze wszystkimi opcjami domyślnymi. Instalacja trwa kilka minut w tle. Jeśli nie określisz opcji */Nu* , zostanie otwarte okno **Windows Update** po zakończeniu instalacji, aby sprawdzić dostępność aktualizacji.

Agent zostanie wyświetlony na liście zainstalowanych programów. Aby wyświetlić listę zainstalowanych programów, przejdź do pozycji **Panel sterowania** > **programy** > **programy i funkcje**.

![Agent zainstalowany](./media/backup-dpm-automation/installed-agent-listing.png)

### <a name="installation-options"></a>Opcje instalacji

Aby wyświetlić wszystkie opcje dostępne za pośrednictwem wiersza polecenia, użyj następującego polecenia:

```powershell
MARSAgentInstaller.exe /?
```

Dostępne opcje to:

| Opcja | Szczegóły | Domyślne |
| --- | --- | --- |
| /q |Instalacja cicha |- |
| /p: "Location" |Ścieżka do folderu instalacji agenta Azure Backup. |C:\Program Files\Microsoft Azure Recovery Services Agent |
| /s: "lokalizacja" |Ścieżka do folderu pamięci podręcznej dla agenta Azure Backup. |C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch |
| /m |Zezwól na Microsoft Update |- |
| /nu |Nie sprawdzaj, czy są aktualizacje po zakończeniu instalacji |- |
| /d |Odinstalowuje agenta Microsoft Azure Recovery Services |- |
| /pH Generuj dyrektywę |Adres hosta serwera proxy |- |
| /po |Numer portu hosta serwera proxy |- |
| /pu |Nazwa użytkownika hosta serwera proxy |- |
| /pw |Hasło serwera proxy |- |

## <a name="registering-dpm-to-a-recovery-services-vault"></a>Rejestrowanie programu DPM w magazynie Recovery Services

Po utworzeniu magazynu Recovery Services Pobierz najnowszego agenta i poświadczenia magazynu i Zapisz go w wygodnej lokalizacji, takiej jak C:\Downloads.

```powershell
$credspath = "C:\downloads"
$credsfilename = Get-AzRecoveryServicesVaultSettingsFile -Backup -Vault $vault1 -Path  $credspath
$credsfilename
```

```Output
C:\downloads\testvault\_Sun Apr 10 2016.VaultCredentials
```

Na serwerze DPM uruchom polecenie cmdlet [Start-OBRegistration](https://docs.microsoft.com/powershell/module/msonlinebackup/start-obregistration?view=winserver2012-ps) , aby zarejestrować maszynę w magazynie.

```powershell
$cred = $credspath + $credsfilename
Start-OBRegistration-VaultCredentials $cred -Confirm:$false
```

```Output
CertThumbprint      :7a2ef2caa2e74b6ed1222a5e89288ddad438df2
SubscriptionID      : ef4ab577-c2c0-43e4-af80-af49f485f3d1
ServiceResourceName: testvault
Region              :West US
Machine registration succeeded.
```

### <a name="initial-configuration-settings"></a>Ustawienia konfiguracji początkowej

Po zarejestrowaniu serwera programu DPM w magazynie Recovery Services rozpoczyna się od domyślnych ustawień subskrypcji. Te ustawienia subskrypcji obejmują sieci, szyfrowanie i obszar przejściowy. Aby zmienić ustawienia subskrypcji, musisz najpierw uzyskać dojście do istniejących ustawień (domyślnych) za pomocą polecenia cmdlet [Get-DPMCloudSubscriptionSetting](https://docs.microsoft.com/powershell/module/dataprotectionmanager/get-dpmcloudsubscriptionsetting?view=systemcenter-ps-2019) :

```powershell
$setting = Get-DPMCloudSubscriptionSetting -DPMServerName "TestingServer"
```

Wszystkie modyfikacje są wprowadzane do tego lokalnego obiektu programu PowerShell ```$setting``` a następnie pełny obiekt jest zatwierdzany w programie DPM i Azure Backup do zapisywania ich przy użyciu polecenia cmdlet [Set-DPMCloudSubscriptionSetting](https://docs.microsoft.com/powershell/module/dataprotectionmanager/set-dpmcloudsubscriptionsetting?view=systemcenter-ps-2019) . Musisz użyć flagi ```–Commit```, aby upewnić się, że zmiany są utrwalane. Ustawienia nie będą stosowane i używane przez Azure Backup, chyba że zostanie zatwierdzone.

```powershell
Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -Commit
```

## <a name="networking"></a>Sieć

Jeśli łączność maszyny programu DPM z usługą Azure Backup w Internecie odbywa się za pośrednictwem serwera proxy, należy zapewnić ustawienia serwera proxy dla udanych kopii zapasowych. Odbywa się to przy użyciu ```-ProxyServer```i ```-ProxyPort```, ```-ProxyUsername``` i ```ProxyPassword``` parametrów za pomocą polecenia cmdlet [Set-DPMCloudSubscriptionSetting](https://docs.microsoft.com/powershell/module/dataprotectionmanager/set-dpmcloudsubscriptionsetting?view=systemcenter-ps-2019) . W tym przykładzie nie ma serwera proxy, dlatego wszystkie informacje związane z serwerem proxy są wyraźnie wyczyszczone.

```powershell
Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -NoProxy
```

Użycie przepustowości może być również kontrolowane z opcjami ```-WorkHourBandwidth``` i ```-NonWorkHourBandwidth``` dla danego zestawu dni tygodnia. W tym przykładzie nie ustawiamy żadnego ograniczenia.

```powershell
Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -NoThrottle
```

## <a name="configuring-the-staging-area"></a>Konfigurowanie obszaru przemieszczania

Agent Azure Backup uruchomiony na serwerze programu DPM wymaga tymczasowego magazynu dla danych przywróconych z chmury (lokalny obszar przemieszczania). Skonfiguruj obszar przemieszczania za pomocą polecenia cmdlet [Set-DPMCloudSubscriptionSetting](https://docs.microsoft.com/powershell/module/dataprotectionmanager/set-dpmcloudsubscriptionsetting?view=systemcenter-ps-2019) i parametru ```-StagingAreaPath```.

```powershell
Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -StagingAreaPath "C:\StagingArea"
```

W powyższym przykładzie obszar tymczasowy zostanie ustawiony na *C:\StagingArea* w ```$setting```obiekcie programu PowerShell. Upewnij się, że określony folder już istnieje, lub w przeciwnym razie ostateczne zatwierdzenie ustawień subskrypcji zakończy się niepowodzeniem.

### <a name="encryption-settings"></a>Ustawienia szyfrowania

Dane kopii zapasowej wysyłane do Azure Backup są szyfrowane w celu ochrony poufności danych. Hasło szyfrowania to "hasło" do odszyfrowania danych w momencie przywracania. Ważne jest, aby zachować te informacje bezpiecznie i bezpiecznie po ich ustawieniu.

W poniższym przykładzie pierwsze polecenie konwertuje ciąg ```passphrase123456789``` na bezpieczny ciąg i przypisuje bezpieczny ciąg do zmiennej o nazwie ```$Passphrase```. Drugie polecenie ustawia bezpieczny ciąg w ```$Passphrase``` jako hasło do szyfrowania kopii zapasowych.

```powershell
$Passphrase = ConvertTo-SecureString -string "passphrase123456789" -AsPlainText -Force

Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -EncryptionPassphrase $Passphrase
```

> [!IMPORTANT]
> Przechowuj informacje o hasłach bezpiecznie i bezpiecznie po ich ustawieniu. Nie będzie można przywrócić danych z platformy Azure bez tego hasła.
>
>

W tym momencie należy wykonać wszystkie wymagane zmiany w obiekcie ```$setting```. Pamiętaj, aby zatwierdzić zmiany.

```powershell
Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -Commit
```

## <a name="protect-data-to-azure-backup"></a>Ochrona danych do Azure Backup

W tej sekcji dodasz serwer produkcyjny do programu DPM, a następnie włączy się ochronę danych do lokalnego magazynu programu DPM, a następnie do Azure Backup. W przykładach pokazano, jak utworzyć kopię zapasową plików i folderów. Logikę można łatwo rozszerzyć, aby utworzyć kopię zapasową dowolnego źródła danych obsługiwanego przez program DPM. Wszystkie kopie zapasowe programu DPM podlegają grupie ochrony (PG) z czterema częściami:

1. **Członkowie grupy** to lista wszystkich obiektów chronionych (nazywanych również *źródłami* danych w programie DPM), które mają być chronione w tej samej grupie ochrony. Na przykład może być konieczne Ochrona maszyn wirtualnych w ramach jednej grupy ochrony i SQL Server baz danych w innej grupie ochrony, ponieważ mogą one mieć różne wymagania dotyczące kopii zapasowych. Aby można było utworzyć kopię zapasową dowolnego źródła danych na serwerze produkcyjnym, należy upewnić się, że Agent programu DPM jest zainstalowany na serwerze i jest zarządzany przez program DPM. Postępuj zgodnie z instrukcjami dotyczącymi [instalowania agenta programu DPM](https://docs.microsoft.com/system-center/dpm/deploy-dpm-protection-agent?view=sc-dpm-2019) i łączenia go z odpowiednim serwerem programu DPM.
2. **Metoda ochrony danych** określa docelowe lokalizacje kopii zapasowych — taśmę, dysk i chmurę. W naszym przykładzie będziemy chronić dane na dysku lokalnym i w chmurze.
3. **Harmonogram tworzenia kopii zapasowych** , który określa, kiedy należy wykonać kopie zapasowe, oraz częstotliwość synchronizacji danych między serwerem programu DPM a serwerem produkcyjnym.
4. **Harmonogram przechowywania** określający, jak długo mają być przechowywane punkty odzyskiwania na platformie Azure.

### <a name="creating-a-protection-group"></a>Tworzenie grupy ochrony

Zacznij od utworzenia nowej grupy ochrony przy użyciu polecenia cmdlet [New-DPMProtectionGroup](https://docs.microsoft.com/powershell/module/dataprotectionmanager/new-dpmprotectiongroup?view=systemcenter-ps-2019) .

```powershell
$PG = New-DPMProtectionGroup -DPMServerName " TestingServer " -Name "ProtectGroup01"
```

Powyższe polecenie cmdlet utworzy grupę ochrony o nazwie *ProtectGroup01*. Istniejącą grupę ochrony można również zmodyfikować później, aby dodać kopię zapasową do chmury platformy Azure. Jednak aby wprowadzić zmiany do grupy ochrony — nowe lub istniejące — musimy uzyskać dojście do *modyfikowalnego* obiektu za pomocą polecenia cmdlet [Get-DPMModifiableProtectionGroup](https://docs.microsoft.com/powershell/module/dataprotectionmanager/get-dpmmodifiableprotectiongroup?view=systemcenter-ps-2019) .

```powershell
$MPG = Get-ModifiableProtectionGroup $PG
```

### <a name="adding-group-members-to-the-protection-group"></a>Dodawanie członków grupy do grupy ochrony

Każdy agent programu DPM wie listę źródeł danych na serwerze, na którym jest zainstalowany. Aby dodać źródło danych do grupy ochrony, Agent programu DPM musi najpierw wysłać listę źródeł DataSources z powrotem do serwera programu DPM. Co najmniej jedno źródło danych jest wybierane i dodawane do grupy ochrony. Kroki programu PowerShell niezbędne do osiągnięcia tego są następujące:

1. Pobierz listę wszystkich serwerów zarządzanych przez program DPM za pomocą agenta programu DPM.
2. Wybierz konkretny serwer.
3. Pobierz listę wszystkich źródeł danych na serwerze.
4. Wybierz co najmniej jedno źródło danych i Dodaj je do grupy ochrony

Lista serwerów, na których zainstalowano agenta programu DPM i jest zarządzana przez serwer programu DPM, jest uzyskiwana przy użyciu polecenia cmdlet [Get-DPMProductionServer](https://docs.microsoft.com/powershell/module/dataprotectionmanager/get-dpmproductionserver?view=systemcenter-ps-2019) . W tym przykładzie będziemy filtrować i konfigurować tylko PS o nazwie *ProductionServer01* na potrzeby tworzenia kopii zapasowych.

```powershell
$server = Get-ProductionServer -DPMServerName "TestingServer" | Where-Object {($_.servername) –contains "productionserver01"}
```

Teraz Pobierz listę źródeł danych na ```$server``` za pomocą polecenia cmdlet [Get-DPMDatasource](https://docs.microsoft.com/powershell/module/dataprotectionmanager/get-dpmdatasource?view=systemcenter-ps-2019) . W tym przykładzie stosujemy filtrowanie dla woluminu *D:\\* , który chcemy skonfigurować do tworzenia kopii zapasowych. To źródło danych jest następnie dodawane do grupy ochrony przy użyciu polecenia cmdlet [Add-DPMChildDatasource](https://docs.microsoft.com/powershell/module/dataprotectionmanager/add-dpmchilddatasource?view=systemcenter-ps-2019) . Pamiętaj, aby użyć obiektu grupy ochrony *modyfikowalnego* ```$MPG```, aby wprowadzić dodatki.

```powershell
$DS = Get-Datasource -ProductionServer $server -Inquire | Where-Object { $_.Name -contains "D:\" }

Add-DPMChildDatasource -ProtectionGroup $MPG -ChildDatasource $DS
```

Powtórz ten krok tyle razy, ile jest to konieczne, dopóki wszystkie wybrane źródła danych nie zostaną dodane do grupy ochrony. Możesz również zacząć od tylko jednego źródła danych i zakończyć przepływ pracy w celu utworzenia grupy ochrony, a później dodać więcej źródeł danych do grupy ochrony.

### <a name="selecting-the-data-protection-method"></a>Wybieranie metody ochrony danych

Po dodaniu źródła danych do grupy ochrony, następnym krokiem jest określenie metody ochrony za pomocą polecenia cmdlet [Set-DPMProtectionType](https://docs.microsoft.com/powershell/module/dataprotectionmanager/set-dpmprotectiontype?view=systemcenter-ps-2019) . W tym przykładzie grupa ochrony jest konfiguracją dla dysku lokalnego i kopii zapasowej w chmurze. Należy również określić źródło danych, które ma być chronione w chmurze za pomocą flagi cmdlet [Add-DPMChildDatasource](https://docs.microsoft.com/powershell/module/dataprotectionmanager/add-dpmchilddatasource?view=systemcenter-ps-2019) z opcją-online.

```powershell
Set-DPMProtectionType -ProtectionGroup $MPG -ShortTerm Disk –LongTerm Online
Add-DPMChildDatasource -ProtectionGroup $MPG -ChildDatasource $DS –Online
```

### <a name="setting-the-retention-range"></a>Ustawianie zakresu przechowywania

Ustaw przechowywanie punktów kopii zapasowych za pomocą polecenia cmdlet [Set-DPMPolicyObjective](https://docs.microsoft.com/powershell/module/dataprotectionmanager/set-dpmpolicyobjective?view=systemcenter-ps-2019) . Chociaż może wydawać się nieparzysta, aby ustawić przechowywanie przed zdefiniowaniem harmonogramu tworzenia kopii zapasowych, za pomocą polecenia cmdlet ```Set-DPMPolicyObjective``` automatycznie ustawia domyślny harmonogram tworzenia kopii zapasowych, który można następnie zmodyfikować. Zawsze jest możliwe ustawienie harmonogramu tworzenia kopii zapasowych i zasad przechowywania po.

W poniższym przykładzie polecenie cmdlet ustawia parametry przechowywania kopii zapasowych dysków. Spowoduje to zachowanie kopii zapasowych przez 10 dni i zsynchronizowanie danych co 6 godzin między serwerem produkcyjnym a serwerem programu DPM. ```SynchronizationFrequencyMinutes``` nie definiuje, jak często jest tworzony punkt kopii zapasowej, ale jak często dane są kopiowane na serwer programu DPM.  To ustawienie uniemożliwia tworzenie kopii zapasowych za duże.

```powershell
Set-DPMPolicyObjective –ProtectionGroup $MPG -RetentionRangeInDays 10 -SynchronizationFrequencyMinutes 360
```

W przypadku kopii zapasowych przechodzących na platformę Azure (program DPM odwołuje się do nich jako kopie zapasowe online) zakresy przechowywania można skonfigurować do [długoterminowego przechowywania przy użyciu systemu dziadka-ojciec-syn (GFS)](backup-azure-backup-cloud-as-tape.md). Oznacza to, że można zdefiniować połączone zasady przechowywania obejmujące dzienne, tygodniowe, miesięczne i roczne zasady przechowywania. W tym przykładzie utworzysz tablicę reprezentującą złożony schemat przechowywania, a następnie skonfigurujesz zakres przechowywania przy użyciu polecenia cmdlet [Set-DPMPolicyObjective](https://docs.microsoft.com/powershell/module/dataprotectionmanager/set-dpmpolicyobjective?view=systemcenter-ps-2019) .

```powershell
$RRlist = @()
$RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 180, Days)
$RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 104, Weeks)
$RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 60, Month)
$RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 10, Years)
Set-DPMPolicyObjective –ProtectionGroup $MPG -OnlineRetentionRangeList $RRlist
```

### <a name="set-the-backup-schedule"></a>Ustawianie harmonogramu tworzenia kopii zapasowych

Program DPM automatycznie ustawia domyślny harmonogram tworzenia kopii zapasowych w przypadku określenia celu ochrony przy użyciu polecenia cmdlet ```Set-DPMPolicyObjective```. Aby zmienić harmonogramy domyślne, należy użyć polecenia cmdlet [Get-DPMPolicySchedule](https://docs.microsoft.com/powershell/module/dataprotectionmanager/get-dpmpolicyschedule?view=systemcenter-ps-2019) , a następnie polecenia cmdlet [Set-DPMPolicySchedule](https://docs.microsoft.com/powershell/module/dataprotectionmanager/set-dpmpolicyschedule?view=systemcenter-ps-2019) .

```powershell
$onlineSch = Get-DPMPolicySchedule -ProtectionGroup $mpg -LongTerm Online
Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[0] -TimesOfDay 02:00
Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[1] -TimesOfDay 02:00 -DaysOfWeek Sa,Su –Interval 1
Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[2] -TimesOfDay 02:00 -RelativeIntervals First,Third –DaysOfWeek Sa
Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[3] -TimesOfDay 02:00 -DaysOfMonth 2,5,8,9 -Months Jan,Jul
Set-DPMProtectionGroup -ProtectionGroup $MPG
```

W powyższym przykładzie ```$onlineSch``` jest tablicą zawierającą cztery elementy, które zawierają istniejący harmonogram ochrony w trybie online dla grupy ochrony w schemacie GFS:

1. ```$onlineSch[0]``` zawiera dzienny harmonogram
2. ```$onlineSch[1]``` zawiera Harmonogram tygodniowy
3. ```$onlineSch[2]``` zawiera Harmonogram miesięczny
4. ```$onlineSch[3]``` zawiera harmonogram roczny

Dlatego jeśli trzeba zmodyfikować harmonogram tygodniowy, należy odwołać się do ```$onlineSch[1]```.

### <a name="initial-backup"></a>Początkowa kopia zapasowa

Podczas tworzenia kopii zapasowej źródła danych po raz pierwszy program DPM musi utworzyć replikę początkową, która tworzy pełną kopię źródła danych, która ma być chroniona na woluminie repliki programu DPM. To działanie można zaplanować do określonego czasu lub może zostać wyzwolone ręcznie przy użyciu polecenia cmdlet [Set-DPMReplicaCreationMethod](https://docs.microsoft.com/powershell/module/dataprotectionmanager/set-dpmreplicacreationmethod?view=systemcenter-ps-2019) z parametrem ```-NOW```.

```powershell
Set-DPMReplicaCreationMethod -ProtectionGroup $MPG -NOW
```

### <a name="changing-the-size-of-dpm-replica--recovery-point-volume"></a>Zmiana rozmiaru repliki programu DPM & wolumin punktu odzyskiwania

Możesz również zmienić rozmiar woluminu repliki programu DPM i woluminu kopii w tle za pomocą polecenia cmdlet [Set-DPMDatasourceDiskAllocation](https://docs.microsoft.com/powershell/module/dataprotectionmanager/set-dpmdatasourcediskallocation?view=systemcenter-ps-2019) , tak jak w poniższym przykładzie: Get-DatasourceDiskAllocation-DataSource $ds Set-DatasourceDiskAllocation-DataSource $ds-Protection $MPG-ręczna-ReplicaArea (2 GB)-ShadowCopyArea (2 GB)

### <a name="committing-the-changes-to-the-protection-group"></a>Zatwierdzanie zmian w grupie ochrony

Na koniec należy zatwierdzić zmiany, aby program DPM mógł wykonać kopię zapasową w ramach nowej konfiguracji grupy ochrony. Można to osiągnąć za pomocą polecenia cmdlet [Set-DPMProtectionGroup](https://docs.microsoft.com/powershell/module/dataprotectionmanager/set-dpmprotectiongroup?view=systemcenter-ps-2019) .

```powershell
Set-DPMProtectionGroup -ProtectionGroup $MPG
```

## <a name="view-the-backup-points"></a>Wyświetlanie punktów kopii zapasowych

Możesz użyć polecenia cmdlet [Get-DPMRecoveryPoint](https://docs.microsoft.com/powershell/module/dataprotectionmanager/get-dpmrecoverypoint?view=systemcenter-ps-2019) , aby uzyskać listę wszystkich punktów odzyskiwania dla źródła danych. W tym przykładzie będziemy:

* Pobierz wszystkie PGs na serwerze DPM i zapisywanych w tablicy ```$PG```
* Pobierz źródła danych odpowiadające ```$PG[0]```
* Pobierz wszystkie punkty odzyskiwania dla źródła danych.

```powershell
$PG = Get-DPMProtectionGroup –DPMServerName "TestingServer"
$DS = Get-DPMDatasource -ProtectionGroup $PG[0]
$RecoveryPoints = Get-DPMRecoverypoint -Datasource $DS[0] -Online
```

## <a name="restore-data-protected-on-azure"></a>Przywracanie danych chronionych na platformie Azure

Przywracanie danych jest kombinacją obiektu ```RecoverableItem``` i ```RecoveryOption``` obiektu. W poprzedniej sekcji mamy listę punktów kopii zapasowych dla źródła danych.

W poniższym przykładzie pokazano, jak przywrócić maszynę wirtualną funkcji Hyper-V z Azure Backup przez połączenie punktów kopii zapasowych z celem odzyskiwania. Ten przykład obejmuje:

* Tworzenie opcji odzyskiwania przy użyciu polecenia cmdlet [New-DPMRecoveryOption](https://docs.microsoft.com/powershell/module/dataprotectionmanager/new-dpmrecoveryoption?view=systemcenter-ps-2019) .
* Pobieranie tablicy punktów kopii zapasowej za pomocą polecenia cmdlet ```Get-DPMRecoveryPoint```.
* Wybieranie punktu kopii zapasowej do przywrócenia.

```powershell
$RecoveryOption = New-DPMRecoveryOption -HyperVDatasource -TargetServer "HVDCenter02" -RecoveryLocation AlternateHyperVServer -RecoveryType Recover -TargetLocation "C:\VMRecovery"

$PG = Get-DPMProtectionGroup –DPMServerName "TestingServer"
$DS = Get-DPMDatasource -ProtectionGroup $PG[0]
$RecoveryPoints = Get-DPMRecoverypoint -Datasource $DS[0] -Online

Restore-DPMRecoverableItem -RecoverableItem $RecoveryPoints[0] -RecoveryOption $RecoveryOption
```

Polecenia można łatwo rozszerzyć dla dowolnego typu źródła danych.

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać więcej informacji na temat programu DPM do Azure Backup zobacz [wprowadzenie do kopii zapasowej programu DPM](backup-azure-dpm-introduction.md)
