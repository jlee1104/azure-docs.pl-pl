---
title: 'CLI: Łączenie aplikacji z Redis'
description: Dowiedz się, jak za pomocą interfejsu wiersza polecenia platformy Azure zautomatyzować wdrażanie aplikacji usługi App Service i zarządzanie nimi. W tym przykładzie pokazano, jak połączyć aplikację z pamięcią podręczną Azure dla redis.
author: msangapu-msft
tags: azure-service-management
ms.assetid: bc8345b2-8487-40c6-a91f-77414e8688e6
ms.devlang: azurecli
ms.topic: sample
ms.date: 12/11/2017
ms.author: msangapu
ms.custom: mvc, seodec18
ms.openlocfilehash: 27b7040bace80fff5cbcb6a12e4020cfe0c7fc9a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "80058642"
---
# <a name="connect-an-app-service-app-to-an-azure-cache-for-redis-using-cli"></a>Łączenie aplikacji usługi App Service z pamięcią podręczną Azure Cache for Redis przy użyciu interfejsu wiersza polecenia

Ten przykładowy skrypt tworzy pamięć podręczną Azure Cache for Redis i aplikację usługi App Service. Następnie łączy on pamięć podręczną Azure Cache for Redis z aplikacją przy użyciu ustawień aplikacji.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, musisz mieć interfejs wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Aby dowiedzieć się, jaka wersja jest używana, uruchom polecenie `az --version`. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli).

## <a name="sample-script"></a>Przykładowy skrypt

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/connect-to-redis/connect-to-redis.sh "Azure Cache for Redis")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Objaśnienia dla skryptu

Ten skrypt używa poniższych poleceń do tworzenia grupy zasobów, aplikacji usługi App Service, pamięci podręcznej Azure Cache for Redis i wszystkich powiązanych zasobów. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [`az group create`](/cli/azure/group?view=azure-cli-latest#az-group-create) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [`az appservice plan create`](/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create) | Tworzy plan usługi App Service. |
| [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) | Tworzy aplikację usługi App Service. |
| [`az redis create`](/cli/azure/redis?view=azure-cli-latest#az-redis-create) | Tworzy nowe wystąpienie pamięci podręcznej Azure Cache for Redis. |
| [`az redis list-keys`](/cli/azure/redis?view=azure-cli-latest#az-redis-list-keys) | Wyświetla klucze dostępu dla wystąpienia pamięci podręcznej Azure Cache for Redis. |
| [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) | Tworzy lub aktualizuje ustawienia aplikacji dla aplikacji usługi App Service. Ustawienia aplikacji są dostępne dla aplikacji jako zmienne środowiskowe. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure).

Dodatkowe przykłady skryptów interfejsu wiersza polecenia usługi App Service można znaleźć w [dokumentacji usługi Azure App Service](../samples-cli.md).
