---
title: Co to jest Azure Application Gateway
description: Dowiedz się, jak za pomocą usługi Azure Application Gateway zarządzać ruchem internetowym kierowanym do aplikacji.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: overview
ms.custom: mvc
ms.date: 03/04/2020
ms.author: victorh
ms.openlocfilehash: 4a4395801218409fe77d1081689ba80b495fcfad
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "78302580"
---
# <a name="what-is-azure-application-gateway"></a>Co to jest Azure Application Gateway?

Azure Application Gateway to moduł równoważenia obciążenia ruchu internetowego, który umożliwia zarządzanie ruchem kierowanym do aplikacji internetowych. Tradycyjne moduły równoważenia obciążenia działają w warstwie transportu (warstwie OSI 4 — TCP i UDP) i kierują ruch na podstawie źródłowego adresu IP i portu do docelowego adresu IP i portu.

Brama aplikacji może podejmować decyzje dotyczące routingu na podstawie dodatkowych atrybutów żądania HTTP, na przykład ścieżki URI lub nagłówków hosta. Na przykład można kierować ruch na podstawie przychodzącego adresu URL. Jeśli w przychodzącym adresie URL jest element `/images`, można kierować ruch do określonego zestawu serwerów (nazywanego pulą) skonfigurowanego na potrzeby obrazów. Jeśli `/video` znajduje się w adresie URL, ten ruch jest kierowany do innej puli zoptymalizowanej pod kątem filmów.

![imageURLroute](./media/application-gateway-url-route-overview/figure1-720.png)

Ten typ routingu jest nazywany równoważeniem obciążenia warstwy aplikacji (warstwy OSI 7). Usługa Azure Application Gateway może wykonywać routing oparty na adresach URL i nie tylko.

>[!NOTE]
> Platforma Azure udostępnia zestaw w pełni zarządzanych rozwiązań do równoważenia obciążenia dla Twoich scenariuszy. Jeśli potrzebujesz wysokiej wydajności, małe opóźnienia, warstwy 4 równoważenia obciążenia, zobacz [Co to jest Azure Load Balancer?](../load-balancer/load-balancer-overview.md) Jeśli szukasz globalnego równoważenia obciążenia DNS, zobacz [Co to jest Traffic Manager?](../traffic-manager/traffic-manager-overview.md) Scenariusze end-to-end mogą korzystać z łączenia tych rozwiązań.
>
> Aby zapoznać się z porównaniem opcji równoważenia obciążenia platformy Azure, zobacz [Omówienie opcji równoważenia obciążenia na platformie Azure.](https://docs.microsoft.com/azure/architecture/guide/technology-choices/load-balancing-overview)

## <a name="features"></a>Funkcje

Aby dowiedzieć się więcej o funkcjach bramy aplikacji, zobacz [funkcje bramy aplikacji platformy Azure](features.md).

## <a name="pricing-and-sla"></a>Ceny i umowy SLA

Aby uzyskać informacje o cenach bramy aplikacji, zobacz [Ustalanie cen bramy aplikacji](https://azure.microsoft.com/pricing/details/application-gateway/).

Aby uzyskać informacje o umowy SLA bramy aplikacji, zobacz [SLA bramy aplikacji](https://azure.microsoft.com/support/legal/sla/application-gateway/v1_2/).

## <a name="next-steps"></a>Następne kroki

W zależności od wymagań i środowiska można utworzyć testową bramę aplikacji przy użyciu witryny Azure portal, azure powershell lub interfejsu wiersza polecenia platformy Azure.

- [Szybki start: bezpośredni ruch internetowy w usłudze Azure Application Gateway — Azure Portal](quick-create-portal.md)
- [Szybki start: bezpośredni ruch internetowy w usłudze Azure Application Gateway — Azure PowerShell](quick-create-powershell.md)
- [Szybki start: bezpośredni ruch internetowy w usłudze Azure Application Gateway — interfejs wiersza polecenia platformy Azure](quick-create-cli.md)
