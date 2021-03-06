---
title: Zarządzanie kluczami konta magazynu przy użyciu Azure Key Vault i interfejsu wiersza polecenia platformy Azure
description: Klucze konta magazynu zapewniają bezproblemową integrację między Azure Key Vault i dostępem opartym na kluczach do konta usługi Azure Storage.
ms.topic: conceptual
services: key-vault
ms.service: key-vault
ms.subservice: secrets
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 09/18/2019
ms.openlocfilehash: 104f3423b07eaa3269ffccc054cd2f779bbdabf8
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/29/2020
ms.locfileid: "78199822"
---
# <a name="manage-storage-account-keys-with-key-vault-and-the-azure-cli"></a>Zarządzanie kluczami konta magazynu przy użyciu Key Vault i interfejsu wiersza polecenia platformy Azure

Konto usługi Azure Storage używa poświadczeń składających się z nazwy konta i klucza. Klucz jest generowany automatycznie i służy jako hasło, a nie jako klucz kryptograficzny. Key Vault zarządza kluczami konta magazynu przez przechowywanie ich jako [Key Vault wpisów tajnych](/azure/key-vault/about-keys-secrets-and-certificates#key-vault-secrets). 

Za pomocą funkcji klucza zarządzanego magazynu Key Vault można wyświetlać (synchronizować) klucze za pomocą konta usługi Azure Storage i ponownie generować (obrócić) klucze okresowo. Można zarządzać kluczami zarówno dla kont magazynu, jak i klasycznych kont magazynu.

Korzystając z funkcji zarządzanego klucza konta magazynu, należy rozważyć następujące kwestie:

- Wartości klucza nigdy nie są zwracane w odpowiedzi na obiekt wywołujący.
- Tylko Key Vault powinny zarządzać kluczami konta magazynu. Nie Zarządzaj kluczami samodzielnie i unikaj zakłócania procesów Key Vault.
- Tylko jeden obiekt Key Vault powinien zarządzać kluczami konta magazynu. Nie Zezwalaj na zarządzanie kluczami z wielu obiektów.
- Możesz zażądać Key Vault, aby zarządzać kontem magazynu za pomocą podmiotu zabezpieczeń użytkownika, ale nie z jednostką usługi.
- Wygeneruj ponownie klucze, używając tylko Key Vault. Nie należy ręcznie generować ponownie kluczy konta magazynu.

Zalecamy korzystanie z integracji usługi Azure Storage z usługą Azure Active Directory (Azure AD), opartą na chmurze firmą Microsoft do zarządzania tożsamościami i dostępem. Integracja z usługą Azure AD jest dostępna dla [obiektów blob i kolejek platformy Azure](../storage/common/storage-auth-aad.md)oraz zapewnia dostęp oparty na tokenach OAuth2 do usługi Azure Storage (podobnie jak Azure Key Vault).

Usługa Azure AD umożliwia uwierzytelnianie aplikacji klienckiej przy użyciu tożsamości aplikacji lub użytkownika, a nie poświadczeń konta magazynu. Możesz użyć [tożsamości zarządzanej usługi Azure AD](/azure/active-directory/managed-identities-azure-resources/) podczas uruchamiania na platformie Azure. Tożsamości zarządzane usuwają potrzebę uwierzytelniania klienta i przechowywania poświadczeń w aplikacji lub w aplikacjach.

Usługa Azure AD używa kontroli dostępu opartej na rolach (RBAC) do zarządzania autoryzacją, która również jest obsługiwana przez Key Vault.

## <a name="service-principal-application-id"></a>Identyfikator aplikacji głównej usługi

Dzierżawa usługi Azure AD udostępnia każdą zarejestrowaną aplikację z jednostką [usługi](/azure/active-directory/develop/developer-glossary#service-principal-object). Jednostka usługi służy jako identyfikator aplikacji, który jest używany podczas konfigurowania autoryzacji w celu uzyskania dostępu do innych zasobów platformy Azure za pośrednictwem RBAC.

Key Vault to aplikacja firmy Microsoft, która jest wstępnie zarejestrowana we wszystkich dzierżawach usługi Azure AD. Key Vault jest zarejestrowany w ramach tego samego identyfikatora aplikacji w każdej chmurze platformy Azure.

| Dzierżawcy | Chmurowa | Identyfikator aplikacji |
| --- | --- | --- |
| Azure AD | Azure Government | `7e7c393b-45d0-48b1-a35e-2905ddf8183c` |
| Azure AD | Usługa Azure Public | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |
| Inne  | Dowolne | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik, należy najpierw wykonać następujące czynności:

- [Zainstaluj interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).
- [Tworzenie magazynu kluczy](quick-create-cli.md)
- [Utwórz konto usługi Azure Storage](../storage/common/storage-account-create.md?tabs=azure-cli). Nazwa konta magazynu musi zawierać tylko małe litery i cyfry. Długość nazwy musi wynosić od 3 do 24 znaków.
      
## <a name="manage-storage-account-keys"></a>Zarządzanie kluczami konta magazynu

### <a name="connect-to-your-azure-account"></a>Nawiąż połączenie z kontem platformy Azure

Uwierzytelnij sesję interfejsu wiersza polecenia platformy Azure za pomocą polecenia [AZ login](/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) .

```azurecli-interactive
az login
``` 

### <a name="give-key-vault-access-to-your-storage-account"></a>Przyznaj Key Vault dostęp do konta magazynu

Użyj interfejsu wiersza polecenia platformy Azure [AZ role Create](/cli/azure/role/assignment?view=azure-cli-latest) , aby nadać Key Vault dostęp do konta magazynu. Podaj następujące wartości parametrów polecenia:

- `--role`: Przekaż rolę RBAC roli usługi operatora kluczy konta magazynu. Ta rola ogranicza zakres dostępu do konta magazynu. W przypadku klasycznego konta magazynu należy zamiast tego przekazać "rolę usługi operatora klucza klasycznego konta magazynu".
- `--assignee-object-id`: przekaż wartość "93c27d83-f79b-4cb2-8dd4-4aa716542e74", która jest IDENTYFIKATORem obiektu dla Key Vault w chmurze publicznej platformy Azure. (Aby uzyskać identyfikator obiektu Key Vault w chmurze Azure Government, zobacz [Identyfikator aplikacji nazwy głównej usługi](#service-principal-application-id)).
- `--scope`: Przekaż identyfikator zasobu konta magazynu, który znajduje się w `/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>`. Aby znaleźć identyfikator subskrypcji, użyj polecenia [AZ Account List](/cli/azure/account?view=azure-cli-latest#az-account-list) usługi Azure CLI, Aby znaleźć nazwę konta magazynu i grupę zasobów konta magazynu, użyj polecenia [AZ Storage account list](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-list) usługi Azure CLI.

```azurecli-interactive
az role assignment create --role "Storage Account Key Operator Service Role" --assignee-object-id 93c27d83-f79b-4cb2-8dd4-4aa716542e74 --scope "/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>"
 ```

### <a name="create-a-key-vault-managed-storage-account"></a>Tworzenie konta magazynu zarządzanego Key Vault

 Utwórz konto magazynu zarządzanego przez usługę Key Vault przy użyciu interfejsu wiersza polecenia platformy Azure AZ datamagazyn [Storage](/cli/azure/keyvault/storage?view=azure-cli-latest#az-keyvault-storage-add) polecenie. Ustaw okres regeneracji wynoszący 90 dni. Po 90 dniach Key Vault regeneruje `key1` i zamienia klucz aktywny z `key2` na `key1`. `key1` jest następnie oznaczany jako klucz aktywny. Podaj następujące wartości parametrów polecenia:

- `--vault-name`: przekaż nazwę magazynu kluczy. Aby znaleźć nazwę magazynu kluczy, użyj polecenia [AZ Key list](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-list) .
- `-n`: przekaż nazwę konta magazynu. Aby znaleźć nazwę konta magazynu, użyj polecenia [AZ Storage account list](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-list) usługi Azure CLI.
- `--resource-id`: Przekaż identyfikator zasobu konta magazynu, który znajduje się w `/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>`. Aby znaleźć identyfikator subskrypcji, użyj polecenia [AZ Account List](/cli/azure/account?view=azure-cli-latest#az-account-list) usługi Azure CLI, Aby znaleźć nazwę konta magazynu i grupę zasobów konta magazynu, użyj polecenia [AZ Storage account list](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-list) usługi Azure CLI.
   
 ```azurecli-interactive
az keyvault storage add --vault-name <YourKeyVaultName> -n <YourStorageAccountName> --active-key-name key1 --auto-regenerate-key --regeneration-period P90D --resource-id "/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>"
 ```

## <a name="shared-access-signature-tokens"></a>Tokeny sygnatury dostępu współdzielonego

Możesz również poproszenie Key Vault o generowanie tokenów sygnatury dostępu współdzielonego. Sygnatury dostępu współdzielonego zapewnia delegowany dostęp do zasobów na koncie magazynu. Możesz udzielić klientom dostępu do zasobów na koncie magazynu bez udostępniania kluczy konta. Sygnatura dostępu współdzielonego zapewnia bezpieczny sposób udostępniania zasobów magazynu bez naruszania kluczy konta.

Polecenia w tej sekcji pełnią następujące czynności:

- Ustaw definicję sygnatury dostępu współdzielonego dla konta `<YourSASDefinitionName>`. Definicja jest ustawiana na Key Vault zarządzanym koncie magazynu `<YourStorageAccountName>` w `<YourKeyVaultName>`magazynie kluczy.
- Utwórz token sygnatury dostępu współdzielonego konta dla usług obiektów blob, plików, tabel i kolejek. Token jest tworzony dla usług, kontenerów i obiektów typu zasób. Token jest tworzony ze wszystkimi uprawnieniami, za pośrednictwem protokołu HTTPS i z określonymi datami rozpoczęcia i zakończenia.
- Ustaw Key Vault zarządzaną definicję sygnatury dostępu współdzielonego magazynu w magazynie. Definicja zawiera identyfikator URI szablonu utworzonego tokenu sygnatury dostępu współdzielonego. Definicja ma typ sygnatury dostępu współdzielonego `account` i jest ważna przez N dni.
- Sprawdź, czy sygnatura dostępu współdzielonego została zapisana w magazynie kluczy jako wpis tajny.

### <a name="create-a-shared-access-signature-token"></a>Tworzenie tokenu sygnatury dostępu współdzielonego

Utwórz definicję sygnatury dostępu współdzielonego przy użyciu interfejsu wiersza polecenia platformy Azure [AZ Storage account Generate-SAS](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-generate-sas) . Ta operacja wymaga uprawnień `storage` i `setsas`.


```azurecli-interactive
az storage account generate-sas --expiry 2020-01-01 --permissions rw --resource-types sco --services bfqt --https-only --account-name <YourStorageAccountName> --account-key 00000000
```
Po pomyślnym uruchomieniu operacji skopiuj dane wyjściowe.

```console
"se=2020-01-01&sp=***"
```

Te dane wyjściowe będą przekazywane do parametru `--template-id` w następnym kroku.

### <a name="generate-a-shared-access-signature-definition"></a>Generowanie definicji sygnatury dostępu współdzielonego

Aby utworzyć definicję sygnatury dostępu współdzielonego, należy użyć interfejsu wiersza polecenia platformy Azure [AZ typu Storage SAS-Definition Create](/cli/azure/keyvault/storage/sas-definition?view=azure-cli-latest#az-keyvault-storage-sas-definition-create) , przekazując dane wyjściowe z poprzedniego kroku do parametru `--template-id`.  Możesz podać wybraną nazwę parametru `-n`.

```azurecli-interactive
az keyvault storage sas-definition create --vault-name <YourKeyVaultName> --account-name <YourStorageAccountName> -n <YourSASDefinitionName> --validity-period P2D --sas-type account --template-uri <OutputOfSasTokenCreationStep>
```

### <a name="verify-the-shared-access-signature-definition"></a>Weryfikowanie definicji sygnatury dostępu współdzielonego

Możesz sprawdzić, czy definicja sygnatury dostępu współdzielonego została zapisana w magazynie kluczy za pomocą interfejsu wiersza polecenia platformy Azure [AZ Key Secret list](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-list) i [AZ Key magazynu Secret show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) Commands.

Najpierw Znajdź definicję sygnatury dostępu współdzielonego w magazynie kluczy przy użyciu polecenia [AZ Key magazynu Secret list](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-list) .

```azurecli-interactive
az keyvault secret list --vault-name <YourKeyVaultName>
```

Wpis tajny odpowiadający definicji sygnatury dostępu współdzielonego będzie miał następujące właściwości:

```console
    "contentType": "application/vnd.ms-sastoken-storage",
    "id": "https://<YourKeyVaultName>.vault.azure.net/secrets/<YourStorageAccountName>-<YourSASDefinitionName>",
```

Teraz możesz użyć polecenia [AZ typu Secret show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) , a właściwości `id`, aby wyświetlić zawartość tego klucza tajnego.

```azurecli-interactive
az keyvault secret show --vault-name <YourKeyVaultName> --id <SasDefinitionID>
```

Dane wyjściowe tego polecenia będą pokazywały ciąg definicji sygnatury dostępu współdzielonego jako`value`.


## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [kluczach, wpisach tajnych i certyfikatach](https://docs.microsoft.com/rest/api/keyvault/).
- Zapoznaj się z artykułami na [blogu zespołu Azure Key Vault](https://blogs.technet.microsoft.com/kv/).
- Zapoznaj się z dokumentacją [AZ magazynu Storage](https://docs.microsoft.com/cli/azure/keyvault/storage?view=azure-cli-latest) Reference.
