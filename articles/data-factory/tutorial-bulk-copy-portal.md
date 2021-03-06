---
title: Kopiuj dane zbiorczo przy użyciu Azure Portal
description: Dowiedz się, jak używać usługi Azure Data Factory i działania kopiowania do zbiorczego kopiowania danych ze źródłowego magazynu danych do docelowego magazynu danych.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 02/27/2020
ms.openlocfilehash: 5108335c0b5d0a51559653d51bfe6154237731f3
ms.sourcegitcommit: 1f738a94b16f61e5dad0b29c98a6d355f724a2c7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/28/2020
ms.locfileid: "78162658"
---
# <a name="copy-multiple-tables-in-bulk-by-using-azure-data-factory"></a>Zbiorcze kopiowanie wielu tabel przy użyciu usługi Azure Data Factory

W tym samouczku przedstawiono **kopiowanie wielu tabel z Azure SQL Database do usługi Azure Synapse Analytics (dawniej SQL DW)** . Tego samego wzorca można użyć także w innych scenariuszach kopiowania. Na przykład kopiowanie tabel z SQL Server/Oracle do Azure SQL Database/Azure Synapse Analytics (dawniej SQL DW)/Azure obiektu BLOB, kopiując różne ścieżki z obiektów BLOB do tabel Azure SQL Database.

> [!NOTE]
> - Jeśli jesteś nowym użytkownikiem usługi Azure Data Factory, zobacz [Wprowadzenie do usługi Azure Data Factory](introduction.md).

Na poziomie ogólnym ten samouczek obejmuje następujące kroki:

> [!div class="checklist"]
> * Tworzenie fabryki danych.
> * Twórz Azure SQL Database, Azure Synapse Analytics (dawniej SQL DW) i połączone usługi Azure Storage.
> * Twórz zestawy danych Azure SQL Database i Azure Synapse Analytics (dawniej SQL DW).
> * Tworzenie potoku w celu wyszukania tabel do skopiowania i innego potoku w celu wykonania samej operacji kopiowania. 
> * Uruchamianie potoku
> * Monitorowanie uruchomień potoku i działań.

W tym samouczku jest używana witryna Azure Portal. Aby dowiedzieć się więcej o zastosowaniu innych narzędzi/zestawów SDK do tworzenia fabryki danych, zapoznaj się z przewodnikami [Szybki start](quickstart-create-data-factory-dot-net.md). 

## <a name="end-to-end-workflow"></a>Kompletny przepływ pracy
W tym scenariuszu masz kilka tabel w Azure SQL Database, które chcesz skopiować do usługi Azure Synapse Analytics (dawniej SQL DW). Oto sekwencja logiczna kroków przepływu pracy, który następuje w potokach:

![Przepływ pracy](media/tutorial-bulk-copy-portal/tutorial-copy-multiple-tables.png)

* Pierwszy potok wyszukuje listę tabel, które należy skopiować do magazynów danych ujścia.  Alternatywnie można utrzymywać tabelę metadanych, która zawiera listę wszystkich tabel do skopiowania do magazynu danych ujścia. Następnie potok wywołuje inny potok, który działa na wszystkich tabelach w bazie danych i wykonuje operację kopiowania danych.
* Drugi potok przeprowadza rzeczywiste kopiowanie. Pobiera listę tabel jako parametr. Dla każdej tabeli na liście Skopiuj określoną tabelę w Azure SQL Database do odpowiedniej tabeli w usłudze Azure Synapse Analytics (dawniej SQL DW) przy użyciu [kopii przygotowanej przez magazyn obiektów blob i bazę danych](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) w celu uzyskania najlepszej wydajności. W tym przykładzie pierwszy potok przekazuje listę tabel jako wartość parametru. 

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne
* **Konto usługi Azure Storage**. Konto usługi Azure Storage jest używane jako przejściowy magazyn obiektów blob w operacji kopiowania zbiorczego. 
* **Usługa Azure SQL Database**. Ta baza danych zawiera dane źródłowe. 
* **Azure Synapse Analytics (dawniej SQL DW)** . Ten magazyn danych służy do przechowywania danych skopiowanych z bazy SQL Database. 

