---
title: Importowanie lub eksportowanie danych za pomocą usługi Azure App Configuration
description: Dowiedz się, jak importować lub eksportować dane do lub z konfiguracji aplikacji platformy Azure
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: lcozzens
ms.openlocfilehash: 2c074cbd99620a482b18cbe2dfcce8f987d78bd5
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79278274"
---
# <a name="import-or-export-configuration-data"></a>Importowanie lub eksportowanie danych konfiguracji

Konfiguracja aplikacji platformy Azure obsługuje operacje importowania i eksportowania danych. Te operacje służą do pracy z danymi konfiguracyjnymi zbiorczo i wymiany danych między magazynem konfiguracji aplikacji i projektem kodu. Na przykład można skonfigurować jeden magazyn konfiguracji aplikacji do testowania i drugi dla środowiska produkcyjnego. Można kopiować ustawienia aplikacji między nimi, aby nie trzeba było wprowadzać danych dwa razy.

Ten artykuł zawiera Przewodnik dotyczący importowania i eksportowania danych przy użyciu konfiguracji aplikacji.

## <a name="import-data"></a>Importowanie danych

Import przenosi dane konfiguracji do magazynu konfiguracji aplikacji z istniejącego źródła. Funkcja import służy do migrowania danych do magazynu konfiguracji aplikacji lub agregowania danych z wielu źródeł. Konfiguracja aplikacji obsługuje importowanie z pliku JSON, YAML lub właściwości.

Importuj dane przy użyciu [Azure Portal](https://portal.azure.com) lub [interfejsu wiersza polecenia platformy Azure](./scripts/cli-import.md). W Azure Portal wykonaj następujące kroki:

1. Przejdź do magazynu konfiguracji aplikacji, a następnie wybierz pozycję **Importuj/Eksportuj** z menu **operacje** .

1. Na karcie **Importowanie** wybierz pozycję **plik konfiguracji** > **usługi źródłowej** .

1. Wybierz pozycję **Język** i wybierz żądany typ danych wejściowych.

1. Wybierz ikonę **folderu** i przejdź do pliku do zaimportowania.

    ![Importuj plik](./media/import-file.png)

1. Wybierz **separator**i opcjonalnie wprowadź **prefiks** do użycia dla zaimportowanych nazw kluczy.

1. Opcjonalnie możesz wybrać **etykietę**.

1. Wybierz pozycję **Zastosuj** , aby zakończyć importowanie.

    ![Zakończono Importowanie pliku](./media/import-file-complete.png)

## <a name="export-data"></a>Eksportowanie danych

Eksportuj dane konfiguracji zapisu przechowywane w konfiguracji aplikacji do innego miejsca docelowego. Użyj funkcji eksportu, na przykład, aby zapisać dane w magazynie konfiguracji aplikacji do pliku, który jest osadzony w kodzie aplikacji podczas wdrażania.

Eksportuj dane przy użyciu [Azure Portal](https://portal.azure.com) lub [interfejsu wiersza polecenia platformy Azure](./scripts/cli-export.md). W Azure Portal wykonaj następujące kroki:

1. Przejdź do magazynu konfiguracji aplikacji, a następnie wybierz pozycję **Importuj/Eksportuj**.

1. Na karcie **eksport** wybierz opcję docelowy **plik konfiguracji** > **usługi** .

1. Opcjonalnie wprowadź **prefiks** i wybierz **etykietę** oraz punkt w czasie, który mają zostać wyeksportowane.

1. Wybierz >  **separatora**.

1. Wybierz pozycję **Zastosuj** , aby zakończyć eksport.

    ![Zakończono eksportowanie pliku](./media/export-file-complete.png)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie aplikacji sieci Web ASP.NET Core](./quickstart-aspnet-core-app.md)  
