---
title: Przykłady interfejsu wiersza polecenia platformy Azure dla sieci wirtualnej
description: Przykłady interfejsu wiersza polecenia platformy Azure dla sieci wirtualnej.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: twooley
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 07/15/2019
ms.author: kumud
ms.openlocfilehash: 5ce10cf37b61b0269e6c8f2279b8814d9dc4a4f9
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2020
ms.locfileid: "78271208"
---
# <a name="azure-cli-samples-for-virtual-network"></a>Przykłady interfejsu wiersza polecenia platformy Azure dla sieci wirtualnej

Poniższa tabela zawiera linki do skryptów bash utworzonych za pomocą poleceń interfejsu wiersza polecenia platformy Azure:

| | |
|----|----|
| [Tworzenie sieci wirtualnej dla aplikacji wielowarstwowych](./scripts/virtual-network-cli-sample-multi-tier-application.md) | Tworzy sieć wirtualną z podsieciami frontonu i zaplecza. Ruch do podsieci frontonu jest ograniczony do protokołów HTTP i SSH, a ruch do podsieci zaplecza jest ograniczony do portu 3306 środowiska MySQL. |
| [Komunikacja równorzędna dwóch sieci wirtualnych](./scripts/virtual-network-cli-sample-peer-two-virtual-networks.md) | Tworzy i łączy dwie sieci wirtualne w tym samym regionie. |
| [Kierowanie ruchu przez wirtualne urządzenie sieciowe](./scripts/virtual-network-cli-sample-route-traffic-through-nva.md) | Tworzy sieć wirtualną z podsieciami frontonu i zaplecza oraz maszyną wirtualną, która może kierować ruchem między dwiema podsieciami. |
| [Filtrowanie przychodzącego i wychodzącego ruchu sieciowego maszyny wirtualnej](./scripts/virtual-network-cli-sample-filter-network-traffic.md) | Tworzy sieć wirtualną z podsieciami frontonu i zaplecza. Ruch sieciowy przychodzący do podsieci frontonu jest ograniczony do protokołów HTTP, HTTPS i SSH. Ruch wychodzący do Internetu z podsieci zaplecza jest niedozwolony. |
|[Konfigurowanie sieci wirtualnej o podwójnym stosie IPv4 + IPv6 z podstawową Load Balancer](./scripts/virtual-network-cli-sample-ipv6-dual-stack.md)|Wdraża sieć wirtualną o podwójnej stercie (IPv4 + IPv6) z dwiema maszynami wirtualnymi i podstawową Load Balancer platformy Azure przy użyciu adresów IPv4 i IPv6. |
|[Konfigurowanie sieci wirtualnej o podwójnym stosie IPv4 + IPv6 przy użyciu usługa Load Balancer w warstwie Standardowa](./scripts/virtual-network-cli-sample-ipv6-dual-stack-standard-load-balancer.md)|Wdraża sieć wirtualną dual-stack (IPv4 + IPv6) z dwiema maszynami wirtualnymi i usługa Load Balancer w warstwie Standardowa platformy Azure z publicznymi adresami IP w protokołach IPv4 i IPv6. |
|[Samouczek: Tworzenie i testowanie bramy translatora adresów sieciowych — interfejs wiersza polecenia platformy Azure](../virtual-network/tutorial-create-validate-nat-gateway-cli.md)|Utwórz i sprawdź poprawność bramy NAT przy użyciu źródłowej i docelowej maszyny wirtualnej. |