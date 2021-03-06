---
title: vCPU przydziały dla platformy Azure
description: Dowiedz się więcej na temat przydziałów vCPU dla platformy Azure.
keywords: ''
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 05/31/2018
ms.author: cynthn
ms.openlocfilehash: 20017f42031e126bbdae40f66bf4c36efd12f17c
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/11/2020
ms.locfileid: "75893468"
---
# <a name="virtual-machine-vcpu-quotas"></a>Limity przydziału procesorów wirtualnych maszyny wirtualnej

Przydziały vCPU dla maszyn wirtualnych i zestawów skalowania maszyn wirtualnych są ułożone w dwóch warstwach dla każdej subskrypcji w każdym regionie. Pierwsza warstwa jest całkowitą procesorów wirtualnych vCPU regionalną, a druga warstwy to różne rdzenie rodzinne rozmiaru maszyny wirtualnej, takie jak seria D procesorów wirtualnych vCPU. Za każdym razem, gdy zostanie wdrożona nowa maszyna wirtualna, procesorów wirtualnych vCPU dla maszyny wirtualnej nie może przekraczać limitu przydziału vCPU dla rodziny rozmiaru maszyny wirtualnej ani łącznego limitu regionalnego vCPU. Jeśli jeden z tych przydziałów zostanie przekroczony, wdrożenie maszyny wirtualnej nie będzie dozwolone. Istnieje również limit przydziału ogólnej liczby maszyn wirtualnych w regionie. Szczegółowe informacje na temat każdego z tych przydziałów można znaleźć w sekcji **użycie i przydziały** strony **subskrypcji** w [Azure Portal](https://portal.azure.com)lub można wykonać zapytanie dotyczące wartości przy użyciu programu PowerShell.

   
 
## <a name="check-usage"></a>Sprawdzanie użycia

Aby sprawdzić użycie przydziału, można użyć polecenia cmdlet [Get-AzVMUsage](https://docs.microsoft.com/powershell/module/az.compute/get-azvmusage) .

```azurepowershell-interactive
Get-AzVMUsage -Location "East US"
```

Dane wyjściowe będą wyglądać podobnie do tego:

```
Name                             Current Value Limit  Unit
----                             ------------- -----  ----
Availability Sets                            0  2000 Count
Total Regional vCPUs                         4   260 Count
Virtual Machines                             4 10000 Count
Virtual Machine Scale Sets                   1  2000 Count
Standard B Family vCPUs                      1    10 Count
Standard DSv2 Family vCPUs                   1   100 Count
Standard Dv2 Family vCPUs                    2   100 Count
Basic A Family vCPUs                         0   100 Count
Standard A0-A7 Family vCPUs                  0   250 Count
Standard A8-A11 Family vCPUs                 0   100 Count
Standard D Family vCPUs                      0   100 Count
Standard G Family vCPUs                      0   100 Count
Standard DS Family vCPUs                     0   100 Count
Standard GS Family vCPUs                     0   100 Count
Standard F Family vCPUs                      0   100 Count
Standard FS Family vCPUs                     0   100 Count
Standard NV Family vCPUs                     0    24 Count
Standard NC Family vCPUs                     0    48 Count
Standard H Family vCPUs                      0     8 Count
Standard Av2 Family vCPUs                    0   100 Count
Standard LS Family vCPUs                     0   100 Count
Standard Dv2 Promo Family vCPUs              0   100 Count
Standard DSv2 Promo Family vCPUs             0   100 Count
Standard MS Family vCPUs                     0     0 Count
Standard Dv3 Family vCPUs                    0   100 Count
Standard DSv3 Family vCPUs                   0   100 Count
Standard Ev3 Family vCPUs                    0   100 Count
Standard ESv3 Family vCPUs                   0   100 Count
Standard FSv2 Family vCPUs                   0   100 Count
Standard ND Family vCPUs                     0     0 Count
Standard NCv2 Family vCPUs                   0     0 Count
Standard NCv3 Family vCPUs                   0     0 Count
Standard LSv2 Family vCPUs                   0     0 Count
Standard Storage Managed Disks               2 10000 Count
Premium Storage Managed Disks                1 10000 Count
```


## <a name="reserved-vm-instances"></a>Wystąpienia zarezerwowane maszyn wirtualnych
Wystąpienia zarezerwowane maszyn wirtualnych, które są objęte zakresem pojedynczej subskrypcji bez elastyczności rozmiaru maszyny wirtualnej, spowodują dodanie nowego aspektu do przydziałów vCPU. Te wartości opisują liczbę wystąpień określonego rozmiaru, które muszą być wdrożone w subskrypcji. Działają jako symbol zastępczy w systemie przydziału, aby zapewnić, że przydział jest zarezerwowany w celu zapewnienia, że zarezerwowane wystąpienia maszyn wirtualnych są wdrażane w ramach subskrypcji. Na przykład jeśli określona subskrypcja ma 10 Standard_D1 zarezerwowanych wystąpień maszyn wirtualnych limit użycia dla Standard_D1 zarezerwowane wystąpienia maszyn wirtualnych będą 10. Spowoduje to, że platforma Azure będzie mieć pewność, że w łącznej liczbie miejscowej przydziału procesorów wirtualnych vCPU będzie dostępna co najmniej 10 procesorów wirtualnych vCPU, które mają być używane dla wystąpień Standard_D1, a w przypadku wystąpień Standard_D1 będzie dostępnych co najmniej 10 procesorów wirtualnych vCPU.

Jeśli do zakupu pojedynczej subskrypcji RI wymagane jest zwiększenie limitu przydziału, można [zażądać zwiększenia limitu przydziału](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request) w ramach subskrypcji.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat rozliczeń i przydziałów, zobacz [limity subskrypcji i usług platformy Azure, limity przydziału i ograniczenia](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits?toc=/azure/billing/TOC.json).
