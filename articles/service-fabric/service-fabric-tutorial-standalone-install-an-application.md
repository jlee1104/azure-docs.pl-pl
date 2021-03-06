---
title: Instalowanie aplikacji w samodzielnym klastrze
description: W tym samouczku dowiesz się, jak zainstalować aplikację w autonomicznym klastrze sieci szkieletowej usług.
author: dkkapur
ms.topic: tutorial
ms.date: 07/22/2019
ms.author: dekapur
ms.custom: mvc
ms.openlocfilehash: 124fc6035c1d0ad504a34b7db227f0f4675f24f7
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75613962"
---
# <a name="tutorial-deploy-an-application-on-your-service-fabric-standalone-cluster"></a>Samouczek: wdrażanie aplikacji w autonomicznym klastrze usługi Service Fabric

Klastry autonomiczne usługi Service Fabric umożliwiają wybór własnego środowiska i utworzenie klastra zgodnie z obowiązującą w usłudze Service Fabric zasadą „dowolnego systemu operacyjnego i dowolnej chmury”. W tej serii samouczków utworzysz autonomiczny klaster hostowany w udziale usługi AWS i wdrożysz w nim aplikację.

Ten samouczek jest trzecią częścią serii.  Klastry autonomiczne sieci szkieletowej usług oferują możliwość wyboru własnego środowiska i utworzenia klastra w ramach naszego podejścia "dowolny system operacyjny, dowolny chmura" z siecią szkieletową usług. W tym samouczku pokazano, jak utworzyć infrastrukturę usług AWS niezbędną do obsługi takiego klastra autonomicznego.

Część trzecia serii zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Pobieranie przykładowej aplikacji
> * Wdrażanie w klastrze

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tego samouczka:

* [Zainstaluj program Visual Studio 2019](https://www.visualstudio.com/) i zainstaluj obciążenia deweloperskie i **ASP.NET i tworzenia sieci Web** platformy **Azure.**
* [Instalowanie zestawu SDK usługi Service Fabric](service-fabric-get-started.md)

## <a name="download-the-voting-sample-application"></a>Pobieranie przykładowej aplikacji do głosowania

W oknie polecenia uruchom następujące polecenie, aby sklonować przykładowe repozytorium aplikacji na komputer lokalny.

```
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart
```

### <a name="deploy-the-app-to-the-service-fabric-cluster"></a>Wdrażanie aplikacji w klastrze usługi Service Fabric

Po pobraniu aplikacji można ją wdrożyć w klastrze bezpośrednio z programu Visual Studio.

1. Otwórz program Visual Studio.

2. Wybierz**pozycję Otwórz** **plik** > 

3. Przejdź do folderu, do którego sklonowano repozytorium git, i wybierz plik Voting.sln.

4. Kliknij prawym przyciskiem myszy projekt aplikacji `Voting` w Eksploratorze rozwiązań i wybierz polecenie **Publikuj**.

5. Wybierz listę rozwijaną **Punkt końcowy połączenia** i wprowadź publiczną nazwę DNS jednego z węzłów w klastrze.  Na przykład `ec2-34-215-183-77.us-west-2.compute.amazonaws.com:19000`. Należy zauważyć, że na platformie Azure w pełni kwalifikowana nazwa domeny (FQDN) nie jest podana automatycznie, ale można ją łatwo [ustawić na stronie Omówienie maszyny Wirtualnej.](https://docs.microsoft.com/azure/virtual-machines/linux/portal-create-fqdn)

6. Otwórz preferowaną przeglądarkę i wpisz adres klastra (punkt końcowy połączenia; ta aplikacja jest wdrożona na porcie 8080 — na przykład ec2-34-215-183-77.us-west-2.compute.amazonaws.com:8080).

    ![Odpowiedź interfejsu API z klastra](./media/service-fabric-tutorial-standalone-cluster/deployed-app.png)

## <a name="next-steps"></a>Następne kroki

W części trzeciej tej serii przedstawiono sposób wdrażania aplikacji w klastrze:

> [!div class="checklist"]
> * Pobieranie przykładowej aplikacji
> * Wdrażanie w klastrze

Przejdź do części czwartej serii, aby wyczyścić klaster.

> [!div class="nextstepaction"]
> [Czyszczenie zasobów](service-fabric-tutorial-standalone-clean-up.md)