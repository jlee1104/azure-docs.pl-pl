---
title: Adresy IP używane przez usługi Application Insights i Log Analytics | Dokumenty firmy Microsoft
description: Wyjątki zapory serwera wymagane przez usługę Application Insights
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 12/19/2019
ms.openlocfilehash: 74d696c19ac2a2d0d367f5a018fde8cd3a0eedb2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79535208"
---
# <a name="ip-addresses-used-by-application-insights-and-log-analytics"></a>Adresy IP używane przez usługi Application Insights i Log Analytics
Usługa [Usługi Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) używa wielu adresów IP. Może być konieczne poznanie tych adresów, jeśli monitorowana aplikacja jest hostowana za zaporą.

> [!NOTE]
> Mimo że te adresy są statyczne, możliwe, że będziemy musieli je zmieniać od czasu do czasu. Cały ruch usługi Application Insights reprezentuje ruch wychodzący, z wyjątkiem monitorowania dostępności i elementów webhook, które wymagają reguł zapory przychodzącej.
> 
> 

> [!TIP]
> Zapisz się do tej strony jako kanał https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/azure-monitor/app/ip-addresses.md.atom RSS, dodając do swojego ulubionego czytnika RSS/ATOM, aby otrzymywać powiadomienia o najnowszych zmianach.
> 
> 

## <a name="outgoing-ports"></a>Porty wychodzące
Należy otworzyć niektóre porty wychodzące w zaporze serwera, aby umożliwić zestawowi SDK usługi Application Insights i/lub Monitorowi stanu wysyłanie danych do portalu:

| Przeznaczenie | Adres URL | Adres IP | Porty |
| --- | --- | --- | --- |
| Telemetria |dc.applicationinsights.azure.com<br/>dc.applicationinsights.microsoft.com<br/>dc.services.visualstudio.com |40.114.241.141<br/>104.45.136.42<br/>40.84.189.107<br/>168.63.242.221<br/>52.167.221.184<br/>52.169.64.244<br/>40.85.218.175<br/>104.211.92.54<br/>52.175.198.74<br/>51.140.6.23<br/>40.71.12.231<br/>13.69.65.22<br/>13.78.108.165<br/>13.70.72.233<br/>20.44.8.7<br/>13.86.218.248<br/>40.79.138.41<br/>52.231.18.241<br/>13.75.38.7<br/>102.133.155.50<br/>52.162.110.67<br/>191.233.204.248<br/>13.69.66.140<br/>13.77.52.29<br/>51.107.59.180<br/>40.71.12.235<br/>20.44.8.10<br/>40.71.13.169<br/>13.66.141.156<br/>40.71.13.170 | 443 |
| Transmisja na żywo (wschodnie stany USA) |use.rt.prod.applicationinsights.trafficmanager.net |23.96.28.38<br/>13.92.40.198<br/>40.112.49.101<br/>40.117.80.207 |443 |
| Transmisja na żywo (południowo-środkowe stany USA) |ussc.rt.prod.applicationinsights.trafficmanager.net |157.55.177.6<br/>104.44.140.84<br/>104.215.81.124<br/>23.100.122.113 |443 |
| Transmisja na żywo (Europa Północna) |eun.rt.prod.applicationinsights.trafficmanager.net |40.115.103.168<br/>40.115.104.31<br/>40.87.140.215<br/>40.87.138.220 |443 |
| Transmisja na żywo (Europa Zachodnia) |euw.rt.prod.applicationinsights.trafficmanager.net |13.80.134.255<br/>40.68.61.229<br/>23.101.69.223<br/>52.232.106.242 |443 |
| Transmisja na żywo (Azja Wschodnia) |ase.rt.prod.applicationinsights.trafficmanager.net |23.100.90.7<br/>23.101.13.65<br/>23.101.0.142<br/>23.101.9.4 |443 |
| Transmisja na żywo (Azja Południowo-Wschodnia) |asse.rt.prod.applicationinsights.trafficmanager.net |207.46.224.101<br/>207.46.236.191<br/>137.116.151.139<br/>13.76.87.86 |443 |

