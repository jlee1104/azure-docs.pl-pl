---
title: 'Modyfikowanie ustawień adresu IP bramy: Program PowerShell'
description: W tym artykule oszukuje się zmieniające się prefiksy adresów IP bramy sieci lokalnej przy użyciu programu PowerShell
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 06/19/2017
ms.author: cherylmc
ms.openlocfilehash: dbaef6e0c81a9230b24aa1e85e7fdc421444047d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75863999"
---
# <a name="modify-local-network-gateway-settings-using-powershell"></a>Modyfikowanie ustawień lokalnej bramy sieci przy użyciu programu PowerShell

Czasami zmieniają się ustawienia adresu bramy sieci lokalnejPrefix lub GatewayIPAddress. W tym artykule pokazano, jak zmodyfikować ustawienia bramy sieci lokalnej. Można również zmodyfikować te ustawienia przy użyciu innej metody, wybierając inną opcję z poniższej listy:

> [!div class="op_single_selector"]
> * [Portal Azure](vpn-gateway-modify-local-network-gateway-portal.md)
> * [Powershell](vpn-gateway-modify-local-network-gateway.md)
> * [Interfejs wiersza polecenia platformy Azure](vpn-gateway-modify-local-network-gateway-cli.md)
>
>

## <a name="before-you-begin"></a><a name="before"></a>Przed rozpoczęciem

Zainstaluj najnowszą wersję poleceń cmdlet programu PowerShell usługi Azure Resource Manager. Aby uzyskać więcej informacji na temat instalowania poleceń cmdlet programu Azure PowerShell, zobacz artykuł [How to install and configure Azure PowerShell](/powershell/azureps-cmdlets-docs) (Instalowanie i konfigurowanie programu Azure PowerShell).

## <a name="modify-ip-address-prefixes"></a><a name="ipaddprefix"></a>Modyfikowanie prefiksów adresów IP

[!INCLUDE [vpn-gateway-modify-ip-prefix-rm](../../includes/vpn-gateway-modify-ip-prefix-rm-include.md)]

## <a name="modify-the-gateway-ip-address"></a><a name="gwip"></a>Modyfikowanie adresu IP bramy

[!INCLUDE [vpn-gateway-modify-lng-gateway-ip-rm](../../includes/vpn-gateway-modify-lng-gateway-ip-rm-include.md)]

## <a name="next-steps"></a>Następne kroki

Możesz zweryfikować połączenie bramy. Zobacz [Zweryfikowanie połączenia bramy](vpn-gateway-verify-connection-resource-manager.md).