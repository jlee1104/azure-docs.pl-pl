---
title: Znane problemy z usługą Azure Data Lake Storage Gen2 | Dokumenty firmy Microsoft
description: Dowiedz się więcej o ograniczeniach i znanych problemach usługi Azure Data Lake Storage Gen2.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 03/20/2020
ms.author: normesta
ms.reviewer: jamesbak
ms.openlocfilehash: 4f8fae6580272ed53b8d440ba3e74c6a1ed1e61a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061508"
---
# <a name="known-issues-with-azure-data-lake-storage-gen2"></a>Znane problemy z usługą Azure Data Lake Storage Gen2

W tym artykule opisano ograniczenia i znane problemy usługi Azure Data Lake Storage Gen2.

## <a name="supported-blob-storage-features"></a>Obsługiwane funkcje magazynu obiektów blob

Coraz więcej funkcji magazynu obiektów Blob działa teraz z kontami, które mają hierarchiczną przestrzeń nazw. Aby uzyskać pełną listę, zobacz [Funkcje magazynu obiektów blob dostępnych w usłudze Azure Data Lake Storage Gen2](data-lake-storage-supported-blob-storage-features.md).

## <a name="supported-azure-service-integrations"></a>Obsługiwane integracje usług platformy Azure

Usługa Data Lake Storage gen2 obsługuje kilka usług platformy Azure, których można używać do pozyskiwania danych, przeprowadzania analiz i tworzenia reprezentacji wizualnych. Aby uzyskać listę obsługiwanych usług platformy Azure, zobacz [usługi platformy Azure obsługujące usługę Azure Data Lake Storage Gen2.](data-lake-storage-supported-azure-services.md)

Zobacz [usługi platformy Azure, które obsługują usługę Azure Data Lake Storage Gen2](data-lake-storage-supported-azure-services.md).

## <a name="supported-open-source-platforms"></a>Obsługiwane platformy open source

Kilka platform open source obsługuje data lake storage gen2. Aby uzyskać pełną listę, zobacz [Platformy open source obsługujące usługę Azure Data Lake Storage Gen2](data-lake-storage-supported-open-source-platforms.md).

Zobacz [platformy open source obsługujące usługę Azure Data Lake Storage Gen2.](data-lake-storage-supported-open-source-platforms.md)

## <a name="blob-storage-apis"></a>Interfejsy API magazynu obiektów blob

Interfejsy API obiektów BLOB i interfejsy API usługi Data Lake Storage Gen2 mogą działać na tych samych danych.

W tej sekcji opisano problemy i ograniczenia związane z używaniem interfejsów API obiektów blob i interfejsów API usługi Data Lake Storage Gen2 do działania na tych samych danych.

