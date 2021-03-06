---
title: Integracja kontroli źródła w usłudze Automatyzacja Azure
description: W tym artykule opisano integrację kontroli źródła z usługą GitHub w Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 12/10/2019
ms.topic: conceptual
ms.openlocfilehash: b0eed8fe9d548ee54698d187e192960bb3b44e44
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/14/2020
ms.locfileid: "79368812"
---
# <a name="source-control-integration-in-azure-automation"></a>Integracja kontroli źródła w usłudze Automatyzacja Azure

 Integracja kontroli źródła w Azure Automation obsługuje synchronizację jednokierunkową z repozytorium kontroli źródła. Kontrola źródła pozwala zachować aktualność elementów Runbook na Twoim koncie usługi Automation za pomocą skryptów w repozytorium usługi GitHub lub Azure Repos kontroli źródła. Ta funkcja ułatwia podwyższenie poziomu kodu, który został przetestowany w środowisku programistycznym, na konto automatyzacji produkcji.
 
 Korzystając z integracji kontroli źródła, możesz łatwo współpracować z zespołem, śledzić zmiany i przywracać wcześniejsze wersje elementów Runbook. Na przykład kontrola źródła pozwala synchronizować różne gałęzie w kontroli źródła z kontami deweloperskimi, testowymi i produkcyjnymi. 

