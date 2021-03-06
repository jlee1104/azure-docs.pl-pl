---
title: Tożsamości zarządzane
description: Dowiedz się, jak działają tożsamości zarządzane w usłudze Azure App Service i usłudze Azure Functions, jak skonfigurować tożsamość zarządzaną i wygenerować token dla zasobu zaplecza.
author: mattchenderson
ms.topic: article
ms.date: 03/04/2020
ms.author: mahender
ms.reviewer: yevbronsh
ms.openlocfilehash: 6e3169f2bfcba0a02af1490f875cbab8a14d02f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280029"
---
# <a name="how-to-use-managed-identities-for-app-service-and-azure-functions"></a>Jak używać tożsamości zarządzanych dla usługi App Service i usługi Azure Functions

> [!Important] 
> Tożsamości zarządzane dla usługi App Service i usługi Azure Functions nie będą zachowywać się zgodnie z oczekiwaniami, jeśli aplikacja jest migrowana między subskrypcjami/dzierżawcami. Aplikacja będzie musiała uzyskać nową tożsamość, co można zrobić, wyłączając i ponownie włączając tę funkcję. Zobacz [Usuwanie tożsamości](#remove) poniżej. Zasoby podrzędne będą również musiały zaktualizować zasady dostępu, aby używać nowej tożsamości.

W tym temacie pokazano, jak utworzyć tożsamość zarządzaną dla aplikacji usługi App Service i usługi Azure Functions oraz jak jej używać do uzyskiwania dostępu do innych zasobów. Tożsamość zarządzana z usługi Azure Active Directory (AAD) umożliwia aplikacji łatwy dostęp do innych zasobów chronionych przez usługę AAD, takich jak usługa Azure Key Vault. Tożsamość jest zarządzana przez platformę Azure i nie wymaga aprowidizacji ani obracania żadnych wpisów tajnych. Aby uzyskać więcej informacji o tożsamościach zarządzanych w usłudze AAD, zobacz [Tożsamości zarządzane dla zasobów platformy Azure.](../active-directory/managed-identities-azure-resources/overview.md)

Wniosek może otrzymać dwa typy tożsamości: 
- **Tożsamość przypisana systemowi** jest powiązana z aplikacją i jest usuwana, jeśli aplikacja zostanie usunięta. Aplikacja może mieć tylko jedną tożsamość przypisaną do systemu.
- **Tożsamość przypisana przez użytkownika** to autonomiczny zasób platformy Azure, który można przypisać do aplikacji. Aplikacja może mieć wiele tożsamości przypisanych przez użytkownika.

## <a name="add-a-system-assigned-identity"></a>Dodawanie tożsamości przypisanej do systemu

Tworzenie aplikacji z tożsamością przypisaną do systemu wymaga ustawienia dodatkowej właściwości w aplikacji.

### <a name="using-the-azure-portal"></a>Korzystanie z witryny Azure Portal

Aby skonfigurować tożsamość zarządzaną w portalu, musisz najpierw utworzyć aplikację w zwykły sposób, a następnie włączyć tę funkcję.

1. Utwórz aplikację w portalu w zwykły sposób. Przejdź do niej w portalu.

2. Jeśli korzystasz z aplikacji funkcyjnej, przejdź do **pozycji Funkcje platformy**. W przypadku innych typów aplikacji przewiń w dół do grupy **Ustawienia** w lewej nawigacji.

3. Wybierz **opcję Tożsamość**.

4. Na karcie **Przypisany** system **przełącz** stan **na Włączone**. Kliknij przycisk **Zapisz**.

    ![Tożsamość zarządzana w usłudze aplikacji](media/app-service-managed-service-identity/system-assigned-managed-identity-in-azure-portal.png)

### <a name="using-the-azure-cli"></a>Korzystanie z interfejsu wiersza polecenia platformy Azure

Aby skonfigurować tożsamość zarządzaną przy użyciu interfejsu `az webapp identity assign` wiersza polecenia platformy Azure, należy użyć polecenia względem istniejącej aplikacji. W tej sekcji dostępne są trzy opcje uruchamiania przykładów:

- Użyj [usługi Azure Cloud Shell](../cloud-shell/overview.md) z witryny Azure portal.
- Użyj osadzonej usługi Azure Cloud Shell za pomocą przycisku "Wypróbuj", znajdującego się w prawym górnym rogu każdego bloku kodu poniżej.
- [Zainstaluj najnowszą wersję interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.31 lub nowszego), jeśli wolisz używać lokalnej konsoli interfejsu wiersza polecenia. 

Poniższe kroki przebiegają przez tworzenie aplikacji sieci web i przypisywanie jej tożsamości przy użyciu interfejsu wiersza polecenia:

1. Jeśli używasz interfejsu wiersza polecenia platformy Azure w konsoli lokalnej, najpierw zaloguj się do platformy Azure za pomocą polecenia [az login](/cli/azure/reference-index#az-login). Użyj konta skojarzonego z subskrypcją platformy Azure, w ramach której chcesz wdrożyć aplikację:

    ```azurecli-interactive
    az login
    ```
2. Utwórz aplikację internetową przy użyciu interfejsu wiersza polecenia. Aby uzyskać więcej przykładów używania interfejsu wiersza polecenia z usługą App Service, zobacz [przykłady interfejsu wiersza polecenia usługi app service:](../app-service/samples-cli.md)

    ```azurecli-interactive
    az group create --name myResourceGroup --location westus
    az appservice plan create --name myPlan --resource-group myResourceGroup --sku S1
    az webapp create --name myApp --resource-group myResourceGroup --plan myPlan
    ```

3. Uruchom `identity assign` polecenie, aby utworzyć tożsamość dla tej aplikacji:

    ```azurecli-interactive
    az webapp identity assign --name myApp --resource-group myResourceGroup
    ```

### <a name="using-azure-powershell"></a>Korzystanie z programu Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Poniższe kroki przebiegają przez tworzenie aplikacji sieci web i przypisywanie jej tożsamości przy użyciu programu Azure PowerShell:

1. W razie potrzeby zainstaluj program Azure PowerShell, korzystając z instrukcji znalezionych `Login-AzAccount` w [przewodniku programu Azure PowerShell,](/powershell/azure/overview)a następnie uruchom w celu utworzenia połączenia z platformą Azure.

2. Tworzenie aplikacji sieci web przy użyciu programu Azure PowerShell. Aby uzyskać więcej przykładów używania programu Azure PowerShell z usługą App Service, zobacz [przykłady usługi App Service PowerShell:](../app-service/samples-powershell.md)

    ```azurepowershell-interactive
    # Create a resource group.
    New-AzResourceGroup -Name myResourceGroup -Location $location
    
    # Create an App Service plan in Free tier.
    New-AzAppServicePlan -Name $webappname -Location $location -ResourceGroupName myResourceGroup -Tier Free
    
    # Create a web app.
    New-AzWebApp -Name $webappname -Location $location -AppServicePlan $webappname -ResourceGroupName myResourceGroup
    ```

3. Uruchom `Set-AzWebApp -AssignIdentity` polecenie, aby utworzyć tożsamość dla tej aplikacji:

    ```azurepowershell-interactive
    Set-AzWebApp -AssignIdentity $true -Name $webappname -ResourceGroupName myResourceGroup 
    ```

### <a name="using-an-azure-resource-manager-template"></a>Korzystanie z szablonu usługi Azure Resource Manager

Szablon usługi Azure Resource Manager może służyć do automatyzacji wdrażania zasobów platformy Azure. Aby dowiedzieć się więcej na temat wdrażania w usłudze i funkcjach aplikacji, zobacz [Automatyzacja wdrażania zasobów w usłudze App Service](../app-service/deploy-complex-application-predictably.md) i [automatyzacja wdrażania zasobów w usłudze Azure Functions](../azure-functions/functions-infrastructure-as-code.md).

Każdy zasób typu `Microsoft.Web/sites` można utworzyć za pomocą tożsamości, dołączając następującą właściwość do definicji zasobu:
```json
"identity": {
    "type": "SystemAssigned"
}    
```

> [!NOTE] 
> Aplikacja może mieć zarówno tożsamości przypisane do systemu, jak i przypisane przez użytkownika w tym samym czasie. W takim przypadku `type` nieruchomość zostanie`SystemAssigned,UserAssigned`

Dodanie typu przypisanego do systemu nakazuje platformie Azure tworzenie tożsamości aplikacji i zarządzanie nią.

Na przykład aplikacja sieci web może wyglądać następująco:
```json
{
    "apiVersion": "2016-08-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('appName')]",
    "location": "[resourceGroup().location]",
    "identity": {
        "type": "SystemAssigned"
    },
    "properties": {
        "name": "[variables('appName')]",
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "hostingEnvironment": "",
        "clientAffinityEnabled": false,
        "alwaysOn": true
    },
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]"
    ]
}
```

Po utworzeniu witryny ma następujące dodatkowe właściwości:
```json
"identity": {
    "type": "SystemAssigned",
    "tenantId": "<TENANTID>",
    "principalId": "<PRINCIPALID>"
}
```

Właściwość tenantId identyfikuje dzierżawę usługi AAD, do której należy tożsamość. Identyfikator główny jest unikatowym identyfikatorem nowej tożsamości aplikacji. W ramach usługi AAD podmiotu zabezpieczeń usługi ma taką samą nazwę, która została nadana do usługi app service lub wystąpienia usługi Azure Functions.


## <a name="add-a-user-assigned-identity"></a>Dodawanie tożsamości przypisanej przez użytkownika

Tworzenie aplikacji z tożsamością przypisaną przez użytkownika wymaga utworzenia tożsamości, a następnie dodania jej identyfikatora zasobów do konfiguracji aplikacji.

### <a name="using-the-azure-portal"></a>Korzystanie z witryny Azure Portal

Najpierw musisz utworzyć zasób tożsamości przypisany przez użytkownika.

1. Utwórz zasób tożsamości zarządzanej przypisany przez użytkownika zgodnie z [tymi instrukcjami](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity).

2. Utwórz aplikację w portalu w zwykły sposób. Przejdź do niej w portalu.

3. Jeśli korzystasz z aplikacji funkcyjnej, przejdź do **pozycji Funkcje platformy**. W przypadku innych typów aplikacji przewiń w dół do grupy **Ustawienia** w lewej nawigacji.

4. Wybierz **opcję Tożsamość**.

5. Na karcie **Przypisany użytkownik** kliknij pozycję **Dodaj**.

6. Wyszukaj wcześniej utworzoną tożsamość i wybierz ją. Kliknij przycisk **Dodaj**.

    ![Tożsamość zarządzana w usłudze aplikacji](media/app-service-managed-service-identity/user-assigned-managed-identity-in-azure-portal.png)

### <a name="using-an-azure-resource-manager-template"></a>Korzystanie z szablonu usługi Azure Resource Manager

Szablon usługi Azure Resource Manager może służyć do automatyzacji wdrażania zasobów platformy Azure. Aby dowiedzieć się więcej na temat wdrażania w usłudze i funkcjach aplikacji, zobacz [Automatyzacja wdrażania zasobów w usłudze App Service](../app-service/deploy-complex-application-predictably.md) i [automatyzacja wdrażania zasobów w usłudze Azure Functions](../azure-functions/functions-infrastructure-as-code.md).

Każdy zasób typu `Microsoft.Web/sites` można utworzyć z tożsamością, dołączając `<RESOURCEID>` następujący blok w definicji zasobu, zastępując identyfikator zasobu żądanej tożsamości:
```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "<RESOURCEID>": {}
    }
}    
```

> [!NOTE] 
> Aplikacja może mieć zarówno tożsamości przypisane do systemu, jak i przypisane przez użytkownika w tym samym czasie. W takim przypadku `type` nieruchomość zostanie`SystemAssigned,UserAssigned`

Dodanie typu przypisanego przez użytkownika nakazuje platformie Azure użycie tożsamości przypisanej przez użytkownika określonej dla aplikacji.

Na przykład aplikacja sieci web może wyglądać następująco:
```json
{
    "apiVersion": "2016-08-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('appName')]",
    "location": "[resourceGroup().location]",
    "identity": {
        "type": "UserAssigned",
        "userAssignedIdentities": {
            "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName'))]": {}
        }
    },
    "properties": {
        "name": "[variables('appName')]",
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "hostingEnvironment": "",
        "clientAffinityEnabled": false,
        "alwaysOn": true
    },
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName'))]"
    ]
}
```

Po utworzeniu witryny ma następujące dodatkowe właściwości:
```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "<RESOURCEID>": {
            "principalId": "<PRINCIPALID>",
            "clientId": "<CLIENTID>"
        }
    }
}
```

Identyfikator dublownika jest unikatowym identyfikatorem tożsamości używanej do administrowania usługi AAD. Identyfikator klienta jest unikatowym identyfikatorem nowej tożsamości aplikacji, który jest używany do określania tożsamości, której należy użyć podczas wywołań środowiska wykonawczego.


## <a name="obtain-tokens-for-azure-resources"></a>Uzyskiwanie tokenów dla zasobów platformy Azure

Aplikacja może używać swojej tożsamości zarządzanej, aby uzyskać tokeny, aby uzyskać dostęp do innych zasobów chronionych przez usługę AAD, takich jak Usługa Azure Key Vault. Tokeny te reprezentują aplikację uzyskującą dostęp do zasobu, a nie dowolnego konkretnego użytkownika aplikacji. 

Może być konieczne skonfigurowanie zasobu docelowego, aby zezwolić na dostęp z aplikacji. Na przykład jeśli zażądasz tokenu, aby uzyskać dostęp do usługi Key Vault, należy upewnić się, że dodano zasady dostępu, który zawiera tożsamość aplikacji. W przeciwnym razie wywołania usługi Key Vault zostaną odrzucone, nawet jeśli zawierają token. Aby dowiedzieć się więcej o zasobach obsługujących tokeny usługi Azure Active Directory, zobacz [Usługi platformy Azure obsługujące uwierzytelnianie usługi Azure AD.](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)

> [!IMPORTANT]
> Usługi zaplecza dla tożsamości zarządzanych utrzymują pamięć podręczną na identyfikator URI zasobu przez około 8 godzin. Jeśli zaktualizujesz zasady dostępu określonego zasobu docelowego i natychmiast pobierzesz token dla tego zasobu, możesz nadal pobierać token buforowany z nieaktuanymi uprawnieniami, dopóki ten token nie wygaśnie. Obecnie nie ma sposobu, aby wymusić odświeżenie tokenu.

Istnieje prosty protokół REST do uzyskiwania tokenu w usłudze app service i usłudze Azure Functions. Może to być używane dla wszystkich aplikacji i języków. W przypadku platformy .NET i Java zestaw SDK platformy Azure zapewnia abstrakcję za pomocą tego protokołu i ułatwia lokalne środowisko programistyczne.

### <a name="using-the-rest-protocol"></a>Korzystanie z protokołu REST

Aplikacja z tożsamością zarządzaną ma zdefiniowane dwie zmienne środowiskowe:

- MSI_ENDPOINT — adres URL do usługi tokenu lokalnego.
- MSI_SECRET — nagłówek używany do ograniczania ataków fałszerstwa żądań po stronie serwera (SSRF). Wartość jest obracana przez platformę.

**MSI_ENDPOINT** to lokalny adres URL, z którego aplikacja może żądać tokenów. Aby uzyskać token dla zasobu, skonsuj żądanie HTTP GET do tego punktu końcowego, w tym następujące parametry:

> |Nazwa parametru|W|Opis|
> |-----|-----|-----|
> |zasób|Zapytanie|Identyfikator URI zasobu AAD zasobu, dla którego należy uzyskać token. Może to być jedna z [usług platformy Azure, które obsługują uwierzytelnianie usługi Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) lub inny identyfikator URI zasobów.|
> |api-version|Zapytanie|Wersja interfejsu API tokenu, który ma być używany. "2017-09-01" jest obecnie jedyną obsługiwaną wersją.|
> |wpis tajny|Nagłówek|Wartość zmiennej środowiskowej MSI_SECRET. Ten nagłówek jest używany w celu złagodzenia ataków fałszerstwa żądań po stronie serwera (SSRF).|
> |clientid|Zapytanie|(Opcjonalnie, chyba że dla przypisanego przez użytkownika) Identyfikator tożsamości przypisanej przez użytkownika do użycia. Jeśli pominięto, używana jest tożsamość przypisana do systemu.|

> [!IMPORTANT]
> Jeśli próbujesz uzyskać tokeny dla tożsamości przypisanych przez użytkownika, `clientid` należy dołączyć właściwość. W przeciwnym razie usługa tokenu podejmie próbę uzyskania tokenu dla tożsamości przypisanej do systemu, która może istnieć lub nie.

Pomyślna odpowiedź 200 OK zawiera obiekt JSON z następującymi właściwościami:

> |Nazwa właściwości|Opis|
> |-------------|----------|
> |access_token|Żądany token dostępu. Wywołująca usługa sieci web może używać tego tokenu do uwierzytelniania w odbierającej usłudze sieci web.|
> |expires_on|Czas wygaśnięcia tokenu dostępu. Data jest reprezentowana jako liczba sekund od 1970-01-01T0:0:0Z UTC do czasu wygaśnięcia. Ta wartość jest używana do określenia okresu istnienia tokenów buforowanych.|
> |zasób|Identyfikator URI identyfikatora aplikacji odbierającej usługi sieci web.|
> |token_type|Wskazuje wartość typu tokenu. Jedynym typem, który obsługuje usługi Azure AD jest Bearer. Aby uzyskać więcej informacji na temat tokenów okaziciela, zobacz [OAuth 2.0 Authorization Framework: Bearer Token Usage (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt).|

Ta odpowiedź jest taka sama jak [odpowiedź dla żądania tokenu dostępu usługi AAD do usługi.](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md#get-a-token)

> [!NOTE]
> Zmienne środowiskowe są konfigurowane podczas pierwszego uruchomienia procesu, więc po włączeniu tożsamości zarządzanej dla aplikacji może być `MSI_ENDPOINT` `MSI_SECRET` konieczne ponowne uruchomienie aplikacji lub ponowne wdrożenie jej kodu przed i są dostępne dla kodu.

### <a name="rest-protocol-examples"></a>Przykłady protokołów REST

Przykładowe żądanie może wyglądać następująco:

```
GET /MSI/token?resource=https://vault.azure.net&api-version=2017-09-01 HTTP/1.1
Host: localhost:4141
Secret: 853b9a84-5bfa-4b22-a3f3-0b9a43d9ad8a
```

Przykładowa odpowiedź może wyglądać następująco:

```
HTTP/1.1 200 OK
Content-Type: application/json

{
    "access_token": "eyJ0eXAi…",
    "expires_on": "09/14/2017 00:00:00 PM +00:00",
    "resource": "https://vault.azure.net",
    "token_type": "Bearer"
}
```

### <a name="code-examples"></a>Przykłady kodu

# <a name="net"></a>[.NET](#tab/dotnet)

> [!TIP]
> W przypadku języków platformy .NET można również użyć [usługi Microsoft.Azure.Services.AppAuthentication](#asal) zamiast samodzielnie tworzyć to żądanie.

```csharp
private readonly HttpClient _client;
// ...
public async Task<HttpResponseMessage> GetToken(string resource)  {
    var request = new HttpRequestMessage(HttpMethod.Get, 
        String.Format("{0}/?resource={1}&api-version=2017-09-01", Environment.GetEnvironmentVariable("MSI_ENDPOINT"), resource));
    request.Headers.Add("Secret", Environment.GetEnvironmentVariable("MSI_SECRET"));
    return await _client.SendAsync(request);
}
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

```javascript
const rp = require('request-promise');
const getToken = function(resource, cb) {
    let options = {
        uri: `${process.env["MSI_ENDPOINT"]}/?resource=${resource}&api-version=2017-09-01`,
        headers: {
            'Secret': process.env["MSI_SECRET"]
        }
    };
    rp(options)
        .then(cb);
}
```

# <a name="python"></a>[Python](#tab/python)

```python
import os
import requests

msi_endpoint = os.environ["MSI_ENDPOINT"]
msi_secret = os.environ["MSI_SECRET"]

def get_bearer_token(resource_uri):
    token_auth_uri = f"{msi_endpoint}?resource={resource_uri}&api-version=2017-09-01"
    head_msi = {'Secret':msi_secret}

    resp = requests.get(token_auth_uri, headers=head_msi)
    access_token = resp.json()['access_token']

    return access_token
```

# <a name="powershell"></a>[Powershell](#tab/powershell)

```powershell
$resourceURI = "https://<AAD-resource-URI-for-resource-to-obtain-token>"
$tokenAuthURI = $env:MSI_ENDPOINT + "?resource=$resourceURI&api-version=2017-09-01"
$tokenResponse = Invoke-RestMethod -Method Get -Headers @{"Secret"="$env:MSI_SECRET"} -Uri $tokenAuthURI
$accessToken = $tokenResponse.access_token
```

---

### <a name="using-the-microsoftazureservicesappauthentication-library-for-net"></a><a name="asal"></a>Korzystanie z biblioteki microsoft.Azure.Services.AppAuthentication dla platformy .NET

W przypadku aplikacji i funkcji platformy .NET najprostszym sposobem pracy z tożsamością zarządzaną jest pakiet Microsoft.Azure.Services.AppAuthentication. Ta biblioteka umożliwia również testowanie kodu lokalnie na komputerze deweloperskim przy użyciu konta użytkownika z programu Visual Studio, [interfejsu wiersza polecenia platformy Azure](/cli/azure)lub zintegrowanego uwierzytelniania usługi Active Directory. Aby uzyskać więcej informacji na temat opcji rozwoju lokalnego za pomocą tej biblioteki, zobacz [odwołanie do microsoft.Azure.Services.AppAuthentication]. W tej sekcji pokazano, jak rozpocząć pracę z biblioteką w kodzie.

1. Dodaj odwołania do [microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) i innych niezbędnych pakietów NuGet do aplikacji. Poniższy przykład używa również [microsoft.Azure.KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault).

2. Dodaj następujący kod do aplikacji, modyfikując do docelowego właściwego zasobu. W tym przykładzie przedstawiono dwa sposoby pracy z usługą Azure Key Vault:

    ```csharp
    using Microsoft.Azure.Services.AppAuthentication;
    using Microsoft.Azure.KeyVault;
    // ...
    var azureServiceTokenProvider = new AzureServiceTokenProvider();
    string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync("https://vault.azure.net");
    // OR
    var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider.KeyVaultTokenCallback));
    ```

Aby dowiedzieć się więcej o microsoft.Azure.Services.AppAuthentication i operacji, które udostępnia, zobacz [Microsoft.Azure.Services.AppAuthentication odwołania] i [usługi aplikacji i KeyVault z MSI .NET próbki](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet).

### <a name="using-the-azure-sdk-for-java"></a>Korzystanie z zestawu SDK platformy Azure dla języka Java

W przypadku aplikacji i funkcji java najprostszym sposobem pracy z tożsamością zarządzaną jest zestaw [SDK platformy Azure dla języka Java.](https://github.com/Azure/azure-sdk-for-java) W tej sekcji pokazano, jak rozpocząć pracę z biblioteką w kodzie.

1. Dodaj odwołanie do [biblioteki zestawów SDK platformy Azure](https://mvnrepository.com/artifact/com.microsoft.azure/azure). W przypadku projektów Maven można dodać ten `dependencies` fragment kodu do sekcji pliku POM projektu:

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure</artifactId>
        <version>1.23.0</version>
    </dependency>
    ```

2. Użyj `AppServiceMSICredentials` obiektu do uwierzytelniania. W tym przykładzie pokazano, jak ten mechanizm może służyć do pracy z usługi Azure Key Vault:

    ```java
    import com.microsoft.azure.AzureEnvironment;
    import com.microsoft.azure.management.Azure;
    import com.microsoft.azure.management.keyvault.Vault
    //...
    Azure azure = Azure.authenticate(new AppServiceMSICredentials(AzureEnvironment.AZURE))
            .withSubscription(subscriptionId);
    Vault myKeyVault = azure.vaults().getByResourceGroup(resourceGroup, keyvaultName);

    ```


## <a name="remove-an-identity"></a><a name="remove"></a>Usuwanie tożsamości

Tożsamość przypisaną do systemu można usunąć, wyłączając tę funkcję za pomocą portalu, programu PowerShell lub interfejsu wiersza polecenia w taki sam sposób, w jaki została utworzona. Tożsamości przypisane przez użytkownika można usunąć indywidualnie. Aby usunąć wszystkie tożsamości, ustaw typ "Brak" w [szablonie ARM:](#using-an-azure-resource-manager-template)

```json
"identity": {
    "type": "None"
}
```

Usunięcie tożsamości przypisanej przez system w ten sposób spowoduje również usunięcie jej z usługi AAD. Tożsamości przypisane do systemu są również automatycznie usuwane z aad po usunięciu zasobu aplikacji.

> [!NOTE]
> Istnieje również ustawienie aplikacji, które można ustawić, WEBSITE_DISABLE_MSI, który po prostu wyłącza usługę tokenu lokalnego. Jednak pozostawia tożsamość w miejscu, a narzędzia nadal będą wyświetlać zarządzana tożsamość jako "on" lub "włączone". W rezultacie użycie tego ustawienia nie jest zalecane.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Bezpieczny dostęp do bazy danych SQL przy użyciu tożsamości zarządzanej](app-service-web-tutorial-connect-msi.md)

[Odwołanie do uwierzytelniania microsoft.Azure.Services.AppAuthentication]: https://go.microsoft.com/fwlink/p/?linkid=862452
