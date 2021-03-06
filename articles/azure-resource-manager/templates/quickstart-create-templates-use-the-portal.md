---
title: Wdrażanie szablonu — witryna Azure portal
description: Dowiedz się, jak utworzyć i wdrożyć swój pierwszy szablon usługi Azure Resource Manager przy użyciu witryny Azure Portal.
author: mumian
ms.date: 06/12/2019
ms.topic: quickstart
ms.author: jgao
ms.openlocfilehash: dd3d9caa8184b8637b509fc3318851751b211405
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "80131870"
---
# <a name="quickstart-create-and-deploy-arm-templates-by-using-the-azure-portal"></a>Szybki start: tworzenie i wdrażanie szablonów ARM przy użyciu witryny Azure portal

Dowiedz się, jak wygenerować szablon usługi Azure Resource Manager (ARM) przy użyciu witryny Azure portal oraz proces edytowania i wdrażania szablonu z portalu. Szablony ARM to pliki JSON, które definiują zasoby potrzebne do wdrożenia dla rozwiązania. Aby zapoznać się z pojęciami związanymi z wdrażaniem rozwiązań platformy Azure i zarządzanie nimi, zobacz [omówienie wdrażania szablonów.](overview.md)

![Diagram portalu Szybkiego startu szablonu Menedżera zasobów](./media/quickstart-create-templates-use-the-portal/azure-resource-manager-export-deploy-template-portal.png)

Po ukończeniu tego samouczka zostanie wdrożone konto usługi Azure Storage. Ten sam proces umożliwia wdrażanie innych zasobów platformy Azure.

Jeśli nie masz subskrypcji platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.

## <a name="generate-a-template-using-the-portal"></a>Generowanie szablonu przy użyciu witryny Azure Portal

Tworzenie szablonu ARM od podstaw nie jest łatwym zadaniem, zwłaszcza jeśli jesteś nowy we wdrożeniu platformy Azure i nie znasz formatu JSON. W witrynie Azure Portal możesz skonfigurować zasób, na przykład konto usługi Azure Storage. Przed wdrożeniem zasobu można wyeksportować konfigurację do szablonu. Szablon możesz zapisać i użyć go ponownie w przyszłości.

