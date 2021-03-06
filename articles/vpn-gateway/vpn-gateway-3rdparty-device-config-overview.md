---
title: Konfiguracje urządzeń sieci VPN partnera do łączenia się z bramami sieci VPN platformy Azure
description: Ten artykuł zawiera omówienie konfiguracji urządzeń sieci VPN partnera do łączenia się z bramami sieci VPN platformy Azure.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: article
ms.date: 06/20/2017
ms.author: yushwang
ms.openlocfilehash: b914afaa6725920078da309981bcda5bb765e155
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79279405"
---
# <a name="overview-of-partner-vpn-device-configurations"></a>Omówienie konfiguracji urządzeń sieci VPN partnera
Ten artykuł zawiera omówienie konfigurowania lokalnych urządzeń sieci VPN do łączenia się z bramami sieci VPN platformy Azure. Przykładowa konfiguracja sieci wirtualnej platformy Azure i bramy sieci VPN służy do pokazywania sposobu łączenia się z różnymi lokalnymi konfiguracjami urządzeń sieci VPN przy użyciu tych samych parametrów.



## <a name="device-requirements"></a>Wymagania dotyczące urządzeń
Bramy sieci VPN platformy Azure używają standardowych pakietów protokołów IPsec/IKE dla tuneli sieci VPN typu lokacja lokacja(S2S). Aby uzyskać listę parametrów IPsec/IKE i algorytmów kryptograficznych dla bram sieci VPN platformy Azure, zobacz [Informacje o urządzeniach sieci VPN](vpn-gateway-about-vpn-devices.md). Można również określić dokładne algorytmy i mocne strony klucza dla określonego połączenia, zgodnie z opisem w [informacje o wymaganiach kryptograficznych](vpn-gateway-about-compliance-crypto.md).

