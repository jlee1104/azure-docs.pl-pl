---
title: 'Program PowerShell: przywracanie kopii zapasowej aplikacji'
description: Dowiedz się, jak za pomocą programu Azure PowerShell zautomatyzować wdrażanie usługi App Service i zarządzanie nimi. W tym przykładzie pokazano, jak przywrócić aplikację z kopii zapasowej.
author: msangapu-msft
tags: azure-service-management
ms.assetid: a2a27d94-d378-4c17-a6a9-ae1e69dc4a72
ms.topic: sample
ms.date: 11/21/2018
ms.author: msangapu
ms.custom: mvc, seodec18
ms.openlocfilehash: a10a451bff73c081d9b0c95c494acb8b2051bd84
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "80057744"
---
# <a name="restore-a-web-app-from-a-backup-using-azure-powershell"></a>Przywracanie aplikacji internetowej z kopii zapasowej przy użyciu programu Azure PowerShell

Ten przykładowy skrypt pobiera poprzednio utworzoną kopię zapasową z istniejącej aplikacji internetowej i przywraca ją przez zastąpienie jej zawartości. 

W razie potrzeby zainstaluj program Azure PowerShell przy użyciu instrukcji znalezionych `Connect-AzAccount` w [przewodniku programu Azure PowerShell](/powershell/azure/overview), a następnie uruchom w celu utworzenia połączenia z platformą Azure. 

## <a name="sample-script"></a>Przykładowy skrypt

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/backup-restore/backup-restore.ps1?highlight=1-2 "Restore a web app from a backup")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia 

Jeśli aplikacja internetowa nie jest już potrzebna, użyj następującego polecenia, aby usunąć grupę zasobów, aplikację internetową i wszystkie powiązane zasoby.

```powershell
Remove-AzResourceGroup -Name $resourceGroupName -Force
```

## <a name="script-explanation"></a>Objaśnienia dla skryptu

W tym skrypcie użyto następujących poleceń. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [Get-AzWebAppBackupList](/powershell/module/az.websites/get-azwebappbackuplist) | Pobiera listę kopii zapasowych dla aplikacji internetowej. |
| [Restore-AzWebAppBackup](/powershell/module/az.websites/restore-azwebappbackup) | Przywraca aplikację internetową z utworzonej wcześniej kopii zapasowej. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat modułu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](/powershell/azure/overview).

Więcej przykładów programu Azure PowerShell dla usługi Azure App Service Web Apps można znaleźć w [przykładach programu Azure PowerShell](../samples-powershell.md).
