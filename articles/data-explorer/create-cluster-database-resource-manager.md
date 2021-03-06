---
title: Tworzenie klastra i bazy danych usługi Azure Data Explorer przy użyciu szablonu usługi Azure Resource Manager
description: Dowiedz się, jak utworzyć klaster i bazę danych usługi Azure Data Explorer przy użyciu szablonu usługi Azure Resource Manager
author: orspod
ms.author: orspodek
ms.reviewer: lugoldbe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/26/2019
ms.openlocfilehash: 56639d8a29ad8eac465845c8d354d04b31ba6093
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75911960"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-an-azure-resource-manager-template"></a>Tworzenie klastra i bazy danych usługi Azure Data Explorer przy użyciu szablonu usługi Azure Resource Manager

> [!div class="op_single_selector"]
> * [Portal](create-cluster-database-portal.md)
> * [Cli](create-cluster-database-cli.md)
> * [Powershell](create-cluster-database-powershell.md)
> * [C #](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
> * [Szablon usługi Azure Resource Manager](create-cluster-database-resource-manager.md)

Azure Data Explorer to szybka i wysoce skalowalna usługa eksploracji danych na potrzeby danych dziennika i telemetrycznych. Aby używać usługi Azure Data Explorer, najpierw utwórz klaster, a następnie utwórz w tym klastrze co najmniej jedną bazę danych. Następnie pozyskaj (załaduj) dane do bazy danych, aby uruchamiać w niej zapytania. 

W tym artykule utworzysz klaster i bazę danych Usługi Azure Data Explorer przy użyciu [szablonu Usługi Azure Resource Manager](../azure-resource-manager/management/overview.md). W tym artykule pokazano, jak zdefiniować, które zasoby są wdrażane i jak zdefiniować parametry, które są określone podczas wdrażania. Można użyć tego szablonu na potrzeby własnych wdrożeń lub dostosować go do konkretnych potrzeb. Aby uzyskać informacje dotyczące tworzenia szablonów, zobacz [tworzenie szablonów usługi Azure Resource Manager](/azure/azure-resource-manager/resource-group-authoring-templates). Aby uzyskać składnię JSON i właściwości używane w szablonie, zobacz [Typy zasobów Microsoft.Kusto](/azure/templates/microsoft.kusto/allversions).

Jeśli nie masz subskrypcji platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.

## <a name="azure-resource-manager-template-for-cluster-and-database-creation"></a>Szablon usługi Azure Resource Manager do tworzenia klastrów i baz danych

W tym artykule używasz [istniejącego szablonu szybkiego startu](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-kusto-cluster-database/azuredeploy.json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "clusters_kustocluster_name": {
          "type": "string",
          "defaultValue": "[concat('kusto', uniqueString(resourceGroup().id))]",
          "metadata": {
            "description": "Name of the cluster to create"
          }
      },
      "databases_kustodb_name": {
          "type": "string",
          "defaultValue": "kustodb",
          "metadata": {
            "description": "Name of the database to create"
          }
      },
      "location": {
        "type": "string",
        "defaultValue": "[resourceGroup().location]",
        "metadata": {
          "description": "Location for all resources."
        }
      }
  },
  "variables": {},
  "resources": [
      {
          "name": "[parameters('clusters_kustocluster_name')]",
          "type": "Microsoft.Kusto/clusters",
          "sku": {
              "name": "Standard_D13_v2",
              "tier": "Standard",
              "capacity": 2
          },
          "apiVersion": "2019-09-07",
          "location": "[parameters('location')]",
          "tags": {
            "Created By": "GitHub quickstart template"
          }
      },
      {
          "name": "[concat(parameters('clusters_kustocluster_name'), '/', parameters('databases_kustodb_name'))]",
          "type": "Microsoft.Kusto/clusters/databases",
          "apiVersion": "2019-09-07",
          "location": "[parameters('location')]",
          "dependsOn": [
              "[resourceId('Microsoft.Kusto/clusters', parameters('clusters_kustocluster_name'))]"
          ],
          "properties": {
              "softDeletePeriodInDays": 365,
              "hotCachePeriodInDays": 31
          }
      }
  ]
}
```

Aby znaleźć więcej przykładów szablonów, zobacz [Szablony szybki start platformy Azure](https://azure.microsoft.com/resources/templates/).

## <a name="deploy-the-template-and-verify-template-deployment"></a>Wdrażanie szablonu i weryfikowanie wdrażania szablonu

Szablon Usługi Azure Resource Manager można wdrożyć [przy użyciu portalu Azure](#use-the-azure-portal-to-deploy-the-template-and-verify-template-deployment) lub za pomocą programu [PowerShell](#use-powershell-to-deploy-the-template-and-verify-template-deployment).

### <a name="use-the-azure-portal-to-deploy-the-template-and-verify-template-deployment"></a>Wdrażanie szablonu i weryfikowanie wdrażania szablonu za pomocą portalu Azure

1. Aby utworzyć klaster i bazę danych, użyj następującego przycisku, aby rozpocząć wdrażanie. Kliknij prawym przyciskiem myszy i wybierz pozycję **Utwórz w nowym oknie**, aby wykonać pozostałe kroki w tym artykule.

    [![Wdrażanie na platformie Azure](media/create-cluster-database-resource-manager/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-kusto-cluster-database%2Fazuredeploy.json)

    Przycisk **Wdróż na platformie Azure** powoduje przejście do witryny Azure Portal w celu wypełnienia formularza wdrożenia.

    ![Wdrażanie na platformie Azure](media/create-cluster-database-resource-manager/deploy-2-azure.png)

    Szablon można [edytować i wdrażać w witrynie Azure portal](/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal#edit-and-deploy-the-template) przy użyciu formularza.

1. Ukończ **sekcje PODSTAWY** i **USTAWIENIA.** Wybierz unikatowe nazwy klastra i bazy danych.
Utworzenie klastra i bazy danych usługi Azure Data Explorer zajmuje kilka minut.

1. Aby zweryfikować wdrożenie, otwórz grupę zasobów w [witrynie Azure Portal,](https://portal.azure.com) aby znaleźć nowy klaster i bazę danych. 

### <a name="use-powershell-to-deploy-the-template-and-verify-template-deployment"></a>Wdrażanie szablonu i weryfikowanie wdrażania szablonu za pomocą programu PowerShell

#### <a name="deploy-the-template-using-powershell"></a>Wdrażanie szablonu przy użyciu programu PowerShell

1. Wybierz **pozycję Wypróbuj z** następującego bloku kodu, a następnie postępuj zgodnie z instrukcjami, aby zalogować się do powłoki usługi Azure Cloud.

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $resourceGroupName = "${projectName}rg"
    $clusterName = "${projectName}cluster"
    $parameters = @{}
    $parameters.Add("clusters_kustocluster_name", $clusterName)
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-kusto-cluster-database/azuredeploy.json"
    New-AzResourceGroup -Name $resourceGroupName -Location $location
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -TemplateParameterObject $parameters
    Write-Host "Press [ENTER] to continue ..."
    ```

