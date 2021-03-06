---
title: Szybki start — tworzenie obiektu blob za pomocą programu PowerShell
titleSuffix: Azure Storage
description: W tym przewodniku Szybki start program Azure PowerShell jest używany w ramach magazynu obiektów (blob). Następnie przy użyciu programu PowerShell przekażesz obiekt blob do usługi Azure Storage, pobierzesz obiekt blob i wyświetlisz obiekty blob w kontenerze.
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.date: 02/26/2020
ms.author: tamram
ms.openlocfilehash: 479145f4d42c0708c109ab582e76e3691971c6ad
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "80061405"
---
# <a name="quickstart-upload-download-and-list-blobs-with-powershell"></a>Szybki start: przekazywanie, pobieranie i wyświetlanie listy obiektów blob za pomocą programu PowerShell

Moduł Azure PowerShell umożliwia tworzenie zasobów platformy Azure i zarządzanie nimi. Tworzenie zasobów platformy Azure lub zarządzanie nimi może odbywać się w wierszu polecenia programu PowerShell lub za pośrednictwem skryptów. Ten przewodnik zawiera opis sposobu użycia programu PowerShell do transferowania plików między dyskiem lokalnym i usługą Azure Blob Storage.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Aby uzyskać dostęp do usługi Azure Storage, potrzebujesz subskrypcji platformy Azure. Jeśli nie masz jeszcze subskrypcji, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Dla tego przewodnika Szybki start wymagany jest moduł Az Azure PowerShell w wersji 0.7 lub nowszej. Uruchom polecenie `Get-InstalledModule -Name Az -AllVersions | select Name,Version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-Az-ps).

[!INCLUDE [storage-quickstart-tutorial-intro-include-powershell](../../../includes/storage-quickstart-tutorial-intro-include-powershell.md)]

## <a name="create-a-container"></a>Tworzenie kontenera

Obiekty blob są zawsze przesyłane do kontenera. Możesz organizować grupy obiektów blob, tak jak organizujesz pliki w folderach na komputerze.

Określ nazwę kontenera, a następnie utwórz kontener przy użyciu polecenia [New-AzStorageContainer](/powershell/module/az.storage/new-AzStoragecontainer). Ustaw uprawnienia na `blob`, aby zezwolić na publiczny dostęp do plików. W tym przykładzie nazwą kontenera jest *quickstartblobs*.

```powershell
$containerName = "quickstartblobs"
New-AzStorageContainer -Name $containerName -Context $ctx -Permission blob
```

## <a name="upload-blobs-to-the-container"></a>Przekazywanie obiektów blob do kontenera

Usługa Blob Storage obsługuje blokowe, uzupełnialne i stronicowe obiekty blob. Pliki VHD, które służą do tworzenia obsługi maszyn wirtualnych usługi IaaS, to stronicowe obiekty blob. Uzupełnialne obiekty blob mogą służyć do rejestrowania, na przykład w sytuacji, w której konieczny jest zapis do pliku, a następnie dodawanie kolejnych informacji. Większość plików przechowywanych w usłudze Blob Storage to blokowe obiekty blob. 

Aby przekazać plik do blokowego obiektu blob, pobierz odwołanie do kontenera i uzyskaj odwołanie do blokowego obiektu blob w tym kontenerze. Po uzyskaniu odwołania do obiektu blob możesz przekazać do niego dane przy użyciu polecenia [Set-AzStorageBlobContent](/powershell/module/az.storage/set-AzStorageblobcontent). Ta operacja tworzy obiekt blob, jeśli nie istnieje, lub zastępuje obiekt blob, jeśli istnieje.

Poniższe przykłady prześledą *image001.jpg* i *Image002.png* z folderu *D:\\_TestImages* na dysku lokalnym do utworzonego kontenera.

```powershell
# upload a file
Set-AzStorageBlobContent -File "D:\_TestImages\Image001.jpg" `
  -Container $containerName `
  -Blob "Image001.jpg" `
  -Context $ctx 

# upload another file
Set-AzStorageBlobContent -File "D:\_TestImages\Image002.png" `
  -Container $containerName `
  -Blob "Image002.png" `
  -Context $ctx
