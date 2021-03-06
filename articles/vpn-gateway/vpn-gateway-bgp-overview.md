---
title: 'BGP i brama sieci VPN platformy Azure: Omówienie'
description: Ten artykuł zawiera omówienie użycia protokołu BGP z bramami sieci VPN na platformie Azure.
services: vpn-gateway
author: yushwang
manager: rossort
ms.service: vpn-gateway
ms.topic: article
ms.date: 07/25/2019
ms.author: yushwang
ms.openlocfilehash: 0b4bb7ed90225fcb52ea170c07be2b57f8afbafe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79279327"
---
# <a name="about-bgp-with-azure-vpn-gateway"></a>Informacje o UDw usłudze Azure w bramie sieci VPN
Ten artykuł zawiera omówienie obsługi protokołu BGP (Border Gateway Protocol) w usłudze Azure VPN Gateway.

BGP to standardowy protokół routingu używany często w Internecie do wymiany informacji o routingu i osiągalności między dwiema lub wieloma sieciami. W przypadku jego użycia w kontekście sieci wirtualnych Azure, protokół BGP umożliwia bramom sieci VPN na platformie Azure i lokalnym urządzeniom sieci VPN (nazywanym elementami równorzędnymi lub sąsiednimi BGP), przeprowadzaną za pośrednictwem bram lub routerów wymianę „tras” zawierających przeznaczone dla obu bram informacje na temat dostępności i osiągalności tych prefiksów. Protokół BGP umożliwia również włączenie routingu tranzytowego między wieloma sieciami poprzez propagowanie tras, których brama BGP uczy się od jednego elementu równorzędnego BGP, we wszystkich innych elementach równorzędnych BGP. 

## <a name="why-use-bgp"></a><a name="why"></a>Dlaczego warto używać protokołu BGP?
Protokół BGP stanowi funkcję opcjonalną, której można używać z bramami sieci VPN na platformie Azure opartymi na trasie. Przed włączeniem funkcji należy również upewnić się, że lokalne urządzenia sieci VPN obsługują protokół BGP. Bram sieci VPN na platformie Azure i lokalnych urządzeń sieci VPN można używać także bez protokołu BGP. Odpowiada to użyciu tras statycznych (bez stosowania protokołu BGP) *w porównaniu do* użycia routingu dynamicznego z zastosowaniem protokołu BGP między sieciami i platformą Azure.

Protokół BGP oferuje kilka zalet i nowych możliwości:

### <a name="support-automatic-and-flexible-prefix-updates"></a><a name="prefix"></a>Obsługa automatycznych i elastycznych aktualizacji prefiksów
W U protokołu BGP wystarczy zadeklarować minimalny prefiks do określonego elementu równorzędnego BGP w tunelu sieci VPN IPsec S2S. Może to być nawet sam prefiks hosta (/32) adresu IP elementu równorzędnego BGP dla lokalnego urządzenia sieci VPN. Można określić, które prefiksy sieci lokalnej mają być ogłaszane na platformie Azure w celu umożliwienia dostępu sieci wirtualnej Azure.

Można również anonsować większe prefiksy, które mogą zawierać niektóre prefiksy adresów sieci wirtualnej, takie jak duża prywatna przestrzeń adresowa IP (na przykład 10.0.0.0/8). Należy zauważyć, że prefiksy nie mogą być identyczne z żadnym z prefiksów sieci wirtualnej. Trasy identyczne z prefiksami w sieci wirtualnej zostaną odrzucone.

### <a name="support-multiple-tunnels-between-a-vnet-and-an-on-premises-site-with-automatic-failover-based-on-bgp"></a><a name="multitunnel"></a>Obsługa wielu tuneli między siecią wirtualną i lokacją lokalną z funkcją automatycznego trybu failover w oparciu o protokół BGP
Można utworzyć wiele połączeń między swoją siecią wirtualną na platformie Azure i lokalnymi urządzeniami sieci VPN w tej samej lokalizacji. Ta funkcja pozwala korzystać z wielu tuneli (ścieżek) między dwiema sieciami w konfiguracji aktywne/aktywne. Jeśli jeden z tuneli zostanie odłączony, odpowiednie trasy zostaną wycofane przez BGP, a ruch zostanie automatycznie przeniesiony do pozostałych tuneli.

Na poniższym diagramie przedstawiono prosty przykład tej konfiguracji charakteryzującej się wysoką dostępnością:

![Wiele aktywnych ścieżek](./media/vpn-gateway-bgp-overview/multiple-active-tunnels.png)

### <a name="support-transit-routing-between-your-on-premises-networks-and-multiple-azure-vnets"></a><a name="transitrouting"></a>Obsługa routingu tranzytowego między sieciami lokalnymi i wieloma sieciami wirtualnymi na platformie Azure
Protokół BGP umożliwia uzyskiwanie i propagowanie przez wiele bram prefiksów z różnych sieci, bez względu na to, czy są połączone bezpośrednio lub pośrednio. Umożliwia to realizowany przy użyciu bram sieci VPN na platformie Azure routing tranzytowy między lokalnymi lokacjami lub w obrębie wielu sieci wirtualnych Azure.

Na poniższym diagramie przedstawiono przykład topologii z wieloma przeskokami z wielu ścieżek, która umożliwia przesyłanie ruchu między dwiema sieciami lokalnymi za pośrednictwem bram sieci VPN na platformie Azure w ramach usługi Microsoft Networks:

![Przesyłanie z wieloma przeskokami](./media/vpn-gateway-bgp-overview/full-mesh-transit.png)

## <a name="bgp-faq"></a><a name="faq"></a>Często zadawane pytania dotyczące protokołu BGP
[!INCLUDE [vpn-gateway-faq-bgp-include](../../includes/vpn-gateway-faq-bgp-include.md)]

## <a name="next-steps"></a>Następne kroki
Opis procedury konfigurowania protokołu BGP pod kątem połączeń obejmujących wiele lokalizacji i połączeń między sieciami wirtualnymi zawiera artykuł [Getting started with BGP on Azure VPN gateways](vpn-gateway-bgp-resource-manager-ps.md) (Rozpoczęcie pracy z protokołem BGP dla bram sieci VPN na platformie Azure).

