---
title: 'Python: Operacje systemu plików w usłudze Azure Data Lake Storage Gen1 | Dokumenty firmy Microsoft'
description: Dowiedz się, jak używać zestawu SDK języka Python do pracy z systemem plików Data Lake Storage Gen1.
services: data-lake-store
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 17b53c508f0c4c5ba8fa257fcdc692cdaa45d470
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76294222"
---
# <a name="filesystem-operations-on-azure-data-lake-storage-gen1-using-python"></a>Operacje systemu plików w usłudze Azure Data Lake Storage Gen1 przy użyciu języka Python
> [!div class="op_single_selector"]
> * [Zestaw SDK platformy .NET](data-lake-store-data-operations-net-sdk.md)
> * [Java SDK](data-lake-store-get-started-java-sdk.md)
> * [INTERFEJS API ODPOCZYNKU](data-lake-store-data-operations-rest-api.md)
> * [Python](data-lake-store-data-operations-python.md)
>
> 

W tym artykule dowiesz się, jak używać zestawu SDK języka Python do wykonywania operacji systemu plików w usłudze Azure Data Lake Storage Gen1. Aby uzyskać instrukcje dotyczące wykonywania operacji zarządzania kontem w programie Data Lake Storage Gen1 przy użyciu języka Python, zobacz [Operacje zarządzania kontami w programie Data Lake Storage Gen1 przy użyciu języka Python](data-lake-store-get-started-python.md).

## <a name="prerequisites"></a>Wymagania wstępne

* **Python**. Możesz pobrać środowisko Python [tutaj](https://www.python.org/downloads/). W tym artykule używany jest język Python 3.6.2.

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Konto usługi Azure Data Lake Storage Gen1**. Postępuj zgodnie z instrukcjami w [usłudze Wprowadzenie do usługi Azure Data Lake Storage Gen1 przy użyciu witryny Azure portal.](data-lake-store-get-started-portal.md)

## <a name="install-the-modules"></a>Instalacja modułów

Aby pracować z magazynem danych Data Lake Gen1 przy użyciu języka Python, należy zainstalować trzy moduły.

* Moduł `azure-mgmt-resource`, który obejmuje moduły platformy Azure dla usługi Active Directory itp.
* Moduł, `azure-mgmt-datalake-store` który obejmuje operacje zarządzania kontem usługi Azure Data Lake Storage Gen1. Aby uzyskać więcej informacji na temat tego modułu, zobacz [odwołanie do modułu azure-mgmt-datalake-store](/python/api/azure-mgmt-datalake-store/).
* Moduł, `azure-datalake-store` który obejmuje operacje systemu plików Usługi Azure Data Lake Storage Gen1. Aby uzyskać więcej informacji na temat tego modułu, zobacz [odwołanie do modułu systemu plików magazynu danych azure](https://docs.microsoft.com/python/api/azure-datalake-store/azure.datalake.store.core/).

Użyj następujących poleceń, aby zainstalować moduły.

```
pip install azure-mgmt-resource
pip install azure-mgmt-datalake-store
pip install azure-datalake-store
```

## <a name="create-a-new-python-application"></a>Tworzenie nowej aplikacji w języku Python

1. W wybranym środowisku IDE utwórz nową aplikację w języku Python, na przykład **mysample.py**.

2. Dodaj następujące wiersze, aby zaimportować wymagane moduły

   ```
   ## Use this only for Azure AD service-to-service authentication
   from azure.common.credentials import ServicePrincipalCredentials

   ## Use this only for Azure AD end-user authentication
   from azure.common.credentials import UserPassCredentials

   ## Use this only for Azure AD multi-factor authentication
   from msrestazure.azure_active_directory import AADTokenCredentials

   ## Required for Azure Data Lake Storage Gen1 account management
   from azure.mgmt.datalake.store import DataLakeStoreAccountManagementClient
   from azure.mgmt.datalake.store.models import DataLakeStoreAccount

   ## Required for Azure Data Lake Storage Gen1 filesystem management
   from azure.datalake.store import core, lib, multithread

   ## Common Azure imports
   from azure.mgmt.resource.resources import ResourceManagementClient
   from azure.mgmt.resource.resources.models import ResourceGroup

   ## Use these as needed for your application
   import logging, getpass, pprint, uuid, time
   ```

3. Zapisz zmiany w aplikacji mysample.py.

## <a name="authentication"></a>Uwierzytelnianie

W tej sekcji omówione zostaną różne sposoby uwierzytelniania w usłudze Azure AD. Dostępne opcje:

* Aby uzyskać uwierzytelnianie użytkowników końcowych dla aplikacji, zobacz [Uwierzytelnianie użytkowników końcowych za pomocą usługi Data Lake Storage Gen1 przy użyciu języka Python](data-lake-store-end-user-authenticate-python.md).
* Aby uzyskać uwierzytelnianie między usługami dla aplikacji, zobacz [Uwierzytelnianie usługi do usługi przy użyciu usługi Lake Storage Gen1 przy użyciu języka Python](data-lake-store-service-to-service-authenticate-python.md).

## <a name="create-filesystem-client"></a>Tworzenie klienta systemu plików

Poniższy fragment kodu najpierw tworzy klienta konta Usługi Data Lake Storage Gen1. Używa obiektu klienta do utworzenia konta Usługi Data Lake Storage Gen1. Na koniec fragment kodu tworzy obiekt klienta systemu plików.

    ## Declare variables
    subscriptionId = 'FILL-IN-HERE'
    adlsAccountName = 'FILL-IN-HERE'

    ## Create a filesystem client object
    adlsFileSystemClient = core.AzureDLFileSystem(adlCreds, store_name=adlsAccountName)

## <a name="create-a-directory"></a>Tworzenie katalogu

    ## Create a directory
    adlsFileSystemClient.mkdir('/mysampledirectory')

## <a name="upload-a-file"></a>Przekazywanie pliku


    ## Upload a file
    multithread.ADLUploader(adlsFileSystemClient, lpath='C:\\data\\mysamplefile.txt', rpath='/mysampledirectory/mysamplefile.txt', nthreads=64, overwrite=True, buffersize=4194304, blocksize=4194304)


## <a name="download-a-file"></a>Pobieranie pliku

    ## Download a file
    multithread.ADLDownloader(adlsFileSystemClient, lpath='C:\\data\\mysamplefile.txt.out', rpath='/mysampledirectory/mysamplefile.txt', nthreads=64, overwrite=True, buffersize=4194304, blocksize=4194304)

## <a name="delete-a-directory"></a>Usuwanie katalogu

    ## Delete a directory
    adlsFileSystemClient.rm('/mysampledirectory', recursive=True)

## <a name="next-steps"></a>Następne kroki
* [Operacje zarządzania kontami w programie Data Lake Storage Gen1 przy użyciu języka Python](data-lake-store-get-started-python.md).

## <a name="see-also"></a>Zobacz też

* [Odwołanie do usługi Azure Data Lake Storage Gen1 Python (Filesystem)](https://docs.microsoft.com/python/api/azure-datalake-store/azure.datalake.store.core)
* [Aplikacje Open Source Big Data zgodne z usługą Azure Data Lake Storage Gen1](data-lake-store-compatible-oss-other-applications.md)
