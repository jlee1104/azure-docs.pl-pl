---
title: Konto magazynu zarządzanego usługi Azure Key Vault — wersja programu PowerShell
description: Funkcja zarządzanego konta magazynu zapewnia bezproblemową integrację między usługą Azure Key Vault a kontem magazynu platformy Azure.
ms.topic: conceptual
ms.service: key-vault
ms.subservice: secrets
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 09/10/2019
ms.openlocfilehash: cbd7bd034c5cbbdf5308ec660a96dc52a9a2b310
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78200706"
---
# <a name="fetch-shared-access-signature-tokens-in-code"></a>Pobieranie tokenów sygnatury dostępu współdzielonego

Możesz zarządzać kontem magazynu za pomocą [tokenów podpisu dostępu współdzielonego](../storage/common/storage-dotnet-shared-access-signature-part-1.md) w magazynie kluczy. W tym artykule przedstawiono przykłady kodu języka C#, który pobiera token sygnatury dostępu Współdzielonego i wykonuje operacje z nim.  Aby uzyskać informacje dotyczące tworzenia i przechowywania tokenów sygnatury dostępu [Współdzielonego, zobacz Zarządzanie kluczami kont magazynu za pomocą usługi Key Vault i interfejsu wiersza polecenia platformy Azure](key-vault-ovw-storage-keys.md) lub [Zarządzanie kluczami kont magazynu za pomocą usługi Key Vault i azure PowerShell](key-vault-overview-storage-keys-powershell.md).

## <a name="code-samples"></a>Przykłady kodu

W tym przykładzie kod pobiera token sygnatury dostępu Współdzielonego z magazynu kluczy, używa go do utworzenia nowego konta magazynu i tworzy nowego klienta usługi obiektów Blob.  

```cs
// After you get a security token, create KeyVaultClient with vault credentials.
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(securityToken));

// Get a shared access signature token for your storage from Key Vault.
// The format for SecretUri is https://<YourKeyVaultName>.vault.azure.net/secrets/<ExamplePassword>
var sasToken = await kv.GetSecretAsync("SecretUri");

// Create new storage credentials by using the shared access signature token.
var accountSasCredential = new StorageCredentials(sasToken.Value);

// Use the storage credentials and the Blob storage endpoint to create a new Blob service client.
var accountWithSas = new CloudStorageAccount(accountSasCredential, new Uri ("https://myaccount.blob.core.windows.net/"), null, null, null);

var blobClientWithSas = accountWithSas.CreateCloudBlobClient();
```

Jeśli token podpisu dostępu współdzielonego wkrótce wygaśnie, możesz pobrać token podpisu dostępu udostępnionego z magazynu kluczy i zaktualizować kod.

```cs
// If your shared access signature token is about to expire,
// get the shared access signature token again from Key Vault and update it.
sasToken = await kv.GetSecretAsync("SecretUri");
accountSasCredential.UpdateSASToken(sasToken);
```


## <a name="next-steps"></a>Następne kroki
- Dowiedz się, jak [zarządzać kluczami kont magazynu za pomocą usługi Key Vault i interfejsu wiersza polecenia platformy Azure](key-vault-ovw-storage-keys.md) lub programu Azure [PowerShell](key-vault-overview-storage-keys-powershell.md).
- Zobacz [przykłady kluczy kont zarządzanego magazynu](https://github.com/Azure-Samples?utf8=%E2%9C%93&q=key+vault+storage&type=&language=)
- [Informacje o kluczach, wpisach tajnych i certyfikatach](about-keys-secrets-and-certificates.md)
- [Odwołanie do programu Key Vault PowerShell](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault)