1. Wybierz przycisk **Kopiuj**, aby skopiować skrypt programu PowerShell.
1. Kliknij prawym przyciskiem myszy konsolę powłoki, a następnie wybierz polecenie **Wklej**.
Utworzenie klastra i bazy danych usługi Azure Data Explorer zajmuje kilka minut.

#### <a name="verify-the-deployment-using-powershell"></a>Weryfikowanie wdrożenia przy użyciu programu PowerShell

Aby zweryfikować wdrożenie, użyj następującego skryptu programu Azure PowerShell.  Jeśli usługa Cloud Shell jest nadal otwarta, nie trzeba kopiować/uruchamiać pierwszego wiersza (Read-Host). Aby uzyskać więcej informacji na temat zarządzania zasobami eksploratora danych platformy Azure w programie PowerShell, przeczytaj [artykuł Az.Kusto](/powershell/module/az.kusto/?view=azps-2.7.0). 

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that you used in the last procedure"

Install-Module -Name Az.Kusto
$resourceGroupName = "${projectName}rg"
$clusterName = "${projectName}cluster"

Get-AzKustoCluster -ResourceGroupName $resourceGroupName -Name $clusterName
Write-Host "Press [ENTER] to continue ..."
```

[!INCLUDE [data-explorer-clean-resources](../../includes/data-explorer-clean-resources.md)]

## <a name="next-steps"></a>Następne kroki

[Pozyskiwania danych do klastra i bazy danych usługi Azure Data Explorer](ingest-data-overview.md)