>[!NOTE]
>Ten artykuł został zaktualizowany o korzystanie z nowego modułu Azure PowerShell Az. Nadal możesz używać modułu AzureRM, który będzie nadal otrzymywać poprawki błędów do co najmniej grudnia 2020 r. Aby dowiedzieć się więcej na temat nowego modułu Az i zgodności z modułem AzureRM, zobacz [Wprowadzenie do nowego modułu Az programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Instrukcje dotyczące instalacji polecenia AZ module w hybrydowym procesie roboczym elementu Runbook znajdują się w temacie [Install the Azure PowerShell module](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). W przypadku konta usługi Automation można zaktualizować moduły do najnowszej wersji przy użyciu [sposobu aktualizowania modułów Azure PowerShell w programie Azure Automation](automation-update-azure-modules.md).

## <a name="source-control-types"></a>Typy kontroli źródła

Azure Automation obsługuje trzy typy kontroli źródła:

* GitHub
* Azure Repos (Git)
* Azure Repos (TFVC)

## <a name="prerequisites"></a>Wymagania wstępne

* Repozytorium kontroli źródła (GitHub lub Azure Repos)
* [Konto Uruchom jako](manage-runas-account.md)
* [Najnowsze moduły platformy Azure](automation-update-azure-modules.md) na koncie usługi Automation, łącznie z modułem `Az.Accounts` (AZ module równoważ of `AzureRM.Profile`)

> [!NOTE]
> Zadania synchronizacji kontroli źródła są uruchamiane na koncie usługi Automation użytkownika i są rozliczane według tej samej stawki co inne zadania automatyzacji.

## <a name="configuring-source-control"></a>Konfigurowanie kontroli źródła

Ta sekcja zawiera informacje na temat konfigurowania kontroli źródła dla konta usługi Automation. Możesz użyć Azure Portal lub programu PowerShell.

### <a name="configure-source-control----azure-portal"></a>Konfigurowanie kontroli źródła — Azure Portal

Użyj tej procedury, aby skonfigurować kontrolę źródła przy użyciu Azure Portal.

1. W ramach konta usługi Automation wybierz pozycję **Kontrola źródła** , a następnie kliknij pozycję **+ Dodaj**.

    ![Wybierz kontrolę źródła](./media/source-control-integration/select-source-control.png)

2. Wybierz **Typ kontroli źródła**, a następnie kliknij przycisk **Uwierzytelnij**. 

3. Zostanie otwarte okno przeglądarki z prośbą o zalogowanie się. Postępuj zgodnie z monitami, aby zakończyć uwierzytelnianie.

4. Na stronie Podsumowanie kontroli źródła Użyj pól, aby wypełnić właściwości kontroli źródła zdefiniowane poniżej. Po zakończeniu kliknij przycisk **Zapisz** . 

    |Właściwość  |Opis  |
    |---------|---------|
    |Nazwa kontroli źródła     | Przyjazna nazwa dla kontroli źródła. Ta nazwa może zawierać tylko litery i cyfry.        |
    |Typ kontroli źródła     | Typ mechanizmu kontroli źródła. Dostępne opcje:</br> * GitHub</br>* Azure Repos (Git)</br> * Azure Repos (TFVC)        |
    |Repozytorium     | Nazwa repozytorium lub projektu. Pobierane są pierwsze repozytoria 200. Aby wyszukać repozytorium, wpisz nazwę w polu, a następnie kliknij pozycję **Wyszukaj w witrynie GitHub**.|
    |Gałęzi     | Gałąź, z której mają zostać pobrane pliki źródłowe. Funkcja określania wartości docelowej gałęzi jest niedostępna dla typu kontroli źródła TFVC.          |
    |Ścieżka folderu     | Folder zawierający elementy Runbook do zsynchronizowania, na przykład **/Runbooks**. Synchronizowane są tylko elementy Runbook w określonym folderze. Rekursja nie jest obsługiwana.        |
    |Synchronizacja autosynchronizacji<sup>1</sup>     | Ustawienie służące do włączania lub wyłączania automatycznej synchronizacji po dokonaniu zatwierdzenia w repozytorium kontroli źródła.        |
    |Publikowanie elementu Runbook     | Ustawienie włączone w przypadku, gdy elementy Runbook są automatycznie publikowane po synchronizacji z kontroli źródła i wyłączone w inny sposób.           |
    |Opis     | Tekst określający dodatkowe szczegóły dotyczące kontroli źródła.        |

    <sup>1</sup> aby włączyć autosynchronizację podczas konfigurowania integracji kontroli źródła z Azure Repos, musisz być administratorem projektu.

   ![Podsumowanie kontroli źródła](./media/source-control-integration/source-control-summary.png)

> [!NOTE]
> Identyfikator logowania dla repozytorium kontroli źródła może być inny niż identyfikator logowania dla Azure Portal. Upewnij się, że logujesz się przy użyciu odpowiedniego konta dla swojego repozytorium kontroli źródła podczas konfigurowania kontroli źródła. W razie wątpliwości Otwórz nową kartę w przeglądarce, Wyloguj się z **VisualStudio.com** lub **GitHub.com**i spróbuj ponownie nawiązać połączenie z kontrolą źródła.

### <a name="configure-source-control----powershell"></a>Konfigurowanie kontroli źródła — PowerShell

Za pomocą programu PowerShell można także skonfigurować kontrolę źródła w Azure Automation. Aby użyć poleceń cmdlet programu PowerShell dla tej operacji, potrzebny jest osobisty token dostępu. Użyj polecenia cmdlet [New-AzAutomationSourceControl](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationsourcecontrol?view=azps-3.5.0
) , aby utworzyć połączenie kontroli źródła. To polecenie cmdlet wymaga bezpiecznego ciągu dla elementu "Binding". Aby dowiedzieć się, jak utworzyć bezpieczny ciąg, zobacz [ConvertTo-SecureString](/powershell/module/microsoft.powershell.security/convertto-securestring?view=powershell-6).

Poniższe podsekcje ilustrują Tworzenie połączenia kontroli źródła dla usługi GitHub, Azure Repos (Git) i Azure Repos (TFVC).

#### <a name="create-source-control-connection-for-github"></a>Utwórz połączenie kontroli źródła dla usługi GitHub

```powershell-interactive
New-AzAutomationSourceControl -Name SCGitHub -RepoUrl https://github.com/<accountname>/<reponame>.git -SourceType GitHub -FolderPath "/MyRunbooks" -Branch master -AccessToken <secureStringofPAT> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

#### <a name="create-source-control-connection-for-azure-repos-git"></a>Utwórz połączenie kontroli źródła dla Azure Repos (Git)

```powershell-interactive
New-AzAutomationSourceControl -Name SCReposGit -RepoUrl https://<accountname>.visualstudio.com/<projectname>/_git/<repositoryname> -SourceType VsoGit -AccessToken <secureStringofPAT> -Branch master -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName> -FolderPath "/Runbooks"
```

#### <a name="create-source-control-connection-for-azure-repos-tfvc"></a>Utwórz połączenie kontroli źródła dla Azure Repos (TFVC)

```powershell-interactive
New-AzAutomationSourceControl -Name SCReposTFVC -RepoUrl https://<accountname>.visualstudio.com/<projectname>/_versionControl -SourceType VsoTfvc -AccessToken <secureStringofPAT> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName> -FolderPath "/Runbooks"
```

#### <a name="personal-access-token-pat-permissions"></a>Uprawnienia do osobistego tokenu dostępu

Kontrola źródła wymaga pewnych minimalnych uprawnień do PATs. Poniższe podsekcje zawierają minimalne uprawnienia wymagane w serwisie GitHub i Azure Repos.

##### <a name="minimum-pat-permissions-for-github"></a>Minimalne uprawnienia dla usługi GitHub

W poniższej tabeli zdefiniowano wymagania dotyczące minimalnych wartości, które są wymagane w serwisie GitHub. Aby uzyskać więcej informacji na temat tworzenia dyspozycji w serwisie GitHub, zobacz [Tworzenie osobistego tokenu dostępu dla wiersza polecenia](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/).

|Zakres  |Opis  |
|---------|---------|
|**`repo`**     |         |
|`repo:status`     | Stan zatwierdzenia dostępu         |
|`repo_deployment`      | Stan wdrożenia dostępu         |
|`public_repo`     | Dostęp do publicznych repozytoriów         |
|**`admin:repo_hook`**     |         |
|`write:repo_hook`     | Zapisz punkty zaczepienia repozytorium         |
|`read:repo_hook`|Odczytaj punkty zaczepienia repozytorium|

##### <a name="minimum-pat-permissions-for-azure-repos"></a>Minimalne uprawnienia dla Azure Repos

Poniższa lista zawiera definicje minimalnych uprawnień, które są wymagane do Azure Repos. Aby uzyskać więcej informacji na temat tworzenia elementu w Azure Repos, zobacz temat [uwierzytelnianie dostępu przy użyciu osobistego tokenu dostępu](/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate).

| Zakres  |  Typ dostępu  |
|---------| ----------|
| Kod      | Odczytywanie  |
| Projekt i zespół | Odczytywanie |
| Tożsamość | Odczytywanie     |
| Profil użytkownika | Odczytywanie     |
| Elementy robocze | Odczytywanie    |
| Połączenia usługi | Odczyt, wykonywanie zapytań, zarządzanie<sup>1</sup>    |

<sup>1</sup> uprawnienie połączenia usługi jest wymagane tylko wtedy, gdy włączono AutoSync.

## <a name="synchronizing"></a>Synchronizowanie

Wykonaj następujące kroki, aby przeprowadzić synchronizację z kontrolą źródła. 

1. Wybierz źródło z tabeli na stronie kontroli źródła. 

2. Kliknij przycisk **Rozpocznij synchronizację** , aby rozpocząć proces synchronizacji. 

3. Aby wyświetlić stan bieżącego zadania synchronizacji lub poprzednich, kliknij kartę **zadania synchronizacji** . 

4. Z menu rozwijanego **kontroli źródła** wybierz mechanizm kontroli źródła.

    ![Stan synchronizacji](./media/source-control-integration/sync-status.png)

5. Kliknięcie zadania umożliwia wyświetlenie danych wyjściowych zadania. Poniższy przykład jest wyjściem z zadania synchronizacji kontroli źródła.

    ```output
    ============================================================================

    Azure Automation Source Control.
    Supported runbooks to sync: PowerShell Workflow, PowerShell Scripts, DSC Configurations, Graphical, and Python 2.

    Setting AzureRmEnvironment.

    Getting AzureRunAsConnection.

    Logging in to Azure...

    Source control information for syncing:

    [Url = https://ContosoExample.visualstudio.com/ContosoFinanceTFVCExample/_versionControl] [FolderPath = /Runbooks]

    Verifying url: https://ContosoExample.visualstudio.com/ContosoFinanceTFVCExample/_versionControl

    Connecting to VSTS...

    Source Control Sync Summary:

    2 files synced:
     - ExampleRunbook1.ps1
     - ExampleRunbook2.ps1

     =========================================================================

    ```

6. Dodatkowe rejestrowanie jest dostępne po wybraniu opcji **wszystkie dzienniki** na stronie Podsumowanie zadania synchronizacji kontroli źródła. Te dodatkowe wpisy dziennika mogą pomóc w rozwiązywaniu problemów, które mogą wystąpić podczas korzystania z kontroli źródła.

## <a name="disconnecting-source-control"></a>Odłączanie kontroli źródła

Aby rozłączyć się z repozytorium kontroli źródła:

1. Otwórz **kontrolę źródła** w obszarze **Ustawienia konta** na koncie usługi Automation.

2. Wybierz mechanizm kontroli źródła do usunięcia. 

3. Na stronie Podsumowanie kontroli źródła kliknij pozycję **Usuń**.

## <a name="handling-encoding-issues"></a>Obsługa problemów z kodowaniem

Jeśli wiele osób edytuje elementy Runbook w repozytorium kontroli źródła przy użyciu różnych edytorów, mogą wystąpić problemy z kodowaniem. Aby dowiedzieć się więcej o tej sytuacji, zobacz [typowe przyczyny problemów z kodowaniem](/powershell/scripting/components/vscode/understanding-file-encoding#common-causes-of-encoding-issues).

## <a name="updating-the-pat"></a>Aktualizowanie przebiegu

Obecnie nie ma możliwości użycia Azure Portal w celu zaktualizowania źródła danych w kontroli kodu źródłowego. Po wygaśnięciu lub odwołaniu danych o stanie kontroli źródła można aktualizować za pomocą nowego tokenu dostępu w jeden z następujących sposobów:

* Użyj [interfejsu API REST](https://docs.microsoft.com/rest/api/automation/sourcecontrol/update).
* Użyj polecenia cmdlet [Update-AzAutomationSourceControl](/powershell/module/az.automation/update-azautomationsourcecontrol) .

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o typach elementów Runbook i ich zaletach i ograniczeniach, zobacz [Azure Automation typów elementów Runbook](automation-runbook-types.md).
