---
title: 'Samouczek: Łowianie danych monitorowania w Eksploratorze danych platformy Azure bez kodu'
description: W tym samouczku dowiesz się, jak pozyskiwania danych monitorowania do usługi Azure Data Explorer bez jednego wiersza kodu i kwerendy tych danych.
author: orspod
ms.author: orspodek
ms.reviewer: kerend
ms.service: data-explorer
ms.topic: tutorial
ms.date: 01/29/2020
ms.openlocfilehash: 3a53a660da2257540f23bc6438fc5933e5229c76
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "78198052"
---
# <a name="tutorial-ingest-and-query-monitoring-data-in-azure-data-explorer"></a>Samouczek: Pozyskiwania i wykonywania zapytań danych monitorowania danych w Eksploratorze danych platformy Azure 

W tym samouczku nauczymy Cię, jak pozyskiwać dane z dzienników diagnostycznych i dzienników aktywności do klastra usługi Azure Data Explorer bez pisania kodu. Ta prosta metoda pozyskiwania pozwala na szybkie rozpoczęcie tworzenia zapytań w usłudze Azure Data Explorer na potrzeby analizy danych.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie tabel i mapowania pozyskiwania w bazie danych usługi Azure Data Explorer.
> * Formatowanie pozyskanych danych za pomocą zasad aktualizacji.
> * Utwórz [centrum zdarzeń](/azure/event-hubs/event-hubs-about) i połącz je z Eksploratorem danych platformy Azure.
> * Przesyłaj strumieniowo dane do centrum zdarzeń z [metryk diagnostycznych](/azure/azure-monitor/platform/diagnostic-settings) usługi Azure Monitor oraz dzienników i [dzienników aktywności.](/azure/azure-monitor/platform/activity-logs-overview)
> * Tworzenie zapytań dotyczących pozyskiwanych danych za pomocą usługi Azure Data Explorer.

> [!NOTE]
> Tworzenie wszystkich zasobów w tej samej lokalizacji lub regionie platformy Azure. 

## <a name="prerequisites"></a>Wymagania wstępne

* Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/) przed rozpoczęciem.
* [Baza danych i klaster usługi Azure Data Explorer](create-cluster-database-portal.md). Nazwa bazy danych używanej w tym samouczku to *TestDatabase*.

## <a name="azure-monitor-data-provider-diagnostic-metrics-and-logs-and-activity-logs"></a>Dostawca danych usługi Azure Monitor: metryki diagnostyczne, dzienniki i dzienniki aktywności

Wyświetl i zrozum dane dostarczone przez metryki diagnostyczne usługi Azure Monitor oraz dzienniki i dzienniki aktywności poniżej. Utworzysz potok pozyskiwania na podstawie tych schematów danych. Należy zauważyć, że każde zdarzenie w dzienniku ma tablicę rekordów. Ta tablica rekordów zostanie podzielona w dalszej części samouczka.

### <a name="examples-of-diagnostic-metrics-and-logs-and-activity-logs"></a>Przykłady metryk diagnostycznych i dzienników i dzienników aktywności

Metryki diagnostyczne platformy Azure i dzienniki i dzienniki aktywności są emitowane przez usługę platformy Azure i dostarczają danych dotyczących działania tej usługi. 

# <a name="diagnostic-metrics"></a>[Metryki diagnostyczne](#tab/diagnostic-metrics)
#### <a name="example"></a>Przykład

Metryki diagnostyczne są agregowane z ziarnem czasu 1 minuty. Poniżej przedstawiono przykład schematu zdarzenia metryk-zdarzenia usługi Azure Data Explorer na czas trwania kwerendy:

```json
{
    "records": [
    {
        "count": 14,
        "total": 0,
        "minimum": 0,
        "maximum": 0,
        "average": 0,
        "resourceId": "/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resource-group>/PROVIDERS/MICROSOFT.KUSTO/CLUSTERS/<cluster-name>",
        "time": "2018-12-20T17:00:00.0000000Z",
        "metricName": "QueryDuration",
        "timeGrain": "PT1M"
    },
    {
        "count": 12,
        "total": 0,
        "minimum": 0,
        "maximum": 0,
        "average": 0,
        "resourceId": "/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resource-group>/PROVIDERS/MICROSOFT.KUSTO/CLUSTERS/<cluster-name>",
        "time": "2018-12-21T17:00:00.0000000Z",
        "metricName": "QueryDuration",
        "timeGrain": "PT1M"
    }
    ]
}
```

# <a name="diagnostic-logs"></a>[Dzienniki diagnostyczne](#tab/diagnostic-logs)
#### <a name="example"></a>Przykład

