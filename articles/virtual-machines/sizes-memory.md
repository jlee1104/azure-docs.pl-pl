---
title: Rozmiary maszyn wirtualnych platformy Azure — pamięć | Microsoft Docs
description: Wyświetla różne rozmiary zoptymalizowane pod kątem pamięci dostępne dla maszyn wirtualnych na platformie Azure. Wyświetla informacje o liczbie procesorów wirtualnych vCPU, dyskach danych i kartach sieciowych oraz o przepływności magazynu i przepustowości sieci dla rozmiarów w tej serii.
services: virtual-machines
documentationcenter: ''
author: jonbeck7
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
keywords: Izolacja maszyny wirtualnej, izolowana maszyna wirtualna, izolacja, izolowana
ms.assetid: ''
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: jonbeck
ms.openlocfilehash: 26a44cdf3e57508cebb070669a8c1f473a59df8a
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/20/2020
ms.locfileid: "77493529"
---
# <a name="memory-optimized-virtual-machine-sizes"></a>Rozmiary maszyn wirtualnych zoptymalizowane pod kątem pamięci

Rozmiary maszyn wirtualnych zoptymalizowane pod kątem pamięci oferują duże proporcje pamięci dla serwerów relacyjnych baz danych, średnich i dużych pamięci podręcznych oraz analizę w pamięci. Ten artykuł zawiera informacje na temat liczby procesorów wirtualnych vCPU, dysków danych i kart sieciowych, a także przepływności magazynu i przepustowości sieci dla każdego rozmiaru w ramach tego grupowania.

- [Dv2 i DSv2 —](dv2-dsv2-series-memory.md)kolejne z serii D, które oferują bardziej wydajny procesor. Seria Dv2 jest o około 35% szybsza niż seria D. Jest ona uruchamiana na procesorach Intel® Xeon® 8171M 2,1 GHz (Skylake) lub Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) lub Intel® Xeon® E5-2673 v3 2,4 GHz (Haswell) oraz z technologią Intel Turbo — rozwiązanie 2,0. Konfiguracje pamięci i dysków serii Dv2 są takie same jak w przypadku serii D.

    Dv2 i DSv2 są idealnym rozwiązaniem w przypadku aplikacji wymagających szybszego procesorów wirtualnych vCPU, lepszej wydajności magazynu tymczasowego lub większego zapotrzebowania na pamięć. Oferują one kombinację opcji o dużych możliwościach dla wielu aplikacji klasy korporacyjnej.

- [Seria Eav4 i Easv4](eav4-easv4-series.md) wykorzystuje procesor AMD 2.35 GHz EPYC<sup>TM</sup> 7452 w konfiguracji WIELOWĄTKOWEJ z maksymalnie 256 MB pamięci podręcznej L3, zwiększając opcje uruchamiania większości zoptymalizowanych pod kątem pamięci. Serie Eav4 i Easv4 mają takie same konfiguracje pamięci i dysków jak seria EV3 & Esv3.

- [EV3 i Esv3 — seria](ev3-esv3-series.md) Intel® Xeon® 8171M 2,1 GHz (Skylake) lub procesor Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) w konfiguracji wielowątkowej, zapewniając lepszą wartość dla większości obciążeń ogólnego przeznaczenia, a także dostosowanie EV3 do wyrównania z maszynami wirtualnymi ogólnego przeznaczenia dla większości innych chmur. Pamięć została rozszerzona (od 7 GiB/vCPU do 8 GiB/vCPU), podczas gdy limity dysku i sieci zostały skorygowane dla poszczególnych rdzeni, aby dostosować je do przenoszenia do funkcji Hyper-Threading. EV3 to kolejne rozmiary maszyn wirtualnych o dużej ilości pamięci w rodzinach D/Dv2.

- [Seria M](m-series.md) oferuje dużą liczbę vCPU (do 128 procesorów wirtualnych vCPU) i dużą ilość pamięci (do 3,8 TIB). Jest on również idealny dla bardzo dużych baz danych lub innych aplikacji, które korzystają z dużej liczby vCPU i dużych ilości pamięci.

- [Seria Mv2](mv2-series.md) oferuje najwyższą liczbę vCPU (do 416 procesorów wirtualnych vCPU) i największą ilość pamięci (do 8,19 TIB) dowolnej maszyny wirtualnej w chmurze. Jest to idealne rozwiązanie dla bardzo dużych baz danych lub innych aplikacji, które korzystają z dużej liczby procesorów wirtualnych vCPU i dużych ilości pamięci.

Usługa Azure COMPUTE oferuje rozmiary maszyn wirtualnych, które są odizolowane od określonego typu sprzętu i przeznaczone dla jednego klienta. Te rozmiary maszyn wirtualnych najlepiej nadają się do obciążeń wymagających wysokiego stopnia izolacji od innych klientów w przypadku obciążeń obejmujących takie elementy, jak zgodność i wymagania prawne. Klienci mogą również dodatkowo podzielić zasoby tych izolowanych maszyn wirtualnych, korzystając z [pomocy technicznej platformy Azure dla zagnieżdżonych maszyn wirtualnych](https://azure.microsoft.com/blog/nested-virtualization-in-azure/). Zapoznaj się z poniższą opcją dla izolowanych opcji maszyn wirtualnych.

## <a name="other-sizes"></a>Inne rozmiary

- [Zastosowania ogólne](sizes-general.md)
- [Optymalizacja pod kątem obliczeń](sizes-compute.md)
- [Optymalizacja pod kątem magazynu](sizes-storage.md)
- [Optymalizacja pod kątem procesora GPU](sizes-gpu.md)
- [Obliczenia o wysokiej wydajności](sizes-hpc.md)
- [Poprzednie generacje](sizes-previous-gen.md)

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o tym, jak [usługa Azure COMPUTE units (ACU)](acu.md) może pomóc w porównaniu wydajności obliczeniowej w ramach jednostek SKU platformy Azure.