---
title: Obsługiwane formaty plików w Azure Data Factory (starsza wersja)
description: W tym temacie opisano formaty plików i kody kompresji, które są obsługiwane przez łączników opartych na plikach w usłudze Azure Data Factory.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: jingwang
ms.openlocfilehash: 423706c391e8d8c2c609798d9f50e5a22f5c39bb
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79260685"
---
# <a name="supported-file-formats-and-compression-codecs-in-azure-data-factory-legacy"></a>Obsługiwane formaty plików i kodeki kompresji w Azure Data Factory (starsza wersja)

*Ten artykuł ma zastosowanie do następujących łączników [: Amazon S3](connector-amazon-simple-storage-service.md), [azure BLOB](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), [system plików](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)i [SFTP](connector-sftp.md).*

>[!IMPORTANT]
>Data Factory wprowadzono nowy model zestawu danych oparty na formacie, zobacz odpowiedni format artykułu ze szczegółami: <br>- [Format Avro](format-avro.md)<br>- [format binarny](format-binary.md)<br>[Format tekstu rozdzielanego](format-delimited-text.md) - <br>- [Format JSON](format-json.md)<br>- [Format Orc](format-orc.md)<br>- [Format Parquet](format-parquet.md)<br>Konfiguracje REST wymienione w tym artykule są nadal obsługiwane w przypadku compabitility z poprzednimi wersjami. Zamierzasz użyć nowego modelu do przodu. 

## <a name="text-format"></a>Format tekstu (starsza wersja)

>[!NOTE]
>Zapoznaj się z nowym modelem w artykule [Formatowanie tekstu rozdzielonego](format-delimited-text.md) . Następujące konfiguracje zestawu danych opartego na plikach są nadal obsługiwane, tak jak w przypadku compabitility z poprzednimi wersjami. Zamierzasz użyć nowego modelu do przodu.

