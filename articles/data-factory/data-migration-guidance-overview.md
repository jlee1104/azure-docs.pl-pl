---
title: Migrowanie danych z usługi Data Lake i magazynu danych na platformę Azure
description: Użyj Azure Data Factory, aby zmigrować dane z usługi Data Lake i magazynu danych na platformę Azure.
services: data-factory
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 7/30/2019
ms.openlocfilehash: aaf1593cc049e8b23f8ebe36fea022b3029ccd04
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2019
ms.locfileid: "74930799"
---
# <a name="use-azure-data-factory-to-migrate-data-from-your-data-lake-or-data-warehouse-to-azure"></a>Używanie Azure Data Factory do migrowania danych z usługi Data Lake lub magazynu danych na platformę Azure

Jeśli chcesz przeprowadzić migrację usługi Data Lake lub magazynu danych przedsiębiorstwa (rozszerzenia) do Microsoft Azure, rozważ użycie Azure Data Factory. Azure Data Factory doskonale nadaje się do następujących scenariuszy:

- Migracja obciążenia danych Big Data z usługi Amazon Simple Storage Service (Amazon S3) lub lokalnego rozproszony system plików Hadoop (HDFS) do platformy Azure
- ROZSZERZENIA migrację z rozwiązań Oracle Exadata, Netezza, Teradata lub Amazon RedShift do platformy Azure

Azure Data Factory może przenosić petabajtów (PB) danych na potrzeby migracji usługi Data Lake oraz dziesiątki terabajtów (TB) danych na potrzeby migracji magazynu danych.

## <a name="why-azure-data-factory-can-be-used-for-data-migration"></a>Dlaczego Azure Data Factory można używać do migracji danych

- Azure Data Factory można łatwo skalować w górę ilość mocy obliczeniowej w celu przenoszenia danych w sposób bezserwerowy z wysoką wydajnością, odpornością i skalowalnością. Płacisz tylko za to, czego używasz. Należy również zwrócić uwagę na następujące kwestie: 
  - Azure Data Factory nie ma ograniczeń dotyczących ilości danych ani liczby plików.
  - Azure Data Factory mogą w pełni wykorzystywać przepustowość sieci i magazynu, aby osiągnąć największą ilość przepływności przenoszenia danych w danym środowisku.
  - Azure Data Factory używa metody płatność zgodnie z rzeczywistym użyciem, dzięki czemu płacisz tylko za czas używany do uruchamiania migracji danych na platformę Azure.  
- Azure Data Factory mogą wykonywać zarówno jednorazowe historyczne obciążenia, jak i zaplanowane obciążenia przyrostowe.
- Azure Data Factory używa platformy Azure Integration Runtime (IR) do przenoszenia danych między publicznie dostępnymi punktami końcowymi usługi Data Lake i magazynem. Może również używać własnego środowiska IR do przeniesienia danych dla punktów końcowych usługi Data Lake i magazynu w ramach platformy Azure Virtual Network (VNet) lub za zaporą.
- Azure Data Factory ma zabezpieczenia klasy korporacyjnej: można użyć Instalator Windows (MSI) lub tożsamości usługi do bezpiecznej integracji usługi z usługą lub użyć Azure Key Vault do zarządzania poświadczeniami.
- Azure Data Factory zapewnia środowisko autorskie bez kodu i bogaty, wbudowany pulpit nawigacyjny monitorowania.  

## <a name="online-vs-offline-data-migration"></a>Migracja danych w trybie online i offline

Azure Data Factory to standardowe narzędzie do migracji danych w trybie online do transferowania danych za pośrednictwem sieci (Internet, ER lub sieci VPN). W przypadku migracji danych w trybie offline użytkownicy mogą fizycznie dostarczać urządzenia transferu danych z organizacji do centrum danych platformy Azure.  

Istnieją trzy kluczowe zagadnienia związane z podejściem do migracji w trybie online i offline:  

- Rozmiar danych do migracji
- Przepustowość sieci
- Okno migracji

Załóżmy na przykład, że planujesz użyć Azure Data Factory, aby ukończyć migrację danych w ciągu dwóch tygodni ( *okna migracji*). Zwróć uwagę na różowe/niebieskie linie wycinania w poniższej tabeli. Najniższa różowa komórka dla każdej podanej kolumny pokazuje rozmiar danych/parowanie przepustowości sieci, których okno migracji jest najbliższe, ale nie mniej niż dwa tygodnie. (Każdy parowanie rozmiaru/przepustowości w niebieskiej komórce ma okno migracji online o więcej niż dwa tygodnie). 

![w porównaniu do trybu offline](media/data-migration-guidance-overview/online-offline.png) ta tabela ułatwia określenie, czy można spełnić zamierzone okno migracji w trybie online (Azure Data Factory) w oparciu o rozmiar danych i dostępną przepustowość sieci. Jeśli okno migracji online ma więcej niż dwa tygodnie, należy użyć migracji w trybie offline.

> [!NOTE]
> Korzystając z migracji w trybie online, można osiągnąć zarówno dane historyczne ładowania, jak i przyrostowe źródła danych za pomocą jednego narzędzia.  Dzięki tej metodzie dane można synchronizować między istniejącym magazynem a nowym magazynem podczas całego okna migracji. Oznacza to, że można ponownie skompilować logikę ETL w nowym sklepie przy użyciu odświeżonych danych.


## <a name="next-steps"></a>Następne kroki

- [Migrowanie danych z AWS S3 na platformę Azure](data-migration-guidance-s3-azure-storage.md)
- [Migrowanie danych z lokalnego klastra Hadoop do platformy Azure](data-migration-guidance-hdfs-azure-storage.md)
- [Migrowanie danych z lokalnego serwera Netezza na platformę Azure](data-migration-guidance-netezza-azure-sqldw.md)