### <a name="prepare-sql-database-and-azure-synapse-analytics-formerly-sql-dw"></a>Przygotowanie SQL Database i usługi Azure Synapse Analytics (dawniej SQL DW)

**Przygotowywanie źródłowej bazy Azure SQL Database**:

Utwórz bazę Azure SQL Database z przykładowymi danymi Adventure Works LT zgodnie z artykułem [Tworzenie bazy danych Azure SQL Database](../sql-database/sql-database-get-started-portal.md). Ten samouczek kopiuje wszystkie tabele z tej przykładowej bazy danych do usługi Azure Synapse Analytics (dawniej SQL DW).

**Przygotuj ujścia usługi Azure Synapse Analytics (dawniej SQL DW)** :

1. Jeśli nie masz usługi Azure Synapse Analytics (dawniej SQL DW), zapoznaj się z artykułem [tworzenie SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-get-started-tutorial.md) .

1. Utwórz odpowiednie schematy tabel w usłudze Azure Synapse Analytics (dawniej SQL DW). Usługa Azure Data Factory służy do migrowania/kopiowania danych na późniejszym etapie.

## <a name="azure-services-to-access-sql-server"></a>Usługi platformy Azure umożliwiające dostęp do serwera SQL

W przypadku SQL Database i usługi Azure Synapse Analytics (dawniej SQL DW) Zezwól usługom platformy Azure na dostęp do programu SQL Server. Upewnij się, że w celu uzyskania dostępu do tego ustawienia serwera na serwerze SQL Azure **jest włączone ustawienie** **Zezwalaj usługom i zasobom platformy Azure na dostęp** . To ustawienie umożliwia usłudze Data Factory odczytywanie danych z Azure SQL Database i zapisywanie danych w usłudze Azure Synapse Analytics (dawniej SQL DW). 

Aby sprawdzić i włączyć to ustawienie, przejdź do usługi Azure SQL Server > Zabezpieczenia > zapory i sieci wirtualne > Ustaw opcję **Zezwól usługom i zasobom platformy Azure na dostęp do tego serwera** **.**

