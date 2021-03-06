---
title: Tworzenie kopii zapasowych udziałów plików platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure
description: Dowiedz się, jak używać interfejsu wiersza polecenia platformy Azure do tworzenia kopii zapasowych udziałów plików platformy Azure w magazynie Recovery Services
ms.topic: conceptual
ms.date: 01/14/2020
ms.openlocfilehash: ff1d8c6245521d2d0262b0440177d65713058742
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2020
ms.locfileid: "76844045"
---
# <a name="back-up-azure-file-shares-with-cli"></a>Tworzenie kopii zapasowych udziałów plików platformy Azure przy użyciu interfejsu wiersza polecenia

Interfejs wiersza polecenia platformy Azure udostępnia środowisko wiersza polecenia do zarządzania zasobami platformy Azure. To doskonałe narzędzie do tworzenia niestandardowych automatyzacji do korzystania z zasobów platformy Azure. W tym artykule szczegółowo opisano sposób tworzenia kopii zapasowych udziałów plików platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure. Te kroki można również wykonać przy użyciu programu [Azure PowerShell](https://docs.microsoft.com/azure/backup/backup-azure-afs-automation) lub w witrynie [Azure Portal](backup-afs.md).

Po zakończeniu tego samouczka dowiesz się, jak wykonać poniższe operacje za pomocą interfejsu wiersza polecenia platformy Azure:

* Tworzenie magazynu usługi Recovery Services
* Włącz tworzenie kopii zapasowych dla udziałów plików platformy Azure
* Wyzwalanie kopii zapasowej na żądanie dla udziałów plików

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Aby zainstalować interfejs wiersza polecenia lokalnie i korzystać z niego, należy korzystać z interfejsu wiersza polecenia platformy Azure w wersji 2.0.18 lub nowszej. Aby znaleźć wersję interfejsu wiersza polecenia, `run az --version`. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="create-a-recovery-services-vault"></a>Tworzenie magazynu Recovery Services

Magazyn usługi Recovery Service jest jednostką, która zapewnia skonsolidowany widok i możliwość zarządzania w ramach wszystkich elementów kopii zapasowej. Gdy zadanie tworzenia kopii zapasowej chronionego zasobu zostaje uruchomione, tworzony jest punkt odzyskiwania w magazynie usługi Recovery Services. Następnie można użyć jednego z tych punktów odzyskiwania w celu przywrócenia danych do danego punktu w czasie.

Wykonaj następujące kroki, aby utworzyć magazyn usługi Recovery Services:

1. Magazyn znajduje się w grupie zasobów. Jeśli nie masz istniejącej grupy zasobów, Utwórz nową przy użyciu [AZ Group Create](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create) . W tym samouczku utworzymy nową grupę zasobów *migracji pamięci* w regionie Wschodnie stany USA.

    ```azurecli-interactive
    az group create --name AzureFiles --location eastus --output table
    ```

    ```output
    Location    Name
    ----------  ----------
    eastus      AzureFiles
    ```

2. Użyj polecenia [AZ Backup magazynu Create](https://docs.microsoft.com/cli/azure/backup/vault?view=azure-cli-latest#az-backup-vault-create) , aby utworzyć magazyn. Określ tę samą lokalizację dla magazynu, który został użyty dla grupy zasobów.

    Poniższy przykład tworzy magazyn usługi Recovery Services o nazwie *azurefilesvault* w regionie Wschodnie stany USA.

    ```azurecli-interactive
    az backup vault create --resource-group azurefiles --name azurefilesvault --location eastus --output table
    ```

    ```output
    Location    Name                ResourceGroup
    ----------  ----------------    ---------------
    eastus      azurefilesvault     azurefiles
    ```

3. Określ typ nadmiarowości, która ma być używana w magazynie magazynu. Można użyć magazynu [lokalnie nadmiarowego](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs) lub [magazynu geograficznie nadmiarowego](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs).

    Poniższy przykład ustawia opcję nadmiarowości magazynu dla *azurefilesvault* do **geonadmiarowości** przy użyciu polecenia [AZ Backup Storage Backup-Properties Set](https://docs.microsoft.com/cli/azure/backup/vault/backup-properties?view=azure-cli-latest#az-backup-vault-backup-properties-set) .

    ```azurecli-interactive
    az backup vault backup-properties set --name azurefilesvault --resource-group azurefiles --backup-storage-redundancy Georedundant
    ```

    Aby sprawdzić, czy magazyn został utworzony pomyślnie, możesz użyć polecenia [AZ Backup magazynu show](https://docs.microsoft.com/cli/azure/backup/vault?view=azure-cli-latest#az-backup-vault-show) , aby uzyskać szczegółowe informacje o magazynie. Poniższy przykład wyświetla szczegóły *azurefilesvault* utworzonej w powyższych krokach.

    ```azurecli-interactive
    az backup vault show --name azurefilesvault --resource-group azurefiles --output table
    ```

    Dane wyjściowe będą podobne do następującej:

    ```output
    Location     Name               ResourceGroup
    ----------   ---------------    ---------------
    eastus       azurefilesvault    azurefiles
    ```

## <a name="enable-backup-for-azure-file-shares"></a>Włącz tworzenie kopii zapasowych dla udziałów plików platformy Azure

W tej sekcji założono, że masz już udział plików platformy Azure, dla którego chcesz skonfigurować kopię zapasową. Jeśli go nie masz, Utwórz udział plików platformy Azure przy użyciu polecenia [AZ Storage Share Create](https://docs.microsoft.com/cli/azure/storage/share?view=azure-cli-latest#az-storage-share-create) .

Aby włączyć tworzenie kopii zapasowych udziałów plików, należy utworzyć zasady ochrony, które definiują, kiedy zadanie tworzenia kopii zapasowej jest uruchamiane oraz jak długo są przechowywane punkty odzyskiwania. Zasady tworzenia kopii zapasowych można utworzyć za pomocą polecenia [AZ Backup Policy Create](https://docs.microsoft.com/cli/azure/backup/policy?view=azure-cli-latest#az-backup-policy-create) .

W poniższym przykładzie użyto polecenia [AZ Backup Protection Enable-for-azurefileshare](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-enable-for-azurefileshare) , aby włączyć tworzenie kopii zapasowej udziału plików *migracji pamięci* na koncie magazynu *afsaccount* przy użyciu zasad tworzenia kopii zapasowej *harmonogramu 1* :

```azurecli-interactive
az backup protection enable-for-azurefileshare --vault-name azurefilesvault --resource-group  azurefiles --policy-name schedule1 --storage-account afsaccount --azure-file-share azurefiles  --output table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
0caa93f4-460b-4328-ac1d-8293521dd928  azurefiles
```

Atrybut **name** w danych wyjściowych odpowiada nazwie zadania, które jest tworzone przez usługę kopii zapasowej dla operacji **włączania kopii zapasowej** . Aby śledzić stan zadania, użyj polecenia [AZ Backup Job show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) cmdlet.

## <a name="trigger-an-on-demand-backup-for-file-share"></a>Wyzwalanie kopii zapasowej na żądanie dla udziału plików

Jeśli chcesz wyzwolić kopię zapasową na żądanie dla udziału plików zamiast czekać, aż zasady tworzenia kopii zapasowych uruchomią zadanie w zaplanowanym czasie, użyj polecenia [AZ Backup Protection Backup-Now](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-backup-now) .

Należy zdefiniować następujące parametry, aby wyzwolić kopię zapasową na żądanie:

* **--Container-Name** to nazwa konta magazynu obsługującego udział plików. Aby pobrać **nazwę** lub **przyjazną nazwę** kontenera, użyj polecenia [AZ Backup Container list](/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list) .
* **--Item-Name** to nazwa udziału plików, dla którego chcesz wyzwolić kopię zapasową na żądanie. Aby pobrać **nazwę** lub **przyjazną nazwę** elementu kopii zapasowej, użyj polecenia [AZ Backup Item list](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-list) .
* **--Zachowaj-do** określa datę, do której ma zostać zachowany punkt odzyskiwania. Wartość powinna być ustawiona w formacie czasu UTC (dd-mm-rrrr).

Poniższy przykład wyzwala tworzenie kopii zapasowych na żądanie dla udziału *azuresfiles* na koncie magazynu *afsaccount* z przechowywaniem do *20-01-2020*.

```azurecli-interactive
az backup protection backup-now --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --retain-until 20-01-2020 --output table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
9f026b4f-295b-4fb8-aae0-4f058124cb12  azurefiles
```

Atrybut **name** w danych wyjściowych odpowiada nazwie zadania tworzonego przez usługę kopii zapasowej dla operacji tworzenia kopii zapasowej na żądanie. Aby śledzić stan zadania, użyj polecenia [AZ Backup Job show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) cmdlet.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [przywrócić udziały plików platformy Azure przy użyciu interfejsu wiersza polecenia](restore-afs-cli.md)
* Dowiedz się, jak [zarządzać usługą Azure File Share ackups przy użyciu interfejsu wiersza polecenia](manage-afs-backup-cli.md)