```

Przed kontynuowaniem można przesłać dowolną liczbę plików.

## <a name="list-the-blobs-in-a-container"></a>Wyświetlanie listy obiektów blob w kontenerze

Pobierz listę obiektów blob w kontenerze za pomocą polecenia [Get-AzStorageBlob](/powershell/module/az.storage/get-AzStorageblob). W tym przykładzie przedstawiono tylko nazwy przekazanych obiektów blob.

```powershell
Get-AzStorageBlob -Container $ContainerName -Context $ctx | select Name
```

## <a name="download-blobs"></a>Pobieranie obiektów blob

Pobierz obiekty blob na swój dysk twardy. Dla każdego obiektu blob, który chcesz pobrać, określ nazwę i wywołaj polecenie [Get-AzStorageBlobContent](/powershell/module/az.storage/get-AzStorageblobcontent), aby pobrać obiekt blob.

W tym przykładzie pobiera obiekty blob do *D:\\_TestImages\Downloads* na dysku lokalnym. 

```powershell
# download first blob
Get-AzStorageBlobContent -Blob "Image001.jpg" `
  -Container $containerName `
  -Destination "D:\_TestImages\Downloads\" `
  -Context $ctx 

# download another blob
Get-AzStorageBlobContent -Blob "Image002.png" `
  -Container $containerName `
  -Destination "D:\_TestImages\Downloads\" `
  -Context $ctx
```

## <a name="data-transfer-with-azcopy"></a>Transfer danych przy użyciu narzędzia AzCopy

Narzędzie wiersza polecenia AzCopy oferuje wydajny, skryptowy transfer danych dla usługi Azure Storage. Za pomocą programu AzCopy można przesyłać dane do i z magazynu obiektów Blob i usługi Azure Files. Aby uzyskać więcej informacji na temat AzCopy v10, najnowszej wersji programu AzCopy, zobacz [Wprowadzenie do AzCopy](../common/storage-use-azcopy-v10.md). Aby dowiedzieć się więcej na temat korzystania z pamięci AzCopy w wersji 10 z magazynem obiektów Blob, zobacz [Przenoszenie danych za pomocą magazynu AzCopy i Blob](../common/storage-use-azcopy-blobs.md).

W poniższym przykładzie użyto AzCopy do przekazania pliku lokalnego do obiektu blob. Pamiętaj, aby zastąpić przykładowe wartości własnymi wartościami:

```powershell
azcopy login
azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt'
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Usuń wszystkie utworzone zasoby. Najprostszym sposobem usunięcia elementów zawartości jest usunięcie grupy zasobów. Usunięcie grupy zasobów spowoduje także usunięcie wszystkich zasobów uwzględnionych w tej grupie. W poniższym przykładzie usunięcie grupy zasobów powoduje usunięcie konta magazynu i samej grupy zasobów.

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start przeprowadzono transfer plików między dyskiem lokalnym a usługą Azure Blob Storage. Aby dowiedzieć się więcej o pracy z magazynem obiektów blob przy użyciu programu PowerShell, przejdź do artykułu Jak używać programu Azure PowerShell z usługą Azure Storage.

> [!div class="nextstepaction"]
> [Używanie programu Azure PowerShell z usługą Azure Storage](../common/storage-powershell-guide-full.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

### <a name="microsoft-azure-powershell-storage-cmdlets-reference"></a>Dokumentacja poleceń cmdlet programu PowerShell dla usługi Microsoft Azure Storage

* [Polecenia cmdlet programu PowerShell usługi Storage](/powershell/module/az.storage)

### <a name="microsoft-azure-storage-explorer"></a>Microsoft Azure Storage Explorer

* [Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) jest bezpłatną aplikacją autonomiczną oferowaną przez firmę Microsoft, która umożliwia wizualną pracę z danymi w usłudze Azure Storage w systemach Windows, macOS i Linux.