## <a name="status-monitor"></a>Monitor stanu
Konfiguracja monitora stanu — wymagana tylko podczas wprowadzania zmian.

| Przeznaczenie | Adres URL | Adres IP | Porty |
| --- | --- | --- | --- |
| Konfigurowanie |`management.core.windows.net` | |`443` |
| Konfigurowanie |`management.azure.com` | |`443` |
| Konfigurowanie |`login.windows.net` | |`443` |
| Konfigurowanie |`login.microsoftonline.com` | |`443` |
| Konfigurowanie |`secure.aadcdn.microsoftonline-p.com` | |`443` |
| Konfigurowanie |`auth.gfx.ms` | |`443` |
| Konfigurowanie |`login.live.com` | |`443` |
| Instalacja | `globalcdn.nuget.org`, `packages.nuget.org` ,`api.nuget.org/v3/index.json` `nuget.org`, `api.nuget.org`, `dc.services.vsallin.net` | |`443` |

## <a name="availability-tests"></a>Testy dostępności
Jest to lista adresów, z których są uruchamiane [testy sieci web dostępności.](../../azure-monitor/app/monitor-web-app-availability.md) Jeśli chcesz uruchomić testy sieci web w aplikacji, ale twój serwer sieci web jest ograniczony do obsługi określonych klientów, musisz zezwolić na ruch przychodzący z naszych serwerów testowych dostępności.

Jeśli używasz grup zabezpieczeń sieciowej platformy Azure, po prostu dodaj **regułę portu przychodzącego,** aby zezwolić na przenoszenie z testów dostępności usługi Application Insights, wybierając **tag usługi** jako **źródło** i **dostępność aplikacji** jako tag **usługi źródłowej.**

>[!div class="mx-imgBorder"]
>![W obszarze Ustawienia wybierz pozycję Reguły zabezpieczeń ruchu przychodzącego, a następnie wybierz pozycję Dodaj u góry karty](./media/ip-addresses/add-inbound-security-rule.png)

>[!div class="mx-imgBorder"]
>![Karta Dodawanie przychodzącej reguły zabezpieczeń](./media/ip-addresses/add-inbound-security-rule2.png)

Otwarte porty 80 (http) i 443 (https) dla ruchu przychodzącego z tych adresów (adresy IP są pogrupowane według lokalizacji):

```
Australia East
20.40.124.176/28
20.40.124.240/28
20.40.125.80/28

Brazil South
191.233.26.176/28
191.233.26.128/28
191.233.26.64/28

France Central (Formerly France South)
20.40.129.96/28
20.40.129.112/28
20.40.129.128/28
20.40.129.144/28

France Central
20.40.129.32/28
20.40.129.48/28
20.40.129.64/28
20.40.129.80/28

East Asia
52.229.216.48/28
52.229.216.64/28
52.229.216.80/28

North Europe
52.158.28.64/28
52.158.28.80/28
52.158.28.96/28
52.158.28.112/28

Japan East
52.140.232.160/28
52.140.232.176/28
52.140.232.192/28

West Europe
51.144.56.96/28
51.144.56.112/28
51.144.56.128/28
51.144.56.144/28
51.144.56.160/28
51.144.56.176/28

UK South
51.105.9.128/28
51.105.9.144/28
51.105.9.160/28

UK West
20.40.104.96/28
20.40.104.112/28
20.40.104.128/28
20.40.104.144/28

Southeast Asia
52.139.250.96/28
52.139.250.112/28
52.139.250.128/28
52.139.250.144/28

West US
40.91.82.48/28
40.91.82.64/28
40.91.82.80/28
40.91.82.96/28
40.91.82.112/28
40.91.82.128/28

Central US
13.86.97.224/28
13.86.97.240/28
13.86.98.48/28
13.86.98.0/28
13.86.98.16/28
13.86.98.64/28

North Central US
23.100.224.16/28
23.100.224.32/28
23.100.224.48/28
23.100.224.64/28
23.100.224.80/28
23.100.224.96/28
23.100.224.112/28
23.100.225.0/28

South Central US
20.45.5.160/28
20.45.5.176/28
20.45.5.192/28
20.45.5.208/28
20.45.5.224/28
20.45.5.240/28

East US
20.42.35.32/28
20.42.35.64/28
20.42.35.80/28
20.42.35.96/28
20.42.35.112/28
20.42.35.128/28

```  

