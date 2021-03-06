---
title: Wybierz rozmiary maszyn wirtualnych dla pul — Azure Batch | Microsoft Docs
description: Jak wybierać dostępne rozmiary maszyn wirtualnych dla węzłów obliczeniowych w pulach Azure Batch
services: batch
documentationcenter: ''
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: ''
ms.service: batch
ms.workload: ''
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 09/12/2019
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: c18190ec5e5d079d51630a976681717a78a46e00
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/08/2020
ms.locfileid: "77087051"
---
# <a name="choose-a-vm-size-for-compute-nodes-in-an-azure-batch-pool"></a>Wybieranie rozmiaru maszyny wirtualnej dla węzłów obliczeniowych w puli Azure Batch

Po wybraniu rozmiaru węzła dla puli Azure Batch można wybrać spośród niemal wszystkich rozmiarów maszyn wirtualnych dostępnych na platformie Azure. Platforma Azure oferuje różne rozmiary maszyn wirtualnych z systemem Linux i Windows dla różnych obciążeń.

Istnieje kilka wyjątków i ograniczeń umożliwiających wybranie rozmiaru maszyny wirtualnej:

* Niektóre maszyny wirtualne lub rozmiary maszyn wirtualnych nie są obsługiwane w usłudze Batch.
* Niektóre rozmiary maszyn wirtualnych są ograniczone i należy je włączyć przed przystąpieniem do przydzielenia.

## <a name="supported-vm-series-and-sizes"></a>Obsługiwane serie maszyn wirtualnych i rozmiary

### <a name="pools-in-virtual-machine-configuration"></a>Pule w konfiguracji maszyny wirtualnej

Pule wsadowe w konfiguracji maszyny wirtualnej obsługują niemal wszystkie rozmiary maszyn wirtualnych ([Linux](../virtual-machines/linux/sizes.md), [Windows](../virtual-machines/windows/sizes.md)). Zapoznaj się z poniższą tabelą, aby dowiedzieć się więcej o obsługiwanych rozmiarach i ograniczeniach.

| Seria maszyn wirtualnych  | Obsługiwane rozmiary |
|------------|---------|
| Podstawowa A | Wszystkie rozmiary *z wyjątkiem* Basic_A0 (a0) |
| A | Wszystkie rozmiary *z wyjątkiem* Standard_A0 |
| Av2 | Wszystkie rozmiary |
| B | None |
| DC | None |
| Dv2, DSv2 | Wszystkie rozmiary |
| Dv3, Dsv3 | Wszystkie rozmiary |
| Dav4, Dasv4 | Brak — nie jest jeszcze dostępna |
| EV3, Esv3 | Wszystkie rozmiary, z wyjątkiem E64is_v3 i E64i_v3 |
| Eav4, Easv4 | Brak — nie jest jeszcze dostępna |
| F, FS | Wszystkie rozmiary |
| Fsv2 | Wszystkie rozmiary |
| G, GS | Wszystkie rozmiary |
| H | Wszystkie rozmiary |
| HB<sup>1</sup> | Wszystkie rozmiary |
| HBv2<sup>1</sup> | Wszystkie rozmiary |
| HC<sup>1</sup> | Wszystkie rozmiary |
| Ls | Wszystkie rozmiary |
| Lsv2 | Brak — nie jest jeszcze dostępna |
| M<sup>1</sup> | Wszystkie rozmiary, z wyjątkiem M64, M64m, M128, M128m |
| Mv2 | Brak — nie jest jeszcze dostępna |
| SIECIOWEGO | Wszystkie rozmiary |
| NCv2<sup>1</sup> | Wszystkie rozmiary |
| Seria NCV3<sup>1</sup> | Wszystkie rozmiary |
| ND<sup>1</sup> | Wszystkie rozmiary |
| NDv2<sup>1</sup> | Brak — nie jest jeszcze dostępna |
| Magazyn | Wszystkie rozmiary |
| NVv3<sup>1</sup> | Wszystkie rozmiary |
| NVv4 | None |
| SAP HANA | None |

