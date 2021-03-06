---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/12/2020
ms.author: dacoulte
ms.openlocfilehash: 4a6c0cad4a93d0d99573a348070823b909c75c7e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79299417"
---
|Nazwa |Opis |Efekt(-y) |Wersja |GitHub |
|---|---|---|---|---|
|[Usługa Azure Backup powinna być włączona dla maszyn wirtualnych](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |Ta zasada pomaga inspekcji, jeśli usługa Azure Backup jest włączona dla wszystkich maszyn wirtualnych. Usługa Azure Backup to ekonomiczne rozwiązanie do tworzenia kopii zapasowych jednym kliknięciem upraszcza odzyskiwanie danych i jest łatwiejsze do włączenia niż inne usługi tworzenia kopii zapasowych w chmurze. |AuditIfNotExists, Wyłączone |1.0.0 |[Link](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json)
|[Konfigurowanie kopii zapasowej na maszynach wirtualnych lokalizacji do istniejącego centralnego programu Vault w tej samej lokalizacji](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F09ce66bc-1220-4153-8104-e3f51c936913) |Ta zasada konfiguruje ochronę usługi Azure Backup na maszynach wirtualnych w danej lokalizacji do istniejącego magazynu centralnego w tej samej lokalizacji. Dotyczy tylko tych maszyn wirtualnych, które nie są jeszcze skonfigurowane do tworzenia kopii zapasowych. Zaleca się, że ta zasada jest przypisana do nie więcej niż 200 maszyn wirtualnych. Jeśli zasada jest przypisana dla więcej niż 200 maszyn wirtualnych, może to spowodować wyzwolenie kopii zapasowej kilka godzin poza zdefiniowanym harmonogramem. Ta zasada zostanie rozszerzona o obsługę większej liczby obrazów maszyn wirtualnych. |deployIfNotExists, auditIfNotExists, wyłączone |1.0.0 |[Link](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachineBackup_Backup_DeployIfNotExists.json)
