---
title: Tworzenie aplikacji funkcji w systemie Linux z witryny Azure Portal
description: Dowiedz się, jak utworzyć pierwszą funkcję platformy Azure do wykonywania bezserwerowego przy użyciu witryny Azure Portal.
ms.topic: quickstart
ms.date: 02/28/2019
ms.openlocfilehash: 1492188f72eb4a691ddceb78aa269601f192e467
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "76963839"
---
# <a name="create-a-function-app-on-linux-in-an-azure-app-service-plan"></a>Tworzenie aplikacji funkcji w systemie Linux w planie usługi Azure App Service

Usługa Azure Functions umożliwia obsługę funkcji w systemie Linux w domyślnym kontenerze usługi Azure App Service. W tym artykule opisano, jak używać [portalu Azure](https://portal.azure.com) do tworzenia aplikacji z funkcją hostowanego przez system Linux, która działa w planie usługi [App Service.](functions-scale.md#app-service-plan) Możesz również [skorzystać z własnego kontenera niestandardowego](functions-create-function-linux-custom-image.md).

![Tworzenie aplikacji funkcji w witrynie Azure Portal](./media/create-function-app-linux-app-service-plan/function-app-in-portal-editor.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem <https://portal.azure.com> przy użyciu danych konta Azure.

## <a name="create-a-function-app"></a>Tworzenie aplikacji funkcji

Do obsługi wykonywania funkcji w systemie Linux potrzebna jest aplikacja funkcji. Aplikacja funkcji zapewnia środowisko do wykonywania kodu funkcji. Umożliwia grupowanie funkcji jako jednostki logicznej w celu łatwiejszego zarządzania, wdrażania, skalowania i udostępniania zasobów. W tym artykule utworzysz plan usługi app service podczas tworzenia aplikacji funkcji.

1. Wybierz przycisk **Utwórz zasób** znajdujący się w lewym górnym rogu witryny Azure Portal, a następnie wybierz pozycję **Compute** > **Aplikacja funkcji**.

    ![Tworzenie aplikacji funkcji w witrynie Azure Portal](./media/create-function-app-linux-app-service-plan/function-app-create-flow.png)

2. Użyj ustawień aplikacji funkcji podanych w tabeli pod obrazem.

    ![Definiowanie nowych ustawień aplikacji funkcji](./media/create-function-app-linux-app-service-plan/function-app-create-flow2.png)

    | Ustawienie      | Sugerowana wartość  | Opis                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nazwa aplikacji** | Nazwa unikatowa w skali globalnej | Nazwa identyfikująca nową aplikację funkcji. Prawidłowe znaki to `a-z`, `0-9` i `-`.  | 
    | **Subskrypcja** | Twoja subskrypcja | Subskrypcja, w ramach której jest tworzona ta nowa aplikacja funkcji. | 
    | **[Grupa zasobów](../azure-resource-manager/management/overview.md)** |  myResourceGroup | Nazwa nowej grupy zasobów, w której ma zostać utworzona aplikacja funkcji. |
    | **System operacyjny** | Linux | Aplikacja funkcji działa na Linuksie. |
    | **Publikowanie** | Code | Używany jest domyślny kontener systemu Linux dla **stosu środowiska wykonawczego.** Wszystko, czego potrzebujesz, to kod projektu aplikacji funkcji. Inną opcją jest opublikowanie niestandardowego [obrazu platformy Docker](functions-create-function-linux-custom-image.md). |
    | **[Plan hostingu](functions-scale.md)** | Plan usługi App Service | Plan hostingu określający sposób przydzielania zasobów do aplikacji funkcji. Po uruchomieniu w planie usługi app service można [kontrolować skalowanie aplikacji funkcji](functions-scale.md).  |
    | **Plan usługi App Service/lokalizacja** | Tworzenie planu | Wybierz **pozycję Utwórz nowy** i podaj nazwę planu usługi app **service.** Wybierz **lokalizację** w [regionie](https://azure.microsoft.com/regions/) w pobliżu lub w pobliżu innych usług, do których mają dostęp funkcje. Wybierz **[żądaną warstwę cenową](https://azure.microsoft.com/pricing/details/app-service/linux/)**. <br/>Nie można uruchomić aplikacji z funkcjami systemu Linux i Windows w tym samym planie usługi App Service. |
    | **Stos środowiska uruchomieniowego** | Preferowany język | Wybierz środowisko uruchomieniowe, które obsługuje ulubiony język programowania funkcji. Wybierz **.NET** dla funkcji w językach C# i F#. |
    | **[Magazyn](../storage/common/storage-account-create.md)** |  Nazwa unikatowa w skali globalnej |  Utwórz konto magazynu używane przez aplikację funkcji. Nazwy kont usługi Magazyn muszą mieć długość od 3 do 24 znaków i mogą zawierać tylko cyfry i małe litery. Możesz również użyć istniejącego konta, które musi spełniać [wymagania dotyczące konta magazynu](storage-considerations.md#storage-account-requirements). |
    | **[Wgląd w aplikacje](functions-monitoring.md)** | Enabled (Włączony) | Usługa Application Insights jest domyślnie wyłączona. Zalecamy włączenie integracji usługi Application Insights teraz i wybranie lokalizacji hostingu w pobliżu lokalizacji planu usługi App Service. Jeśli chcesz to zrobić później, zobacz [Monitorowanie funkcji platformy Azure](functions-monitoring.md).  |

3. Wybierz pozycję **Utwórz**, aby zaaprowizować aplikację funkcji i wdrożyć ją.

4. Wybierz ikonę Powiadomienie w prawym górnym rogu portalu i poszukaj komunikatu **Wdrażanie zakończone pomyślnie**.

    ![Definiowanie nowych ustawień aplikacji funkcji](./media/create-function-app-linux-app-service-plan/function-app-create-notification.png)

5. Wybierz pozycję **Przejdź do zasobu**, aby wyświetlić nową aplikację funkcji.

Następnie należy utworzyć funkcję w nowej aplikacji funkcji. Nawet po aplikacji funkcji jest dostępna, może upłynąć kilka minut, aby w pełni zainicjować.

## <a name="create-an-http-triggered-function"></a><a name="create-function"></a>Tworzenie funkcji wyzwalanej przez protokół HTTP

W tej sekcji pokazano, jak utworzyć funkcję w nowej aplikacji funkcji w portalu.

> [!NOTE]
> Środowisko rozwoju portalu może być przydatne do wypróbowania usługi Azure Functions. W większości scenariuszy należy rozważyć tworzenie funkcji lokalnie i publikowanie projektu w aplikacji funkcji przy użyciu [kodu programu Visual Studio](functions-create-first-function-vs-code.md#create-an-azure-functions-project) lub narzędzi [podstawowych usług Azure Functions Core.](functions-run-local.md#create-a-local-functions-project)  

1. W nowej aplikacji funkcji, wybierz **kartę Przegląd,** a po załadowaniu całkowicie wybierz **+ Nowa funkcja**.

    ![Tworzenie nowej funkcji na karcie Przegląd](./media/create-function-app-linux-app-service-plan/overview-create-function.png)

1. Na karcie **Szybki start** wybierz pozycję **W portalu**i wybierz pozycję **Kontynuuj**.

    ![Wybierz platformę rozwoju funkcji.](./media/create-function-app-linux-app-service-plan/function-app-quickstart-choose-portal.png)

1. Wybierz opcję **Element WebHook + interfejs API**, a następnie wybierz opcję **Utwórz**.

    ![Szybkie rozpoczynanie pracy z usługą Functions w witrynie Azure Portal.](./media/create-function-app-linux-app-service-plan/function-app-quickstart-node-webhook.png)

Funkcja zostanie utworzona przy użyciu szablonu funkcji wyzwalanej przez protokół HTTP właściwego dla danego języka.

Możesz teraz uruchomić nową funkcję, wysyłając żądanie HTTP.

## <a name="test-the-function"></a>Testowanie funkcji

1. W nowej funkcji kliknij pozycję **</> Pobierz adres URL funkcji** w prawym górnym rogu, wybierz pozycję **domyślne (klawisz funkcji)**, a następnie kliknij pozycję **Kopiuj**. 

    ![Kopiowanie adresu URL funkcji z witryny Azure Portal](./media/create-function-app-linux-app-service-plan/function-app-develop-tab-testing.png)

2. Wklej adres URL funkcji do paska adresu przeglądarki. Dodaj wartość ciągu zapytania `&name=<yourname>` na końcu tego adresu URL, a następnie naciśnij klawisz `Enter` na klawiaturze, aby wykonać żądanie. W przeglądarce powinna zostać wyświetlona odpowiedź zwrócona przez funkcję.  

    Poniższy przykład przedstawia odpowiedź w przeglądarce:

    ![Odpowiedź funkcji wyświetlona w przeglądarce.](./media/create-function-app-linux-app-service-plan/function-app-browser-testing.png)

    Adres URL żądania zawiera klucz, który domyślnie jest wymagany do uzyskania dostępu do funkcji za pośrednictwem protokołu HTTP.

3. Gdy funkcja działa, informacje o śledzeniu są zapisywane w dziennikach. Aby wyświetlić dane wyjściowe śledzenia z poprzedniego wykonania, wróć do funkcji w portalu, a następnie kliknij strzałkę w dół w dolnej części ekranu, aby rozwinąć pozycję **Dzienniki**.

   ![Podgląd dziennika usługi Functions w witrynie Azure Portal.](./media/create-function-app-linux-app-service-plan/function-view-logs.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Następne kroki

Utworzono aplikację funkcji z prostą funkcją wyzwalaną przez protokół HTTP.  

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

Aby uzyskać więcej informacji, zobacz [Powiązania protokołu HTTP w usłudze Azure Functions](functions-bindings-http-webhook.md).
