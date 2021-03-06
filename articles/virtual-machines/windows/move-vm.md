---
title: Przenoszenie zasobu maszyny Wirtualnej systemu Windows na platformie Azure
description: Przenoszenie maszyny Wirtualnej systemu Windows do innej subskrypcji platformy Azure lub grupy zasobów w modelu wdrażania Menedżera zasobów.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 4e383427-4aff-4bf3-a0f4-dbff5c6f0c81
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: article
ms.date: 07/03/2019
ms.author: cynthn
ms.openlocfilehash: ed29c92d20a6b0d749ec44a22f42ec446ec58650
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77919570"
---
# <a name="move-a-windows-vm-to-another-azure-subscription-or-resource-group"></a>Przenoszenie maszyny wirtualnej z systemem Windows do innej subskrypcji platformy Azure lub grupy zasobów
W tym artykule opisano, jak przenieść maszynę wirtualną systemu Windows (VM) między grupami zasobów lub subskrypcjami. Przejście między subskrypcjami może być przydatne, jeśli pierwotnie utworzono maszynę wirtualną w subskrypcji osobistej, a teraz chcesz przenieść ją do subskrypcji firmy, aby kontynuować pracę. Nie trzeba uruchamiać maszyny Wirtualnej, aby przenieść go i należy kontynuować pracę podczas przenoszenia.

> [!IMPORTANT]
>Nowe identyfikatory zasobów są tworzone jako część przenoszenia. Po przeniesieniu maszyny Wirtualnej należy zaktualizować narzędzia i skrypty, aby użyć nowych identyfikatorów zasobów.
>
>

[!INCLUDE [virtual-machines-common-move-vm](../../../includes/virtual-machines-common-move-vm.md)]

## <a name="use-powershell-to-move-a-vm"></a>Przenoszenie maszyny Wirtualnej za pomocą programu PowerShell

Aby przenieść maszynę wirtualną do innej grupy zasobów, należy się upewnić, że również przenieść wszystkie zasoby zależne. Aby uzyskać listę z identyfikatorem zasobu każdego z tych zasobów, należy użyć polecenia cmdlet [Get-AzResource.](https://docs.microsoft.com/powershell/module/az.resources/get-azresource)

```azurepowershell-interactive
 Get-AzResource -ResourceGroupName myResourceGroup | Format-table -wrap -Property ResourceId
```

Dane wyjściowe poprzedniego polecenia można użyć do utworzenia oddzielonej przecinkami listy identyfikatorów zasobów do [move-AzResource](https://docs.microsoft.com/powershell/module/az.resources/move-azresource) w celu przeniesienia każdego zasobu do miejsca docelowego.

```azurepowershell-interactive
Move-AzResource -DestinationResourceGroupName "myDestinationResourceGroup" `
    -ResourceId <myResourceId,myResourceId,myResourceId>
```

Aby przenieść zasoby do innej subskrypcji, należy dołączyć **parametr -DestinationSubscriptionId.**

```azurepowershell-interactive
Move-AzResource -DestinationSubscriptionId "<myDestinationSubscriptionID>" `
    -DestinationResourceGroupName "<myDestinationResourceGroup>" `
    -ResourceId <myResourceId,myResourceId,myResourceId>
```


Gdy zostaniesz poproszony o potwierdzenie, że chcesz przenieść określone zasoby, wprowadź **Y,** aby potwierdzić.

## <a name="next-steps"></a>Następne kroki
Można przenieść wiele różnych typów zasobów między grupami zasobów i subskrypcji. Aby uzyskać więcej informacji, zobacz [Przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](../../azure-resource-manager/management/move-resource-group-and-subscription.md).    