Oto przykład dziennika pozyskiwania [diagnostycznego](using-diagnostic-logs.md#diagnostic-logs-schema)usługi Azure Data Explorer:

```json
{
    "time": "2019-08-26T13:22:36.8804326Z",
    "resourceId": "/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resource-group>/PROVIDERS/MICROSOFT.KUSTO/CLUSTERS/<cluster-name>",
    "operationName": "MICROSOFT.KUSTO/CLUSTERS/INGEST/ACTION",
    "operationVersion": "1.0",
    "category": "FailedIngestion",
    "resultType": "Failed",
    "correlationId": "d59882f1-ad64-4fc4-b2ef-d663b6cc1cc5",
    "properties": {
        "OperationId": "00000000-0000-0000-0000-000000000000",
        "Database": "Kusto",
        "Table": "Table_13_20_prod",
        "FailedOn": "2019-08-26T13:22:36.8804326Z",
        "IngestionSourceId": "d59882f1-ad64-4fc4-b2ef-d663b6cc1cc5",
        "Details":
        {
            "error": 
            {
                "code": "BadRequest_DatabaseNotExist",
                "message": "Request is invalid and cannot be executed.",
                "@type": "Kusto.Data.Exceptions.DatabaseNotFoundException",
                "@message": "Database 'Kusto' was not found.",
                "@context": 
                {
                    "timestamp": "2019-08-26T13:22:36.7179157Z",
                    "serviceAlias": "<cluster-name>",
                    "machineName": "KEngine000001",
                    "processName": "Kusto.WinSvc.Svc",
                    "processId": 5336,
                    "threadId": 6528,
                    "appDomainName": "Kusto.WinSvc.Svc.exe",
                    "clientRequestd": "DM.IngestionExecutor;a70ddfdc-b471-4fc7-beac-bb0f6e569fe8",
                    "activityId": "f13e7718-1153-4e65-bf82-8583d712976f",
                    "subActivityId": "2cdad9d0-737b-4c69-ac9a-22cf9af0c41b",
                    "activityType": "DN.AdminCommand.DataIngestPullCommand",
                    "parentActivityId": "2f65e533-a364-44dd-8d45-d97460fb5795",
                    "activityStack": "(Activity stack: CRID=DM.IngestionExecutor;a70ddfdc-b471-4fc7-beac-bb0f6e569fe8 ARID=f13e7718-1153-4e65-bf82-8583d712976f > DN.Admin.Client.ExecuteControlCommand/5b764b32-6017-44a2-89e7-860eda515d40 > P.WCF.Service.ExecuteControlCommandInternal..IAdminClientServiceCommunicationContract/c2ef9344-069d-44c4-88b1-a3570697ec77 > DN.FE.ExecuteControlCommand/2f65e533-a364-44dd-8d45-d97460fb5795 > DN.AdminCommand.DataIngestPullCommand/2cdad9d0-737b-4c69-ac9a-22cf9af0c41b)"
                },
                "@permanent": true
            }
        },
        "ErrorCode": "BadRequest_DatabaseNotExist",
        "FailureStatus": "Permanent",
        "RootActivityId": "00000000-0000-0000-0000-000000000000",
        "OriginatesFromUpdatePolicy": false,
        "ShouldRetry": false,
        "IngestionSourcePath": "https://c0skstrldkereneus01.blob.core.windows.net/aam-20190826-temp-e5c334ee145d4b43a3a2d3a96fbac1df/3216_test_3_columns_invalid_8f57f0d161ed4a8c903c6d1073005732_59951f9ca5d143b6bdefe52fa381a8ca.zip"
    }
}
```
# <a name="activity-logs"></a>[Dzienniki aktywności](#tab/activity-logs)
#### <a name="example"></a>Przykład

Dzienniki aktywności platformy Azure są dziennikami na poziomie subskrypcji, które zapewniają wgląd w operacje wykonywane na zasobach w ramach subskrypcji. Poniżej przedstawiono przykład zdarzenia dziennika aktywności do sprawdzania dostępu:

```json
{
    "records": [
    {
        "time": "2018-12-26T16:23:06.1090193Z",
        "resourceId": "/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resource-group>/PROVIDERS/MICROSOFT.WEB/SITES/CLNB5F73B70-DCA2-47C2-BB24-77B1A2CAAB4D/PROVIDERS/MICROSOFT.AUTHORIZATION",
        "operationName": "MICROSOFT.AUTHORIZATION/CHECKACCESS/ACTION",
        "category": "Action",
        "resultType": "Start",
        "resultSignature": "Started.",
        "durationMs": 0,
        "callerIpAddress": "13.66.225.188",
        "correlationId": "0de9f4bc-4adc-4209-a774-1b4f4ae573ed",
        "identity": {
            "authorization": {
                ...
            },
            "claims": {
                ...
            }
        },
        "level": "Information",
        "location": "global",
        "properties": {
            ...
        }
    },
    {
        "time": "2018-12-26T16:23:06.3040244Z",
        "resourceId": "/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resource-group>/PROVIDERS/MICROSOFT.WEB/SITES/CLNB5F73B70-DCA2-47C2-BB24-77B1A2CAAB4D/PROVIDERS/MICROSOFT.AUTHORIZATION",
        "operationName": "MICROSOFT.AUTHORIZATION/CHECKACCESS/ACTION",
        "category": "Action",
        "resultType": "Success",
        "resultSignature": "Succeeded.OK",
        "durationMs": 194,
        "callerIpAddress": "13.66.225.188",
        "correlationId": "0de9f4bc-4adc-4209-a774-1b4f4ae573ed",
        "identity": {
            "authorization": {
                ...
            },
            "claims": {
                ...
            }
        },
        "level": "Information",
        "location": "global",
        "properties": {
            "statusCode": "OK",
            "serviceRequestId": "87acdebc-945f-4c0c-b931-03050e085626"
        }
    }]
}
```
---

## <a name="set-up-an-ingestion-pipeline-in-azure-data-explorer"></a>Konfigurowanie potoku pozyskiwania w usłudze Azure Data Explorer

Konfigurowanie potoku w usłudze Azure Data Explorer obejmuje kilka kroków, takich jak [tworzenie tabeli i pozyskiwanie danych](/azure/data-explorer/ingest-sample-data#ingest-data). Można również modyfikować, mapować i aktualizować dane.

### <a name="connect-to-the-azure-data-explorer-web-ui"></a>Łączenie z internetowym interfejsem użytkownika usługi Azure Data Explorer

W bazie danych *TestDatabase* usługi Azure Data Explorer wybierz pozycję **Zapytanie**, aby otworzyć internetowy interfejs użytkownika usługi Azure Data Explorer.

![Strona zapytań](media/ingest-data-no-code/query-database.png)

### <a name="create-the-target-tables"></a>Tworzenie tabel docelowych

Struktura dzienników usługi Azure Monitor nie jest tabelaryczne. Będziesz manipulować danymi i rozwijać każde zdarzenie do jednego lub więcej rekordów. Nieprzetworzone dane zostaną pojąne do tabeli pośredniej o nazwie *ActivityLogsRawRecords* dla dzienników działań i *DiagnosticRawRecords* dla metryk diagnostycznych i dzienników. W tym momencie dane będą modyfikowane i rozwijane. Przy użyciu zasad aktualizacji, rozszerzone dane zostaną następnie pochłonięta do *activitylogs* tabeli dzienników aktywności, *DiagnosticMetrics* dla metryk diagnostycznych i *DiagnosticLogs* dla dzienników diagnostycznych. Oznacza to, że należy utworzyć dwie oddzielne tabele do pozyskiwania dzienników aktywności i trzy oddzielne tabele do pozyskiwania metryk diagnostycznych i dzienników.

Do utworzenia tabel docelowych w bazie danych usługi Azure Data Explorer użyj internetowego interfejsu użytkownika usługi Azure Data Explorer.

# <a name="diagnostic-metrics"></a>[Metryki diagnostyczne](#tab/diagnostic-metrics)
#### <a name="create-tables-for-the-diagnostic-metrics"></a>Tworzenie tabel dla metryk diagnostycznych

1. W bazie danych *TestDatabase* utwórz tabelę o nazwie *DiagnosticMetrics* do przechowywania rekordów metryk diagnostycznych. Użyj następującego polecenia kontroli `.create table`:

    ```kusto
    .create table DiagnosticMetrics (Timestamp:datetime, ResourceId:string, MetricName:string, Count:int, Total:double, Minimum:double, Maximum:double, Average:double, TimeGrain:string)
    ```

1. Wybierz pozycję **Uruchom**, aby utworzyć tabelę.

    ![Uruchamianie zapytania](media/ingest-data-no-code/run-query.png)

1. Utwórz tabelę danych pośrednich o nazwie *DiagnosticRawRecords* w bazie danych *TestDatabase* do manipulowania danymi przy użyciu następującej kwerendy. Wybierz pozycję **Uruchom**, aby utworzyć tabelę.

    ```kusto
    .create table DiagnosticRawRecords (Records:dynamic)
    ```

1. Ustaw [zasady zerowego przechowywania](/azure/kusto/management/retention-policy) dla tabeli pośredniej:

    ```kusto
    .alter-merge table DiagnosticRawRecords policy retention softdelete = 0d
    ```

# <a name="diagnostic-logs"></a>[Dzienniki diagnostyczne](#tab/diagnostic-logs)
#### <a name="create-tables-for-the-diagnostic-logs"></a>Tworzenie tabel dla dzienników diagnostycznych 

1. W bazie danych *TestDatabase* utwórz tabelę o nazwie *DiagnosticLogs* do przechowywania rekordów dziennika diagnostycznego. Użyj następującego polecenia kontroli `.create table`:

    ```kusto
    .create table DiagnosticLogs (Timestamp:datetime, ResourceId:string, OperationName:string, Result:string, OperationId:string, Database:string, Table:string, IngestionSourceId:string, IngestionSourcePath:string, RootActivityId:string, ErrorCode:string, FailureStatus:string, Details:string)
    ```

1. Wybierz pozycję **Uruchom**, aby utworzyć tabelę.

1. Utwórz tabelę danych pośrednich o nazwie *DiagnosticRawRecords* w bazie danych *TestDatabase* do manipulowania danymi przy użyciu następującej kwerendy. Wybierz pozycję **Uruchom**, aby utworzyć tabelę.

    ```kusto
    .create table DiagnosticRawRecords (Records:dynamic)
    ```

1. Ustaw [zasady zerowego przechowywania](/azure/kusto/management/retention-policy) dla tabeli pośredniej:

    ```kusto
    .alter-merge table DiagnosticRawRecords policy retention softdelete = 0d
    ```

# <a name="activity-logs"></a>[Dzienniki aktywności](#tab/activity-logs)
#### <a name="create-tables-for-the-activity-logs"></a>Tworzenie tabel dla dzienników aktywności 

1. Utwórz tabelę o nazwie *ActivityLogs* w bazie danych *TestDatabase,* aby odbierać rekordy dziennika aktywności. Uruchom następujące zapytanie usługi Azure Data Explorer w celu utworzenia tabeli:

    ```kusto
    .create table ActivityLogs (Timestamp:datetime, ResourceId:string, OperationName:string, Category:string, ResultType:string, ResultSignature:string, DurationMs:int, IdentityAuthorization:dynamic, IdentityClaims:dynamic, Location:string, Level:string)
    ```

1. Utwórz tabelę danych pośrednich o nazwie *ActivityLogsRawRecords* w bazie danych *TestDatabase* na potrzeby modyfikowania danych:

    ```kusto
    .create table ActivityLogsRawRecords (Records:dynamic)
    ```

1. Ustaw [zasady zerowego przechowywania](/azure/kusto/management/retention-policy) dla tabeli pośredniej:

    ```kusto
    .alter-merge table ActivityLogsRawRecords policy retention softdelete = 0d
    ```
---

### <a name="create-table-mappings"></a>Tworzenie mapowań tabel

 Format danych to `json`, dlatego wymagane jest mapowanie danych. Mapowanie `json` mapuje każdą ścieżkę JSON na nazwę kolumny tabeli.

# <a name="diagnostic-metrics--diagnostic-logs"></a>[Metryki diagnostyczne / Dzienniki diagnostyczne](#tab/diagnostic-metrics+diagnostic-logs) 
#### <a name="map-diagnostic-metrics-and-logs-to-the-table"></a>Mapowanie metryk diagnostycznych i dzienników do tabeli

Aby zamapować dane diagnostyczne i dane dziennika do tabeli, użyj następującej kwerendy:

```kusto
.create table DiagnosticRawRecords ingestion json mapping 'DiagnosticRawRecordsMapping' '[{"column":"Records","path":"$.records"}]'
```

# <a name="activity-logs"></a>[Dzienniki aktywności](#tab/activity-logs)
#### <a name="map-activity-logs-to-the-table"></a>Mapowanie dzienników aktywności do tabeli

Aby zamapować dane dziennika aktywności do tabeli, użyj następującej kwerendy:

```kusto
.create table ActivityLogsRawRecords ingestion json mapping 'ActivityLogsRawRecordsMapping' '[{"column":"Records","path":"$.records"}]'
```
---

### <a name="create-the-update-policy-for-metric-and-log-data"></a>Tworzenie zasad aktualizacji danych metryk i dzienników

# <a name="diagnostic-metrics"></a>[Metryki diagnostyczne](#tab/diagnostic-metrics)
#### <a name="create-data-update-policy-for-diagnostics-metrics"></a>Tworzenie zasad aktualizacji danych dla metryk diagnostycznych

1. Utwórz [funkcję,](/azure/kusto/management/functions) która rozszerza kolekcję rekordów metryk diagnostycznych, tak aby każda wartość w kolekcji otrzymała oddzielny wiersz. Użyj [`mv-expand`](/azure/kusto/query/mvexpandoperator) operatora:
     ```kusto
    .create function DiagnosticMetricsExpand() {
        DiagnosticRawRecords
        | mv-expand events = Records
        | where isnotempty(events.metricName)
        | project
            Timestamp = todatetime(events['time']),
            ResourceId = tostring(events.resourceId),
            MetricName = tostring(events.metricName),
            Count = toint(events['count']),
            Total = todouble(events.total),
            Minimum = todouble(events.minimum),
            Maximum = todouble(events.maximum),
            Average = todouble(events.average),
            TimeGrain = tostring(events.timeGrain)
    }
    ```

2. Dodaj [zasady aktualizacji](/azure/kusto/concepts/updatepolicy) do tabeli docelowej. Ta zasada automatycznie uruchomi kwerendę na wszystkich nowo pochłoniętych danych w tabeli danych *pośrednich DiagnosticRawRecords* i pochłonie jej wyniki w tabeli *DiagnosticMetrics:*

    ```kusto
    .alter table DiagnosticMetrics policy update @'[{"Source": "DiagnosticRawRecords", "Query": "DiagnosticMetricsExpand()", "IsEnabled": "True", "IsTransactional": true}]'
    ```

# <a name="diagnostic-logs"></a>[Dzienniki diagnostyczne](#tab/diagnostic-logs)
#### <a name="create-data-update-policy-for-diagnostics-logs"></a>Tworzenie zasad aktualizacji danych dla dzienników diagnostycznych

1. Utwórz [funkcję,](/azure/kusto/management/functions) która rozszerza kolekcję rekordów dzienników diagnostycznych, tak aby każda wartość w kolekcji odbierała osobny wiersz. Włączysz dzienniki pozyskiwania w klastrze usługi Azure Data Explorer i [użyjesz schematu dzienników pozyskiwania.](/azure/data-explorer/using-diagnostic-logs#diagnostic-logs-schema) Utworzysz jedną tabelę dla pomyślnego i nieudanego pozyskiwania, podczas gdy niektóre pola będą puste dla pomyślnego pozyskiwania (na przykład Kod błędu). Użyj [`mv-expand`](/azure/kusto/query/mvexpandoperator) operatora:

    ```kusto
    .create function DiagnosticLogsExpand() {
        DiagnosticRawRecords
        | mv-expand events = Records
        | where isnotempty(events.operationName)
        | project
            Timestamp = todatetime(events['time']),
            ResourceId = tostring(events.resourceId),
            OperationName = tostring(events.operationName),
            Result = tostring(events.resultType),
            OperationId = tostring(events.properties.OperationId),
            Database = tostring(events.properties.Database),
            Table = tostring(events.properties.Table),
            IngestionSourceId = tostring(events.properties.IngestionSourceId),
            IngestionSourcePath = tostring(events.properties.IngestionSourcePath),
            RootActivityId = tostring(events.properties.RootActivityId),
            ErrorCode = tostring(events.properties.ErrorCode),
            FailureStatus = tostring(events.properties.FailureStatus),
            Details = tostring(events.properties.Details)
    }
    ```

2. Dodaj [zasady aktualizacji](/azure/kusto/concepts/updatepolicy) do tabeli docelowej. Ta zasada automatycznie uruchomi kwerendę na wszystkich nowo pochłoniętych danych w tabeli danych *pośrednich DiagnosticRawRecords* i pochłonie jej wyniki w tabeli *DiagnosticLogs:*

    ```kusto
    .alter table DiagnosticLogs policy update @'[{"Source": "DiagnosticRawRecords", "Query": "DiagnosticLogsExpand()", "IsEnabled": "True", "IsTransactional": true}]'
    ```

# <a name="activity-logs"></a>[Dzienniki aktywności](#tab/activity-logs)
#### <a name="create-data-update-policy-for-activity-logs"></a>Tworzenie zasad aktualizacji danych dla dzienników aktywności

1. Utwórz [funkcję,](/azure/kusto/management/functions) która rozszerza kolekcję rekordów dziennika aktywności, tak aby każda wartość w kolekcji otrzymała osobny wiersz. Użyj [`mv-expand`](/azure/kusto/query/mvexpandoperator) operatora:

    ```kusto
    .create function ActivityLogRecordsExpand() {
        ActivityLogsRawRecords
        | mv-expand events = Records
        | project
            Timestamp = todatetime(events['time']),
            ResourceId = tostring(events.resourceId),
            OperationName = tostring(events.operationName),
            Category = tostring(events.category),
            ResultType = tostring(events.resultType),
            ResultSignature = tostring(events.resultSignature),
            DurationMs = toint(events.durationMs),
            IdentityAuthorization = events.identity.authorization,
            IdentityClaims = events.identity.claims,
            Location = tostring(events.location),
            Level = tostring(events.level)
    }
    ```

2. Dodaj [zasady aktualizacji](/azure/kusto/concepts/updatepolicy) do tabeli docelowej. Ta zasada automatycznie uruchomi kwerendę na wszystkich nowo przyciągniętych danych w tabeli danych *pośrednich ActivityLogsRawRecords* i pochłonie jej wyniki w tabeli *ActivityLogs:*

    ```kusto
    .alter table ActivityLogs policy update @'[{"Source": "ActivityLogsRawRecords", "Query": "ActivityLogRecordsExpand()", "IsEnabled": "True", "IsTransactional": true}]'
    ```
---

## <a name="create-an-azure-event-hubs-namespace"></a>Tworzenie przestrzeni nazw usługi Azure Event Hubs

Ustawienia diagnostyczne platformy Azure umożliwiają eksportowanie metryk i dzienników do konta magazynu lub do centrum zdarzeń. W tym samouczku będziemy metryki i dzienniki za pośrednictwem centrum zdarzeń. Utworzysz obszar nazw centrum zdarzeń i centrum zdarzeń dla metryk diagnostycznych i dzienników w poniższych krokach. Usługa Azure Monitor utworzy centrum zdarzeń *insights-operational-logs* na potrzeby dzienników aktywności.

1. Utwórz centrum zdarzeń przy użyciu szablonu usługi Azure Resource Manager w witrynie Azure Portal. Aby wykonać pozostałe kroki w tym artykule, kliknij prawym przyciskiem myszy przycisk **Wdróż na platformie Azure** i wybierz pozycję **Otwórz w nowym oknie**. Przycisk **Wdróż na platformie Azure** powoduje przejście do witryny Azure Portal.

    [![Przycisk Wdrażanie na platformie Azure](media/ingest-data-no-code/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-event-hubs-create-event-hub-and-consumer-group%2Fazuredeploy.json)

1. Utwórz centrum zdarzeń i przestrzeń nazw usługi Event Hubs na potrzeby dzienników diagnostycznych.

    ![Tworzenie centrum zdarzeń](media/ingest-data-no-code/event-hub.png)

1. Wypełnij formularz, używając poniższych informacji. W przypadku wszystkich ustawień, które nie są wymienione w poniższej tabeli, użyj wartości domyślnych.

    **Ustawienie** | **Sugerowana wartość** | **Opis**
    |---|---|---|
    | **Subskrypcja** | *Twoja subskrypcja* | Wybierz subskrypcję platformy Azure, która ma być używana dla centrum zdarzeń.|
    | **Grupa zasobów** | *grupa test-zasób* | Utwórz nową grupę zasobów. |
    | **Lokalizacja** | Wybierz region, który najlepiej odpowiada Twoim potrzebom. | Utwórz przestrzeń nazw usługi Event Hubs w tej samej lokalizacji co inne zasoby.
    | **Nazwa przestrzeni nazw** | *AzureMonitoringData* | Wybierz unikatową nazwę, która identyfikuje Twoją przestrzeń nazw.
    | **Nazwa centrum zdarzeń** | *Dane diagnostyczne* | Centrum zdarzeń znajduje się w przestrzeni nazw, która zapewnia unikatowy kontener określania zakresu. |
    | **Nazwa grupy konsumentów** | *adxpipeline* | Utwórz nazwę grupy użytkowników. Dzięki grupom konsumentów każda z wielu aplikacji korzystających z danych może mieć osobny widok strumienia zdarzeń. |
    | | |

## <a name="connect-azure-monitor-metrics-and-logs-to-your-event-hub"></a>Łączenie metryk i dzienników usługi Azure Monitor z centrum zdarzeń

Teraz musisz połączyć metryki diagnostyczne i dzienniki i dzienniki aktywności z centrum zdarzeń.

# <a name="diagnostic-metrics--diagnostic-logs"></a>[Metryki diagnostyczne / Dzienniki diagnostyczne](#tab/diagnostic-metrics+diagnostic-logs) 
### <a name="connect-diagnostic-metrics-and-logs-to-your-event-hub"></a>Łączenie metryk diagnostycznych i dzienników z centrum zdarzeń

Wybierz zasób, z którego chcesz eksportować metryki. Kilka typów zasobów obsługuje eksportowanie danych diagnostycznych, w tym obszar nazw usługi Event Hubs, usługa Azure Key Vault, usługa Azure IoT Hub i klastry usługi Azure Data Explorer. W tym samouczku użyjemy klastra usługi Azure Data Explorer jako naszego zasobu, przejrzymy metryki wydajności zapytań i dzienniki wyników pozyskiwania.

1. Wybierz klaster usługi Kusto w witrynie Azure Portal.
1. Wybierz pozycję **Ustawienia diagnostyczne**, a następnie wybierz link **Włącz diagnostykę**. 

    ![Ustawienia diagnostyczne](media/ingest-data-no-code/diagnostic-settings.png)

1. Zostanie otwarte okienko **Ustawienia diagnostyczne**. Wykonaj następujące kroki:
   1. Nadaj danym z dziennika diagnostycznego nazwę *ADXExportedData*.
   1. W obszarze **DZIENNIK**zaznacz pola wyboru **Ponieś udział** w dzienniku i ponieśniu **powodem** niepowodzenia.
   1. W **obszarze METRYKA**zaznacz pole wyboru **Wydajność kwerendy.**
   1. Zaznacz pole wyboru **Przesyłaj strumieniowo do centrum zdarzeń**.
   1. Wybierz pozycję **Konfiguruj**.

      ![Okienko ustawień diagnostycznych](media/ingest-data-no-code/diagnostic-settings-window.png)

1. W okienku **Wybieranie centrum zdarzeń** skonfiguruj sposób eksportowania danych z dzienników diagnostycznych do utworzonego centrum zdarzeń:
    1. Z listy **Wybierz przestrzeń nazw centrum zdarzeń** wybierz pozycję *AzureMonitoringData*.
    1. Na liście **Wybierz nazwę centrum zdarzeń** wybierz pozycję *DiagnosticData*.
    1. Z listy **Wybierz nazwę zasad centrum zdarzeń** wybierz pozycję **RootManagerSharedAccessKey**.
    1. Kliknij przycisk **OK**.

1. Wybierz **pozycję Zapisz**.

# <a name="activity-logs"></a>[Dzienniki aktywności](#tab/activity-logs)
### <a name="connect-activity-logs-to-your-event-hub"></a>Łączenie dzienników aktywności z centrum zdarzeń

1. W menu po lewej stronie w witrynie Azure Portal wybierz pozycję **Dziennik aktywności**.
1. Zostanie otwarte okno **Dziennik aktywności**. Wybierz pozycję **Eksportuj do centrum zdarzeń**.

    ![Okno Dziennik aktywności](media/ingest-data-no-code/activity-log.png)

1. Zostanie otwarte okno **Eksportuj dziennik aktywności**:
 
    ![Okno Eksportuj dziennik aktywności](media/ingest-data-no-code/export-activity-log.png)

1. W oknie **Eksportuj dziennik aktywności** wykonaj następujące czynności:
      1. Wybierz subskrypcję.
      1. Z listy **Regiony** wybierz pozycję **Zaznacz wszystko**.
      1. Zaznacz pole wyboru **Eksportuj do centrum zdarzeń**.
      1. Wybierz pozycję **Wybierz przestrzeń nazw usługi Service Bus**, aby otworzyć okienko **Wybierz centrum zdarzeń**.
      1. W okienku **Wybierz centrum zdarzeń** wybierz swoją subskrypcję.
      1. Z listy **Wybierz przestrzeń nazw centrum zdarzeń** wybierz pozycję *AzureMonitoringData*.
      1. Z listy **Wybierz nazwę zasad centrum zdarzeń** wybierz nazwę domyślnych zasad centrum zdarzeń.
      1. Kliknij przycisk **OK**.
      1. W lewym górnym rogu okna wybierz pozycję **Zapisz**.
   Zostanie utworzone centrum zdarzeń o nazwie *insights-operational-logs*.
---

### <a name="see-data-flowing-to-your-event-hubs"></a>Wyświetlanie danych przepływających do centrum zdarzeń

1. Poczekaj kilka minut, aż połączenie zostanie zdefiniowane i zakończy się eksport dziennika aktywności do centrum zdarzeń. Przejdź do przestrzeni nazw usługi Event Hubs, aby wyświetlić utworzone centra zdarzeń.

    ![Utworzone centra zdarzeń](media/ingest-data-no-code/event-hubs-created.png)

1. Zapoznaj się z danymi przepływającymi do centrum zdarzeń:

    ![Dane centrum zdarzeń](media/ingest-data-no-code/event-hubs-data.png)

## <a name="connect-an-event-hub-to-azure-data-explorer"></a>Łączenie centrum zdarzeń z usługą Azure Data Explorer

Teraz musisz utworzyć połączenia danych dla metryk diagnostycznych i dzienników i dzienników aktywności.

### <a name="create-the-data-connection-for-diagnostic-metrics-and-logs-and-activity-logs"></a>Tworzenie połączenia danych dla metryk diagnostycznych, dzienników i dzienników aktywności

1. W klastrze usługi Azure Data Explorer o nazwie *kustodocs* wybierz pozycję **Bazy danych** w menu po lewej stronie.
1. W oknie **Bazy danych** wybierz nazwę bazy danych *TestDatabase*.
1. W menu po lewej stronie wybierz **pozycję Połkowanie danych**.
1. W oknie **Pozyskiwania danych** kliknij pozycję + Dodaj **połączenie danych**.
1. W oknie **Połączenie danych** wprowadź następujące informacje:

    ![Połączenie danych centrum zdarzeń](media/ingest-data-no-code/event-hub-data-connection.png)

# <a name="diagnostic-metrics--diagnostic-logs"></a>[Metryki diagnostyczne / Dzienniki diagnostyczne](#tab/diagnostic-metrics+diagnostic-logs) 

1. Użyj następujących ustawień w oknie **Połączenie danych**:

    Źródło danych:

    **Ustawienie** | **Sugerowana wartość** | **Opis pola**
    |---|---|---|
    | **Nazwa połączenia danych** | *DiagnosticsLogsConnection* | Nazwa połączenia, które chcesz utworzyć w usłudze Azure Data Explorer.|
    | **Obszar nazw centrum zdarzeń** | *AzureMonitoringData* | Wybrana wcześniej nazwa, która identyfikuje Twoją przestrzeń nazw. |
    | **Centrum zdarzeń** | *Dane diagnostyczne* | Utworzone przez Ciebie centrum zdarzeń. |
    | **Grupa konsumentów** | *adxpipeline* | Grupa konsumentów zdefiniowana w utworzonym przez Ciebie centrum zdarzeń. |
    | | |

    Tabela docelowa:

    Dostępne są dwie opcje routingu: *statyczny* i *dynamiczny*. W tym samouczku będziesz używać routingu statycznego (opcja domyślna), w którym określisz nazwę tabeli, format danych i mapowanie. Pozostaw pole **Moje dane zawierają informacje o routingu** niezaznaczone.

     **Ustawienie** | **Sugerowana wartość** | **Opis pola**
    |---|---|---|
    | **Tabeli** | *Rekordy diagnostyczne* | Tabela utworzona w bazie danych *TestDatabase*. |
    | **Format danych** | *Json* | Format używany w tabeli. |
    | **Mapowanie kolumn** | *DiagnostykaRawRecordsMapping* | Mapowanie utworzone w bazie danych *TestDatabase,* która mapuje przychodzące dane JSON do nazw kolumn i typów danych w tabeli *DiagnosticRawRecords.*|
    | | |

1. Wybierz **pozycję Utwórz**.  

# <a name="activity-logs"></a>[Dzienniki aktywności](#tab/activity-logs)

1. Użyj następujących ustawień w oknie **Połączenie danych**:

    Źródło danych:

    **Ustawienie** | **Sugerowana wartość** | **Opis pola**
    |---|---|---|
    | **Nazwa połączenia danych** | *ActivityLogsConnection* | Nazwa połączenia, które chcesz utworzyć w usłudze Azure Data Explorer.|
    | **Obszar nazw centrum zdarzeń** | *AzureMonitoringData* | Wybrana wcześniej nazwa, która identyfikuje Twoją przestrzeń nazw. |
    | **Centrum zdarzeń** | *insights-operational-logs* | Utworzone przez Ciebie centrum zdarzeń. |
    | **Grupa konsumentów** | *$Default* | Domyślna grupa użytkowników. W razie potrzeby możesz utworzyć inną grupę użytkowników. |
    | | |

    Tabela docelowa:

    Dostępne są dwie opcje routingu: *statyczny* i *dynamiczny*. W tym samouczku będziesz używać routingu statycznego (opcja domyślna), w którym określisz nazwę tabeli, format danych i mapowanie. Pozostaw pole **Moje dane zawierają informacje o routingu** niezaznaczone.

     **Ustawienie** | **Sugerowana wartość** | **Opis pola**
    |---|---|---|
    | **Tabeli** | *ActivityLogsRawRecords* | Tabela utworzona w bazie danych *TestDatabase*. |
    | **Format danych** | *Json* | Format używany w tabeli. |
    | **Mapowanie kolumn** | *ActivityLogsRawRecordsMapping* | Mapowanie utworzone w bazie danych *TestDatabase*, które mapuje przychodzące dane JSON na nazwy kolumn i typy danych tabeli *ActivityLogsRawRecords*.|
    | | |

1. Wybierz **pozycję Utwórz**.  
---

## <a name="query-the-new-tables"></a>Tworzenie zapytań dotyczących nowych tabel

Masz teraz potok z przepływającymi danymi. Pozyskiwanie za pośrednictwem klastra trwa domyślnie 5 minut, co pozwala na przepływ danych przez kilka minut przed rozpoczęciem tworzenia zapytania.

# <a name="diagnostic-metrics"></a>[Metryki diagnostyczne](#tab/diagnostic-metrics)
### <a name="query-the-diagnostic-metrics-table"></a>Zapytanie do tabeli metryk diagnostycznych

Następująca kwerenda analizuje dane dotyczące czasu trwania kwerendy z rekordów metryk diagnostycznych w Eksploratorze danych platformy Azure:

```kusto
DiagnosticMetrics
| where Timestamp > ago(15m) and MetricName == 'QueryDuration'
| summarize avg(Average)
```

Wyniki zapytania:

|   |   |
| --- | --- |
|   |  avg_Average |
|   | 00:06,156 |
| | |

# <a name="diagnostic-logs"></a>[Dzienniki diagnostyczne](#tab/diagnostic-logs)
### <a name="query-the-diagnostic-logs-table"></a>Zapytanie do tabeli dzienników diagnostycznych

Ten potok generuje pozyskiwania za pośrednictwem centrum zdarzeń. Będziesz przeglądać wyniki tych spożycia.
Następująca kwerenda analizuje liczbę połów naliczonych w ciągu `Database` `Table` minuty, w tym próbkę i `IngestionSourcePath` dla każdego interwału:

```kusto
DiagnosticLogs
| where Timestamp > ago(15m) and OperationName has 'INGEST'
| summarize count(), any(Database, Table, IngestionSourcePath) by bin(Timestamp, 1m)
```

Wyniki zapytania:

|   |   |
| --- | --- |
|   |  count_ | any_Database | any_Table | any_IngestionSourcePath
|   | 00:06,156 | TestDatabase | Rekordy diagnostyczne | https://rtmkstrldkereneus00.blob.core.windows.net/20190827-readyforaggregation/1133_TestDatabase_DiagnosticRawRecords_6cf02098c0c74410bd8017c2d458b45d.json.zip
| | |

# <a name="activity-logs"></a>[Dzienniki aktywności](#tab/activity-logs)
### <a name="query-the-activity-logs-table"></a>Kwerenda w tabeli dzienników aktywności

Następujące zapytanie analizuje dane z rekordów dziennika aktywności w usłudze Azure Data Explorer:

```kusto
ActivityLogs
| where OperationName == 'MICROSOFT.EVENTHUB/NAMESPACES/AUTHORIZATIONRULES/LISTKEYS/ACTION'
| where ResultType == 'Success'
| summarize avg(DurationMs)
```

Wyniki zapytania:

|   |   |
| --- | --- |
|   |  avg(DurationMs) |
|   | 768,333 |
| | |

---

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak zapisywać o wiele więcej zapytań dotyczących danych wyodrębnionych z Eksploratora danych platformy Azure przy użyciu [zapytań do zapisu dla Eksploratora danych platformy Azure.](write-queries.md)
* [Monitorowanie operacji pozyskiwania usługi Azure Data Explorer przy użyciu dzienników diagnostycznych](using-diagnostic-logs.md)
* [Monitorowanie kondycji klastra przy użyciu metryk](using-metrics.md)
