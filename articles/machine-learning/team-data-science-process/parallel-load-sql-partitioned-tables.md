---
title: Równoległy zbiorczy import danych w tabeli partycji SQL - zespołu danych dla celów naukowych
description: Twórz partycjonowane tabele, szybkie równoległy zbiorczy importowania danych do bazy danych programu SQL Server.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 673a801e218d055bf482dc97972e36584cddd402
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76721340"
---
# <a name="build-and-optimize-tables-for-fast-parallel-import-of-data-into-a-sql-server-on-an-azure-vm"></a>Tworzenia i optymalizowania tabele, szybkie równoległe importowania danych do programu SQL Server na Maszynie wirtualnej platformy Azure

W tym artykule opisano sposób tworzenia partycjonowane tabele, szybkie równoległy zbiorczy importowania danych do bazy danych programu SQL Server. W celu ładowania i przesyłania danych Big Data do bazy danych SQL można ulepszyć Importowanie danych do usługi SQL DB i kolejnych zapytań przy użyciu *partycjonowanych tabel i widoków*. 

## <a name="create-a-new-database-and-a-set-of-filegroups"></a>Utwórz nową bazę danych i zestaw grup plików
* [Utwórz nową bazę danych](https://technet.microsoft.com/library/ms176061.aspx), jeśli jeszcze nie istnieje.
* Dodawanie grup plików bazy danych do bazy danych, która przechowuje pliki fizyczne podzielonym na partycje. 
* Można to zrobić przy użyciu [polecenia](https://msdn.microsoft.com/library/bb522682.aspx) [CREATE DATABASE](https://technet.microsoft.com/library/ms176061.aspx) , jeśli baza danych już istnieje.
* Dodaj jeden lub więcej plików (stosownie do potrzeb) do każdej grupy plików bazy danych.
  
  > [!NOTE]
  > Określ docelowy grupy plików, która przechowuje dane dla tej partycji i nazwy plików fizycznej bazy danych, przechowywania danych plików.
  > 
  > 

Poniższy przykład tworzy nową bazę danych z trzech grup plików innych niż podstawowy i grupy dzienników, zawierający jednym fizycznym pliku, w każdym. Pliki bazy danych są tworzone w domyślnym folderze danych programu SQL Server, zgodnie z konfiguracją w wystąpieniu programu SQL Server. Aby uzyskać więcej informacji na temat domyślnych lokalizacji plików, zapoznaj się z tematem [lokalizacje plików dla domyślnych i nazwanych wystąpień SQL Server](https://msdn.microsoft.com/library/ms143547.aspx).

    DECLARE @data_path nvarchar(256);
    SET @data_path = (SELECT SUBSTRING(physical_name, 1, CHARINDEX(N'master.mdf', LOWER(physical_name)) - 1)
      FROM master.sys.master_files
      WHERE database_id = 1 AND file_id = 1);

    EXECUTE ('
        CREATE DATABASE <database_name>
         ON  PRIMARY 
        ( NAME = ''Primary'', FILENAME = ''' + @data_path + '<primary_file_name>.mdf'', SIZE = 4096KB , FILEGROWTH = 1024KB ), 
         FILEGROUP [filegroup_1] 
        ( NAME = ''FileGroup1'', FILENAME = ''' + @data_path + '<file_name_1>.ndf'' , SIZE = 4096KB , FILEGROWTH = 1024KB ), 
         FILEGROUP [filegroup_2] 
        ( NAME = ''FileGroup2'', FILENAME = ''' + @data_path + '<file_name_2>.ndf'' , SIZE = 4096KB , FILEGROWTH = 1024KB ), 
         FILEGROUP [filegroup_3] 
        ( NAME = ''FileGroup3'', FILENAME = ''' + @data_path + '<file_name_3>.ndf'' , SIZE = 102400KB , FILEGROWTH = 10240KB ) 
         LOG ON 
        ( NAME = ''LogFileGroup'', FILENAME = ''' + @data_path + '<log_file_name>.ldf'' , SIZE = 1024KB , FILEGROWTH = 10%)
    ')

## <a name="create-a-partitioned-table"></a>Tworzenie tabeli partycjonowanej
Aby utworzyć partycjonowane tabele według schematu danych mapowany do grupy plików bazy danych utworzone w poprzednim kroku, należy najpierw utworzyć funkcji partycji i schematu. Jeśli dane są zbiorczego importowania do tabel podzielonym na partycje, rekordy są rozłożone grup plików według schematu partycji, zgodnie z poniższym opisem.

### <a name="1-create-a-partition-function"></a>1. Utwórz funkcję partycji
[Tworzenie funkcji partycji](https://msdn.microsoft.com/library/ms187802.aspx) Ta funkcja definiuje zakres wartości/granic do uwzględnienia w każdej tabeli partycji pojedynczej, na przykład w celu ograniczenia partycji według miesiąca (niektóre\_pole DateTime\_) w roku 2013:
  
        CREATE PARTITION FUNCTION <DatetimeFieldPFN>(<datetime_field>)  
        AS RANGE RIGHT FOR VALUES (
            '20130201', '20130301', '20130401',
            '20130501', '20130601', '20130701', '20130801',
            '20130901', '20131001', '20131101', '20131201' )

### <a name="2-create-a-partition-scheme"></a>2. Utwórz schemat partycji
[Utwórz schemat partycji](https://msdn.microsoft.com/library/ms179854.aspx). Ten schemat mapuje każdy zakres partycji w funkcji partycji fizycznych grupy plików, na przykład:
  
        CREATE PARTITION SCHEME <DatetimeFieldPScheme> AS  
        PARTITION <DatetimeFieldPFN> TO (
        <filegroup_1>, <filegroup_2>, <filegroup_3>, <filegroup_4>,
        <filegroup_5>, <filegroup_6>, <filegroup_7>, <filegroup_8>,
        <filegroup_9>, <filegroup_10>, <filegroup_11>, <filegroup_12> )
  
  Aby sprawdzić zakresy obowiązuje w poszczególnych partycjach, zgodnie z funkcji/schematu, uruchom następujące zapytanie:
  
        SELECT psch.name as PartitionScheme,
            prng.value AS PartitionValue,
            prng.boundary_id AS BoundaryID
        FROM sys.partition_functions AS pfun
        INNER JOIN sys.partition_schemes psch ON pfun.function_id = psch.function_id
        INNER JOIN sys.partition_range_values prng ON prng.function_id=pfun.function_id
        WHERE pfun.name = <DatetimeFieldPFN>

### <a name="3-create-a-partition-table"></a>3. Tworzenie tabeli partycji
[Utwórz partycjonowane tabele](https://msdn.microsoft.com/library/ms174979.aspx)zgodnie ze schematem danych i określ schemat partycji oraz pole ograniczenia używane do partycjonowania tabeli, na przykład:
  
        CREATE TABLE <table_name> ( [include schema definition here] )
        ON <TablePScheme>(<partition_field>)

Aby uzyskać więcej informacji, zobacz [Tworzenie partycjonowanych tabel i indeksów](https://msdn.microsoft.com/library/ms188730.aspx).

## <a name="bulk-import-the-data-for-each-individual-partition-table"></a>Zbiorcze importowanie danych dla każdej tabeli poszczególnych partycji

* Można użyć narzędzia BCP, BULK INSERT lub innych metod, takich jak [SQL Server Kreatora migracji](https://sqlazuremw.codeplex.com/). Podany przykład używa metody BCP.
* Zmień [bazę danych](https://msdn.microsoft.com/library/bb522682.aspx) , aby zmienić schemat rejestrowania transakcji na BULK_LOGGED, aby zminimalizować obciążenie rejestrowania, na przykład:
  
        ALTER DATABASE <database_name> SET RECOVERY BULK_LOGGED
* Aby przyspieszyć ładowanie danych, należy uruchomić operacji importu zbiorczego równolegle. Aby uzyskać porady dotyczące przyspieszania importowania zbiorczego danych Big Data do baz danych SQL Server, zobacz [obciążenie 1 TB w czasie krótszym niż 1 godzina](https://blogs.msdn.com/b/sqlcat/archive/2006/05/19/602142.aspx).

Poniższy skrypt programu PowerShell znajduje się przykład danych równoległe ładowanie przy użyciu narzędzia BCP.

    # Set database name, input data directory, and output log directory
    # This example loads comma-separated input data files
    # The example assumes the partitioned data files are named as <base_file_name>_<partition_number>.csv
    # Assumes the input data files include a header line. Loading starts at line number 2.

    $dbname = "<database_name>"
    $indir  = "<path_to_data_files>"
    $logdir = "<path_to_log_directory>"

    # Select authentication mode
    $sqlauth = 0

    # For SQL authentication, set the server and user credentials
    $sqlusr = "<user@server>"
    $server = "<tcp:serverdns>"
    $pass   = "<password>"

    # Set number of partitions per table - Should match the number of input data files per table
    $numofparts = <number_of_partitions>

    # Set table name to be loaded, basename of input data files, input format file, and number of partitions
    $tbname = "<table_name>"
    $basename = "<base_input_data_filename_no_extension>"
    $fmtfile = "<full_path_to_format_file>"

    # Create log directory if it does not exist
    New-Item -ErrorAction Ignore -ItemType directory -Path $logdir

    # BCP example using Windows authentication
    $ScriptBlock1 = {
       param($dbname, $tbname, $basename, $fmtfile, $indir, $logdir, $num)
       bcp ($dbname + ".." + $tbname) in ($indir + "\" + $basename + "_" + $num + ".csv") -o ($logdir + "\" + $tbname + "_" + $num + ".txt") -h "TABLOCK" -F 2 -C "RAW" -f ($fmtfile) -T -b 2500 -t "," -r \n
    }

    # BCP example using SQL authentication
    $ScriptBlock2 = {
       param($dbname, $tbname, $basename, $fmtfile, $indir, $logdir, $num, $sqlusr, $server, $pass)
       bcp ($dbname + ".." + $tbname) in ($indir + "\" + $basename + "_" + $num + ".csv") -o ($logdir + "\" + $tbname + "_" + $num + ".txt") -h "TABLOCK" -F 2 -C "RAW" -f ($fmtfile) -U $sqlusr -S $server -P $pass -b 2500 -t "," -r \n
    }

    # Background processing of all partitions
    for ($i=1; $i -le $numofparts; $i++)
    {
       Write-Output "Submit loading trip and fare partitions # $i"
       if ($sqlauth -eq 0) {
          # Use Windows authentication
          Start-Job -ScriptBlock $ScriptBlock1 -Arg ($dbname, $tbname, $basename, $fmtfile, $indir, $logdir, $i)
       } 
       else {
          # Use SQL authentication
          Start-Job -ScriptBlock $ScriptBlock2 -Arg ($dbname, $tbname, $basename, $fmtfile, $indir, $logdir, $i, $sqlusr, $server, $pass)
       }
    }

    Get-Job

    # Optional - Wait till all jobs complete and report date and time
    date
    While (Get-Job -State "Running") { Start-Sleep 10 }
    date


## <a name="create-indexes-to-optimize-joins-and-query-performance"></a>Tworzenie indeksów, aby zoptymalizować sprzężenia i wydajności zapytań
* Jeśli dane dotyczące modelowania są wyodrębniane z wielu tabel, indeksy należy utworzyć w klucze sprzężenia, aby zwiększyć wydajność sprzężenia.
* [Utwórz indeksy](https://technet.microsoft.com/library/ms188783.aspx) (klastrowane lub nieklastrowane) przeznaczone dla tej samej grupy plików dla każdej partycji, na przykład:
  
        CREATE CLUSTERED INDEX <table_idx> ON <table_name>( [include index columns here] )
        ON <TablePScheme>(<partition)field>)
  Ewentualnie
  
        CREATE INDEX <table_idx> ON <table_name>( [include index columns here] )
        ON <TablePScheme>(<partition)field>)
  
  > [!NOTE]
  > Istnieje możliwość tworzenia indeksów przed zbiorcze importowanie danych. Tworzenie indeksu, przed zaimportowaniem zbiorcze spowalnia ładowania danych.
  > 
  > 

## <a name="advanced-analytics-process-and-technology-in-action-example"></a>Proces zaawansowane funkcje analityczne i technologii w przykładzie akcji
Aby zapoznać się z kompleksowym przykładem, korzystając z procesu nauki o danych zespołowych z publicznym zestawem danych, zobacz [zespołowe przetwarzanie danych w ramach działania: używanie SQL Server](sql-walkthrough.md).

