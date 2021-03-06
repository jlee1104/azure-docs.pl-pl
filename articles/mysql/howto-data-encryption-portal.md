---
title: Szyfrowanie danych — Azure Database for MySQL Azure Portal
description: Dowiedz się, jak skonfigurować szyfrowanie danych i zarządzać nimi Azure Database for MySQL przy użyciu Azure Portal.
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: 78a290b1e2984719645fb4d4ff253ab021a0826e
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79299043"
---
# <a name="data-encryption-for-azure-database-for-mysql-by-using-the-azure-portal"></a>Szyfrowanie danych dla Azure Database for MySQL przy użyciu Azure Portal

Dowiedz się, jak za pomocą Azure Portal skonfigurować szyfrowanie danych dla Azure Database for MySQL i zarządzać nimi.

## <a name="prerequisites-for-azure-cli"></a>Wymagania wstępne dotyczące interfejsu wiersza polecenia platformy Azure

* Musisz mieć subskrypcję platformy Azure i być administratorem tej subskrypcji.
* W Azure Key Vault Utwórz magazyn kluczy i klucz do użycia dla klucza zarządzanego przez klienta.
* Magazyn kluczy musi mieć następujące właściwości, które mają być używane jako klucz zarządzany przez klienta:
  * [Usuwanie nietrwałe](../key-vault/key-vault-ovw-soft-delete.md)

    ```azurecli-interactive
    az resource update --id $(az keyvault show --name \ <key_vault_name> -o tsv | awk '{print $1}') --set \ properties.enableSoftDelete=true
    ```

  * [Przeczyść chronione](../key-vault/key-vault-ovw-soft-delete.md#purge-protection)

    ```azurecli-interactive
    az keyvault update --name <key_vault_name> --resource-group <resource_group_name>  --enable-purge-protection true
    ```

* Klucz musi mieć następujące atrybuty do użycia jako klucz zarządzany przez klienta:
  * Brak daty wygaśnięcia
  * Niewyłączone
  * Możliwość wykonania operacji get, zawijania klucza, odpakowania klucza

## <a name="set-the-right-permissions-for-key-operations"></a>Ustaw odpowiednie uprawnienia dla operacji Key

1. W Key Vault wybierz pozycję **zasady dostępu** > **Dodaj zasady dostępu**.

   ![Zrzut ekranu przedstawiający Key Vault, z zasadami dostępu i wyróżnionymi zasadami dostępu](media/concepts-data-access-and-security-data-encryption/show-access-policy-overview.png)

2. Wybierz pozycję **uprawnienia**, a następnie wybierz pozycję **Pobierz**, **Zawijaj**, **Odpakuj**oraz **podmiot zabezpieczeń**, który jest nazwą serwera MySQL. Jeśli nie można znaleźć podmiotu zabezpieczeń serwera na liście istniejących podmiotów zabezpieczeń, należy go zarejestrować. Zostanie wyświetlony monit o zarejestrowanie podmiotu zabezpieczeń serwera podczas próby skonfigurowania szyfrowania danych po raz pierwszy i niepowodzenie.

   ![Przegląd zasad dostępu](media/concepts-data-access-and-security-data-encryption/access-policy-wrap-unwrap.png)

3. Wybierz pozycję **Zapisz**.

## <a name="set-data-encryption-for-azure-database-for-mysql"></a>Ustaw szyfrowanie danych dla Azure Database for MySQL

1. W obszarze Azure Database for MySQL wybierz pozycję **szyfrowanie danych** , aby skonfigurować klucz zarządzany przez klienta.

   ![Zrzut ekranu przedstawiający Azure Database for MySQL z wyróżnionym szyfrowaniem danych](media/concepts-data-access-and-security-data-encryption/data-encryption-overview.png)

2. Można wybrać Magazyn kluczy i parę kluczy lub wprowadzić identyfikator klucza.

   ![Zrzut ekranu przedstawiający Azure Database for MySQL, z wyróżnionymi opcjami szyfrowania danych](media/concepts-data-access-and-security-data-encryption/setting-data-encryption.png)

3. Wybierz pozycję **Zapisz**.

4. Aby upewnić się, że wszystkie pliki (w tym pliki tymczasowe) są całkowicie zaszyfrowane, należy ponownie uruchomić serwer.

## <a name="restore-or-create-a-replica-of-the-server"></a>Przywróć lub Utwórz replikę serwera

Gdy Azure Database for MySQL jest szyfrowany przy użyciu klucza zarządzanego przez klienta przechowywanego w Key Vault, nowo utworzona kopia serwera zostanie również zaszyfrowana. Tę nową kopię można wykonać za pomocą operacji w trybie lokalnym lub z możliwością przywracania geograficznego albo za pomocą operacji repliki (lokalnej/obejmującej wiele regionów). W przypadku szyfrowanego serwera MySQL można wykonać następujące czynności, aby utworzyć zaszyfrowany przywrócony serwer.

1. Na serwerze wybierz pozycję **przegląd** > **Przywróć**.

   ![Zrzut ekranu przedstawiający Azure Database for MySQL, z wyróżnioną funkcją przegląd i przywracanie](media/concepts-data-access-and-security-data-encryption/show-restore.png)

   Lub dla serwera z włączoną replikacją, w obszarze **Ustawienia** wybierz pozycję **replikacja**.

   ![Zrzut ekranu przedstawiający Azure Database for MySQL, z wyróżnioną replikacją](media/concepts-data-access-and-security-data-encryption/mysql-replica.png)

2. Po zakończeniu operacji przywracania tworzony nowy serwer jest szyfrowany przy użyciu klucza serwera podstawowego. Jednak funkcje i opcje na serwerze są wyłączone, a serwer jest niedostępny. Zapobiega to manipulowaniu danymi, ponieważ dla nowej tożsamości serwera nie udzielono jeszcze uprawnienia dostępu do magazynu kluczy.

   ![Zrzut ekranu przedstawiający Azure Database for MySQL z wyróżnionym stanem niedostępnym](media/concepts-data-access-and-security-data-encryption/show-restore-data-encryption.png)

3. Aby zapewnić dostęp do serwera, ponownie sprawdź poprawność klucza na przywróconym serwerze. Wybierz pozycję **szyfrowanie danych** > ponownie **Zweryfikuj klucz**.

   > [!NOTE]
   > Pierwsza próba ponownego zweryfikowania zakończy się niepowodzeniem, ponieważ nazwa główna usługi nowego serwera musi mieć dostęp do magazynu kluczy. Aby wygenerować jednostkę usługi, wybierz pozycję **Sprawdź ponownie klucz**, co spowoduje wyświetlenie błędu, ale wygeneruje nazwę główną usługi. Następnie zapoznaj się z [tymi krokami](#set-the-right-permissions-for-key-operations) wcześniej w tym artykule.

   ![Zrzut ekranu przedstawiający Azure Database for MySQL, z wyróżnionym krokiem ponownej walidacji](media/concepts-data-access-and-security-data-encryption/show-revalidate-data-encryption.png)

   Musisz nadać magazynowi kluczy dostęp do nowego serwera.

4. Po zarejestrowaniu nazwy głównej usługi należy ponownie sprawdzić poprawność klucza, a serwer wznawia jego normalne działanie.

   ![Zrzut ekranu przedstawiający Azure Database for MySQL, pokazujący przywrócone funkcje](media/concepts-data-access-and-security-data-encryption/restore-successful.png)


## <a name="using-an-azure-resource-manager-template-to-enable-data-encryption"></a>Używanie szablonu Azure Resource Manager do włączania szyfrowania danych

Oprócz Azure Portal można również włączyć szyfrowanie danych na serwerze Azure Database for MySQL przy użyciu szablonów Azure Resource Manager dla nowych i istniejących serwerów.

### <a name="for-a-new-server"></a>Dla nowego serwera

Użyj jednego z wstępnie utworzonych szablonów Azure Resource Manager, aby zainicjować obsługę serwera z włączonym szyfrowaniem danych: [przykład z szyfrowaniem danych](https://github.com/Azure/azure-mysql/tree/master/arm-templates/ExampleWithDataEncryption)

Ten Azure Resource Manager szablonu służy do tworzenia serwera Azure Database for MySQL i korzystania z **magazynu** **kluczy oraz klucza** , który został przesłany jako parametry, aby umożliwić szyfrowanie danych na serwerze.

### <a name="for-an-existing-server"></a>Dla istniejącego serwera
Ponadto można użyć szablonów Azure Resource Manager, aby włączyć szyfrowanie danych na istniejących serwerach Azure Database for MySQL.

* Przekaż identyfikator URI klucza Azure Key Vault, który został wcześniej skopiowany we właściwości `keyVaultKeyUri` w obiekcie Properties.

* Użyj *2020-01-01-Preview* jako wersji interfejsu API.

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string"
    },
    "serverName": {
      "type": "string"
    },
    "keyVaultName": {
      "type": "string",
      "metadata": {
        "description": "Key vault name where the key to use is stored"
      }
    },
    "keyVaultResourceGroupName": {
      "type": "string",
      "metadata": {
        "description": "Key vault resource group name where it is stored"
      }
    },
    "keyName": {
      "type": "string",
      "metadata": {
        "description": "Key name in the key vault to use as encryption protector"
      }
    },
    "keyVersion": {
      "type": "string",
      "metadata": {
        "description": "Version of the key in the key vault to use as encryption protector"
      }
    }
  },
  "variables": {
    "serverKeyName": "[concat(parameters('keyVaultName'), '_', parameters('keyName'), '_', parameters('keyVersion'))]"
  },
  "resources": [
    {
      "type": "Microsoft.DBforMySQL/servers",
      "apiVersion": "2017-12-01",
      "kind": "",
      "location": "[parameters('location')]",
      "identity": {
        "type": "SystemAssigned"
      },
      "name": "[parameters('serverName')]",
      "properties": {
      }
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2019-05-01",
      "name": "addAccessPolicy",
      "resourceGroup": "[parameters('keyVaultResourceGroupName')]",
      "dependsOn": [
        "[resourceId('Microsoft.DBforMySQL/servers', parameters('serverName'))]"
      ],
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "resources": [
            {
              "type": "Microsoft.KeyVault/vaults/accessPolicies",
              "name": "[concat(parameters('keyVaultName'), '/add')]",
              "apiVersion": "2018-02-14-preview",
              "properties": {
                "accessPolicies": [
                  {
                    "tenantId": "[subscription().tenantId]",
                    "objectId": "[reference(resourceId('Microsoft.DBforMySQL/servers/', parameters('serverName')), '2017-12-01', 'Full').identity.principalId]",
                    "permissions": {
                      "keys": [
                        "get",
                        "wrapKey",
                        "unwrapKey"
                      ]
                    }
                  }
                ]
              }
            }
          ]
        }
      }
    },
    {
      "name": "[concat(parameters('serverName'), '/', variables('serverKeyName'))]",
      "type": "Microsoft.DBforMySQL/servers/keys",
      "apiVersion": "2020-01-01-preview",
      "dependsOn": [
        "addAccessPolicy",
        "[resourceId('Microsoft.DBforMySQL/servers', parameters('serverName'))]"
      ],
      "properties": {
        "serverKeyType": "AzureKeyVault",
        "uri": "[concat(reference(resourceId(parameters('keyVaultResourceGroupName'), 'Microsoft.KeyVault/vaults/', parameters('keyVaultName')), '2018-02-14-preview', 'Full').properties.vaultUri, 'keys/', parameters('keyName'), '/', parameters('keyVersion'))]"
      }
    }
  ]
}

```

## <a name="next-steps"></a>Następne kroki

 Aby dowiedzieć się więcej na temat szyfrowania danych, zobacz [Azure Database for MySQL szyfrowanie danych za pomocą klucza zarządzanego przez klienta](concepts-data-encryption-mysql.md).
