---
title: 'Samouczek: Operacje ETL z interacypcyjnym zapytaniem — Usługa Azure HDInsight'
description: Samouczek — dowiedz się, jak wyodrębnić dane z nieprzetworzonego zestawu danych CSV, przekształcić je za pomocą interaktywnej kwerendy w programie HDInsight, a następnie załadować przekształcone dane do bazy danych SQL platformy Azure przy użyciu apache Sqoop.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: tutorial
ms.date: 07/02/2019
ms.author: hrasheed
ms.custom: hdinsightactive,mvc
ms.openlocfilehash: d1136c153a529f58db1de277ec84ac332b9f78ae
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "73494156"
---
# <a name="tutorial-extract-transform-and-load-data-using-interactive-query-in-azure-hdinsight"></a>Samouczek: wyodrębnianie, przekształcanie i ładowanie danych przy użyciu interaktywnej kwerendy w usłudze Azure HDInsight

W tym samouczku należy wziąć plik danych CSV z publicznie dostępnych danych lotu, zaimportować go do magazynu klastra HDInsight, a następnie przekształcić dane przy użyciu zapytania interaktywnego w usłudze Azure HDInsight. Przekształcone dane można załadować do bazy danych Azure SQL Database przy użyciu narzędzia [Apache Sqoop](https://sqoop.apache.org/).

Ten samouczek obejmuje następujące zadania:

> [!div class="checklist"]
> * Pobieranie przykładowych danych lotów
> * Przekazywanie danych do klastra usługi HDInsight
> * Przekształcanie danych przy użyciu zapytania interaktywnego
> * Tworzenie tabeli w bazie danych SQL platformy Azure
> * Eksportowanie danych do bazy danych SQL usługi Azure za pomocą funkcji Sqoop

## <a name="prerequisites"></a>Wymagania wstępne

* Interaktywny klaster zapytań w programie HDInsight. Zobacz [Tworzenie klastrów Apache Hadoop przy użyciu portalu Azure](../hdinsight-hadoop-create-linux-clusters-portal.md) i wybierz opcję **Zapytanie interaktywne** dla **typu klastra**.

* Baza danych SQL platformy Azure. Używasz bazy danych Azure SQL Database jako docelowego magazynu danych. Jeśli nie masz bazy danych SQL, zobacz [Tworzenie bazy danych Azure SQL Database w witrynie Azure Portal](/azure/sql-database/sql-database-single-database-get-started).

* Klient SSH. Aby uzyskać więcej informacji, zobacz [Łączenie się z usługą HDInsight (Apache Hadoop) przy użyciu protokołu SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="download-the-flight-data"></a>Pobieranie danych lotów

1. Przejdź do strony [Research and Innovative Technology Administration, Bureau of Transportation Statistics](https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time).

2. Na stronie wyczyść wszystkie pola, a następnie wybierz następujące wartości:

   | Nazwa | Wartość |
   | --- | --- |
   | Rok filtrowania |2019 |
   | Okres filtrowania |January (Styczeń) |
   | Pola |Rok, FlightDate, Reporting_Airline, DOT_ID_Reporting_Airline, Flight_Number_Reporting_Airline, OriginAirportID, Origin, OriginCityName, OriginState, DestAirportID, Dest, DestCityName, DestState, DepDelayMinutes, ArrDelay, ArrDelayMinutes, CarrierDelay, WeatherDelay, NASDelay, SecurityDelay, LateAircraftDelay. |

3. Wybierz przycisk **Download** (Pobierz). Uzyskasz plik zip z wybranymi polami danych.

## <a name="upload-data-to-an-hdinsight-cluster"></a>Przekazywanie danych do klastra usługi HDInsight

Istnieje wiele sposobów przekazywania danych do magazynu skojarzonego z klastrem usługi HDInsight. W tej sekcji przekażesz dane za pomocą polecenia `scp`. Aby dowiedzieć się więcej o innych sposobach przekazywania danych, zobacz [Upload data to HDInsight (przekazywanie danych do usługi HDInsight)](../hdinsight-upload-data.md).

1. Przekaż plik zip do węzła głównego klastra HDInsight. Edytuj poniższe polecenie, zastępując `FILENAME` nazwą pliku zip `CLUSTERNAME` i nazwą klastra HDInsight. Następnie otwórz wiersz polecenia, ustaw katalog roboczy na lokalizację pliku, a następnie wprowadź polecenie.

    ```cmd
    scp FILENAME.zip sshuser@CLUSTERNAME-ssh.azurehdinsight.net:FILENAME.zip
    ```

    Jeśli zostanie wyświetlony monit o wprowadzenie tak lub nie, aby kontynuować, wpisz tak w wierszu polecenia i naciśnij klawisz Enter. Tekst nie jest widoczny w oknie podczas pisania.

2. Po zakończeniu przekazywania połącz się z klastrem przy użyciu protokołu SSH. Edytuj poniższe polecenie, zastępując `CLUSTERNAME` nazwą klastra HDInsight. Wprowadź następujące polecenie:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

3. Konfigurowanie zmiennej środowiskowej po nawiązaniu połączenia SSH. `FILE_NAME`Zastąp `SQL_USER`, `SQL_PASWORD` `SQL_SERVERNAME` `SQL_DATABASE`, i odpowiednimi wartościami. Następnie wprowadź polecenie:

    ```bash
    export FILENAME=FILE_NAME
    export SQLSERVERNAME=SQL_SERVERNAME
    export DATABASE=SQL_DATABASE
    export SQLUSER=SQL_USER
    export SQLPASWORD='SQL_PASWORD'
    ```

4. Rozpakuj plik zip, wpisując poniższe polecenie:

    ```bash
    unzip $FILENAME.zip
    ```

5. Utwórz katalog w magazynie HDInsight, a następnie skopiuj plik csv do katalogu, wprowadzając poniższe polecenie:

    ```bash
    hdfs dfs -mkdir -p /tutorials/flightdelays/data
    hdfs dfs -put $FILENAME.csv /tutorials/flightdelays/data/
    ```

## <a name="transform-data-using-a-hive-query"></a>Przekształcanie danych za pomocą zapytania oprogramowania Hive

Istnieje wiele sposobów uruchamiania zadania oprogramowania Hive w klastrze usługi HDInsight. W tej sekcji uruchomisz zadanie oprogramowania Hive za pomocą usługi [Beeline](https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-Beeline%E2%80%93CommandLineShell). Aby uzyskać informacje o innych metodach uruchamiania zadania Hive, zobacz [Używanie oprogramowania Apache Hive w usłudze HDInsight](../hadoop/hdinsight-use-hive.md).

W ramach zadania oprogramowania Hive można zaimportować dane z pliku csv do tabeli oprogramowania Hive o nazwie **Delays**.

1. Z wiersza SSH, który masz już dla klastra HDInsight, użyj następującego polecenia, aby utworzyć i edytować nowy plik o nazwie **flightdelays.hql**:

    ```bash
    nano flightdelays.hql
    ```

2. Użyj następującego tekstu jako zawartości tego pliku:

    ```hiveql
    DROP TABLE delays_raw;
    -- Creates an external table over the csv file
    CREATE EXTERNAL TABLE delays_raw (
        YEAR string,
        FL_DATE string,
        UNIQUE_CARRIER string,
        CARRIER string,
        FL_NUM string,
        ORIGIN_AIRPORT_ID string,
        ORIGIN string,
        ORIGIN_CITY_NAME string,
        ORIGIN_CITY_NAME_TEMP string,
        ORIGIN_STATE_ABR string,
        DEST_AIRPORT_ID string,
        DEST string,
        DEST_CITY_NAME string,
        DEST_CITY_NAME_TEMP string,
        DEST_STATE_ABR string,
        DEP_DELAY_NEW float,
        ARR_DELAY_NEW float,
        CARRIER_DELAY float,
        WEATHER_DELAY float,
        NAS_DELAY float,
        SECURITY_DELAY float,
        LATE_AIRCRAFT_DELAY float)
    -- The following lines describe the format and location of the file
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ','
    LINES TERMINATED BY '\n'
    STORED AS TEXTFILE
    LOCATION '/tutorials/flightdelays/data';

    -- Drop the delays table if it exists
    DROP TABLE delays;
    -- Create the delays table and populate it with data
    -- pulled in from the CSV file (via the external table defined previously)
    CREATE TABLE delays AS
    SELECT YEAR AS year,
        FL_DATE AS flight_date,
        substring(UNIQUE_CARRIER, 2, length(UNIQUE_CARRIER) -1) AS unique_carrier,
        substring(CARRIER, 2, length(CARRIER) -1) AS carrier,
        substring(FL_NUM, 2, length(FL_NUM) -1) AS flight_num,
        ORIGIN_AIRPORT_ID AS origin_airport_id,
        substring(ORIGIN, 2, length(ORIGIN) -1) AS origin_airport_code,
        substring(ORIGIN_CITY_NAME, 2) AS origin_city_name,
        substring(ORIGIN_STATE_ABR, 2, length(ORIGIN_STATE_ABR) -1)  AS origin_state_abr,
        DEST_AIRPORT_ID AS dest_airport_id,
        substring(DEST, 2, length(DEST) -1) AS dest_airport_code,
        substring(DEST_CITY_NAME,2) AS dest_city_name,
        substring(DEST_STATE_ABR, 2, length(DEST_STATE_ABR) -1) AS dest_state_abr,
        DEP_DELAY_NEW AS dep_delay_new,
        ARR_DELAY_NEW AS arr_delay_new,
        CARRIER_DELAY AS carrier_delay,
        WEATHER_DELAY AS weather_delay,
        NAS_DELAY AS nas_delay,
        SECURITY_DELAY AS security_delay,
        LATE_AIRCRAFT_DELAY AS late_aircraft_delay
    FROM delays_raw;
    ```

3. Aby zapisać plik, naciśnij **klawisze Ctrl + X**, a następnie **y**, a następnie wprowadź.

4. Aby uruchomić oprogramowanie Hive i uruchomić plik **flightdelays.hql**, użyj następującego polecenia:

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -f flightdelays.hql
    ```

5. Gdy skrypt **flightdelays.hql** zakończy działanie, użyj następującego polecenia, aby otworzyć interaktywną sesję usługi Beeline:

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http'
    ```

6. Po otrzymaniu wiersza `jdbc:hive2://localhost:10001/>` użyj następującego zapytania, aby pobrać dane z zaimportowanych danych opóźnień lotów:

    ```hiveql
    INSERT OVERWRITE DIRECTORY '/tutorials/flightdelays/output'
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
    SELECT regexp_replace(origin_city_name, '''', ''),
        avg(weather_delay)
    FROM delays
    WHERE weather_delay IS NOT NULL
    GROUP BY origin_city_name;
    ```

    To zapytanie pobiera listę miejscowości z opóźnieniami ze względu na pogodę i średni czas opóźnienia, a następnie zapisuje ją w lokalizacji `/tutorials/flightdelays/output`. Później narzędzie Sqoop odczyta dane z tej lokalizacji i wyeksportuje je do bazy danych Azure SQL Database.

7. Aby zakończyć działanie usługi Beeline, wprowadź polecenie `!quit` w wierszu polecenia.

## <a name="create-a-sql-database-table"></a>Tworzenie tabeli bazy danych SQL

Istnieje wiele sposobów nawiązywania połączenia z bazą danych SQL i tworzenia tabeli. W poniższej procedurze użyto rozwiązania [FreeTDS](http://www.freetds.org/) z klastra usługi HDInsight.

1. Aby zainstalować usługę FreeTDS, użyj następującego polecenia z otwartego połączenia SSH z klastrem:

    ```bash
    sudo apt-get --assume-yes install freetds-dev freetds-bin
    ```

2. Po zakończeniu instalacji użyj poniższego polecenia, aby połączyć się z serwerem usługi SQL Database.

    ```bash
    TDSVER=8.0 tsql -H $SQLSERVERNAME.database.windows.net -U $SQLUSER -p 1433 -D $DATABASE -P $SQLPASWORD
    ```

    Uzyskasz dane wyjściowe podobne do następującego tekstu:

    ```
    locale is "en_US.UTF-8"
    locale charset is "UTF-8"
    using default charset "UTF-8"
    Default database being set to <yourdatabase>
    1>
    ```

3. W wierszu `1>` wprowadź następujące wiersze:

    ```hiveql
    CREATE TABLE [dbo].[delays](
    [origin_city_name] [nvarchar](50) NOT NULL,
    [weather_delay] float,
    CONSTRAINT [PK_delays] PRIMARY KEY CLUSTERED
    ([origin_city_name] ASC))
    GO
    ```

    Jeśli wprowadzono instrukcję `GO`, zostaną obliczone poprzednie instrukcje. Ta instrukcja tworzy tabelę o nazwie **opóźnienia**, z indeksem klastrowanym.

    Użyj następującego zapytania, aby sprawdzić, czy utworzono tabelę:

    ```hiveql
    SELECT * FROM information_schema.tables
    GO
    ```

    Dane wyjściowe będą podobne do następującego tekstu:

    ```
    TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
    databaseName       dbo             delays        BASE TABLE
    ```

4. Wprowadź ciąg `exit` w wierszu `1>`, aby zakończyć działanie narzędzia tsql.

## <a name="export-data-to-sql-database-using-apache-sqoop"></a>Eksportowanie danych do bazy danych SQL przy użyciu narzędzia Apache Sqoop

W poprzednich sekcjach skopiowano przekształcone dane w lokalizacji `/tutorials/flightdelays/output`. W tej sekcji użyjesz narzędzia Sqoop, aby wyeksportować dane z lokalizacji `/tutorials/flightdelays/output` do tabeli utworzonej w bazie danych Azure SQL Database.

1. Sprawdź, czy Sqoop może zobaczyć bazę danych SQL, wprowadzając poniższe polecenie:

    ```bash
    sqoop list-databases --connect jdbc:sqlserver://$SQLSERVERNAME.database.windows.net:1433 --username $SQLUSER --password $SQLPASWORD
    ```

    To polecenie zwraca listę baz danych, w tym `delays` bazę danych, w której utworzono tabelę wcześniej.

2. Eksportuj `/tutorials/flightdelays/output` dane `delays` z tabeli, wprowadzając poniższe polecenie:

    ```bash
    sqoop export --connect "jdbc:sqlserver://$SQLSERVERNAME.database.windows.net:1433;database=$DATABASE" --username $SQLUSER --password $SQLPASWORD --table 'delays' --export-dir '/tutorials/flightdelays/output' --fields-terminated-by '\t' -m 1
    ```

    Sqoop łączy się z bazą danych zawierającą `delays` `/tutorials/flightdelays/output` tabelę i `delays` eksportuje dane z katalogu do tabeli.

3. Po zakończeniu polecenia sqoop użyj narzędzia tsql, aby połączyć się z bazą danych, wprowadzając poniższe polecenie:

    ```bash
    TDSVER=8.0 tsql -H $SQLSERVERNAME.database.windows.net -U $SQLUSER -p 1433 -D $DATABASE -P $SQLPASWORD
    ```

    Aby sprawdzić, czy dane zostały wyeksportowane do tabeli delays, użyj następujących instrukcji:

    ```sql
    SELECT * FROM delays
    GO
    ```

    Powinna zostać wyświetlona lista danych w tabeli. Tabela zawiera nazwę miejscowości i średni czas opóźnienia lotów dla tej miejscowości. 

    Wpisz `exit`, aby zakończyć działanie narzędzia tsql.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po ukończeniu korzystania z samouczka warto usunąć klaster. Dzięki usłudze HDInsight dane są przechowywane w usłudze Azure Storage, więc można bezpiecznie usunąć klaster, gdy nie jest używany. Opłaty za klaster usługi HDInsight są naliczane nawet wtedy, gdy nie jest używany. Ponieważ opłaty za klaster są wielokrotnie większe niż opłaty za magazyn, ze względów ekonomicznych warto usuwać klastry, gdy nie są używane.

Aby usunąć klaster, zobacz [Usuwanie klastra HDInsight przy użyciu przeglądarki, programu PowerShell lub interfejsu wiersza polecenia platformy Azure](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Następne kroki

W tym samouczku zajęłeś nieprzetworzony plik danych CSV, zaimportowano go do magazynu klastra HDInsight, a następnie przekształciłeś dane przy użyciu zapytania interaktywnego w usłudze Azure HDInsight.  Przejdź do następnego samouczka, aby dowiedzieć się więcej o łączniku magazynu hive apache.

> [!div class="nextstepaction"]
>[Integracja platformy Apache Spark i ula Apache z łącznikiem magazynu hive](./apache-hive-warehouse-connector.md)