## <a name="application-insights-api"></a>Application Insights API
| Przeznaczenie | Identyfikator URI | Adres IP | Porty |
| --- | --- | --- | --- |
| interfejs API |api.applicationinsights.io<br/>api1.applicationinsights.io<br/>api2.applicationinsights.io<br/>api3.applicationinsights.io<br/>api4.applicationinsights.io<br/>api5.applicationinsights.io |23.96.58.253<br/>13.78.151.158<br/>40.74.59.40<br/>40.70.42.246<br/>40.117.198.0<br/>137.116.226.91<br/>52.163.88.44<br/>52.189.210.240<br/>13.77.201.34<br/>13.78.149.206<br/>52.232.28.146<br/>52.175.241.170<br/>20.36.36.66<br/>52.147.29.101<br/>40.115.155.252<br/>20.188.34.152<br/>52.141.32.103 |80 443 |
| Dokumenty API |dev.applicationinsights.io<br/>dev.applicationinsights.microsoft.com<br/>dev.aisvc.visualstudio.com<br/>www.applicationinsights.io<br/>www.applicationinsights.microsoft.com<br/>www.aisvc.visualstudio.com |23.96.58.253<br/>13.78.151.158<br/>40.74.59.40<br/>40.70.42.246<br/>40.117.198.0<br/>137.116.226.91<br/>52.163.88.44<br/>52.189.210.240<br/>13.77.201.34<br/>13.78.149.206<br/>52.232.28.146<br/>52.175.241.170<br/>20.36.36.66<br/>52.147.29.101<br/>40.115.155.252<br/>20.188.34.152<br/>52.141.32.103 |80 443 |
| Rozszerzenie adnotacji potoku platformy Azure |aigs1.aisvc.visualstudio.com |dynamic|443 |

## <a name="log-analytics-api"></a>Log Analytics API

| Przeznaczenie | Identyfikator URI | Adres IP | Porty |
| --- | --- | --- | --- |
| interfejs API |api.loganalytics.io<br/>*.api.loganalytics.io |23.96.58.253<br/>13.78.151.158<br/>40.74.59.40<br/>40.70.42.246<br/>40.117.198.0<br/>137.116.226.91<br/>52.163.88.44<br/>52.189.210.240<br/>13.77.201.34<br/>13.78.149.206<br/>52.232.28.146<br/>52.175.241.170<br/>20.36.36.66<br/>52.147.29.101<br/>40.115.155.252<br/>20.188.34.152<br/>52.141.32.103 |80 443 |
| Dokumenty API |dev.loganalytics.io<br/>docs.loganalytics.io<br/>www.loganalytics.io |23.96.58.253<br/>13.78.151.158<br/>40.74.59.40<br/>40.70.42.246<br/>40.117.198.0<br/>137.116.226.91<br/>52.163.88.44<br/>52.189.210.240<br/>13.77.201.34<br/>13.78.149.206<br/>52.232.28.146<br/>52.175.241.170<br/>20.36.36.66<br/>52.147.29.101<br/>40.115.155.252<br/>20.188.34.152<br/>52.141.32.103 |80 443 |

## <a name="application-insights-analytics"></a>Analiza wglądu w aplikacje

| Przeznaczenie | Identyfikator URI | Adres IP | Porty |
| --- | --- | --- | --- |
| Portal analityczny | analytics.applicationinsights.io | dynamic | 80 443 |
| CDN | applicationanalytics.azureedge.net | dynamic | 80 443 |
| Sieć CDN multimediów | applicationanalyticsmedia.azureedge.net | dynamic | 80 443 |

Uwaga: domena *.applicationinsights.io jest własnością zespołu usługi Application Insights.

## <a name="log-analytics-portal"></a>Portal analizy dzienników

| Przeznaczenie | Identyfikator URI | Adres IP | Porty |
| --- | --- | --- | --- |
| Portal | portal.loganalytics.io | dynamic | 80 443 |
| CDN | applicationanalytics.azureedge.net | dynamic | 80 443 |