## <a name="single-vpn-tunnel"></a><a name ="singletunnel"></a>Pojedynczy tunel VPN
Pierwsza konfiguracja w przykładzie składa się z jednego tunelu sieci VPN S2S między bramą sieci VPN platformy Azure a lokalnym urządzeniem sieci VPN. Opcjonalnie można skonfigurować [protokół BGP (Border Gateway Protocol) w tunelu sieci VPN.](#bgp)

![Diagram pojedynczego tunelu sieci VPN S2S](./media/vpn-gateway-3rdparty-device-config-overview/singletunnel.png)

Aby uzyskać instrukcje krok po kroku dotyczące konfigurowania jednego tunelu sieci VPN, zobacz [Konfigurowanie połączenia lokacja-lokacja](vpn-gateway-howto-site-to-site-resource-manager-portal.md). W poniższych sekcjach określono parametry połączenia dla przykładowej konfiguracji i udostępnij skrypt programu PowerShell, aby ułatwić rozpoczęcie pracy.

### <a name="connection-parameters"></a>Parametry połączenia
W tej sekcji wymieniono parametry przykładów opisanych w poprzednich sekcjach.

| **Parametr**                | **Wartość**                    |
| ---                          | ---                          |
| Prefiksy adresów sieci wirtualnej        | 10.11.0.0/16<br>10.12.0.0/16 |
| Adres IP bramy sieci VPN platformy Azure         | Adres IP bramy sieci VPN platformy Azure         |
| Prefiksy adresów lokalnych | 10.51.0.0/16<br>10.52.0.0/16 |
| Lokalny adres IP urządzenia sieci VPN    | Lokalny adres IP urządzenia sieci VPN    |
| * Sieć wirtualna BGP ASN                | 65010                        |
| * Adres IP elementu równorzędnego usługi Azure BGP           | 10.12.255.30                 |
| * Lokalnie BGP ASN         | 65050                        |
| * Lokalny adres IP elementu równorzędnego BGP     | 10.52.255.254                |

\*Opcjonalny parametr tylko dla protokołu BGP.

### <a name="sample-powershell-script"></a>Przykładowy skrypt programu PowerShell
Ta sekcja zawiera przykładowy skrypt, aby rozpocząć pracę. Aby uzyskać szczegółowe instrukcje, zobacz [Tworzenie połączenia sieci VPN S2S przy użyciu programu PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md).

```powershell
# Declare your variables

$Sub1          = "Replace_With_Your_Subscription_Name"
$RG1           = "TestRG1"
$Location1     = "East US 2"
$VNetName1     = "TestVNet1"
$FESubName1    = "FrontEnd"
$BESubName1    = "Backend"
$GWSubName1    = "GatewaySubnet"
$VNetPrefix11  = "10.11.0.0/16"
$VNetPrefix12  = "10.12.0.0/16"
$FESubPrefix1  = "10.11.0.0/24"
$BESubPrefix1  = "10.12.0.0/24"
$GWSubPrefix1  = "10.12.255.0/27"
$VNet1ASN      = 65010
$DNS1          = "8.8.8.8"
$GWName1       = "VNet1GW"
$GWIPName1     = "VNet1GWIP"
$GWIPconfName1 = "gwipconf1"
$Connection15  = "VNet1toSite5"
$LNGName5      = "Site5"
$LNGPrefix50   = "10.52.255.254/32"
$LNGPrefix51   = "10.51.0.0/16"
$LNGPrefix52   = "10.52.0.0/16"
$LNGIP5        = "Your_VPN_Device_IP"
$LNGASN5       = 65050
$BGPPeerIP5    = "10.52.255.254"

# Connect to your subscription and create a new resource group

Connect-AzAccount
Select-AzSubscription -SubscriptionName $Sub1
New-AzResourceGroup -Name $RG1 -Location $Location1

# Create virtual network

$fesub1 = New-AzVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1 $besub1 = New-AzVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
$gwsub1 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

New-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1

# Create VPN gateway

$gwpip1    = New-AzPublicIpAddress -Name $GWIPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic
$vnet1     = Get-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
$subnet1   = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
$gwipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfName1 -Subnet $subnet1 -PublicIpAddress $gwpip1

New-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gwipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -Asn $VNet1ASN

# Create local network gateway

New-AzLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG1 -Location $Location1 -GatewayIpAddress $LNGIP5 -AddressPrefix $LNGPrefix51,$LNGPrefix52 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP5

# Create the S2S VPN connection

$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng5gw  = Get-AzLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG1

New-AzVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $False
```

### <a name="optional-use-custom-ipsecike-policy-with-usepolicybasedtrafficselectors"></a><a name ="policybased"></a>(Opcjonalnie) Używanie niestandardowych zasad IPsec/IKE z wybranymi selektorami usepolicybasedtraffic
Jeśli urządzenia sieci VPN nie obsługują żadnych selektorów ruchu, takich jak konfiguracje oparte na trasach lub VTI, utwórz niestandardowe zasady IPsec/IKE za pomocą opcji [UsePolicyBasedTrafficSelectors.](vpn-gateway-connect-multiple-policybased-rm-ps.md)

> [!IMPORTANT]
> Należy utworzyć zasady IPsec/IKE, aby włączyć opcję **UsePolicyBasedTrafficSelectors** w połączeniu.


Przykładowy skrypt tworzy zasady IPsec/IKE z następującymi algorytmami i parametrami:
* IKEv2: AES256, SHA384, DHGroup24
* Protokół IPsec: AES256, SHA1, PFS24, Okres istnienia SA 7200 sekund i 20 480 000 KB (20 GB)

Skrypt stosuje zasady IPsec/IKE i włącza **opcję UsePolicyBasedTrafficSelectors** na połączenie.

```powershell
$ipsecpolicy5 = New-AzIpsecPolicy -IkeEncryption AES256 -IkeIntegrity SHA384 -DhGroup DHGroup24 -IpsecEncryption AES256 -IpsecIntegrity SHA1 -PfsGroup PFS24 -SALifeTimeSeconds 7200 -SADataSizeKilobytes 20480000

$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng5gw  = Get-AzLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG1

New-AzVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $False -IpsecPolicies $ipsecpolicy5 -UsePolicyBasedTrafficSelectors $True
```

### <a name="optional-use-bgp-on-s2s-vpn-connection"></a><a name ="bgp"></a>(Opcjonalnie) Używanie protokołu BGP na połączeniu sieci VPN S2S
Podczas tworzenia połączenia sieci VPN S2S opcjonalnie można użyć [protokołu BGP dla bramy sieci VPN](vpn-gateway-bgp-resource-manager-ps.md). Podejście to ma dwie różnice:

* Prefiksy adresu lokalnego mogą być pojedynczym adresem hosta. Lokalny adres równorzędnego adresu IP BGP jest określony w następujący sposób:

    ```powershell
    New-AzLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG1 -Location $Location1 -GatewayIpAddress $LNGIP5 -AddressPrefix $LNGPrefix50 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP5
    ```

* Podczas tworzenia połączenia należy ustawić opcję **-EnableBGP,** aby $True:

    ```powershell
    New-AzVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $True
    ```

## <a name="next-steps"></a>Następne kroki
Aby uzyskać instrukcje krok po kroku dotyczące konfigurowania aktywnych i aktywnych bram sieci VPN, zobacz [Konfigurowanie aktywnych i aktywnych bram sieci VPN dla połączeń międzylokajno-sieciowych i sieci wirtualnej](vpn-gateway-activeactive-rm-powershell.md)do sieci wirtualnej .

