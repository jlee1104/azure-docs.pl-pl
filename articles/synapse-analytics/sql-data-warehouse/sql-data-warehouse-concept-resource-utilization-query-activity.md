---
title: Łatwość zarządzania i monitorowanie - działanie zapytań, wykorzystanie zasobów
description: Dowiedz się, jakie możliwości są dostępne do zarządzania i monitorowania usługi Azure Synapse Analytics. Użyj witryny Azure portal i dynamicznych widoków zarządzania (DMV), aby zrozumieć aktywność zapytań i wykorzystanie zasobów w magazynie danych.
services: synapse-analytics
author: kevinvngo
manager: craigg-msft
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 03/11/2020
ms.author: kevin
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 9d2a9bb0fcaab38f897987a1922e9c95497821d4
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350670"
---
# <a name="monitoring-resource-utilization-and-query-activity-in-azure-synapse-analytics"></a>Monitorowanie wykorzystania zasobów i aktywności zapytań w usłudze Azure Synapse Analytics
Usługa Azure Synapse Analytics zapewnia zaawansowane środowisko monitorowania w witrynie Azure portal, aby uzyskać szczegółowe informacje dotyczące obciążenia magazynu danych. Portal Azure jest zalecanym narzędziem podczas monitorowania magazynu danych, ponieważ zapewnia konfigurowalne okresy przechowywania, alerty, zalecenia i dostosowywane wykresy i pulpity nawigacyjne dla metryk i dzienników. Portal umożliwia również integrację z innymi usługami monitorowania platformy Azure, takimi jak Azure Monitor (dzienniki) z analizą dzienników, aby zapewnić całościowe środowisko monitorowania nie tylko dla magazynu danych, ale także całej platformy analitycznej platformy Azure dla zintegrowanej doświadczenie w monitorowaniu. W tej dokumentacji opisano, jakie funkcje monitorowania są dostępne do optymalizacji platformy analitycznej i zarządzania nią za pomocą usługi SQL Analytics. 