Jeśli chcesz czytać z pliku tekstowego lub zapisywać do pliku tekstowego, ustaw właściwość `type` w sekcji `format` zestawu danych na wartość **TextFormat**. Ponadto możesz określić następujące **opcjonalne** właściwości w sekcji `format`. Aby uzyskać informacje na temat sposobu konfigurowania, zobacz sekcję [Przykład formatu TextFormat](#textformat-example).

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| columnDelimiter |Znak używany do rozdzielania kolumn w pliku. Aby wziąć pod uwagę do użycia rzadkich znak niedrukowalny, który nie istnieje w danych. Na przykład określić "\u0001", który reprezentuje Start z nagłówkiem (raportu o kondycji). |Dozwolony jest tylko jeden znak. Wartość **domyślna** to **przecinek (,)** . <br/><br/>Aby użyć znaku Unicode, zapoznaj się ze [znakami Unicode](https://en.wikipedia.org/wiki/List_of_Unicode_characters) w celu uzyskania odpowiedniego kodu. |Nie |
| rowDelimiter |Znak używany do rozdzielania wierszy w pliku. |Dozwolony jest tylko jeden znak. Wartością **domyślną** jest dowolna z następujących wartości przy odczycie: **[„\r\n”, „\r”, „\n”]** oraz wartość **„\r\n”** przy zapisie. |Nie |
| escapeChar |Znak specjalny służący do zmiany interpretacji ogranicznika kolumny w zawartości pliku wejściowego. <br/><br/>W przypadku tabeli nie można określić zarówno właściwości escapeChar, jak i quoteChar. |Dozwolony jest tylko jeden znak. Brak wartości domyślnej. <br/><br/>Przykład: jeśli ogranicznikiem kolumny jest przecinek (,), ale chcesz, aby znak przecinka występował w tekście (przykładowo: „Witaj, świecie”), możesz zdefiniować znak „$” jako znak ucieczki i użyć ciągu „Witaj$, świecie” w źródle. |Nie |
| quoteChar |Znak używany do umieszczania wartości ciągu w cudzysłowie. Ograniczniki kolumny i wiersza umieszczone w cudzysłowie są traktowane jako część wartości ciągu. Ta właściwość ma zastosowanie zarówno do wejściowych, jak i wyjściowych zestawów danych.<br/><br/>W przypadku tabeli nie można określić zarówno właściwości escapeChar, jak i quoteChar. |Dozwolony jest tylko jeden znak. Brak wartości domyślnej. <br/><br/>Na przykład jeśli ogranicznikiem kolumny jest przecinek (,), ale chcesz, aby znak przecinka występował w tekście (przykład: <Witaj, świecie>), możesz zdefiniować cudzysłów (") jako znak cudzysłowu i użyć ciągu "Witaj, świecie" w źródle. |Nie |
| nullValue |Co najmniej jeden znak służący do reprezentowania wartości null. |Co najmniej jeden znak. Wartości **domyślne** to **„\N” i „NULL”** przy odczycie oraz **„\N”** przy zapisie. |Nie |
| encodingName |Określa nazwę kodowania. |Prawidłowa nazwa kodowania. Zobacz [właściwość Encoding.EncodingName](https://msdn.microsoft.com/library/system.text.encoding.aspx). Przykład: windows-1250 lub shift_jis. Wartość **domyślna** to **UTF-8**. |Nie |
| firstRowAsHeader |Określa, czy pierwszy wiersz ma być traktowany jako nagłówek. W przypadku zestawu danych wejściowych usługa Data Factory odczytuje pierwszy wiersz jako nagłówek. W przypadku zestawu danych wyjściowych usługa Data Factory zapisuje pierwszy wiersz jako nagłówek. <br/><br/>Aby uzyskać przykładowe scenariusze, zobacz sekcję [Scenariusze użycia właściwości `firstRowAsHeader` oraz `skipLineCount`](#scenarios-for-using-firstrowasheader-and-skiplinecount). |True<br/><b>False (domyślnie)</b> |Nie |
| skipLineCount |Wskazuje liczbę **niepustych** wierszy do pominięcia podczas odczytywania danych z plików wejściowych. Jeśli określono zarówno właściwość skipLineCount, jak i firstRowAsHeader, najpierw zostaną pominięte wiersze, a następnie zostaną odczytane informacje nagłówka z pliku wejściowego. <br/><br/>Aby uzyskać przykładowe scenariusze, zobacz sekcję [Scenariusze użycia właściwości `firstRowAsHeader` oraz `skipLineCount`](#scenarios-for-using-firstrowasheader-and-skiplinecount). |Liczba całkowita |Nie |
| treatEmptyAsNull |Określa, czy ciąg pusty lub o wartości null ma być traktowany jako wartość null podczas odczytu danych z pliku wejściowego. |**True (domyślnie)**<br/>Fałsz |Nie |

### <a name="textformat-example"></a>Przykład formatu TextFormat

W poniższej definicji JSON dla zestawu danych podano części właściwości opcjonalnych.

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

## <a name="json-format"></a>Format JSON (starsza wersja)

>[!NOTE]
>Poznaj nowy model z artykułu w [formacie JSON](format-json.md) . Następujące konfiguracje zestawu danych opartego na plikach są nadal obsługiwane, tak jak w przypadku compabitility z poprzednimi wersjami. Zamierzasz użyć nowego modelu do przodu.

Aby **zaimportować/wyeksportować plik JSON w postaci-do/z Azure Cosmos DB**, zobacz sekcję importowanie/eksportowanie dokumentów JSON w artykule [przenoszenie danych do/z Azure Cosmos DB](connector-azure-cosmos-db.md) .

Jeśli chcesz przeanalizować pliki JSON lub zapisać dane w formacie JSON, ustaw właściwość `type` w sekcji `format` na **formatu jsonformat**. Ponadto możesz określić następujące **opcjonalne** właściwości w sekcji `format`. Aby uzyskać informacje na temat sposobu konfigurowania, zobacz sekcję [Przykład formatu JsonFormat](#jsonformat-example).

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| filePattern |Wskazuje wzorzec danych przechowywanych w każdym pliku JSON. Dozwolone wartości to: **setOfObjects** i **arrayOfObjects**. Wartością **domyślną** jest **setOfObjects**. Aby uzyskać szczegółowe informacje o tych wzorcach, zobacz sekcję [Wzorce plików JSON](#json-file-patterns). |Nie |
| jsonNodeReference | Jeśli chcesz wykonać iterację i ekstrakcję danych z obiektów wewnątrz pola tablicy o tym samym wzorcu, określ ścieżkę JSON tej tablicy. Ta właściwość jest obsługiwana tylko podczas kopiowania danych **z** plików JSON. | Nie |
| jsonPathDefinition | Określa wyrażenie ścieżki JSON dla każdego mapowania kolumny z niestandardową nazwą kolumny (musi zaczynać się małą literą). Ta właściwość jest obsługiwana tylko podczas kopiowania danych **z** plików JSON i można wyodrębnić dane z obiektu lub tablicy. <br/><br/> W przypadku pól obiektu głównego na początku użyj elementu głównego $. W przypadku pól wewnątrz tablicy wybranej przez właściwość `jsonNodeReference` najpierw podaj element tablicy. Aby uzyskać informacje na temat sposobu konfigurowania, zobacz sekcję [Przykład formatu JsonFormat](#jsonformat-example). | Nie |
| encodingName |Określa nazwę kodowania. Aby uzyskać listę prawidłowych nazw kodowania, zobacz właściwość [Encoding.EncodingName](https://msdn.microsoft.com/library/system.text.encoding.aspx). Na przykład: windows-1250 lub shift_jis. Wartość **domyślna** to: **UTF-8**. |Nie |
| nestingSeparator |Znak używany do rozdzielania poziomów zagnieżdżenia. Wartość domyślna to „.” (kropka). |Nie |

>[!NOTE]
>W przypadku krzyżowego stosowania danych w tablicy do wielu wierszy (przypadek 1-> próbki 2 w [przykładach formatu jsonformat](#jsonformat-example)) można rozwinąć tylko jedną tablicę przy użyciu właściwości `jsonNodeReference`.

### <a name="json-file-patterns"></a>Wzorce plików JSON

Działanie kopiowania może przeanalizować poniższe wzorce plików JSON:

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

| ID | deviceType | targetResourceType | resourceManagementProcessRunId | occurrenceTime |
| --- | --- | --- | --- | --- |
| ed0e4960-d9c5-11e6-85dc-d7996816aad3 | PC | Microsoft.Compute/virtualMachines | 827f8aaa-ab72-437c-ba48-d8917a7336a3 | 1/13/2017 11:24:37 AM |

Zestaw danych wejściowych typu **JsonFormat** jest zdefiniowany następująco: (częściowa definicja zawierająca tylko stosowne fragmenty). Więcej szczegółów:

- Sekcja `structure` definiuje niestandardowe nazwy kolumn i odpowiedni typ danych podczas konwersji na dane tabelaryczne. Ta sekcja jest **opcjonalna**, o ile nie trzeba wykonać mapowania kolumn. Aby uzyskać więcej informacji, zobacz [Mapowanie kolumn źródłowych zestawów danych do docelowych kolumn zestawu danych](copy-activity-schema-and-type-mapping.md).
- Właściwość `jsonPathDefinition` określa ścieżkę JSON dla każdej kolumny, wskazując, skąd mają zostać wyodrębnione dane. Aby skopiować dane z tablicy, można użyć `array[x].property` do wyodrębnienia wartości danej właściwości z obiektu `xth` lub można użyć `array[*].property`, aby znaleźć wartość z dowolnego obiektu zawierającego taką właściwość.

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

| `ordernumber` | `orderdate` | `order_pd` | `order_price` | `city` |
| --- | --- | --- | --- | --- |
| 01 | 20170122 | P1 | 23 | `[{"sanmateo":"No 1"}]` |
| 01 | 20170122 | P2 | 13 | `[{"sanmateo":"No 1"}]` |
| 01 | 20170122 | P3 | 231 | `[{"sanmateo":"No 1"}]` |


Zestaw danych wejściowych typu **JsonFormat** jest zdefiniowany następująco: (częściowa definicja zawierająca tylko stosowne fragmenty). Więcej szczegółów:

- Sekcja `structure` definiuje niestandardowe nazwy kolumn i odpowiedni typ danych podczas konwersji na dane tabelaryczne. Ta sekcja jest **opcjonalna**, o ile nie trzeba wykonać mapowania kolumn. Aby uzyskać więcej informacji, zobacz [Mapowanie kolumn źródłowych zestawów danych do docelowych kolumn zestawu danych](copy-activity-schema-and-type-mapping.md).
- `jsonNodeReference` wskazuje na iterację i wyodrębnienie danych z obiektów z tym samym wzorcem w obszarze `orderlines`**tablicy** .
- Właściwość `jsonPathDefinition` określa ścieżkę JSON dla każdej kolumny, wskazując, skąd mają zostać wyodrębnione dane. W tym przykładzie `ordernumber`, `orderdate`i `city` znajdują się pod obiektem głównym z ścieżką JSON rozpoczynającą się od `$.`, podczas gdy `order_pd` i `order_price` są zdefiniowane przy użyciu ścieżki pochodnej elementu Array bez `$.`.

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

Jeśli masz poniższej tabeli w bazie danych SQL:

| ID | order_date | order_price | order_by |
| --- | --- | --- | --- |
| 1 | 20170119 | 2000 | David |
| 2 | 20170120 | 3500 | Patrick |
| 3 | 20170121 | 4000 | Jason |

i dla każdego rekordu będziesz zapisywać w obiekcie JSON w następującym formacie:

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

Zestaw danych wyjściowych typu **JsonFormat** jest zdefiniowany następująco: (częściowa definicja zawierająca tylko stosowne fragmenty). Dokładniej, `structure` sekcja definiuje niestandardowe nazwy właściwości w pliku docelowym, `nestingSeparator` (wartość domyślna to ".") służy do identyfikowania warstwy zagnieżdżonej na podstawie nazwy. Ta sekcja jest **opcjonalna**, o ile nie chcesz zmieniać nazwy właściwości na podstawie porównania z nazwą kolumny źródłowej ani zagnieżdżać właściwości.

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

## <a name="parquet-format"></a>Format Parquet (starsza wersja)

>[!NOTE]
>Poznaj nowy model z artykułu [Parquet format](format-parquet.md) . Następujące konfiguracje zestawu danych opartego na plikach są nadal obsługiwane, tak jak w przypadku compabitility z poprzednimi wersjami. Zamierzasz użyć nowego modelu do przodu.

Jeśli chcesz analizować pliki Parquet lub zapisywać dane w formacie Parquet, ustaw właściwość `format` `type` na **ParquetFormat**. Nie musisz określać żadnych właściwości w sekcji Format należącej do sekcji typeProperties. Przykład:

```json
"format":
{
    "type": "ParquetFormat"
}
```

Pamiętaj o następujących kwestiach:

* Złożone typy danych nie są obsługiwane (mapa, lista).
* Biały znak w nazwie kolumny nie jest obsługiwany.
* Plik Parquet ma następujące opcje związane z kompresją: NONE, SNAPPY, GZIP oraz LZO. Data Factory obsługuje odczytywanie danych z pliku Parquet w dowolnym z tych skompresowanych formatów, z wyjątkiem LZO — używa kodera-dekoder kompresji w metadanych do odczytu danych. Podczas zapisywania w pliku Parquet usługa Data Factory wybiera natomiast opcję SNAPPY, która jest domyślna dla formatu Parquet. Obecnie nie ma możliwości zastąpienia tego zachowania.

> [!IMPORTANT]
> W przypadku kopii obsługiwanej przez samodzielne Integration Runtime, np. między lokalnym i magazynem danych w chmurze, jeśli nie kopiujesz plików Parquet w taki **sposób**, musisz zainstalować **64-bitową JRE, Java Runtime Environment lub OPENJDK** na maszynie IR. Więcej informacji można znaleźć w poniższym akapicie.

W przypadku kopiowania uruchomionego na samoobsługowym środowisku IR przy użyciu serializacji/deserializacji pliku Parquet można zlokalizować środowisko uruchomieniowe języka Java, sprawdzając najpierw *`(SOFTWARE\JavaSoft\Java Runtime Environment\{Current Version}\JavaHome)`* rejestru dla środowiska JRE, jeśli nie zostanie on znaleziony, a następnie podczas drugiej kontroli zmiennej systemowej *`JAVA_HOME`* dla OpenJDK.

- **Aby użyć środowiska JRE**: 64-bitowy IR wymaga 64-bitowego środowiska JRE. Można je znaleźć w [tym miejscu](https://go.microsoft.com/fwlink/?LinkId=808605).
- **Aby użyć OpenJDK**: jest obsługiwana od czasu IR w wersji 3,13. Spakuj plik JVM. dll ze wszystkimi innymi wymaganymi zestawami OpenJDK do samodzielnej maszyny IR, a następnie ustaw dla zmiennej środowiskowej systemowe JAVA_HOME odpowiednio.

>[!TIP]
>Jeśli skopiujesz dane do/z formatu Parquet przy użyciu samodzielnego Integration Runtime i błędu trafień mówiąc "Wystąpił błąd podczas wywoływania języka Java, komunikat: **Java. lang. OutOfMemoryError: przestrzeń sterty Java**", można dodać zmienną środowiskową `_JAVA_OPTIONS` na maszynie, która hostuje własne środowisko IR, aby dostosować rozmiar sterty minimalny/maksymalny dla JVM, a następnie ponownie uruchomić potok.

![Ustawianie rozmiaru sterty JVM na samoobsługowym środowisku IR](./media/supported-file-formats-and-compression-codecs/set-jvm-heap-size-on-selfhosted-ir.png)

Przykład: Ustaw zmienną `_JAVA_OPTIONS` przy użyciu `-Xms256m -Xmx16g`wartości. Flaga `Xms` określa początkową pulę alokacji pamięci dla wirtualna maszyna Java (JVM), podczas gdy `Xmx` określa maksymalną pulę alokacji pamięci. Oznacza to, że JVM zostanie uruchomione z `Xms` ilości pamięci i będzie można użyć maksymalnie `Xmx` ilości pamięci. Domyślnie funkcja ADF korzysta z minimalnej 64 MB i maksymalnej wartości 1G.

### <a name="data-type-mapping-for-parquet-files"></a>Mapowanie plików Parquet — typ danych

| Typ danych tymczasowych fabryki danych | Typ pierwotny parquet | Parquet oryginalny typ (deserializacji) | Parquet oryginalny typ (serializować) |
|:--- |:--- |:--- |:--- |
| Wartość logiczna | Wartość logiczna | N/D | N/D |
| Sbyte — | Int32 | Int8 | Int8 |
| Bajtów | Int32 | UInt8 | Int16 |
| Int16 | Int32 | Int16 | Int16 |
| UInt16 | Int32 | UInt16 | Int32 |
| Int32 | Int32 | Int32 | Int32 |
| UInt32 | Int64 | UInt32 | Int64 |
| Int64 | Int64 | Int64 | Int64 |
| UInt64 | Int64/dane binarne | UInt64 | Dziesiętna |
| Single | Float | N/D | N/D |
| Podwójne | Podwójne | N/D | N/D |
| Dziesiętna | Binarny | Dziesiętna | Dziesiętna |
| Ciąg | Binarny | Utf8 | Utf8 |
| DateTime | Int96 | N/D | N/D |
| przedział_czasu | Int96 | N/D | N/D |
| DateTimeOffset | Int96 | N/D | N/D |
| ByteArray | Binarny | N/D | N/D |
| Guid | Binarny | Utf8 | Utf8 |
| Char | Binarny | Utf8 | Utf8 |
| CharArray | Nieobsługiwane | N/D | N/D |

## <a name="orc-format"></a>Format ORC (starsza wersja)

>[!NOTE]
>Poznaj nowy model z artykułu [Orc format](format-orc.md) . Następujące konfiguracje zestawu danych opartego na plikach są nadal obsługiwane, tak jak w przypadku compabitility z poprzednimi wersjami. Zamierzasz użyć nowego modelu do przodu.

Jeśli chcesz analizować pliki ORC lub zapisywać dane w formacie ORC, ustaw właściwość `format` `type` na **OrcFormat**. Nie musisz określać żadnych właściwości w sekcji Format należącej do sekcji typeProperties. Przykład:

```json
"format":
{
    "type": "OrcFormat"
}
```

Pamiętaj o następujących kwestiach:

* Złożone typy danych nie są obsługiwane (struktura, mapa, lista, Unia).
* Biały znak w nazwie kolumny nie jest obsługiwany.
* Plik ORC ma trzy [opcje związane z kompresją](https://hortonworks.com/blog/orcfile-in-hdp-2-better-compression-better-performance/): NONE, ZLIB, SNAPPY. Usługa Data Factory obsługuje odczyt danych z pliku ORC w dowolnym z tych skompresowanych formatów. Do odczytywania danych używa kodera-dekodera kompresji z metadanych. Podczas zapisywania w pliku ORC usługa Data Factory wybiera natomiast opcję ZLIB, która jest domyślna dla formatu ORC. Obecnie nie ma możliwości zastąpienia tego zachowania.

> [!IMPORTANT]
> W przypadku kopii obsługiwanej przez samodzielne Integration Runtime, np. między lokalnym i magazynem danych w chmurze, jeśli nie kopiujesz plików ORC w taki **sposób**, musisz zainstalować **64-bitową JRE, Java Runtime Environment lub OPENJDK** na maszynie IR. Więcej informacji można znaleźć w poniższym akapicie.

W przypadku kopiowania uruchomionego na samoobsługowym środowisku IR przy użyciu serializacji/deserializacji pliku ORC można zlokalizować środowisko uruchomieniowe języka Java, sprawdzając najpierw *`(SOFTWARE\JavaSoft\Java Runtime Environment\{Current Version}\JavaHome)`* rejestru dla środowiska JRE, jeśli nie zostanie on znaleziony, a następnie podczas drugiej kontroli zmiennej systemowej *`JAVA_HOME`* dla OpenJDK.

- **Aby użyć środowiska JRE**: 64-bitowy IR wymaga 64-bitowego środowiska JRE. Można je znaleźć w [tym miejscu](https://go.microsoft.com/fwlink/?LinkId=808605).
- **Aby użyć OpenJDK**: jest obsługiwana od czasu IR w wersji 3,13. Spakuj plik JVM. dll ze wszystkimi innymi wymaganymi zestawami OpenJDK do samodzielnej maszyny IR, a następnie ustaw dla zmiennej środowiskowej systemowe JAVA_HOME odpowiednio.

### <a name="data-type-mapping-for-orc-files"></a>Mapowanie plików ORC — typ danych

| Typ danych tymczasowych fabryki danych | Typy ORC |
|:--- |:--- |
| Wartość logiczna | Wartość logiczna |
| Sbyte — | Bajtów |
| Bajtów | Krótkie |
| Int16 | Krótkie |
| UInt16 | Int |
| Int32 | Int |
| UInt32 | Długie |
| Int64 | Długie |
| UInt64 | Ciąg |
| Single | Float |
| Podwójne | Podwójne |
| Dziesiętna | Dziesiętna |
| Ciąg | Ciąg |
| DateTime | Znacznik czasu |
| DateTimeOffset | Znacznik czasu |
| przedział_czasu | Znacznik czasu |
| ByteArray | Binarny |
| Guid | Ciąg |
| Char | Char(1) |

## <a name="avro-format"></a>Format AVRO (starsza wersja)

>[!NOTE]
>Poznaj nowy model z artykułu [Avro format](format-avro.md) . Następujące konfiguracje zestawu danych opartego na plikach są nadal obsługiwane, tak jak w przypadku compabitility z poprzednimi wersjami. Zamierzasz użyć nowego modelu do przodu.

Jeśli chcesz analizować pliki Avro lub zapisywać dane w formacie Avro, ustaw właściwość `format` `type` na **AvroFormat**. Nie musisz określać żadnych właściwości w sekcji Format należącej do sekcji typeProperties. Przykład:

```json
"format":
{
    "type": "AvroFormat",
}
```

Aby użyć formatu Avro w tabeli programu Hive, możesz zapoznać się z [samouczkiem oprogramowania Apache Hive](https://cwiki.apache.org/confluence/display/Hive/AvroSerDe).

Pamiętaj o następujących kwestiach:

* [Złożone typy danych](https://avro.apache.org/docs/current/spec.html#schema_complex) nie są obsługiwane (rekordy, wyliczenia, tablice, mapy, Unii i stałe).

## <a name="compression-support"></a>Obsługa kompresji (starsza wersja)

Usługa Azure Data Factory obsługuje Kompresuj/dekompresji danych podczas kopiowania. Po określeniu właściwości `compression` w wejściowym zestawie danych działanie kopiowania odczytuje skompresowane dane ze źródła i dekompresuje je. Po określeniu właściwości w wyjściowym zestawie danych działanie kopiowania kompresuje następnie dane do ujścia. Poniżej przedstawiono kilka przykładowych scenariuszy:

* GZIP odczytu skompresowane dane z obiektu blob platformy Azure zdekompresować go i zapisać dane wynikowe do usługi Azure SQL database. Należy zdefiniować zestaw danych wejściowych obiektów blob platformy Azure z właściwością `compression` `type` jako GZIP.
* Odczytywanie danych z pliku tekstowego z systemu plików w środowisku lokalnym, skompresować je w formacie GZip i zapisać skompresowane dane obiektu blob platformy Azure. Można zdefiniować wyjściowy zestaw danych obiektów blob platformy Azure z właściwością `compression` `type` jako GZip.
* Odczytaj plik zip z serwera FTP Dekompresuj ją, aby pobrać pliki wewnątrz i te pliki znajdą się w usłudze Azure Data Lake Store. Można zdefiniować wejściowy zestaw danych FTP z właściwością `compression` `type` jako ZipDeflate.
* Odczytywanie danych z kompresji GZIP obiektu blob platformy Azure, zdekompresować, skompresować je przy użyciu BZIP2 i zapisać dane wynikowe do usługi Azure blob. Zdefiniuj zestaw danych wejściowych obiektów blob platformy Azure z `compression` `type` ustawionym na GZIP, a wyjściowy zestaw danych z `compression` `type` ustawiony na BZIP2.

Aby określić kompresję zestawu danych, należy użyć właściwości **Compression** w kodzie JSON zestawu danych, jak w poniższym przykładzie:

```json
{
    "name": "AzureBlobDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": {
            "referenceName": "StorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "fileName": "pagecounts.csv.gz",
            "folderPath": "compression/file/",
            "format": {
                "type": "TextFormat"
            },
            "compression": {
                "type": "GZip",
                "level": "Optimal"
            }
        }
    }
}
```

Sekcja **kompresji** ma dwie właściwości:

* **Typ:** koder-dekoder kompresji, który może być **gzip**, **Wklęśnięcie**, **bzip2**lub **ZipDeflate**. Uwaga w przypadku używania działania kopiowania do dekompresowania plików ZipDeflate i zapisywania w magazynie danych ujścia opartych na plikach pliki zostaną wyodrębnione do folderu: `<path specified in dataset>/<folder named as source zip file>/`.
* **Poziom:** współczynnik kompresji, który może być **optymalny** lub **najszybszy**.

  * **Najszybsze:** Operacja kompresji powinna zostać ukończona tak szybko, jak to możliwe, nawet jeśli plik nie jest optymalnie kompresowany.
  * **Optymalnie**: operacja kompresji powinna być optymalnie skompresowana, nawet jeśli operacja trwa dłużej.

    Aby uzyskać więcej informacji, zobacz temat [poziom kompresji](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) .

> [!NOTE]
> Ustawienia kompresji nie są obsługiwane w przypadku danych w **AvroFormat**, **OrcFormat**lub **ParquetFormat**. Podczas odczytywania plików w tych formatach, Data Factory wykrywa i używa kodera-dekodera kompresji w metadanych. Podczas zapisywania plików w tych formatach, Data Factory wybiera natomiast opcję domyślną kodera-dekodera kompresji dla tego formatu. Na przykład ZLIB OrcFormat i SNAPPY dla ParquetFormat.

## <a name="unsupported-file-types-and-compression-formats"></a>Nieobsługiwane typy plików i formaty kompresji

Korzystając z funkcji rozszerzalności Azure Data Factory, można przekształcać pliki, które nie są obsługiwane.
Dwie opcje obejmują Azure Functions i zadania niestandardowe przy użyciu Azure Batch.

Możesz zobaczyć przykład, który używa funkcji platformy Azure, aby [wyodrębnić zawartość pliku tar](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV2/UntarAzureFilesWithAzureFunction). Aby uzyskać więcej informacji, zobacz [Azure Functions działania](https://docs.microsoft.com/azure/data-factory/control-flow-azure-function-activity).

Tę funkcję można także skompilować przy użyciu niestandardowego działania dotnet. Dodatkowe informacje są dostępne [tutaj](https://docs.microsoft.com/azure/data-factory/transform-data-using-dotnet-custom-activity)

## <a name="next-steps"></a>Następne kroki

Poznaj najnowsze obsługiwane formaty plików i kompresji z [obsługiwanych formatów plików i kompresji](supported-file-formats-and-compression-codecs.md).