Wielu doświadczonych deweloperów szablonów używa tej metody do generowania szablonów podczas próby wdrożenia zasobów platformy Azure, które nie są zaznajomieni z. Aby uzyskać więcej informacji na temat eksportowania szablonów przy użyciu portalu, zobacz [Eksportowanie grup zasobów do szablonów](../management/manage-resource-groups-portal.md#export-resource-groups-to-templates). Innym sposobem znalezienia działającego szablonu są [szablony szybki start platformy Azure](https://azure.microsoft.com/resources/templates/).

1. W przeglądarce sieci Web przejdź do [witryny Azure portal](https://portal.azure.com) i zaloguj się.
1. Z menu Portalu platformy Azure wybierz polecenie **Utwórz zasób**.

    ![Wybieranie opcji Utwórz zasób z menu portalu platformy Azure](./media/quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-create-a-resource.png)

1. Wybierz**konto** **Magazyn** > .

    ![Tworzenie konta usługi Azure Storage](./media/quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-create-storage-account-portal.png)
1. Wprowadź następujące informacje:

    |Nazwa|Wartość|
    |----|----|
    |**Grupa zasobów**|Wybierz pozycję **Utwórz nową** i podaj wybraną nazwę grupy zasobów. Na zrzucie ekranu nazwa grupy zasobów to *mystorage1016rg*. Grupa zasobów jest kontenerem zasobów platformy Azure. Grupa zasobów ułatwia zarządzanie zasobami platformy Azure. |
    |**Nazwa**|nadaj unikatową nazwę kontu magazynu. Nazwa konta magazynu musi być unikatowa na całej platformie Azure i zawiera tylko małe litery i cyfry. Nazwa musi zawierać od 3 do 24 znaków. Jeśli zostanie wyświetlony komunikat o błędzie "Nazwa konta magazynu 'mystorage1016' jest już podjęta, spróbuj użyć ** &lt;nazwy>pamięci&lt;masowej Dzisiejsza data w mmdd>**, na przykład **johndolestorage1016**. Aby uzyskać więcej informacji, zobacz [Reguły nazewnictwa i ograniczenia](/azure/architecture/best-practices/resource-naming).|

    Dla pozostałych właściwości możesz użyć wartości domyślnych.

    ![Tworzenie konfiguracji konta usługi Azure Storage za pomocą witryny Azure Portal](./media/quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-create-storage-account.png)

    > [!NOTE]
    > W niektórych z wyeksportowanych szablonów należy wprowadzić pewne zmiany przed ich wdrożeniem.

1. Wybierz pozycję **Przeglądanie + tworzenie** w dolnej części ekranu. Nie należy **wybierać opcji Utwórz** w następnym kroku.
1. Wybierz pozycję **Pobierz szablon do automatyzacji** w dolnej części ekranu. W portalu zostanie wyświetlony wygenerowany szablon:

    ![Generowanie szablonu z poziomu witryny Azure Portal](./media/quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-create-storage-account-template.png)

    W okienku głównym wyświetlany jest szablon. Jest to plik JSON z sześcioma elementami najwyższego poziomu — `schema`, `contentVersion`, `parameters`, `variables`, `resources` i `output`. Aby uzyskać więcej informacji, zobacz [Opis struktury i składni szablonów ARM](./template-syntax.md)

    Istnieje sześć zdefiniowanych parametrów. Jeden z nich jest nazywany **storageAccountName**. Druga wyróżniona część na poprzednim zrzucie ekranu pokazuje, jak odwoływać się do tego parametru w szablonie. W następnej sekcji będziesz edytować szablon w celu użycia wygenerowanej nazwy konta magazynu.

    W szablonie zdefiniowany jest jeden zasób platformy Azure. Typ jest `Microsoft.Storage/storageAccounts`. Spójrz, jak zasób jest zdefiniowany i struktury definicji.
1. Wybierz **pobierz** z góry ekranu.
1. Otwórz pobrany plik zip, a następnie zapisz **plik template.json** na komputerze. W następnej sekcji użyjesz narzędzia do wdrażania szablonu do edycji szablonu.
1. Wybierz kartę **Parametr**, aby zobaczyć wartości podane dla parametrów. Zapisz te wartości, ponieważ będą potrzebne w następnej sekcji podczas wdrażania szablonu.

    ![Generowanie szablonu z poziomu witryny Azure Portal](./media/quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-create-storage-account-template-parameters.png)

    Korzystając z pliku szablonu i pliku parametrów, można utworzyć zasób, w tym samouczku, konto magazynu platformy Azure.

## <a name="edit-and-deploy-the-template"></a>Edytowanie i wdrażanie szablonu

Witryna Azure Portal może służyć do wykonywania niektórych podstawowych czynności edycji szablonu. W tym przewodniku Szybki start użyjesz narzędzia portalu o nazwie *Wdrożenie szablonu*. Narzędzie *Wdrożenie szablonu* jest używane w tym samouczku, aby umożliwić ukończenie całego samouczka przy użyciu jednego interfejsu — witryny Azure Portal. Aby edytować bardziej złożony szablon, należy rozważyć użycie [programu Visual Studio Code](quickstart-create-templates-use-visual-studio-code.md), który zapewnia bogatsze funkcje edycji.

> [!IMPORTANT]
> Wdrażanie szablonów zapewnia interfejs do testowania prostych szablonów. Nie zaleca się używania tej funkcji w produkcji. Zamiast tego należy przechowywać szablony na koncie magazynu platformy Azure lub repozytorium kodu źródłowego, takiego jak GitHub.

Platforma Azure wymaga, aby każda usługa miała unikatową nazwę. Wdrożenie może zakończyć się niepowodzeniem, jeśli zostanie wprowadzona już istniejąca nazwa konta magazynu. W celu uniknięcia tego problemu zmodyfikuj szablon, aby użyć wywołania funkcji szablonu `uniquestring()` do wygenerowania unikatowej nazwy konta magazynu.

1. Z menu portalu platformy Azure lub na stronie **głównej** wybierz pozycję **Utwórz zasób**.
1. W obszarze **Przeszukaj witrynę Marketplace** wpisz **wdrożenie szablonu**, a następnie naciśnij klawisz **ENTER**.
1. Wybierz pozycję **Wdrożenie szablonu**.

    ![Biblioteka szablonów usługi Azure Resource Manager](./media/quickstart-create-templates-use-the-portal/azure-resource-manager-template-library.png)
1. Wybierz **pozycję Utwórz**.
1. Wybierz pozycję **Utwórz własny szablon w edytorze**.
1. Wybierz pozycję **Załaduj plik**, a następnie postępuj zgodnie z instrukcjami, aby załadować plik template.json pobrany w poprzedniej sekcji.
1. Wykonuj następujące trzy zmiany w szablonie:

    ![Szablony usługi Azure Resource Manager](./media/quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-edit-storage-account-template-revised.png)

   - Usuń **parametr storageAccountName,** jak pokazano na poprzednim zrzucie ekranu.
   - Dodaj jedną zmienną o nazwie **storageAccountName,** jak pokazano na poprzednim zrzucie ekranu:

       ```json
       "storageAccountName": "[concat(uniqueString(subscription().subscriptionId), 'storage')]"
       ```

       W tym miejscu są używane dwie funkcje szablonów: `concat()` i `uniqueString()`.
   - Zaktualizuj element name zasobu **Microsoft.Storage/storageAccounts**, aby użyta została nowo zdefiniowana zmienna zamiast parametru:

       ```json
       "name": "[variables('storageAccountName')]",
       ```

     Ostateczny szablon powinien wyglądać następująco:

     ```json
     {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {
           "location": {
               "type": "string"
           },
           "accountType": {
               "type": "string"
           },
           "kind": {
               "type": "string"
           },
           "accessTier": {
               "type": "string"
           },
           "supportsHttpsTrafficOnly": {
               "type": "bool"
           }
       },
       "variables": {
           "storageAccountName": "[concat(uniqueString(subscription().subscriptionId), 'storage')]"
       },
       "resources": [
           {
               "name": "[variables('storageAccountName')]",
               "type": "Microsoft.Storage/storageAccounts",
               "apiVersion": "2018-07-01",
               "location": "[parameters('location')]",
               "properties": {
                   "accessTier": "[parameters('accessTier')]",
                   "supportsHttpsTrafficOnly": "[parameters('supportsHttpsTrafficOnly')]"
               },
               "dependsOn": [],
               "sku": {
                   "name": "[parameters('accountType')]"
               },
               "kind": "[parameters('kind')]"
           }
       ],
       "outputs": {}
     }
     ```
1. Wybierz **pozycję Zapisz**.
1. Wprowadź następujące wartości:

    |Nazwa|Wartość|
    |----|----|
    |**Grupa zasobów**|Wybierz nazwę grupy zasobów utworzoną w ostatniej sekcji. |
    |**Lokalizacja**|Wybierz lokalizację dla konta magazynu. Na przykład **Środkowe stany USA**. |
    |**Typ konta**|wprowadź wartość **Standard_LRS** na potrzeby tego przewodnika Szybki start. |
    |**Rodzaju**|wprowadź wartość **StorageV2** na potrzeby tego przewodnika Szybki start. |
    |**Warstwa dostępu**|wprowadź wartość **Gorąca** na potrzeby tego przewodnika Szybki start. |
    |**Tylko ruch https włączony**| Wybierz wartość **true** na potrzeby tego przewodnika Szybki start. |
    |**Wyrażam zgodę na warunki określone powyżej**|(wybierz)|

    Poniżej przedstawiono zrzut ekranu przedstawiający przykładowe wdrożenie:

    ![Wdrażanie szablonów usługi Azure Resource Manager](./media/quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-deploy.png)

1. Wybierz pozycję **Kup**.
1. Wybierz ikonę dzwonka (powiadomienia) w górnej części ekranu, aby wyświetlić stan wdrożenia. Powinien zostać wyświetlony komunikat **Wdrożenie jest w toku**. Poczekaj na zakończenie wdrożenia.

    ![Powiadomienie dotyczące wdrażania szablonów usługi Azure Resource Manager](./media/quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-portal-notification.png)

1. W okienku powiadomień wybierz pozycję **Przejdź do grupy zasobów**. Zobaczysz ekran podobny do poniższego:

    ![Grupa zasobów w ramach wdrażania szablonów usługi Azure Resource Manager](./media/quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-portal-deployment-resource-group.png)

    Możesz zobaczyć, że stan wdrożenia został oznaczony jako zakończony pomyślnie, natomiast w grupie zasobów jest tylko jedno konto magazynu. Nazwa konta magazynu jest unikatowym ciągiem wygenerowanym przez szablon. Aby dowiedzieć się więcej o korzystaniu z kont magazynu platformy Azure, zobacz przewodnik [Szybki start — przekazywanie, pobieranie i wyświetlanie listy obiektów blob za pomocą witryny Azure Portal](../../storage/blobs/storage-quickstart-blobs-portal.md).

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy zasoby platformy Azure nie będą już potrzebne, wyczyść wdrożone zasoby, usuwając grupę zasobów.

1. W witrynie Azure Portal wybierz pozycję **Grupy zasobów** w menu po lewej stronie.
1. Wprowadź nazwę grupy zasobów w polu **Filtruj według nazwy**.
1. Wybierz nazwę grupy zasobów.  Zostanie wyświetlone konto magazynu w grupie zasobów.
1. Wybierz pozycję **Usuń grupę zasobów** w górnym menu.

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób generowania i wdrażania szablonu w witrynie Azure Portal. Szablon używany w tym przewodniku Szybki start to prosty szablon z jednym zasobem platformy Azure. Gdy szablon jest złożony, do jego tworzenia łatwiej jest użyć programu Visual Studio Code lub Visual Studio. Aby dowiedzieć się więcej o tworzeniu szablonów, zobacz naszą nową serię samouczków dla początkujących:

> [!div class="nextstepaction"]
> [Samouczki dla początkujących](./template-tutorial-create-first-template.md)