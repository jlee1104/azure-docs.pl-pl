---
title: 'WIERSZ WIERSZA POLECENIA: Tworzenie zaplanowanej kopii zapasowej'
description: Dowiedz się, jak za pomocą interfejsu wiersza polecenia platformy Azure zautomatyzować wdrażanie aplikacji usługi App Service i zarządzanie nimi. W tym przykładzie pokazano, jak utworzyć zaplanowaną kopię zapasową dla aplikacji.
author: msangapu-msft
tags: azure-service-management
ms.devlang: azurecli
ms.topic: sample
ms.date: 12/11/2017
ms.author: msangapu
ms.reviewer: cephalin
ms.custom: mvc, seodec18
ms.openlocfilehash: 327b48052e0134ae97f0a97f0a43f36e75f2e003
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "80058775"
---
# <a name="create-a-scheduled-backup-for-an-app-service-app-using-cli"></a>Tworzenie zaplanowanej kopii zapasowej aplikacji usługi App Service przy użyciu interfejsu wiersza polecenia

Ten przykładowy skrypt tworzy aplikację w usłudze App Service wraz z jej powiązanymi zasobami, a następnie tworzy zaplanowaną kopię zapasową na potrzeby tej aplikacji. 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, musisz mieć interfejs wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Aby dowiedzieć się, jaka wersja jest używana, uruchom polecenie `az --version`. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Przykładowy skrypt

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/backup-scheduled/backup-scheduled.sh?highlight=3-7 "Create a scheduled backup for an app")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Objaśnienia dla skryptu

W tym skrypcie użyto następujących poleceń. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [`az group create`](/cli/azure/group?view=azure-cli-latest#az-group-create) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [`az storage account create`](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create) | Tworzy konto magazynu. |
| [`az storage container create`](/cli/azure/storage/container?view=azure-cli-latest#az-storage-container-create) | Tworzy kontener usługi Azure Storage. |
| [`az storage container generate-sas`](/cli/azure/storage/container?view=azure-cli-latest#az-storage-container-generate-sas) | Generuje token SAS dla kontenera usługi Azure Storage.  |
| [`az appservice plan create`](/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create) | Tworzy plan usługi App Service. |
| [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) | Tworzy aplikację usługi App Service. |
| [`az webapp config backup update`](/cli/azure/webapp/config/backup?view=azure-cli-latest#az-webapp-config-backup-update) | Konfiguruje nowy harmonogram tworzenia kopii zapasowej aplikacji usługi App Service. |
| [`az webapp config backup show`](/cli/azure/webapp/config/backup?view=azure-cli-latest#az-webapp-config-backup-show) | Wyświetla harmonogram tworzenia kopii zapasowej aplikacji usługi App Service. |
| [`az webapp config backup list`](/cli/azure/webapp/config/backup?view=azure-cli-latest#az-webapp-config-backup-list) | Pobiera listę kopii zapasowych dla aplikacji usługi App Service. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure).

Dodatkowe przykłady skryptów interfejsu wiersza polecenia usługi App Service można znaleźć w [dokumentacji usługi Azure App Service](../samples-cli.md).