<sup>1</sup> te rozmiary maszyn wirtualnych można przydzielyć w pulach wsadowym w konfiguracji maszyny wirtualnej, ale należy utworzyć nowe konto wsadowe i zażądać [zwiększenia limitu przydziału](batch-quota-limit.md#increase-a-quota). To ograniczenie zostanie usunięte, gdy przydział vCPU na serię maszyn wirtualnych jest w pełni obsługiwany dla kont usługi Batch.

### <a name="pools-in-cloud-service-configuration"></a>Pule w konfiguracji usługi w chmurze

Pule wsadowe w konfiguracji usługi w chmurze obsługują wszystkie [rozmiary maszyn wirtualnych dla Cloud Services](../cloud-services/cloud-services-sizes-specs.md) **z wyjątkiem** następujących:

| Seria maszyn wirtualnych  | Nieobsługiwane rozmiary |
|------------|-------------------|
| Seria A   | Bardzo małe       |
| Seria Av2 | Standard_A1_v2, Standard_A2_v2, Standard_A2m_v2 |

## <a name="size-considerations"></a>Zagadnienia dotyczące rozmiaru

* **Wymagania dotyczące aplikacji** — należy wziąć pod uwagę charakterystyki i wymagania aplikacji, które będą uruchamiane w węzłach. Takie czynniki jak to, czy aplikacja jest wielowątkowa oraz ile pamięci zużywa, mogą pomóc w wyborze najbardziej odpowiedniego i ekonomicznego rozmiar węzła. W przypadku [obciążeń MPI](batch-mpi.md) z obsługą wiele wystąpień lub aplikacji cuda należy odpowiednio [rozważyć wyspecjalizowane](../virtual-machines/linux/sizes-hpc.md) rozmiary maszyn wirtualnych lub maszyny wirtualne [obsługujące procesor GPU](../virtual-machines/linux/sizes-gpu.md) . (Zobacz [Używanie wystąpień obsługujących funkcję RDMA lub GPU w pulach wsadowym](batch-pool-compute-intensive-sizes.md)).

* **Zadania na węzeł** — typowym zadaniem jest wybranie rozmiaru węzła, przy założeniu, że jedno zadanie jest uruchamiane w węźle w danym momencie. Jednak może być korzystne wykonywanie wielu zadań (i w związku z tym wiele wystąpień aplikacji) [równolegle](batch-parallel-node-tasks.md) w węzłach obliczeniowych podczas wykonywania zadania. W tym przypadku często należy wybrać rozmiar węzła wielordzeniowego, aby uwzględnić zwiększony popyt wykonywania zadań równoległych.

* **Poziomy obciążenia dla różnych zadań** — ten sam rozmiar wynosi wszystkie węzły w puli. Jeśli planujesz uruchamiać aplikacje z różnymi wymaganiami systemowymi i/lub poziomami obciążenia, zalecamy użycie oddzielnych pul.

* **Dostępność regionu** — seria maszyn wirtualnych lub rozmiar może nie być dostępny w regionach, w których tworzysz konta w usłudze Batch. Aby sprawdzić, czy rozmiar jest dostępny, zobacz [dostępne produkty według regionów](https://azure.microsoft.com/regions/services/).

* **Limity** przydziału — liczba [rdzeni](batch-quota-limit.md#resource-quotas) w ramach konta w usłudze Batch może ograniczać liczbę węzłów o danym rozmiarze, które można dodać do puli wsadowej. Aby zażądać zwiększenia limitu przydziału, zobacz [ten artykuł](batch-quota-limit.md#increase-a-quota). 

* **Konfiguracja puli** — ogólnie rzecz biorąc, masz więcej opcji rozmiaru maszyny wirtualnej podczas tworzenia puli w konfiguracji maszyny wirtualnej w porównaniu z konfiguracją usługi w chmurze.

## <a name="next-steps"></a>Następne kroki

* Szczegółowe omówienie usługi Batch można znaleźć w temacie [programowanie równoległych rozwiązań obliczeniowych na dużą skalę za pomocą usługi Batch](batch-api-basics.md).
* Aby uzyskać informacje o używaniu rozmiarów maszyn wirtualnych intensywnie korzystających z obliczeń, zobacz [Używanie wystąpień z obsługą funkcji RDMA lub procesorów GPU w pulach usługi Batch](batch-pool-compute-intensive-sizes.md).
