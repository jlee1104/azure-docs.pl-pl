---
title: 'Szybki start: tworzenie aplikacji z wieloma kontenerami'
description: Rozpocznij pracę z aplikacjami z wieloma kontenerami w usłudze Azure App Service, wdrażając pierwszą aplikację z wieloma kontenerami.
keywords: azure app service, aplikacja internetowa, Linux, docker, compose, multicontainer, multi-container, aplikacja internetowa dla kontenerów, wiele kontenerów, kontener, wordpress, azure db dla mysql, produkcyjna baza danych z kontenerami
author: msangapu-msft
ms.topic: quickstart
ms.date: 08/23/2019
ms.author: msangapu
ms.custom: mvc, seodec18
ms.openlocfilehash: 62e34859775cb8c574d8d463f636ed81dce8ece3
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "80045872"
---
# <a name="create-a-multi-container-preview-app-using-a-docker-compose-configuration"></a>Tworzenie aplikacji z wieloma kontenerami (w wersji zapoznawczej) przy użyciu konfiguracji narzędzia Docker Compose

> [!NOTE]
> Multi-container jest w wersji zapoznawczej.

Funkcja [Web App for Containers](app-service-linux-intro.md) oferuje elastyczny sposób korzystania z obrazów platformy Docker. Ten przewodnik Szybki start pokazuje, jak wdrożyć aplikację z wieloma kontenerami (w wersji zapoznawczej) w aplikacji Web App for Containers w [usłudze Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) przy użyciu konfiguracji dokowanekomonce.

Użyjesz tego przewodnika Szybki start w usłudze Cloud Shell, ale możesz również uruchomić te polecenia lokalnie za pomocą [interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) (w wersji 2.0.32 lub nowszej). 

![Przykładowa aplikacja wielokontenerowa funkcji Web App for Containers][1]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="download-the-sample"></a>Pobierz przykład

W tym przewodniku Szybki Start używany jest plik Compose usługi [Docker](https://docs.docker.com/compose/wordpress/#define-the-project). Plik konfiguracji można znaleźć na stronie z [przykładami dla platformy Azure](https://github.com/Azure-Samples/multicontainerwordpress).

[!code-yml[Main](../../../azure-app-service-multi-container/docker-compose-wordpress.yml)]

W usłudze Cloud Shell utwórz katalog Szybki start, a następnie przejdź do niego.

```bash
mkdir quickstart

cd $HOME/quickstart
```

Uruchom następujące polecenie, aby sklonować przykładowe repozytorium aplikacji na komputer lokalny. Następnie zmień katalog na `multicontainerwordpress`.

```bash
git clone https://github.com/Azure-Samples/multicontainerwordpress

cd multicontainerwordpress
```

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

[!INCLUDE [resource group intro text](../../../includes/resource-group.md)]

W usłudze Cloud Shell utwórz [`az group create`](/cli/azure/group?view=azure-cli-latest#az-group-create) grupę zasobów za pomocą polecenia. Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie *myResourceGroup* w lokalizacji *Południowo-środkowe stany USA*. Aby wyświetlić wszystkie obsługiwane lokalizacje dla usługi App Service w systemie Linux w warstwie **Standardowa**, uruchom polecenie [`az appservice list-locations --sku S1 --linux-workers-enabled`](/cli/azure/appservice?view=azure-cli-latest#az-appservice-list-locations).

```azurecli-interactive
az group create --name myResourceGroup --location "South Central US"
```

Zasadniczo grupy zasobów i zasoby są tworzone w pobliskim regionie.

Po zakończeniu działania polecenia zostaną wyświetlone dane wyjściowe JSON z właściwościami grupy zasobów.

## <a name="create-an-azure-app-service-plan"></a>Tworzenie planu usługi Azure App Service

W aplikacji Cloud Shell utwórz plan usługi app [`az appservice plan create`](/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create) service w grupie zasobów za pomocą polecenia.

Poniższy przykład tworzy plan usługi App Service o nazwie `myAppServicePlan` przy użyciu warstwy cenowej **Standardowa** (`--sku S1`) w kontenerze systemu Linux (`--is-linux`).

```azurecli-interactive
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku S1 --is-linux
```

Po utworzeniu planu usługi App Service interfejs wiersza polecenia platformy Azure wyświetli informacje podobne do następujących:

```json
{
  "adminSiteName": null,
  "appServicePlanName": "myAppServicePlan",
  "geoRegion": "South Central US",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan",
  "kind": "linux",
  "location": "South Central US",
  "maximumNumberOfWorkers": 1,
  "name": "myAppServicePlan",
  < JSON data removed for brevity. >
  "targetWorkerSizeId": 0,
  "type": "Microsoft.Web/serverfarms",
  "workerTierName": null
}
```

## <a name="create-a-docker-compose-app"></a>Tworzenie aplikacji narzędzia Docker Compose

> [!NOTE]
> Docker Compose w usłudze Azure App Services ma obecnie limit 4000 znaków w tej chwili.

W terminalu usługi Cloud Shell utwórz wielokontenerową [aplikację internetową](app-service-linux-intro.md) w ramach planu usługi App Service `myAppServicePlan`, używając polecenia [az webapp create](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create). Nie zapomnij `0-9`zastąpić `-` _ \<app_name>_ unikatową nazwą aplikacji (prawidłowe znaki `a-z`to , i ).

```azurecli
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app_name> --multicontainer-config-type compose --multicontainer-config-file compose-wordpress.yml
```

Po utworzeniu aplikacji internetowej w interfejsie wiersza polecenia platformy Azure zostaną wyświetlone dane wyjściowe podobne do następujących:

```json
{
  "additionalProperties": {},
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

### <a name="browse-to-the-app"></a>Przechodzenie do aplikacji

Przejdź do wdrożonej aplikacji pod adresem `http://<app_name>.azurewebsites.net`. Ładowanie aplikacji może potrwać kilka minut. Jeśli wystąpi błąd, zaczekaj jeszcze kilka minut, a następnie odśwież przeglądarkę.

![Przykładowa aplikacja wielokontenerowa funkcji Web App for Containers][1]

**Gratulacje**, utworzono aplikację wielu kontenerów w aplikacji sieci Web dla kontenerów.

[!INCLUDE [Clean-up section](../../../includes/cli-script-clean-up.md)]

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Samouczek: Wieloobiektowa aplikacja WordPress](tutorial-multi-container-app.md)

> [!div class="nextstepaction"]
> [Konfigurowanie kontenera niestandardowego](configure-custom-container.md)

<!--Image references-->
[1]: ./media/tutorial-multi-container-app/azure-multi-container-wordpress-install.png
