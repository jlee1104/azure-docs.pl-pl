---
title: Dokumentacja interfejsu API usługi Azure Application Insights Agent
description: Dokumentacja interfejsu API agenta Application Insights. Disable-InstrumentationEngine. Monitorowanie wydajności witryny sieci Web bez ponownego wdrażania witryny sieci Web. Współpracuje z usługą ASP.NET Web Apps hostowaną lokalnie, na maszynach wirtualnych lub na platformie Azure.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: dafa12db95a10df467bf0d042cfd9395720648b2
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/27/2020
ms.locfileid: "77671379"
---
# <a name="application-insights-agent-api-disable-instrumentationengine"></a>Interfejs API agenta Application Insights: Disable-InstrumentationEngine

W tym artykule opisano polecenie cmdlet, które jest członkiem [modułu programu PowerShell AZ. ApplicationMonitor](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

## <a name="description"></a>Opis
Wyłącza aparat instrumentacji, usuwając niektóre klucze rejestru.
Uruchom ponownie usługi IIS, aby zmiany zaczęły obowiązywać.

> [!IMPORTANT] 
> To polecenie cmdlet wymaga sesji programu PowerShell z uprawnieniami administratora.

## <a name="examples"></a>Przykłady

```powershell
PS C:\> Disable-InstrumentationEngine
```

## <a name="parameters"></a>Parametry 

### <a name="-verbose"></a>-Verbose
**Wspólny parametr.** Ten przełącznik umożliwia wyprowadzanie szczegółowych dzienników.

## <a name="output"></a>Dane wyjściowe


#### <a name="example-output-from-successfully-disabling-the-instrumentation-engine"></a>Przykładowe dane wyjściowe pomyślnie wyłączające aparat Instrumentacji

```
Configuring IIS Environment for instrumentation engine...
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]'
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]'
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]'
Configuring registry for instrumentation engine...
```


## <a name="next-steps"></a>Następne kroki

 Zrób więcej dzięki Application Insights agentowi:
 - Skorzystaj z naszego przewodnika, aby [rozwiązać problemy z](status-monitor-v2-troubleshoot.md) agentem Application Insights.