Uwaga: domena *.loganalytics.io jest własnością zespołu usługi Log Analytics.

## <a name="application-insights-azure-portal-extension"></a>Rozszerzenie portalu usługi Azure usługi Application Insights

| Przeznaczenie | Identyfikator URI | Adres IP | Porty |
| --- | --- | --- | --- |
| Rozszerzenie usługi Application Insights | stamp2.app.insightsportal.visualstudio.com | dynamic | 80 443 |
| Rozszerzenie usługi CDN usługi Application Insights | insightsportal-prod2-cdn.aisvc.visualstudio.com<br/>insightsportal-prod2-asiae-cdn.aisvc.visualstudio.com<br/>insightsportal-cdn-aimon.applicationinsights.io | dynamic | 80 443 |

## <a name="application-insights-sdks"></a>SDK usługi application insights

| Przeznaczenie | Identyfikator URI | Adres IP | Porty |
| --- | --- | --- | --- |
| Usługa JS SDK usługi JS SDK | az416426.vo.msecnd.net | dynamic | 80 443 |
| SDK java aplikacji Insights | aijavasdk.blob.core.windows.net | dynamic | 80 443 |

## <a name="alert-webhooks"></a>Alerty dotyczące elementów webhook

| Przeznaczenie | Adres IP | Porty
| --- | --- | --- |
| Generowanie alertów | 23.96.11.4 | 443 |

## <a name="profiler"></a>Profiler

| Przeznaczenie | Identyfikator URI | Adres IP | Porty |
| --- | --- | --- | --- |
| Agent | agent.azureserviceprofiler.net<br/>*.agent.azureserviceprofiler.net | 20.190.60.38<br/>20.190.60.32<br/>52.173.196.230<br/>52.173.196.209<br/>23.102.44.211<br/>23.102.45.216<br/>13.69.51.218<br/>13.69.51.175<br/>138.91.32.98<br/>138.91.37.93<br/>40.121.61.208<br/>40.121.57.2<br/>51.140.60.235<br/>51.140.180.52<br/>52.138.31.112<br/>52.138.31.127<br/>104.211.90.234<br/>104.211.91.254<br/>13.70.124.27<br/>13.75.195.15<br/>52.185.132.101<br/>52.185.132.170<br/>20.188.36.28<br/>40.89.153.171<br/>52.141.22.239<br/>52.141.22.149<br/>102.133.162.233<br/>102.133.161.73<br/>191.232.214.6<br/>191.232.213.239 | 443
| Portal | gateway.azureserviceprofiler.net | dynamic | 443
| Magazyn | *.core.windows.net | dynamic | 443

## <a name="snapshot-debugger"></a>Debuger migawek

> [!NOTE]
> Profilera i debuger migawki współużytkuje ten sam zestaw adresów IP.

| Przeznaczenie | Identyfikator URI | Adres IP | Porty |
| --- | --- | --- | --- |
| Agent | ppe.azureserviceprofiler.net<br/>*.ppe.azureserviceprofiler.net | 20.190.60.38<br/>20.190.60.32<br/>52.173.196.230<br/>52.173.196.209<br/>23.102.44.211<br/>23.102.45.216<br/>13.69.51.218<br/>13.69.51.175<br/>138.91.32.98<br/>138.91.37.93<br/>40.121.61.208<br/>40.121.57.2<br/>51.140.60.235<br/>51.140.180.52<br/>52.138.31.112<br/>52.138.31.127<br/>104.211.90.234<br/>104.211.91.254<br/>13.70.124.27<br/>13.75.195.15<br/>52.185.132.101<br/>52.185.132.170<br/>20.188.36.28<br/>40.89.153.171<br/>52.141.22.239<br/>52.141.22.149<br/>102.133.162.233<br/>102.133.161.73<br/>191.232.214.6<br/>191.232.213.239 | 443
| Portal | ppe.gateway.azureserviceprofiler.net | dynamic | 443
| Magazyn | *.core.windows.net | dynamic | 443