## <a name="resource-utilization"></a>Wykorzystanie zasobów 
Następujące metryki są dostępne w witrynie Azure portal dla usługi SQL Analytics. Te metryki są widoczne za pośrednictwem [usługi Azure Monitor.](https://docs.microsoft.com/azure/azure-monitor/platform/data-collection#metrics)


| Nazwa metryki             | Opis                                                  | Typ agregacji |
| ----------------------- | ------------------------------------------------------------ | ---------------- |
| Procent użycia procesora CPU          | Wykorzystanie procesora CPU we wszystkich węzłach magazynu danych      | Śr.    |
| Procent użycia operacji we/wy na danych      | Wykorzystanie we/wy we wszystkich węzłach dla magazynu danych       | Śr.    |
| Procent pamięci       | Wykorzystanie pamięci (SQL Server) we wszystkich węzłach magazynu danych | Śr.   |
| Aktywne zapytania          | Liczba aktywnych zapytań wykonywanych w systemie             | Suma              |
| Kwerendy w kolejce          | Liczba kwerend w kolejce oczekujących na rozpoczęcie wykonywania          | Suma              |
| Udane połączenia  | Liczba udanych połączeń z danymi                 | Suma, Liczba       |
| Połączenia zakończone niepowodzeniem      | Liczba nieudanych połączeń z magazynem danych           | Suma, Liczba       |
| Zablokowane przez zaporę sieciową     | Liczba logowań do magazynu danych, który został zablokowany     | Suma, Liczba       |
| Limit DWU               | Cel poziomu usług magazynu danych                | Śr.    |
| Dwu procent          | Maksymalna wartość procentowa procesora a procent we/wy danych        | Śr.    |
| DWU używany                | Limit DWU * PROCENT DWU                                   | Śr.    |
| Procent trafienia w pamięci podręcznej    | (trafienia w pamięci podręcznej / cache miss) * 100, gdzie trafienia w pamięci podręcznej to suma wszystkich segmentów magazynu kolumn trafień w lokalnej pamięci podręcznej SSD i braku pamięci podręcznej jest segmentami magazynu kolumn w lokalnej pamięci podręcznej SSD sumowanych we wszystkich węzłach | Śr.    |
| Wartość procentowa używanej pamięci podręcznej   | (używana pamięć podręczna / pojemność pamięci podręcznej) * 100, gdzie używana pamięć podręczna jest sumą wszystkich bajtów w lokalnej pamięci podręcznej SSD we wszystkich węzłach i pojemności pamięci podręcznej, jest sumą pojemności lokalnej pamięci podręcznej SSD we wszystkich węzłach | Śr.    |
| Lokalny procent tempdb | Lokalne wykorzystanie bazy danych tempdb we wszystkich węzłach obliczeniowych — wartości są emitowane co pięć minut | Śr.    |
| Rozmiar magazynu danych | Całkowity rozmiar danych załadowanych do bazy danych. Obejmuje to dane znajdujące się w tabelach CCI i innych niż CCI, w których rozmiar tabel innych niż CCI jest mierzony przez całkowity rozmiar pliku bazy danych | Suma |
| Rozmiar odzyskiwania po awarii | Całkowity rozmiar kopii zapasowej geograficznej wykonanej co 24 godziny | Suma |
| Rozmiar magazynu migawki | Całkowity rozmiar migawek wykonanych w celu zapewnienia punktów przywracania bazy danych. Obejmuje to automatyczne i zdefiniowane przez użytkownika migawki. | Suma |

Co należy wziąć pod uwagę podczas wyświetlania danych i ustawiania alertów:

- Dwu używane reprezentuje tylko **wysokiego poziomu reprezentacji użycia** w całej puli SQL i nie ma być kompleksowy wskaźnik wykorzystania. Aby ustalić, czy skalować w górę lub w dół, należy wziąć pod uwagę wszystkie czynniki, które mogą mieć wpływ dwu, takich jak współbieżność, pamięci, tempdb i pojemności adaptacyjnej pamięci podręcznej. Zalecamy [uruchomienie obciążenia przy różnych ustawieniach DWU,](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-manage-compute-overview#finding-the-right-size-of-data-warehouse-units) aby określić, co działa najlepiej, aby osiągnąć cele biznesowe.
- Połączenia nie powiodły się i pomyślne są zgłaszane dla określonego magazynu danych — nie dla serwera logicznego
- Procent pamięci odzwierciedla wykorzystanie, nawet jeśli magazyn danych jest w stanie bezczynnym — nie odzwierciedla zużycia pamięci aktywnego obciążenia. Użyj i śledź tę metrykę wraz z innymi (pamięć podręczna tempdb, gen2), aby podjąć całościową decyzję o tym, czy skalowanie w celu uzyskania dodatkowej pojemności pamięci podręcznej zwiększy wydajność obciążenia, aby spełnić wymagania.


## <a name="query-activity"></a>Działanie kwerendy
Aby uzyskać środowisko programowe podczas monitorowania analizy SQL za pośrednictwem protokołu T-SQL, usługa udostępnia zestaw dynamicznych widoków zarządzania (DMV). Widoki te są przydatne, gdy aktywnie rozwiązywania problemów i identyfikowanie wąskich gardeł wydajności z obciążeniem.

Aby wyświetlić listę dmvs, które sql analytics zapewnia, zapoznaj się z tą [dokumentacją](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-reference-tsql-system-views#sql-data-warehouse-dynamic-management-views-dmvs). 

## <a name="metrics-and-diagnostics-logging"></a>Rejestrowanie metryk i informacji diagnostycznych
Zarówno metryki, jak i dzienniki mogą być eksportowane do usługi Azure Monitor, w szczególności składnik [dzienników usługi Azure Monitor](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) i mogą być programowo dostępne za pomocą [zapytań dziennika.](https://docs.microsoft.com/azure/log-analytics/log-analytics-tutorial-viewdata) Opóźnienie dziennika dla usługi SQL Analytics wynosi około 10-15 minut. Aby uzyskać więcej informacji na temat czynników wpływających na opóźnienie, odwiedź następującą dokumentację.


## <a name="next-steps"></a>Następne kroki
W poniższych przewodnikach opisano typowe scenariusze i przypadki użycia podczas monitorowania magazynu danych i zarządzania nim:

- [Monitorowanie obciążenia magazynu danych za pomocą rejestratorów DMV](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-manage-monitor)
