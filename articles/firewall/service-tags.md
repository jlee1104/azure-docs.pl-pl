---
title: Omówienie tagów usługi Zapory platformy Azure
description: Tag usługi reprezentuje grupę prefiksów adresów IP, aby zminimalizować złożoność tworzenia reguły zabezpieczeń.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: ec9fb3c9b27d21ac65888379f59d51ecc8042eb0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74168693"
---
# <a name="azure-firewall-service-tags"></a>Tagi usługi Zapory platformy Azure

Tag usługi reprezentuje grupę prefiksów adresów IP, aby zminimalizować złożoność tworzenia reguły zabezpieczeń. Nie można utworzyć własnego tagu usługi ani określić adresów IP uwzględnionych w tagu. Firma Microsoft zarządza prefiksami adresów obejmowanymi przez tag usługi i automatycznie aktualizuje tag usługi, gdy adresy ulegną zmianie.

Tagi usługi Zapory platformy Azure mogą być używane w polu docelowym reguł sieciowych. Można ich używać zamiast określonych adresów IP.

## <a name="supported-service-tags"></a>Obsługiwane tagi usług

Zobacz [grupy zabezpieczeń,](../virtual-network/security-overview.md#service-tags) aby uzyskać listę tagów usług, które są dostępne do użycia w regułach sieci zapory platformy Azure.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o regułach Zapory platformy Azure, zobacz [logika przetwarzania reguł zapory platformy Azure](rule-processing.md).