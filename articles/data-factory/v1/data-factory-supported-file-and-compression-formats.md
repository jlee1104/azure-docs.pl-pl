---
title: Formaty plików i kompresji w usłudze Azure Data Factory
description: Dowiedz się więcej o formatach plików obsługiwanych przez usługę Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 901e15994b8a51a5fd45d57ca7a4db7778d968e1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281615"
---
# <a name="file-and-compression-formats-supported-by-azure-data-factory"></a>Formaty plików i kompresji obsługiwane przez usługę Azure Data Factory
*W tym temacie stosuje się następujące łączniki: [Amazon S3](data-factory-amazon-simple-storage-service-connector.md), [Azure Blob](data-factory-azure-blob-connector.md), [Azure Data Lake Store](data-factory-azure-datalake-connector.md), System [plików](data-factory-onprem-file-system-connector.md), [FTP](data-factory-ftp-connector.md), [HDFS](data-factory-hdfs-connector.md), [HTTP](data-factory-http-connector.md)i [SFTP](data-factory-sftp-connector.md).*

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Azure Data Factory. Jeśli używasz bieżącej wersji usługi Data Factory, zobacz [obsługiwane formaty plików i kodeki kompresji w ustawieniach Data Factory](../supported-file-formats-and-compression-codecs.md).

Usługa Azure Data Factory obsługuje następujące typy formatów plików:

* [Format tekstu](#text-format)
* [Format JSON](#json-format)
* [Format Avro](#avro-format)
* [Format ORC](#orc-format)
* [Format parkietu](#parquet-format)

## <a name="text-format"></a>Format tekstu
Jeśli chcesz odczytać z pliku tekstowego lub zapisać do `type` pliku `format` tekstowego, ustaw właściwość w sekcji zestawu danych na **TextFormat**. Ponadto możesz określić następujące **opcjonalne** właściwości w sekcji `format`. Aby uzyskać informacje na temat sposobu konfigurowania, zobacz sekcję [Przykład formatu TextFormat](#textformat-example).

| Właściwość | Opis | Dozwolone wartości | Wymagany |
| --- | --- | --- | --- |
| columnDelimiter |Znak używany do rozdzielania kolumn w pliku. Można rozważyć użycie rzadkiego, niedrukowalne char, który może nie istnieć w danych. Na przykład określ "\u0001", który reprezentuje początek nagłówka (SOH). |Dozwolony jest tylko jeden znak. Wartość **domyślna** to **przecinek (,)**. <br/><br/>Aby użyć znaku Unicode, należy zapoznać się ze [znakami Unicode,](https://en.wikipedia.org/wiki/List_of_Unicode_characters) aby uzyskać odpowiedni kod dla niego. |Nie |
| rowDelimiter |Znak używany do rozdzielania wierszy w pliku. |Dozwolony jest tylko jeden znak. Wartością **domyślną** jest dowolna z następujących wartości przy odczycie: **[„\r\n”, „\r”, „\n”]** oraz wartość **„\r\n”** przy zapisie. |Nie |
| escapeChar |Znak specjalny służący do zmiany interpretacji ogranicznika kolumny w zawartości pliku wejściowego. <br/><br/>W przypadku tabeli nie można określić zarówno właściwości escapeChar, jak i quoteChar. |Dozwolony jest tylko jeden znak. Brak wartości domyślnej. <br/><br/>Przykład: jeśli ogranicznikiem kolumny jest przecinek (,), ale chcesz, aby znak przecinka występował w tekście (przykładowo: „Witaj, świecie”), możesz zdefiniować znak „$” jako znak ucieczki i użyć ciągu „Witaj$, świecie” w źródle. |Nie |
| quoteChar |Znak używany do umieszczania wartości ciągu w cudzysłowie. Ograniczniki kolumny i wiersza umieszczone w cudzysłowie są traktowane jako część wartości ciągu. Ta właściwość ma zastosowanie zarówno do wejściowych, jak i wyjściowych zestawów danych.<br/><br/>W przypadku tabeli nie można określić zarówno właściwości escapeChar, jak i quoteChar. |Dozwolony jest tylko jeden znak. Brak wartości domyślnej. <br/><br/>Na przykład jeśli ogranicznikiem kolumny jest przecinek (,), ale chcesz, aby znak przecinka występował w tekście (przykład: <Witaj, świecie>), możesz zdefiniować cudzysłów (") jako znak cudzysłowu i użyć ciągu "Witaj, świecie" w źródle. |Nie |
| nullValue |Co najmniej jeden znak służący do reprezentowania wartości null. |Co najmniej jeden znak. Wartości **domyślne** to **„\N” i „NULL”** przy odczycie oraz **„\N”** przy zapisie. |Nie |
| encodingName |Określa nazwę kodowania. |Prawidłowa nazwa kodowania. Zobacz [właściwość Encoding.EncodingName](https://msdn.microsoft.com/library/system.text.encoding.aspx). Przykład: windows-1250 lub shift_jis. Wartość **domyślna** to **UTF-8**. |Nie |
| firstRowAsHeader |Określa, czy pierwszy wiersz ma być traktowany jako nagłówek. W przypadku zestawu danych wejściowych usługa Data Factory odczytuje pierwszy wiersz jako nagłówek. W przypadku zestawu danych wyjściowych usługa Data Factory zapisuje pierwszy wiersz jako nagłówek. <br/><br/>Aby uzyskać przykładowe scenariusze, zobacz sekcję [Scenariusze użycia właściwości `firstRowAsHeader` oraz `skipLineCount`](#scenarios-for-using-firstrowasheader-and-skiplinecount). |True<br/><b>False (domyślnie)</b> |Nie |
| skipLineCount |Wskazuje liczbę wierszy do pominięcia podczas odczytywania danych z plików wejściowych. Jeśli określono zarówno właściwość skipLineCount, jak i firstRowAsHeader, najpierw zostaną pominięte wiersze, a następnie zostaną odczytane informacje nagłówka z pliku wejściowego. <br/><br/>Aby uzyskać przykładowe scenariusze, zobacz sekcję [Scenariusze użycia właściwości `firstRowAsHeader` oraz `skipLineCount`](#scenarios-for-using-firstrowasheader-and-skiplinecount). |Liczba całkowita |Nie |
| treatEmptyAsNull |Określa, czy ciąg pusty lub o wartości null ma być traktowany jako wartość null podczas odczytu danych z pliku wejściowego. |**True (domyślnie)**<br/>False |Nie |

### <a name="textformat-example"></a>Przykład formatu TextFormat
W poniższej definicji JSON dla zestawu danych niektóre właściwości opcjonalne są określone.

```json
"typeProperties":
{
    "folderPath": "mycontainer/myfolder",
    "fileName": "myblobname",
    "format":
    {
        "type": "TextFormat",
        "columnDelimiter": ",",
        "rowDelimiter": ";",
        "quoteChar": "\"",
        "NullValue": "NaN",
        "firstRowAsHeader": true,
        "skipLineCount": 0,
        "treatEmptyAsNull": true
    }
},
```

Aby użyć właściwości `escapeChar` zamiast `quoteChar`, zastąp wiersz z właściwością `quoteChar` następującą właściwością escapeChar:

```json
"escapeChar": "$",
```

### <a name="scenarios-for-using-firstrowasheader-and-skiplinecount"></a>Scenariusze użycia właściwości firstRowAsHeader oraz skipLineCount
* Kopiujesz dane ze źródła innego niż plik do pliku tekstowego i chcesz dodać wiersz nagłówka zawierający metadane schematu (na przykład: schemat SQL). Ustaw właściwość `firstRowAsHeader` na wartość true w zestawie danych wyjściowych dla tego scenariusza.
* Kopiujesz dane z pliku tekstowego zawierającego wiersz nagłówka do ujścia innego niż plik i chcesz pominąć ten wiersz. Ustaw właściwość `firstRowAsHeader` na wartość true w zestawie danych wejściowych.
* Kopiujesz dane z pliku tekstowego i chcesz pominąć kilka początkowych wierszy, które nie zawierają żadnych danych bądź informacji nagłówka. Określ właściwość `skipLineCount`, aby wskazać liczbę wierszy do pominięcia. Jeśli pozostała część pliku zawiera wiersz nagłówka, możesz również określić właściwość `firstRowAsHeader`. Jeśli określono zarówno właściwość `skipLineCount`, jak i `firstRowAsHeader`, najpierw zostaną pominięte wiersze, a następnie zostaną odczytane informacje nagłówka z pliku wejściowego

## <a name="json-format"></a>Format JSON
Aby **zaimportować/wyeksportować plik JSON jako — do/z usługi Azure Cosmos DB**, zobacz [Sekcję Importuj/eksportuj dokumenty JSON](data-factory-azure-documentdb-connector.md#importexport-json-documents) w [artykule Przenoszenie danych do/z usługi Azure Cosmos DB.](data-factory-azure-documentdb-connector.md)

Jeśli chcesz przeanalizować pliki JSON lub zapisać dane w formacie `type` JSON, `format` ustaw właściwość w sekcji na **JsonFormat**. Ponadto możesz określić następujące **opcjonalne** właściwości w sekcji `format`. Aby uzyskać informacje na temat sposobu konfigurowania, zobacz sekcję [Przykład formatu JsonFormat](#jsonformat-example).

| Właściwość | Opis | Wymagany |
| --- | --- | --- |
| filePattern |Wskazuje wzorzec danych przechowywanych w każdym pliku JSON. Dozwolone wartości to: **setOfObjects** i **arrayOfObjects**. Wartością **domyślną** jest **setOfObjects**. Aby uzyskać szczegółowe informacje o tych wzorcach, zobacz sekcję [Wzorce plików JSON](#json-file-patterns). |Nie |
| jsonNodeReference | Jeśli chcesz wykonać iterację i ekstrakcję danych z obiektów wewnątrz pola tablicy o tym samym wzorcu, określ ścieżkę JSON tej tablicy. Ta właściwość jest obsługiwana tylko podczas kopiowania danych z plików JSON. | Nie |
| jsonPathDefinition | Określa wyrażenie ścieżki JSON dla każdego mapowania kolumny z niestandardową nazwą kolumny (musi zaczynać się małą literą). Ta właściwość jest obsługiwana tylko podczas kopiowania danych z plików JSON; dane możesz wyodrębnić z obiektu lub tablicy. <br/><br/> W przypadku pól obiektu głównego na początku użyj elementu głównego $. W przypadku pól wewnątrz tablicy wybranej przez właściwość `jsonNodeReference` najpierw podaj element tablicy. Aby uzyskać informacje na temat sposobu konfigurowania, zobacz sekcję [Przykład formatu JsonFormat](#jsonformat-example). | Nie |
| encodingName |Określa nazwę kodowania. Aby uzyskać listę prawidłowych nazw kodowania, zobacz właściwość [Encoding.EncodingName](https://msdn.microsoft.com/library/system.text.encoding.aspx). Na przykład: windows-1250 lub shift_jis. Wartość **domyślna** to: **UTF-8**. |Nie |
| nestingSeparator |Znak używany do rozdzielania poziomów zagnieżdżenia. Wartość domyślna to „.” (kropka). |Nie |

### <a name="json-file-patterns"></a>Wzorce plików JSON

Działanie kopiowania może analizować następujące wzorce plików JSON:

- **Typ I: setOfObjects**

    Każdy plik zawiera pojedynczy obiekt lub wiele obiektów rozdzielonych wierszami bądź połączonych. W przypadku wybrania tej opcji w zestawie danych wyjściowych działanie kopiowania tworzy pojedynczy plik JSON z każdym obiektem w osobnym wierszu (rozdzielanie wierszami).

    * **przykład kodu JSON z pojedynczym obiektem**

        ```json
        {
            "time": "2015-04-29T07:12:20.9100000Z",
            "callingimsi": "466920403025604",
            "callingnum1": "678948008",
            "callingnum2": "567834760",
            "switch1": "China",
            "switch2": "Germany"
        }
        ```

    * **przykład kodu JSON z obiektami rozdzielonymi wierszami**

        ```json
        {"time":"2015-04-29T07:12:20.9100000Z","callingimsi":"466920403025604","callingnum1":"678948008","callingnum2":"567834760","switch1":"China","switch2":"Germany"}
        {"time":"2015-04-29T07:13:21.0220000Z","callingimsi":"466922202613463","callingnum1":"123436380","callingnum2":"789037573","switch1":"US","switch2":"UK"}
        {"time":"2015-04-29T07:13:21.4370000Z","callingimsi":"466923101048691","callingnum1":"678901578","callingnum2":"345626404","switch1":"Germany","switch2":"UK"}
        ```

    * **przykład kodu JSON z obiektami połączonymi**

        ```json
        {
            "time": "2015-04-29T07:12:20.9100000Z",
            "callingimsi": "466920403025604",
            "callingnum1": "678948008",
            "callingnum2": "567834760",
            "switch1": "China",
            "switch2": "Germany"
        }
        {
            "time": "2015-04-29T07:13:21.0220000Z",
            "callingimsi": "466922202613463",
            "callingnum1": "123436380",
            "callingnum2": "789037573",
            "switch1": "US",
            "switch2": "UK"
        }
        {
            "time": "2015-04-29T07:13:21.4370000Z",
            "callingimsi": "466923101048691",
            "callingnum1": "678901578",
            "callingnum2": "345626404",
            "switch1": "Germany",
            "switch2": "UK"
        }
        ```

- **Typ II: arrayOfObjects**

    Każdy plik zawiera tablicę obiektów.

    ```json
    [
        {
            "time": "2015-04-29T07:12:20.9100000Z",
            "callingimsi": "466920403025604",
            "callingnum1": "678948008",
            "callingnum2": "567834760",
            "switch1": "China",
            "switch2": "Germany"
        },
        {
            "time": "2015-04-29T07:13:21.0220000Z",
            "callingimsi": "466922202613463",
            "callingnum1": "123436380",
            "callingnum2": "789037573",
            "switch1": "US",
            "switch2": "UK"
        },
        {
            "time": "2015-04-29T07:13:21.4370000Z",
            "callingimsi": "466923101048691",
            "callingnum1": "678901578",
            "callingnum2": "345626404",
            "switch1": "Germany",
            "switch2": "UK"
        }
    ]
    ```

### <a name="jsonformat-example"></a>Przykład formatu JsonFormat

**Przypadek 1. Kopiowanie danych z plików JSON**

Zobacz następujące dwie przykłady podczas kopiowania danych z plików JSON. Ogólne wskazuje na uwagę:

**Przykład 1. Wyodrębnianie danych z obiektu i tablicy**

W tym przykładzie oczekiwany jest jeden główny obiekt JSON mapowany na pojedynczy rekord w wyniku tabelarycznym. Jeśli masz plik JSON z następującą zawartością:  

```json
{
    "id": "ed0e4960-d9c5-11e6-85dc-d7996816aad3",
    "context": {
        "device": {
            "type": "PC"
        },
        "custom": {
            "dimensions": [
                {
                    "TargetResourceType": "Microsoft.Compute/virtualMachines"
                },
                {
                    "ResourceManagementProcessRunId": "827f8aaa-ab72-437c-ba48-d8917a7336a3"
                },
                {
                    "OccurrenceTime": "1/13/2017 11:24:37 AM"
                }
            ]
        }
    }
}
```
i chcesz skopiować ją do tabeli usługi Azure SQL w następującym formacie przez wyodrębnienie danych z obiektu i tabeli:

| id | deviceType | targetResourceType | identyfikator y gospodarowania zasobamiProcessRunId | occurrenceTime |
| --- | --- | --- | --- | --- |
| ed0e4960-d9c5-11e6-85dc-d7996816aad3 | PC | Microsoft.Compute/virtualMachines | 827f8aaa-ab72-437c-ba48-d8917a7336a3 | 1/13/2017 11:24:37 AM |

Zestaw danych wejściowych typu **JsonFormat** jest zdefiniowany następująco: (częściowa definicja zawierająca tylko stosowne fragmenty). Więcej szczegółów:

- Sekcja `structure` definiuje niestandardowe nazwy kolumn i odpowiedni typ danych podczas konwersji na dane tabelaryczne. Ta sekcja jest **opcjonalna**, o ile nie trzeba wykonać mapowania kolumn. Aby uzyskać więcej informacji, zobacz [Mapowanie kolumn źródłowych zestawów danych do sekcji docelowych kolumn zestawu danych.](data-factory-map-columns.md)
- Właściwość `jsonPathDefinition` określa ścieżkę JSON dla każdej kolumny, wskazując, skąd mają zostać wyodrębnione dane. Aby skopiować dane z tablicy, można użyć **array[x].właściwość** wyodrębnić wartość danej właściwości z xth obiektu lub można użyć **array[*].właściwość,** aby znaleźć wartość z dowolnego obiektu zawierającego taką właściwość.

```json
"properties": {
    "structure": [
        {
            "name": "id",
            "type": "String"
        },
        {
            "name": "deviceType",
            "type": "String"
        },
        {
            "name": "targetResourceType",
            "type": "String"
        },
        {
            "name": "resourceManagementProcessRunId",
            "type": "String"
        },
        {
            "name": "occurrenceTime",
            "type": "DateTime"
        }
    ],
    "typeProperties": {
        "folderPath": "mycontainer/myfolder",
        "format": {
            "type": "JsonFormat",
            "filePattern": "setOfObjects",
            "jsonPathDefinition": {"id": "$.id", "deviceType": "$.context.device.type", "targetResourceType": "$.context.custom.dimensions[0].TargetResourceType", "resourceManagementProcessRunId": "$.context.custom.dimensions[1].ResourceManagementProcessRunId", "occurrenceTime": " $.context.custom.dimensions[2].OccurrenceTime"}      
        }
    }
}
```

**Przykład 2. Krzyżowe stosowanie tego samego wzorca z tabeli do wielu obiektów**

W tym przykładzie oczekiwane jest przetransformowanie jednego głównego obiektu JSON na wiele rekordów w wyniku tabelarycznym. Jeśli masz plik JSON z następującą zawartością:  

```json
{
    "ordernumber": "01",
    "orderdate": "20170122",
    "orderlines": [
        {
            "prod": "p1",
            "price": 23
        },
        {
            "prod": "p2",
            "price": 13
        },
        {
            "prod": "p3",
            "price": 231
        }
    ],
    "city": [ { "sanmateo": "No 1" } ]
}
```
i chcesz ją skopiować do tabeli Azure SQL w następującym formacie, spłaszczając dane wewnątrz tablicy i łącząc je krzyżowo ze wspólnymi informacjami głównymi:

| ordernumber | orderdate | order_pd | order_price | city |
| --- | --- | --- | --- | --- |
| 01 | 20170122 | P1 | 23 | [{"sanmateo":"No 1"}] |
| 01 | 20170122 | P2 | 13 | [{"sanmateo":"No 1"}] |
| 01 | 20170122 | P3 | 231 | [{"sanmateo":"No 1"}] |

Zestaw danych wejściowych typu **JsonFormat** jest zdefiniowany następująco: (częściowa definicja zawierająca tylko stosowne fragmenty). Więcej szczegółów:

- Sekcja `structure` definiuje niestandardowe nazwy kolumn i odpowiedni typ danych podczas konwersji na dane tabelaryczne. Ta sekcja jest **opcjonalna**, o ile nie trzeba wykonać mapowania kolumn. Aby uzyskać więcej informacji, zobacz [Mapowanie kolumn źródłowych zestawów danych do sekcji docelowych kolumn zestawu danych.](data-factory-map-columns.md)
- Właściwość `jsonNodeReference` określa, że ma zostać wykonana iteracja i ekstrakcja danych z obiektów o tym samym wzorcu w **tablicy** orderlines.
- Właściwość `jsonPathDefinition` określa ścieżkę JSON dla każdej kolumny, wskazując, skąd mają zostać wyodrębnione dane. W tym przykładzie kolumny „ordernumber”, „orderdate” i „city” znajdują się w obiekcie głównym ze ścieżką JSON rozpoczynającą się od znaków „$.”, natomiast kolumny „order_pd” i „order_price” są zdefiniowane przy użyciu ścieżki pochodzącej od elementu tablicy bez ciągu „$.”.

```json
"properties": {
    "structure": [
        {
            "name": "ordernumber",
            "type": "String"
        },
        {
            "name": "orderdate",
            "type": "String"
        },
        {
            "name": "order_pd",
            "type": "String"
        },
        {
            "name": "order_price",
            "type": "Int64"
        },
        {
            "name": "city",
            "type": "String"
        }
    ],
    "typeProperties": {
        "folderPath": "mycontainer/myfolder",
        "format": {
            "type": "JsonFormat",
            "filePattern": "setOfObjects",
            "jsonNodeReference": "$.orderlines",
            "jsonPathDefinition": {"ordernumber": "$.ordernumber", "orderdate": "$.orderdate", "order_pd": "prod", "order_price": "price", "city": " $.city"}         
        }
    }
}
```

**Pamiętaj o następujących kwestiach:**

* Jeśli sekcja `structure` i właściwość `jsonPathDefinition` nie są zdefiniowane w zestawie danych usługi Data Factory, działanie kopiowania wykrywa schemat z pierwszego obiektu i spłaszcza cały obiekt.
* Jeśli dane wejściowe JSON zawierają tablicę, działanie kopiowania domyślnie konwertuje całą wartość tablicy na ciąg. Możesz wyodrębnić z niej dane przy użyciu właściwości `jsonNodeReference` i/lub `jsonPathDefinition` bądź ją pominąć, nie określając jej we właściwości `jsonPathDefinition`.
* Jeśli na tym samym poziomie występują zduplikowane nazwy, działanie kopiowania wybierze ostatnią z nich.
* W przypadku nazw właściwości wielkość liter ma znaczenie. Dwie właściwości o takiej samej nazwie, ale zapisanej przy użyciu różnej wielkości liter, są traktowane jako dwie osobne właściwości.

**Przypadek 2. Zapisywanie danych do pliku JSON**

Jeśli masz następującą tabelę w bazie danych SQL:

| id | order_date | order_price | order_by |
| --- | --- | --- | --- |
| 1 | 20170119 | 2000 | David |
| 2 | 20170120 | 3500 | Patrick |
| 3 | 20170121 | 4000 | Jason |

i dla każdego rekordu, można oczekiwać, aby zapisać do obiektu JSON w następującym formacie:
```json
{
    "id": "1",
    "order": {
        "date": "20170119",
        "price": 2000,
        "customer": "David"
    }
}
```

Zestaw danych wyjściowych typu **JsonFormat** jest zdefiniowany następująco: (częściowa definicja zawierająca tylko stosowne fragmenty). W szczególności `structure` sekcja definiuje niestandardowe nazwy właściwości w `nestingSeparator` pliku docelowym (domyślnie jest to ".") są używane do identyfikowania warstwy gniazda z nazwy. Ta sekcja jest **opcjonalna**, o ile nie chcesz zmieniać nazwy właściwości na podstawie porównania z nazwą kolumny źródłowej ani zagnieżdżać właściwości.

```json
"properties": {
    "structure": [
        {
            "name": "id",
            "type": "String"
        },
        {
            "name": "order.date",
            "type": "String"
        },
        {
            "name": "order.price",
            "type": "Int64"
        },
        {
            "name": "order.customer",
            "type": "String"
        }
    ],
    "typeProperties": {
        "folderPath": "mycontainer/myfolder",
        "format": {
            "type": "JsonFormat"
        }
    }
}
```

## <a name="avro-format"></a>Format AVRO
Jeśli chcesz analizować pliki Avro lub zapisywać dane w formacie Avro, ustaw właściwość `format` `type` na wartość **AvroFormat**. Nie musisz określać żadnych właściwości w sekcji Format należącej do sekcji typeProperties. Przykład:

```json
"format":
{
    "type": "AvroFormat",
}
```

Aby użyć formatu Avro w tabeli programu Hive, możesz zapoznać się z [samouczkiem oprogramowania Apache Hive](https://cwiki.apache.org/confluence/display/Hive/AvroSerDe).

Pamiętaj o następujących kwestiach:  

* [Złożone typy danych](https://avro.apache.org/docs/current/spec.html#schema_complex) nie są obsługiwane (rekordy, wyliczenia, tablice, mapy, związki i stałe).

## <a name="orc-format"></a>Format ORC
Jeśli chcesz analizować pliki ORC lub zapisywać dane w formacie ORC, ustaw właściwość `format` `type` na wartość **OrcFormat**. Nie musisz określać żadnych właściwości w sekcji Format należącej do sekcji typeProperties. Przykład:

```json
"format":
{
    "type": "OrcFormat"
}
```

> [!IMPORTANT]
> Jeśli nie kopiujesz plików ORC **w niezmienionej postaci** między lokalnymi i chmurowymi magazynami danych, musisz zainstalować środowisko JRE 8 (Java Runtime Environment) na maszynie bramy. Brama 64-bitowa wymaga 64-bitowego środowiska JRE, natomiast brama 32-bitowa — 32-bitowego środowiska JRE. Obie wersje można znaleźć [tutaj](https://go.microsoft.com/fwlink/?LinkId=808605). Wybierz odpowiednią.
>
>

Pamiętaj o następujących kwestiach:

* Złożone typy danych nie są obsługiwane (struktura, mapa, lista, unia)
* Plik ORC ma trzy [opcje związane z kompresją](https://hortonworks.com/blog/orcfile-in-hdp-2-better-compression-better-performance/): NONE, ZLIB, SNAPPY. Usługa Data Factory obsługuje odczyt danych z pliku ORC w dowolnym z tych skompresowanych formatów. Do odczytywania danych używa kodera-dekodera kompresji z metadanych. Podczas zapisywania w pliku ORC usługa Data Factory wybiera natomiast opcję ZLIB, która jest domyślna dla formatu ORC. Obecnie nie ma możliwości zastąpienia tego zachowania.

## <a name="parquet-format"></a>Format Parquet
Jeśli chcesz analizować pliki Parquet lub zapisywać dane w formacie Parquet, ustaw właściwość `format` `type` na wartość **ParquetFormat**. Nie musisz określać żadnych właściwości w sekcji Format należącej do sekcji typeProperties. Przykład:

```json
"format":
{
    "type": "ParquetFormat"
}
```
> [!IMPORTANT]
> Jeśli nie kopiujesz plików Parquet **w niezmienionej postaci** między lokalnymi i chmurowymi magazynami danych, musisz zainstalować środowisko JRE 8 (Java Runtime Environment) na maszynie bramy. Brama 64-bitowa wymaga 64-bitowego środowiska JRE, natomiast brama 32-bitowa — 32-bitowego środowiska JRE. Obie wersje można znaleźć [tutaj](https://go.microsoft.com/fwlink/?LinkId=808605). Wybierz odpowiednią.
>
>

Pamiętaj o następujących kwestiach:

* Złożone typy danych nie są obsługiwane (mapa, lista)
* Plik Parquet ma następujące opcje związane z kompresją: NONE, SNAPPY, GZIP oraz LZO. Usługa Data Factory obsługuje odczyt danych z pliku ORC w dowolnym z tych skompresowanych formatów. Do odczytywania danych używa kodera-dekodera kompresji z metadanych. Podczas zapisywania w pliku Parquet usługa Data Factory wybiera natomiast opcję SNAPPY, która jest domyślna dla formatu Parquet. Obecnie nie ma możliwości zastąpienia tego zachowania.

## <a name="compression-support"></a>Obsługa kompresji
Przetwarzanie dużych zestawów danych może spowodować wąskie gardła we/wy i sieci. W związku z tym skompresowane dane w sklepach mogą nie tylko przyspieszyć transfer danych w sieci i zaoszczędzić miejsce na dysku, ale także przynieść znaczną poprawę wydajności w przetwarzaniu dużych zbiorów danych. Obecnie kompresja jest obsługiwana dla magazynów danych opartych na plikach, takich jak Azure Blob lub lokalny system plików.  

Aby określić kompresję dla zestawu danych, należy użyć właściwości **kompresji** w zestawie danych JSON, jak w poniższym przykładzie:   

```json
{  
    "name": "AzureBlobDataSet",  
    "properties": {  
        "availability": {  
            "frequency": "Day",  
              "interval": 1  
        },  
        "type": "AzureBlob",  
        "linkedServiceName": "StorageLinkedService",  
        "typeProperties": {  
            "fileName": "pagecounts.csv.gz",  
            "folderPath": "compression/file/",  
            "compression": {  
                "type": "GZip",  
                "level": "Optimal"  
            }  
        }  
    }  
}  
```

Załóżmy, że przykładowy zestaw danych jest używany jako dane wyjściowe działania kopiowania, działanie kopiowania kompresuje dane wyjściowe z kodekiem GZIP przy użyciu optymalnego współczynnika, a następnie zapisuje skompresowane dane w pliku o nazwie pagecounts.csv.gz w magazynie obiektów Blob platformy Azure.

> [!NOTE]
> Ustawienia kompresji nie są obsługiwane dla danych w **avroformat**, **OrcFormat**lub **ParquetFormat**. Podczas odczytywania plików w tych formatach usługa Data Factory wykrywa i używa kodeka kompresji w metadanych. Podczas zapisywania do plików w tych formatach usługa Data Factory wybiera domyślny kodek kompresji dla tego formatu. Na przykład ZLIB dla OrcFormat i SNAPPY dla ParkietFormat.   

Sekcja **kompresji** ma dwie właściwości:  

* **Typ:** kodek kompresji, który może być **GZIP**, **Deflate**, **BZIP2**lub **ZipDeflate**.  
* **Poziom:** stopień kompresji, który może być **optymalny** lub **najszybszy**.

  * **Najszybszy:** Operacja kompresji powinna zakończyć się tak szybko, jak to możliwe, nawet jeśli wynikowy plik nie jest optymalnie skompresowany.
  * **Optymalne**: Operacja kompresji powinna być optymalnie skompresowana, nawet jeśli operacja trwa dłużej.

    Aby uzyskać więcej informacji, zobacz temat [Poziom kompresji.](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx)

Po określeniu `compression` właściwości w wejściowym zestawie danych JSON potok może odczytywać skompresowane dane ze źródła; i po określeniu właściwości w wyjściowym zestawie danych JSON, działanie kopiowania można zapisać skompresowane dane do miejsca docelowego. Oto kilka przykładowych scenariuszy:

* Odczyt gzip skompresowane dane z obiektu blob platformy Azure, dekompresować go i zapisać dane wyników do bazy danych SQL platformy Azure. Definiujesz wejściowy zestaw danych obiektów `compression` `type` blob platformy Azure z właściwością JSON jako GZIP.
* Odczytuj dane z pliku zwykłego tekstu z lokalnego systemu plików, skompresuj je przy użyciu formatu GZip i zapisz skompresowane dane w obiekcie blob platformy Azure. Definiujesz wyjściowy zestaw danych obiektów `compression` `type` blob platformy Azure z właściwością JSON jako GZip.
* Odczyt pliku zip z serwera FTP, rozpakować go, aby uzyskać pliki wewnątrz i wylądować te pliki w usłudze Azure Data Lake Store. Definiujesz wejściowy zestaw danych `compression` `type` FTP z właściwością JSON jako ZipDeflate.
* Odczytu danych skompresowanych przez GZIP z obiektu blob platformy Azure, dekompresuj go, skompresuj przy użyciu BZIP2 i zapisuj dane wyników w obiekcie azure. Definiujesz wejściowy zestaw danych `compression` `type` obiektów blob platformy Azure z `compression` `type` zestawem GZIP i wyjściowym zestawem danych z zestawem BZIP2 w tym przypadku.   


## <a name="next-steps"></a>Następne kroki
Zobacz następujące artykuły dotyczące magazynów danych opartych na plikach obsługiwanych przez usługę Azure Data Factory:

- [Magazyn obiektów blob platformy Azure](data-factory-azure-blob-connector.md)
- [Azure Data Lake Store](data-factory-azure-datalake-connector.md)
- [FTP](data-factory-ftp-connector.md)
- [HDFS](data-factory-hdfs-connector.md)
- [System plików](data-factory-onprem-file-system-connector.md)
- [Amazon S3](data-factory-amazon-simple-storage-service-connector.md)