* Nie można używać zarówno interfejsów API obiektów Blob, jak i interfejsów API magazynu usługi Data Lake do zapisywania w tym samym wystąpieniu pliku. Jeśli piszesz do pliku przy użyciu interfejsów API usługi Data Lake Storage Gen2, bloki tego pliku nie będą widoczne dla wywołań interfejsu API obiektu blob [Pobierz listę bloków.](https://docs.microsoft.com/rest/api/storageservices/get-block-list) Plik można zastąpić przy użyciu interfejsów API usługi Data Lake Storage Gen2 lub interfejsów API obiektów blob. Nie wpłynie to na właściwości pliku.

* Korzystając z operacji [Lista obiektów blob](https://docs.microsoft.com/rest/api/storageservices/list-blobs) bez określania ogranicznika, wyniki będą zawierać zarówno katalogi, jak i obiekty BLOB. Jeśli zdecydujesz się użyć ogranicznika, użyj`/`tylko ukośnika do przodu ( ). Jest to jedyny obsługiwany ogranicznik.

* Jeśli używasz interfejsu API [usuń obiekt obiektu blob,](https://docs.microsoft.com/rest/api/storageservices/delete-blob) aby usunąć katalog, ten katalog zostanie usunięty tylko wtedy, gdy jest pusty. Oznacza to, że nie można użyć interfejsu API obiektów blob usunąć katalogi cyklicznie.

Te interfejsy API REST obiektów blob nie są obsługiwane:

* [Umieść obiekt blob (strona)](https://docs.microsoft.com/rest/api/storageservices/put-blob)
* [Umieść stronę](https://docs.microsoft.com/rest/api/storageservices/put-page)
* [Uzyskaj zakresy stron](https://docs.microsoft.com/rest/api/storageservices/get-page-ranges)
* [Obiekt blob kopiowania przyrostowego](https://docs.microsoft.com/rest/api/storageservices/incremental-copy-blob)
* [Umieść stronę z adresu URL](https://docs.microsoft.com/rest/api/storageservices/put-page-from-url)
* [Umieść obiekt blob (dołącz)](https://docs.microsoft.com/rest/api/storageservices/put-blob)
* [Dołącz blok](https://docs.microsoft.com/rest/api/storageservices/append-block)
* [Dołącz blok z adresu URL](https://docs.microsoft.com/rest/api/storageservices/append-block-from-url)

Niezarządzane dyski maszyn wirtualnych nie są obsługiwane na kontach, które mają hierarchiczny obszar nazw. Jeśli chcesz włączyć hierarchiczny obszar nazw na koncie magazynu, umieść niezarządzane dyski maszyn wirtualnych na koncie magazynu, na które nie ma włączonej funkcji hierarchicznego obszaru nazw.

<a id="api-scope-data-lake-client-library" />

## <a name="file-system-support-in-sdks"></a>Obsługa systemu plików w skusie SDK

Pobierz i ustaw operacje listy ACL nie są obecnie cykliczne.

## <a name="file-system-support-in-powershell-and-azure-cli"></a>Obsługa systemu plików w programach PowerShell i azure CLI

- Obsługa interfejsu [wiersza polecenia programu](data-lake-storage-directory-file-acl-cli.md) [PowerShell](data-lake-storage-directory-file-acl-powershell.md) i platformy Azure są dostępne w publicznej wersji zapoznawczej.
- Pobierz i ustaw operacje listy ACL nie są obecnie cykliczne.

## <a name="lifecycle-management-policies"></a>Zasady zarządzania cyklem życia

* Usuwanie migawek obiektów blob nie jest jeszcze obsługiwane.  

## <a name="archive-tier"></a>Warstwa archiwum

Obecnie występuje błąd, który wpływa na warstwę dostępu do archiwum.


## <a name="blobfuse"></a>Bluzka

Bluzka nie jest obsługiwana.

<a id="known-issues-tools" />

## <a name="azcopy"></a>Narzędzie AzCopy

Używaj tylko najnowszej wersji AzCopy ([AzCopy v10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2ftables%2ftoc.json)).Wcześniejsze wersje programu AzCopy, takie jak AzCopy v8.1, nie są obsługiwane.

<a id="storage-explorer" />

## <a name="azure-storage-explorer"></a>Eksplorator usługi Azure Storage

Używaj `1.6.0` tylko wersji lub wyższych.

<a id="explorer-in-portal" />

## <a name="storage-explorer-in-the-azure-portal"></a>Eksplorator magazynu w witrynie Azure portal

Listy ACL nie są jeszcze obsługiwane.

<a id="third-party-apps" />

## <a name="thirdpartyapplications"></a>Aplikacje innych firm

Aplikacje innych firm, które używają interfejsów API REST do pracy będzie nadal działać, jeśli używasz ich z aplikacjami Data Lake Storage Gen2, które wywołują interfejsy API obiektów Blob prawdopodobnie będzie działać.

## <a name="access-control-lists-acl-and-anonymous-read-access"></a>Listy kontroli dostępu (ACL) i anonimowy dostęp do odczytu

Jeśli [anonimowy dostęp do odczytu](storage-manage-access-to-resources.md) został przyznany kontenerowi, listy ACL nie mają wpływu na ten kontener lub pliki w tym kontenerze.

## <a name="windows-azure-storage-blob-wasb-driver"></a>Sterownik obiektów blob usługi Windows Azure Storage (WASB)

Obecnie istnieje kilka problemów związanych z używaniem sterownika WASB wraz z kontami, które mają hierarchiczną przestrzeń nazw. Zaleca się użycie sterownika [systemu plików obiektów Blob azure (ABFS)](data-lake-storage-abfs-driver.md) w obciążeniach. 





