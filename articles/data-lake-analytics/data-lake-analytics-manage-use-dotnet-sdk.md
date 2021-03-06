---
title: Zarządzanie usługą Azure Data Lake Analytics przy użyciu sdk platformy Azure .NET
description: W tym artykule opisano sposób używania narzędzia Azure .NET SDK do pisania aplikacji, które zarządzają zadaniami usługi Data Lake Analytics, źródłami danych & użytkownikami.
services: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: 811d172d-9873-4ce9-a6d5-c1a26b374c79
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 06/18/2017
ms.openlocfilehash: 0a10af73d754596e9b5bb34b2974d7f1647d06f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60617711"
---
# <a name="manage-azure-data-lake-analytics-a-net-app"></a>Zarządzanie usługą Azure Data Lake Analytics za pomocą aplikacji platformy .NET

[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

W tym artykule opisano sposób zarządzania kontami usługi Azure Data Lake Analytics, źródłami danych, użytkownikami i zadaniami przy użyciu aplikacji napisanej przy użyciu narzędzia Azure .NET SDK. 

## <a name="prerequisites"></a>Wymagania wstępne

* **Zainstalowany program Visual Studio 2015, Visual Studio 2013 Update 4 lub Visual Studio 2012 z językiem Visual C++**.
* **Zestaw Microsoft Azure SDK dla programu .NET w wersji 2.5 lub nowszej**.  Można go zainstalować przy użyciu [Instalatora platformy sieci Web](https://www.microsoft.com/web/downloads/platform.aspx).
* **Wymagane pakiety NuGet**

### <a name="install-nuget-packages"></a>Instalowanie pakietów NuGet

|Pakiet|Wersja|
|-------|-------|
|[Microsoft.Rest.ClientRuntime.Azure.Authentication](https://www.nuget.org/packages/Microsoft.Rest.ClientRuntime.Azure.Authentication)| 2.3.1|
|[Microsoft.Azure.Management.DataLake.Analytics](https://www.nuget.org/packages/Microsoft.Azure.Management.DataLake.Analytics)|3.0.0|
|[Microsoft.Azure.Management.DataLake.Store](https://www.nuget.org/packages/Microsoft.Azure.Management.DataLake.Store)|2.2.0|
|[Microsoft.Azure.Management.ResourceManager](https://www.nuget.org/packages/Microsoft.Azure.Management.ResourceManager)|1.6.0-podgląd|
|[Microsoft.Azure.Graph.RBAC](https://www.nuget.org/packages/Microsoft.Azure.Management.ResourceManager)|3.4.0-podgląd|

Te pakiety można zainstalować za pomocą wiersza polecenia NuGet z następującymi poleceniami:

```powershell
Install-Package -Id Microsoft.Rest.ClientRuntime.Azure.Authentication  -Version 2.3.1
Install-Package -Id Microsoft.Azure.Management.DataLake.Analytics  -Version 3.0.0
Install-Package -Id Microsoft.Azure.Management.DataLake.Store  -Version 2.2.0
Install-Package -Id Microsoft.Azure.Management.ResourceManager  -Version 1.6.0-preview
Install-Package -Id Microsoft.Azure.Graph.RBAC -Version 3.4.0-preview
```

## <a name="common-variables"></a>Typowe zmienne

```csharp
string subid = "<Subscription ID>"; // Subscription ID (a GUID)
string tenantid = "<Tenant ID>"; // AAD tenant ID or domain. For example, "contoso.onmicrosoft.com"
string rg == "<value>"; // Resource  group name
string clientid = "1950a258-227b-4e31-a9cf-717495945fc2"; // Sample client ID (this will work, but you should pick your own)
```

## <a name="authentication"></a>Uwierzytelnianie

Masz wiele opcji logowania do usługi Azure Data Lake Analytics. Poniższy fragment kodu przedstawia przykład uwierzytelniania za pomocą interaktywnego uwierzytelniania użytkownika za pomocą wyskakującym okienkiem.

``` csharp
using System;
using System.IO;
using System.Threading;
using System.Security.Cryptography.X509Certificates;

using Microsoft.Rest;
using Microsoft.Rest.Azure.Authentication;
using Microsoft.Azure.Management.DataLake.Analytics;
using Microsoft.Azure.Management.DataLake.Analytics.Models;
using Microsoft.Azure.Management.DataLake.Store;
using Microsoft.Azure.Management.DataLake.Store.Models;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using Microsoft.Azure.Graph.RBAC;

public static Program
{
   public static string TENANT = "microsoft.onmicrosoft.com";
   public static string CLIENTID = "1950a258-227b-4e31-a9cf-717495945fc2";
   public static System.Uri ARM_TOKEN_AUDIENCE = new System.Uri( @"https://management.core.windows.net/");
   public static System.Uri ADL_TOKEN_AUDIENCE = new System.Uri( @"https://datalake.azure.net/" );
   public static System.Uri GRAPH_TOKEN_AUDIENCE = new System.Uri( @"https://graph.windows.net/" );

   static void Main(string[] args)
   {
      string MY_DOCUMENTS= System.Environment.GetFolderPath( System.Environment.SpecialFolder.MyDocuments);
      string TOKEN_CACHE_PATH = System.IO.Path.Combine(MY_DOCUMENTS, "my.tokencache");

      var tokenCache = GetTokenCache(TOKEN_CACHE_PATH);
      var armCreds = GetCreds_User_Popup(TENANT, ARM_TOKEN_AUDIENCE, CLIENTID, tokenCache);
      var adlCreds = GetCreds_User_Popup(TENANT, ADL_TOKEN_AUDIENCE, CLIENTID, tokenCache);
      var graphCreds = GetCreds_User_Popup(TENANT, GRAPH_TOKEN_AUDIENCE, CLIENTID, tokenCache);
   }
}
```

Kod źródłowy **dla GetCreds_User_Popup** i kod dla innych opcji uwierzytelniania są objęte [opcjami uwierzytelniania Usługi Data Lake Analytics .NET](https://github.com/Azure-Samples/data-lake-analytics-dotnet-auth-options)


## <a name="create-the-client-management-objects"></a>Tworzenie obiektów zarządzania klientami

``` csharp
var resourceManagementClient = new ResourceManagementClient(armCreds) { SubscriptionId = subid };

var adlaAccountClient = new DataLakeAnalyticsAccountManagementClient(armCreds);
adlaAccountClient.SubscriptionId = subid;

var adlsAccountClient = new DataLakeStoreAccountManagementClient(armCreds);
adlsAccountClient.SubscriptionId = subid;

var adlaCatalogClient = new DataLakeAnalyticsCatalogManagementClient(adlCreds);
var adlaJobClient = new DataLakeAnalyticsJobManagementClient(adlCreds);

var adlsFileSystemClient = new DataLakeStoreFileSystemManagementClient(adlCreds);

var  graphClient = new GraphRbacManagementClient(graphCreds);
graphClient.TenantID = domain;

```

## <a name="manage-accounts"></a>Zarządzanie kontami

### <a name="create-an-azure-resource-group"></a>Tworzenie grupy zasobów platformy Azure

Jeśli jeszcze go nie utworzono, musisz mieć grupę zasobów platformy Azure, aby utworzyć składniki usługi Data Lake Analytics. Potrzebne są poświadczenia uwierzytelniania, identyfikator subskrypcji i lokalizacja. Poniższy kod pokazuje, jak utworzyć grupę zasobów:

``` csharp
var resourceGroup = new ResourceGroup { Location = location };
resourceManagementClient.ResourceGroups.CreateOrUpdate(groupName, rg);
```

Aby uzyskać więcej informacji, zobacz Grupy zasobów platformy Azure i analiza usługi Data Lake.

### <a name="create-a-data-lake-store-account"></a>Tworzenie konta usługi Data Lake Store

Zawsze konto ADLA wymaga konta ADLS. Jeśli nie masz jeszcze jednego do użycia, możesz go utworzyć z następującym kodem:

``` csharp
var new_adls_params = new DataLakeStoreAccount(location: _location);
adlsAccountClient.Account.Create(rg, adls, new_adls_params);
```

### <a name="create-a-data-lake-analytics-account"></a>Tworzenie konta Data Lake Analytics

Poniższy kod tworzy konto ADLS

``` csharp
var new_adla_params = new DataLakeAnalyticsAccount()
{
   DefaultDataLakeStoreAccount = adls,
   Location = location
};

adlaClient.Account.Create(rg, adla, new_adla_params);
```

### <a name="list-data-lake-store-accounts"></a>Lista kont w sklepie Data Lake Store

``` csharp
var adlsAccounts = adlsAccountClient.Account.List().ToList();
foreach (var adls in adlsAccounts)
{
   Console.WriteLine($"ADLS: {0}", adls.Name);
}
```

### <a name="list-data-lake-analytics-accounts"></a>Lista kont Data Lake Analytics

``` csharp
var adlaAccounts = adlaClient.Account.List().ToList();

for (var adla in AdlaAccounts)
{
   Console.WriteLine($"ADLA: {0}, adla.Name");
}
```

### <a name="checking-if-an-account-exists"></a>Sprawdzanie, czy istnieje konto

``` csharp
bool exists = adlaClient.Account.Exists(rg, adla));
```

### <a name="get-information-about-an-account"></a>Uzyskaj informacje o koncie

``` csharp
bool exists = adlaClient.Account.Exists(rg, adla));
if (exists)
{
   var adla_accnt = adlaClient.Account.Get(rg, adla);
}
```

### <a name="delete-an-account"></a>Usuwanie konta

``` csharp
if (adlaClient.Account.Exists(rg, adla))
{
   adlaClient.Account.Delete(rg, adla);
}
```

### <a name="get-the-default-data-lake-store-account"></a>Pobierz domyślne konto Data Lake Store

Każde konto Usługi Data Lake Analytics wymaga domyślnego konta Data Lake Store. Ten kod służy do określania domyślnego konta Sklepu dla konta Analytics.

``` csharp
if (adlaClient.Account.Exists(rg, adla))
{
  var adla_accnt = adlaClient.Account.Get(rg, adla);
  string def_adls_account = adla_accnt.DefaultDataLakeStoreAccount;
}
```

## <a name="manage-data-sources"></a>Zarządzanie źródłami danych

Usługa Data Lake Analytics obsługuje obecnie następujące źródła danych:

* [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md)
* [Konto usługi Azure Storage](../storage/common/storage-introduction.md)

### <a name="link-to-an-azure-storage-account"></a>Łącze do konta usługi Azure Storage

Można utworzyć łącza do kont usługi Azure Storage.

``` csharp
string storage_key = "xxxxxxxxxxxxxxxxxxxx";
string storage_account = "mystorageaccount";
var addParams = new AddStorageAccountParameters(storage_key);            
adlaClient.StorageAccounts.Add(rg, adla, storage_account, addParams);
```

### <a name="list-azure-storage-data-sources"></a>Wyświetlanie listy źródeł danych usługi Azure Storage

``` csharp
var stg_accounts = adlaAccountClient.StorageAccounts.ListByAccount(rg, adla);

if (stg_accounts != null)
{
  foreach (var stg_account in stg_accounts)
  {
      Console.WriteLine($"Storage account: {0}", stg_account.Name);
  }
}
```

### <a name="list-data-lake-store-data-sources"></a>Lista źródeł danych Magazynu usługi Data Lake

``` csharp
var adls_accounts = adlsClient.Account.List();

if (adls_accounts != null)
{
  foreach (var adls_accnt in adls_accounts)
  {
      Console.WriteLine($"ADLS account: {0}", adls_accnt.Name);
  }
}
```

### <a name="upload-and-download-folders-and-files"></a>Przesyłanie i pobieranie folderów i plików

Za pomocą obiektu zarządzania klientami systemu plików Usługi Data Lake Store można przekazywać i pobierać poszczególne pliki lub foldery z platformy Azure na komputer lokalny, korzystając z następujących metod:

- Wyślijfolder
- Uploadfile
- Plik do pobrania
- Downloadfile

Pierwszym parametrem dla tych metod jest nazwa konta magazynu usługi Data Lake Store, a następnie parametry ścieżki źródłowej i ścieżki docelowej.

W poniższym przykładzie pokazano, jak pobrać folder w magazynie usługi Data Lake Store.

``` csharp
adlsFileSystemClient.FileSystem.DownloadFolder(adls, sourcePath, destinationPath);
```

### <a name="create-a-file-in-a-data-lake-store-account"></a>Tworzenie pliku na koncie usługi Data Lake Store

``` csharp
using (var memstream = new MemoryStream())
{
   using (var sw = new StreamWriter(memstream, UTF8Encoding.UTF8))
   {
      sw.WriteLine("Hello World");
      sw.Flush();
      
      memstream.Position = 0;

      adlsFileSystemClient.FileSystem.Create(adls, "/Samples/Output/randombytes.csv", memstream);
   }
}
```

### <a name="verify-azure-storage-account-paths"></a>Weryfikowanie ścieżek kont usługi Azure Storage

Poniższy kod sprawdza, czy konto usługi Azure Storage (storageAccntName) istnieje na koncie usługi Data Lake Analytics (analyticsAccountName) i jeśli kontener (containerName) istnieje na koncie usługi Azure Storage.

``` csharp
string storage_account = "mystorageaccount";
string storage_container = "mycontainer";
bool accountExists = adlaClient.Account.StorageAccountExists(rg, adla, storage_account));
bool containerExists = adlaClient.Account.StorageContainerExists(rg, adla, storage_account, storage_container));
```

## <a name="manage-catalog-and-jobs"></a>Zarządzanie katalogiem i zadaniami

Obiekt DataLakeAnalyticsCatalogManagementClient udostępnia metody zarządzania bazą danych SQL dla każdego konta usługi Azure Data Lake Analytics. DataLakeAnalyticsJobManagementClient udostępnia metody przesyłania i zarządzania zadaniami uruchamiane w bazie danych za pomocą skryptów U-SQL.

### <a name="list-databases-and-schemas"></a>Lista baz danych i schematów

Wśród kilku rzeczy, które można wymienić, najczęściej są bazy danych i ich schemat. Poniższy kod uzyskuje kolekcję baz danych, a następnie wylicza schemat dla każdej bazy danych.

``` csharp
var databases = adlaCatalogClient.Catalog.ListDatabases(adla);
foreach (var db in databases)
{
  Console.WriteLine($"Database: {db.Name}");
  Console.WriteLine(" - Schemas:");
  var schemas = adlaCatalogClient.Catalog.ListSchemas(adla, db.Name);
  foreach (var schm in schemas)
  {
      Console.WriteLine($"\t{schm.Name}");
  }
}
```

### <a name="list-table-columns"></a>Kolumny tabeli listy

Poniższy kod pokazuje, jak uzyskać dostęp do bazy danych za pomocą klienta zarządzania wykazem usługi Data Lake Analytics, aby wyświetlić listę kolumn w określonej tabeli.

```csharp
var tbl = adlaCatalogClient.Catalog.GetTable(adla, "master", "dbo", "MyTableName");
IEnumerable<USqlTableColumn> columns = tbl.ColumnList;

foreach (USqlTableColumn utc in columns)
{
  Console.WriteLine($"\t{utc.Name}");
}
```

### <a name="submit-a-u-sql-job"></a>Przesyłanie zadania U-SQL

Poniższy kod pokazuje, jak użyć klienta zarządzania zadaniami usługi Data Lake Analytics do przesłania zadania.

``` csharp
string scriptPath = "/Samples/Scripts/SearchResults_Wikipedia_Script.txt";
Stream scriptStrm = adlsFileSystemClient.FileSystem.Open(_adlsAccountName, scriptPath);
string scriptTxt = string.Empty;
using (StreamReader sr = new StreamReader(scriptStrm))
{
    scriptTxt = sr.ReadToEnd();
}

var jobName = "SR_Wikipedia";
var jobId = Guid.NewGuid();
var properties = new USqlJobProperties(scriptTxt);
var parameters = new JobInformation(jobName, JobType.USql, properties, priority: 1, degreeOfParallelism: 1, jobId: jobId);
var jobInfo = adlaJobClient.Job.Create(adla, jobId, parameters);
Console.WriteLine($"Job {jobName} submitted.");
```

### <a name="list-failed-jobs"></a>Lista zadań, które nie powiodły się

Poniższy kod zawiera informacje o zadaniach, które nie powiodły się.

```csharp
var odq = new ODataQuery<JobInformation> { Filter = "result eq 'Failed'" };
var jobs = adlaJobClient.Job.List(adla, odq);
foreach (var j in jobs)
{
   Console.WriteLine($"{j.Name}\t{j.JobId}\t{j.Type}\t{j.StartTime}\t{j.EndTime}");
}
```

### <a name="list-pipelines"></a>Lista potoków

Poniższy kod zawiera informacje o każdym potoku zadań przesłanych do konta.

``` csharp
var pipelines = adlaJobClient.Pipeline.List(adla);
foreach (var p in pipelines)
{
   Console.WriteLine($"Pipeline: {p.Name}\t{p.PipelineId}\t{p.LastSubmitTime}");
}
```

### <a name="list-recurrences"></a>Listy cykli

Poniższy kod zawiera informacje o każdym cyklu zadań przesłanych do konta.

``` csharp
var recurrences = adlaJobClient.Recurrence.List(adla);
foreach (var r in recurrences)
{
   Console.WriteLine($"Recurrence: {r.Name}\t{r.RecurrenceId}\t{r.LastSubmitTime}");
}
```

## <a name="common-graph-scenarios"></a>Typowe scenariusze wykresów

### <a name="look-up-user-in-the-aad-directory"></a>Szukaj użytkownika w katalogu AAD

``` csharp
var userinfo = graphClient.Users.Get( "bill@contoso.com" );
```

### <a name="get-the-objectid-of-a-user-in-the-aad-directory"></a>Pobierz identyfikator obiektu użytkownika w katalogu usługi AAD

``` csharp
var userinfo = graphClient.Users.Get( "bill@contoso.com" );
Console.WriteLine( userinfo.ObjectId )
```

## <a name="manage-compute-policies"></a>Zarządzanie zasadami obliczeniowymi

Obiekt DataLakeAnalyticsAccountManagementClient udostępnia metody zarządzania zasadami obliczeniowymi dla konta usługi Data Lake Analytics.

### <a name="list-compute-policies"></a>Lista zasad obliczeniowych

Poniższy kod pobiera listę zasad obliczeniowych dla konta usługi Data Lake Analytics.

``` csharp
var policies = adlaAccountClient.ComputePolicies.ListByAccount(rg, adla);
foreach (var p in policies)
{
   Console.WriteLine($"Name: {p.Name}\tType: {p.ObjectType}\tMax AUs / job: {p.MaxDegreeOfParallelismPerJob}\tMin priority / job: {p.MinPriorityPerJob}");
}
```

### <a name="create-a-new-compute-policy"></a>Tworzenie nowej zasady obliczeniowej

Poniższy kod tworzy nową zasadę obliczeniową dla konta usługi Data Lake Analytics, ustawiając maksymalną liczbę jednostek analizy dostępną dla określonego użytkownika na 50, a minimalny priorytet zadania na 250.

``` csharp
var userAadObjectId = "3b097601-4912-4d41-b9d2-78672fc2acde";
var newPolicyParams = new ComputePolicyCreateOrUpdateParameters(userAadObjectId, "User", 50, 250);
adlaAccountClient.ComputePolicies.CreateOrUpdate(rg, adla, "GaryMcDaniel", newPolicyParams);
```

## <a name="next-steps"></a>Następne kroki

* [Omówienie usługi Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Zarządzanie usługą Azure Data Lake Analytics przy użyciu witryny Azure portal](data-lake-analytics-manage-use-portal.md)
* [Monitorowanie zadań usługi Azure Data Lake Analytics i rozwiązywanie problemów przy użyciu witryny Azure Portal](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)