## <a name="create-a-data-factory"></a>Tworzenie fabryki danych
1. Uruchom przeglądarkę internetową **Microsoft Edge** lub **Google Chrome**. Obecnie interfejs użytkownika usługi Data Factory jest obsługiwany tylko przez przeglądarki internetowe Microsoft Edge i Google Chrome.
1. Przejdź do witryny [Azure Portal](https://portal.azure.com). 
1. Po lewej stronie menu Azure Portal wybierz pozycję **Utwórz zasób** > **Analytics** > **Data Factory**. 
   ![Data Factory zaznaczenie w okienku "nowy"](./media/doc-common-process/new-azure-data-factory-menu.png)
1. Na stronie **Nowa fabryka danych** wprowadź **ADFTutorialBulkCopyDF** w polu **Nazwa**. 
 
   Nazwa fabryki danych platformy Azure musi być **globalnie unikatowa**. Jeśli dla pola nazwy wystąpi poniższy błąd, zmień nazwę fabryki danych (np. twojanazwaADFTutorialBulkCopyDF). Artykuł [Data Factory — Naming Rules (Usługa Data Factory — reguły nazewnictwa)](naming-rules.md) zawiera reguły nazewnictwa artefaktów usługi Data Factory.
  
       `Data factory name “ADFTutorialBulkCopyDF” is not available`
1. Wybierz **subskrypcję** Azure, w której chcesz utworzyć fabrykę danych. 
1. Dla opcji **Grupa zasobów** wykonaj jedną z następujących czynności:
     
   - Wybierz pozycję **Użyj istniejącej**, a następnie wybierz istniejącą grupę zasobów z listy rozwijanej. 
   - Wybierz pozycję **Utwórz nową**, a następnie wprowadź nazwę grupy zasobów.   
         
     Informacje na temat grup zasobów znajdują się w artykule [Using resource groups to manage your Azure resources](../azure-resource-manager/management/overview.md) (Używanie grup zasobów do zarządzania zasobami platformy Azure).  
1. Wybierz opcję **V2** w obszarze **Wersja**.
1. Na liście **lokalizacja** wybierz lokalizację fabryki danych. Aby uzyskać listę regionów platformy Azure, w których obecnie jest dostępna usługa Data Factory, wybierz dane regiony na poniższej stronie, a następnie rozwiń węzeł **Analiza**, aby zlokalizować pozycję **Data Factory**: [Produkty dostępne według regionu](https://azure.microsoft.com/global-infrastructure/services/). Magazyny danych (Azure Storage, Azure SQL Database itp.) i jednostki obliczeniowe (HDInsight itp.) używane przez fabrykę danych mogą mieścić się w innych regionach.
1. Kliknij przycisk **Utwórz**.
1. Po zakończeniu tworzenia wybierz pozycję **Przejdź do zasobu** , aby przejść do strony **Data Factory** . 
   
1. Kliknij kafelek **Tworzenie i monitorowanie**, aby w osobnej karcie uruchomić aplikację interfejsu użytkownika usługi Data Factory.
1. Na stronie **wprowadzenie przejdź do** karty **autor** w lewym panelu, jak pokazano na poniższej ilustracji:

     ![Strona Wprowadzenie](./media/doc-common-process/get-started-page-author-button.png)

## <a name="create-linked-services"></a>Tworzenie połączonych usług
Połączone usługi są tworzone w celu połączenia magazynów danych i obliczeń z fabryką danych. Połączona usługa ma informacje o połączeniu, których usługa Data Factory używa do nawiązywania połączenia z magazynem danych w środowisku uruchomieniowym. 

W ramach tego samouczka nawiążesz połączenie Azure SQL Database, usługi Azure Synapse Analytics (dawniej SQL DW) i magazynów danych platformy Azure Blob Storage do fabryki danych. Usługa Azure SQL Database to magazyn danych będący źródłem. Usługa Azure Synapse Analytics (wcześniej SQL DW) to magazyn danych ujścia/docelowy. Usługa Azure Blob Storage polega na przygotowaniu danych przed załadowaniem danych do usługi Azure Synapse Analytics (dawniej SQL DW) za pomocą programu Base. 

### <a name="create-the-source-azure-sql-database-linked-service"></a>Tworzenie źródłowej połączonej usługi Azure SQL Database
W tym kroku utworzysz połączoną usługę służącą do łączenia bazy danych Azure SQL Database z fabryką danych. 

1. Kliknij pozycję **połączenia** w dolnej części okna, a następnie kliknij pozycję **+ Nowy** na pasku narzędzi (przycisk**połączenia** znajduje się u dołu lewej kolumny w obszarze **zasoby fabryki**). 

1. W oknie **Nowa połączona usługa** wybierz pozycję **Azure SQL Database**, a następnie kliknij pozycję **Kontynuuj**. 
1. W oknie **Nowa połączona usługa (Azure SQL Database)** wykonaj następujące czynności: 

    a. Wprowadź wartość **AzureSqlDatabaseLinkedService** w polu **Nazwa**.
    
    b. W polu **Nazwa serwera** wybierz swój serwer usługi Azure SQL.
    
    c. W polu **Nazwa bazy danych** wybierz swoją bazę danych Azure SQL Database. 
    
    d. Wprowadź **nazwę użytkownika**, aby nawiązać połączenie z bazą danych Azure SQL Database. 
    
    e. Wprowadź **hasło** dla użytkownika. 

    f. Aby przetestować połączenie z bazą danych Azure SQL Database przy użyciu określonych informacji, kliknij pozycję **Testuj połączenie**.
  
    g. Kliknij przycisk **Utwórz** , aby zapisać połączoną usługę.


### <a name="create-the-sink-azure-synapse-analytics-formerly-sql-dw-linked-service"></a>Tworzenie połączonej usługi Azure Synapse Analytics (dawniej SQL DW)

1. Na karcie **Połączenia** kliknij ponownie pozycję **+ Nowy** na pasku narzędzi. 
1. W oknie **Nowa połączona usługa** wybierz pozycję **Azure Synapse Analytics (wcześniej SQL DW)** , a następnie kliknij pozycję **Kontynuuj**. 
1. W oknie **Nowa połączona usługa (dawniej SQL DW)** okno wykonaj następujące czynności: 
   
    a. Wprowadź wartość **AzureSqlDWLinkedService** w polu **Nazwa**.
     
    b. W polu **Nazwa serwera** wybierz swój serwer usługi Azure SQL.
     
    c. W polu **Nazwa bazy danych** wybierz swoją bazę danych Azure SQL Database. 
     
    d. Wprowadź **nazwę użytkownika** , aby nawiązać połączenie z usługą Azure SQL Database. 
     
    e. Wprowadź **hasło** dla użytkownika. 
     
    f. Aby przetestować połączenie z bazą danych Azure SQL Database przy użyciu określonych informacji, kliknij pozycję **Testuj połączenie**.
     
    g. Kliknij przycisk **Utwórz**.

### <a name="create-the-staging-azure-storage-linked-service"></a>Tworzenie przejściowej połączonej usługi Azure Storage
W tym samouczku magazyn obiektów blob platformy Azure służy jako obszar przejściowy, pozwalający na włączenie programu PolyBase w celu podniesienia wydajności kopiowania.

1. Na karcie **Połączenia** kliknij ponownie pozycję **+ Nowy** na pasku narzędzi. 
1. W oknie **Nowa połączona usługa** wybierz pozycję **Azure Blob Storage**, a następnie kliknij pozycję **Kontynuuj**. 
1. W oknie **Nowa połączona usługa (Azure Blob Storage)** wykonaj następujące czynności: 

    a. Wprowadź wartość **AzureStorageLinkedService** w polu **Nazwa**.                                                 
    b. Wybierz swoje **konto usługi Azure Storage** w polu **Nazwa konta magazynu**.
    
    c. Kliknij przycisk **Utwórz**.


## <a name="create-datasets"></a>Tworzenie zestawów danych
W tym samouczku utworzysz zestawy danych będące źródłem i ujściem, określające lokalizację przechowywania danych. 

Wejściowy zestaw danych **AzureSqlDatabaseDataset** odwołuje się do elementu **AzureSqlDatabaseLinkedService**. Połączona usługa określa parametry połączenia w celu nawiązania połączenia z bazą danych. Zestaw danych określa nazwę bazy danych i tabelę, która zawiera dane źródłowe. 

Wyjściowy zestaw danych **AzureSqlDWDataset** odwołuje się do elementu **AzureSqlDWLinkedService**. Połączona usługa określa parametry połączenia w celu nawiązania połączenia z usługą Azure Synapse Analytics (dawniej SQL DW). Zestaw danych określa bazę danych i tabelę, do którego dane są kopiowane. 

W tym samouczku źródłowe i docelowe tabele SQL nie są ustalone w definicjach zestawów danych. W zamian działanie ForEach przekazuje nazwę tabeli w czasie wykonywania do działania kopiowania. 

### <a name="create-a-dataset-for-source-sql-database"></a>Tworzenie zestawu danych źródłowej bazy danych SQL Database

1. Kliknij pozycję **+ (plus)** w okienku po lewej stronie, a następnie kliknij pozycję **zestaw danych**. 

    ![Menu Nowy zestaw danych](./media/tutorial-bulk-copy-portal/new-dataset-menu.png)
1. W oknie **Nowy zestaw danych** wybierz pozycję **Azure SQL Database**, a następnie kliknij przycisk **Kontynuuj**. 
    
1. W oknie **dialogowym Ustawianie właściwości** w obszarze **Nazwa**wprowadź **AzureSqlDatabaseDataset**. W obszarze **połączona usługa**wybierz pozycję **AzureSqlDatabaseLinkedService**. Następnie kliknij przycisk **OK**.

1. Przejdź do karty **połączenie** , a następnie wybierz dowolną tabelę dla **tabeli**. Jest to tabela fikcyjna. Zapytanie dotyczące źródłowego zestawu danych jest określane podczas tworzenia potoku. Zapytanie służy do wyodrębniania danych z bazy danych Azure SQL Database. Alternatywnie możesz kliknąć przycisk **Edytuj** , a następnie w polu Nazwa tabeli wprowadzić nazwę **dbo. fikcyjnname.** 
 

### <a name="create-a-dataset-for-sink-azure-synapse-analytics-formerly-sql-dw"></a>Tworzenie zestawu danych dla ujścia usługi Azure Synapse Analytics (dawniej: SQL DW)

1. Kliknij pozycję **+ (plus)** w lewym okienku, a następnie kliknij pozycję **Zestaw danych**. 
1. W oknie **Nowy zestaw danych** wybierz pozycję **Azure Synapse Analytics (wcześniej SQL DW)** , a następnie kliknij przycisk **Kontynuuj**.
1. W oknie **dialogowym Ustawianie właściwości** w obszarze **Nazwa**wprowadź **AzureSqlDWDataset**. W obszarze **połączona usługa**wybierz pozycję **AzureSqlDWLinkedService**. Następnie kliknij przycisk **OK**.
1. Przejdź do karty **Parametry**, a następnie kliknij pozycję **+ Nowy** i wprowadź ciąg **DWTableName** jako nazwę parametru. Jeśli skopiujesz/wkleisz tę nazwę ze strony, upewnij się, że na końcu elementu **DWTableName**nie ma końcowego **znaku spacji** .
1. Przejdź do karty **Połączenie**. 

    a. W polu **tabela**zaznacz opcję **Edytuj** . W polu tekstowym Nazwa pierwszej tabeli wprowadź nazwę **dbo** . A następnie zaznacz pole wyboru w drugim polu wejściowym i kliknij link **Dodaj zawartość dynamiczną** poniżej. 

    ![Zestaw danych ConnectionName](./media/tutorial-bulk-copy-portal/dataset-connection-tablename.png)

    b. Na stronie **Dodaj zawartość dynamiczną** kliknij pozycję **DWTAbleName** w obszarze **Parametry**, co spowoduje automatyczne wypełnienie `@dataset().DWTableName`pola tekstowego wyrażenie Top, a następnie kliknij przycisk **Zakończ**. Właściwość **tableName** zestawu danych jest ustawiana na wartość przekazywaną jako argument parametru **DWTableName**. Działanie ForEach iteruje w obrębie listy tabel i przekazuje je po jednej do działania Copy (Kopiowanie). 

    ![Konstruktor parametrów zestawu danych](./media/tutorial-bulk-copy-portal/dataset-parameter-builder.png)
 
## <a name="create-pipelines"></a>Tworzenie potoków
W tym samouczku tworzysz dwa potoki: **IterateAndCopySQLTables** i **GetTableListAndTriggerCopyData**. 

Potok **GetTableListAndTriggerCopyData** wykonuje dwie czynności:

* Wyszukuje tabelę systemową bazy Azure SQL Database w celu pobrania listy tabel do skopiowania.
* Wyzwala potok **IterateAndCopySQLTables**, który przeprowadza rzeczywiste kopiowanie danych.

Potok **IterateAndCopySQLTables** pobiera listę tabel jako parametr. Dla każdej tabeli na liście kopiuje dane z tabeli w Azure SQL Database do usługi Azure Synapse Analytics (dawniej SQL DW) przy użyciu kopii etapowej i bazy danych.

### <a name="create-the-pipeline-iterateandcopysqltables"></a>Tworzenie potoku IterateAndCopySQLTables

1. W lewym okienku kliknij pozycję **+ (plus)** , a następnie kliknij pozycję **Potok**.

    ![Menu Nowy potok](./media/tutorial-bulk-copy-portal/new-pipeline-menu.png)
1. Na karcie **Ogólne** określ jako nazwę wartość **IterateAndCopySQLTables**. 

1. Przejdź do karty **Parametry** i wykonaj następujące czynności: 

    a. Kliknij pozycję **+ Nowy**. 
    
    b. Wprowadź **ciąg tablelist jako** dla **nazwy**parametru.
    
    c. Wybierz wartość **Array** w polu **Typ**.

1. W przyborniku **Działania** rozwiń pozycję **Iteracja i warunki**, a następnie przeciągnij i upuść działanie **ForEach** do powierzchni projektu potoku. Możesz również wyszukać działania w przyborniku **Działania**. 

    a. Na karcie **Ogólne** u dołu wprowadź wartość **IterateSQLTables** w polu **Nazwa**. 

    b. Przejdź do karty **Ustawienia** , kliknij pole wejściowe dla **pozycji elementy**, a następnie kliknij link **Dodaj zawartość dynamiczną** poniżej. 

    c. Na stronie **Dodaj zawartość dynamiczną** Zwiń sekcje **systemowe** i **funkcje** , kliknij **ciąg tablelist jako** w obszarze **Parametry**, co spowoduje automatyczne wypełnienie pola tekstowego pierwsze wyrażenie jako `@pipeline().parameter.tableList`. Następnie kliknij przycisk **Zakończ**. 

    ![Konstruktor parametru ForEach](./media/tutorial-bulk-copy-portal/for-each-parameter-builder.png)
    
    d. Przejdź do karty **działania** , kliknij **ikonę ołówka** , aby dodać działanie podrzędne do działania **foreach** .
    ![Konstruktor działań foreach](./media/tutorial-bulk-copy-portal/for-each-activity-builder.png)

1. W przyborniku **działania** rozwiń pozycję **Przenieś & transfer**, a następnie przeciągnij i upuść działanie **Kopiuj dane** do powierzchni projektanta potoku. Zwróć uwagę na menu linków do stron nadrzędnych w górnej części. **IterateAndCopySQLTable** jest nazwą potoku, a **wartość iteratesqltables** jest nazwą działania foreach. Projektant należy do zakresu działania. Aby przełączyć się z powrotem do edytora potoku z edytora ForEach, można kliknąć link w menu łączy. 

    ![Kopiowanie w działaniu ForEach](./media/tutorial-bulk-copy-portal/copy-in-for-each.png)

1. Przejdź do karty **Źródło** i wykonaj następujące czynności:

    1. Wybierz wartość **AzureSqlDatabaseDataset** w polu **Źródło danych**. 
    1. Wybierz opcję **zapytania** dla opcji **Użyj zapytania**. 
    1. Kliknij pole wprowadzania **Zapytanie** -> wybierz pozycję **Dodaj zawartość dynamiczną** poniżej -> wprowadź poniższe wyrażenie w polu **Zapytanie** -> wybierz przycisk **Zakończ**.

        ```sql
        SELECT * FROM [@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]
        ``` 


1. Przejdź do karty **Ujście** i wykonaj następujące czynności: 

    1. Wybierz pozycję **AzureSqlDWDataset** w polu **Zestaw danych będący ujściem**.
    1. Kliknij pole wejściowe dla wartości parametru DWTableName-> wybierz pozycję **Dodaj zawartość dynamiczną** poniżej, wprowadź wyrażenie `[@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]` jako skrypt, > wybierz pozycję **Zakończ**.
    1. W przypadku metody copy wybierz opcję **Base**. 
    1. Wyczyść opcję **Użyj opcji domyślnej** . 
    1. Kliknij pole wprowadzania **Skrypt wstępnego kopiowania** -> wybierz pozycję **Dodaj zawartość dynamiczną** poniżej -> wprowadź poniższe wyrażenie jako skrypt -> wybierz przycisk **Zakończ**. 

        ```sql
        TRUNCATE TABLE [@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]
        ```

        ![Ustawienia ujścia kopiowania](./media/tutorial-bulk-copy-portal/copy-sink-settings.png)
1. Przejdź do karty **Ustawienia** i wykonaj następujące czynności: 

    1. Zaznacz pole wyboru **Włącz przemieszczanie**.
    1. Wybierz wartość **AzureStorageLinkedService** w polu **Połączona usługa konta magazynu**.

1. Aby zweryfikować ustawienia potoku, kliknij pozycję **Weryfikuj** na górnym pasku narzędzi dla potoku. Upewnij się, że nie wystąpił błąd sprawdzania poprawności. Aby zamknąć okno **Raport weryfikacji potoku**, kliknij pozycję **>>** .

### <a name="create-the-pipeline-gettablelistandtriggercopydata"></a>Tworzenie potoku GetTableListAndTriggerCopyData

Ten potok wykonuje dwie czynności:

* Wyszukuje tabelę systemową bazy Azure SQL Database w celu pobrania listy tabel do skopiowania.
* Wyzwala potok „IterateAndCopySQLTables”, który przeprowadza rzeczywiste kopiowanie danych.

1. W lewym okienku kliknij pozycję **+ (plus)** , a następnie kliknij pozycję **Potok**.
1. Na karcie **Ogólne** Zmień nazwę potoku na **GetTableListAndTriggerCopyData**. 

1. W przyborniku **działania** rozwiń węzeł **Ogólne**, a następnie przeciągnij i upuść działanie **Lookup (wyszukiwanie** ) do powierzchni projektanta potoku i wykonaj następujące czynności:

    1. Wprowadź wartość **LookupTableList** w polu **Nazwa**. 
    1. Wprowadź wartość **Pobieranie listy tabel z bazy danych Azure SQL Database** w polu **Opis**.

1. Przejdź do karty **Ustawienia** i wykonaj następujące czynności:

    1. Wybierz wartość **AzureSqlDatabaseDataset** w polu **Źródło danych**. 
    1. Wybierz pozycję **zapytanie** dla opcji **Użyj zapytania**. 
    1. W obszarze **Zapytanie** wprowadź następujące zapytanie SQL.

        ```sql
        SELECT TABLE_SCHEMA, TABLE_NAME FROM information_schema.TABLES WHERE TABLE_TYPE = 'BASE TABLE' and TABLE_SCHEMA = 'SalesLT' and TABLE_NAME <> 'ProductModel'
        ```
    1. Usuń zaznaczenie pola wyboru **Tylko pierwszy wiersz**.

        ![Działanie Lookup (Wyszukiwanie) — strona ustawień](./media/tutorial-bulk-copy-portal/lookup-settings-page.png)
1. Przeciągnij i upuść działanie **Wykonaj potoku** z przybornika działania na powierzchnię projektanta potoku i Ustaw nazwę na **TriggerCopy**.

1. Aby **połączyć** działanie **Lookup** z działaniem **Wykonaj potok** , przeciągnij **zielony pole** dołączone do działania Lookup (wyszukiwanie) na lewo od działania wykonaj potoku.

    ![Łączenie działań Lookup (Wyszukiwanie) i Execute Pipeline (Wykonywanie potoku)](./media/tutorial-bulk-copy-portal/connect-lookup-execute-pipeline.png)

1. Przejdź do karty **Ustawienia** działania **wykonywania potoku** i wykonaj następujące czynności: 

    1. Wybierz wartość **IterateAndCopySQLTables** w polu **Wywołany potok**. 
    1. Rozwiń sekcję **Zaawansowane** i usuń zaznaczenie pola wyboru **Zaczekaj na zakończenie**.
    1. Kliknij pozycję **+ Nowy** w sekcji **Parametry**. 
    1. Wprowadź **ciąg tablelist jako** dla **nazwy**parametru.
    1. Kliknij pole wprowadzania WARTOŚĆ -> wybierz pozycję **Dodaj zawartość dynamiczną** poniżej -> wprowadź `@activity('LookupTableList').output.value` jako wartość nazwy tabeli -> wybierz przycisk **Zakończ**. Ustawiasz listę wyników działania wyszukiwania jako dane wejściowe do drugiego potoku. Lista wyników zawiera listę tabel, których dane trzeba skopiować do miejsca docelowego. 

        ![Działanie Execute Pipeline (Wykonywanie potoku) — strona Ustawienia](./media/tutorial-bulk-copy-portal/execute-pipeline-settings-page.png)

1. Aby zweryfikować potok, kliknij pozycję **Weryfikuj** na pasku narzędzi. Potwierdź, że weryfikacja nie zwróciła błędów. Aby zamknąć okno **Raport weryfikacji potoku**, kliknij pozycję **>>** .

1. Aby opublikować jednostki (zestawy danych, potoki itp.) w usłudze Data Factory, kliknij przycisk **Opublikuj wszystko** w górnej części okna. Poczekaj na pomyślne zakończenie publikowania. 

## <a name="trigger-a-pipeline-run"></a>Wyzwalanie uruchomienia potoku

1. Przejdź do **GetTableListAndTriggerCopyData**potoku, kliknij pozycję **Dodaj wyzwalacz** na górnym pasku narzędzi potoku, a następnie kliknij pozycję **Wyzwól teraz**. 

1. Potwierdź uruchomienie na stronie **uruchomienia potoku** , a następnie wybierz pozycję **Zakończ**.

## <a name="monitor-the-pipeline-run"></a>Monitorowanie działania potoku

1. Przejdź do karty **monitorowanie** . kliknij pozycję **Odśwież** , aby zobaczyć przebiegi dla potoków w rozwiązaniu. Kontynuuj odświeżanie listy do momentu wyświetlenia stanu **Powodzenie**. 

1. Aby wyświetlić uruchomienia działań skojarzone z potoku **GetTableListAndTriggerCopyData** , kliknij link Nazwa potoku dla potoku. Powinny zostać wyświetlone dwa uruchomienia działania dla tego uruchomienia potoku. 
    ![uruchomienie potoku monitorowania](./media/tutorial-bulk-copy-portal/monitor-pipeline.png)
1. Aby wyświetlić dane wyjściowe działania **Lookup (wyszukiwanie** ), kliknij link **wyjściowy** obok działania w kolumnie **Nazwa działania** . Okno **Dane wyjściowe** można maksymalizować i przywracać. Po przejrzeniu kliknij znak **X**, aby zamknąć okno **Dane wyjściowe**.

    ```json
    {
        "count": 9,
        "value": [
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "Customer"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "ProductDescription"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "Product"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "ProductModelProductDescription"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "ProductCategory"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "Address"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "CustomerAddress"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "SalesOrderDetail"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "SalesOrderHeader"
            }
        ],
        "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US)",
        "effectiveIntegrationRuntimes": [
            {
                "name": "DefaultIntegrationRuntime",
                "type": "Managed",
                "location": "East US",
                "billedDuration": 0,
                "nodes": null
            }
        ]
    }
    ```    
1. Aby przełączyć się z powrotem do widoku **uruchomienia potoków** , kliknij link **wszystkie uruchomienia potoków** w górnej części menu stron nadrzędnych. Kliknij link **IterateAndCopySQLTables** (w kolumnie **Nazwa potoku** ), aby wyświetlić uruchomienia działania potoku. Zauważ, że dla każdej tabeli w danych wyjściowych działania **Lookup** jest uruchomione jedno działanie **kopiowania** . 

1. Upewnij się, że dane zostały skopiowane do docelowej usługi Azure Synapse Analytics (dawniej SQL DW) użytego w tym samouczku. 

## <a name="next-steps"></a>Następne kroki
W ramach tego samouczka wykonano następujące procedury: 

> [!div class="checklist"]
> * Tworzenie fabryki danych.
> * Twórz Azure SQL Database, Azure Synapse Analytics (dawniej SQL DW) i połączone usługi Azure Storage.
> * Twórz zestawy danych Azure SQL Database i Azure Synapse Analytics (dawniej SQL DW).
> * Tworzenie potoku w celu wyszukania tabel do skopiowania i innego potoku w celu wykonania samej operacji kopiowania. 
> * Uruchamianie potoku
> * Monitorowanie uruchomień potoku i działań.

Przejdź do poniższego samouczka, aby dowiedzieć się, jak przyrostowo kopiować dane z lokalizacji źródłowej do docelowej:
> [!div class="nextstepaction"]
>[Przyrostowe kopiowanie danych](tutorial-incremental-copy-portal.md)
