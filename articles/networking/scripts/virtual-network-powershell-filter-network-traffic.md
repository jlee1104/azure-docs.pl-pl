---
title: Przykładowy skrypt programu Azure PowerShell — filtrowanie ruchu sieciowego maszyny wirtualnej | Microsoft Docs
description: Przykładowy skrypt programu Azure PowerShell — filtrowanie przychodzącego i wychodzącego ruchu sieciowego maszyny wirtualnej.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: powershell
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 05/16/2017
ms.author: kumud
ms.openlocfilehash: 46b368220a320679e93024f592d980f2b756c3a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73890216"
---
# <a name="filter-inbound-and-outbound-vm-network-traffic"></a>Filtrowanie przychodzącego i wychodzącego ruchu sieciowego maszyny wirtualnej

Ten przykładowy skrypt tworzy sieć wirtualną z podsieciami frontonu i zaplecza. Przychodzący ruch sieciowy do podsieci frontonalnej jest ograniczony do protokołu HTTP i HTTPS, podczas gdy ruch wychodzący do Internetu z podsieci zaplecza jest niedozwolony. Po uruchomieniu skryptu będziesz mieć jedną maszynę wirtualną z dwiema kartami sieciowymi. Każda karta sieciowa jest połączona z inną podsiecią.

W razie potrzeby zainstaluj program Azure PowerShell przy użyciu instrukcji znalezionych `Connect-AzAccount` w [przewodniku programu Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/), a następnie uruchom w celu utworzenia połączenia z platformą Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Przykładowy skrypt

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../powershell_scripts/virtual-network/filter-network-traffic/filter-network-traffic.ps1  "Filter VM network traffic")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia 

Uruchom następujące polecenie, aby usunąć grupę zasobów, maszynę wirtualną i wszystkie powiązane zasoby.

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Objaśnienia dla skryptu

Ten skrypt zawiera następujące polecenia służące do tworzenia grupy zasobów, sieci wirtualnej i sieciowych grup zabezpieczeń. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | Tworzy obiekt konfiguracji podsieci. |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Tworzy sieć wirtualną i podsieć frontonu platformy Azure. |
| [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) | Tworzy reguły zabezpieczeń do przypisania do sieciowej grupy zabezpieczeń. |
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) |Tworzy reguły sieciowych grup zabezpieczeń, które zezwalają na użycie określonych portów w wybranych sieciach lub blokują te porty. |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) | Tworzy skojarzenia sieciowych grup zabezpieczeń z podsieciami. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Tworzy publiczny adres IP, aby uzyskać dostęp do maszyny Wirtualnej z Internetu. |
| [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) | Tworzy interfejsy sieci wirtualnej i dołącza je do podsieci frontonu i zaplecza sieci wirtualnej. |
| [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig) | Tworzy konfigurację maszyny wirtualnej. Ta konfiguracja zawiera informacje, takie jak nazwa maszyny wirtualnej, system operacyjny i poświadczenia administracyjne. Jest ona używana podczas tworzenia maszyny wirtualnej. |
| [New-AzVM](/powershell/module/az.compute/new-azvm) | Tworzy maszynę wirtualną. |
|[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Usuwa grupę zasobów i wszystkie zasoby w niej zawarte. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat programu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).

Dodatkowe przykłady skryptów programu PowerShell dla sieci można znaleźć w [dokumentacji i omówieniu sieci platformy Azure](../powershell-samples.md?toc=%2fazure%2fnetworking%2ftoc.json).
