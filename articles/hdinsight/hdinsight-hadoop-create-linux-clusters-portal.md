---
title: Tworzenie klastrów Apache Hadoop przy użyciu przeglądarki sieci Web, Azure HDInsight
description: Dowiedz się, jak tworzyć klastry Apache Hadoop, Apache HBase, Apache Storm lub Apache Spark w systemie Linux dla usługi HDInsight przy użyciu przeglądarki sieci Web i Azure Portal.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/19/2020
ms.openlocfilehash: bcc71b7244ba3498b2fcde023d372e67f41d6117
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/26/2020
ms.locfileid: "77623293"
---
# <a name="create-linux-based-clusters-in-hdinsight-by-using-the-azure-portal"></a>Tworzenie klastrów opartych na systemie Linux w usłudze HDInsight przy użyciu Azure Portal

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Azure Portal to oparte na sieci Web narzędzie do zarządzania usługami i zasobami hostowanymi w chmurze Microsoft Azure. W tym artykule dowiesz się, jak tworzyć klastry usługi Azure HDInsight oparte na systemie Linux przy użyciu portalu. Dodatkowe szczegóły są dostępne w obszarze [Tworzenie klastrów usługi HDInsight](./hdinsight-hadoop-provision-linux-clusters.md).

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Azure Portal uwidacznia większość właściwości klastra. Za pomocą szablonów Azure Resource Manager można ukryć wiele szczegółów. Aby uzyskać więcej informacji, zobacz [Tworzenie klastrów Apache Hadoop w usłudze HDInsight przy użyciu szablonów Menedżer zasobów](hdinsight-hadoop-create-linux-clusters-arm-templates.md).

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-clusters"></a>Tworzenie klastrów

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]

1. Zaloguj się do [Azure portal](https://portal.azure.com).

1. W górnym menu wybierz pozycję **+ Utwórz zasób**.

    ![Utwórz nowy klaster w Azure Portal](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-create-resource.png "Tworzenie nowego klastra w Azure Portal")

1. Wybierz pozycję **analiza** > **usłudze Azure HDInsight** , aby przejść do strony **Tworzenie klastra usługi HDInsight** .

## <a name="basics"></a>Podstawy

![Podstawowe informacje dotyczące tworzenia klastra usługi HDInsight](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-basics.png "Tworzenie nowego klastra w Azure Portal")

Na karcie **podstawowe** podaj następujące informacje:

|Właściwość |Opis |
|---|---|
|Subskrypcja|Z listy rozwijanej wybierz subskrypcję platformy Azure używaną na potrzeby klastra.|
|Grupa zasobów|Z listy rozwijanej wybierz istniejącą grupę zasobów lub wybierz pozycję **Utwórz nową**.|
|Nazwa klastra|Wprowadź unikatową nazwę globalną.|
|Region|Z listy rozwijanej wybierz region, w którym tworzony jest klaster.|
|Typ klastra|Wybierz pozycję **Wybierz typ klastra** , aby otworzyć listę. Wybierz z listy żądany typ klastra. Klastry usługi HDInsight są dostępne w różnych typach. Są one zgodne z obciążeniem lub technologią, dla której jest dostrojony klaster. Nie ma obsługiwanej metody tworzenia klastra, który łączy wiele typów.|
|Wersja|Z listy rozwijanej wybierz **wersję**. Użyj wersji domyślnej, jeśli nie wiesz, co wybrać. Więcej informacji można znaleźć w temacie [HDInsight cluster versions](hdinsight-component-versioning.md) (Wersje klastrów usługi HDInsight).|
|Nazwa użytkownika logowania klastra|Podaj nazwę użytkownika — wartość domyślna to **admin**.|
|Hasło logowania klastra|Podaj hasło.|
|Potwierdź hasło logowania klastra|Wprowadź ponownie hasło|
|Nazwa użytkownika protokołu SSH (Secure Shell)|Podaj nazwę użytkownika, wartość domyślna to **sshuser**|
|Użyj hasła logowania do klastra dla protokołu SSH|Jeśli chcesz użyć tego samego hasła SSH jako hasła administratora określonego wcześniej, zaznacz pole wyboru **Użyj hasła logowania klastra dla SSH** . Jeśli nie, podaj **hasło** lub **klucz publiczny** , aby uwierzytelnić użytkownika ssh. Zaleca się użycie klucza publicznego. Wybierz **pozycję Wybierz** u dołu, aby zapisać konfigurację poświadczeń.  Aby uzyskać więcej informacji, zobacz [nawiązywanie połączenia z usługą HDInsight (Apache Hadoop) przy użyciu protokołu SSH](hdinsight-hadoop-linux-use-ssh-unix.md).|

Wybierz pozycję **Dalej: > magazynu >** , aby przejść do następnej karty.

## <a name="storage"></a>Storage

![Magazyn tworzenia klastra usługi HDInsight](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-storage.png "Tworzenie nowego klastra w magazynie Azure Portal")

### <a name="primary-storage"></a>Magazyn podstawowy

Z listy rozwijanej **Typ magazynu podstawowego**wybierz swój domyślny typ magazynu. Kolejne pola, które mają zostać wykonane, różnią się w zależności od dokonanego wyboru. W przypadku **usługi Azure Storage**:

1. W **obszarze Metoda wyboru**wybierz opcję **Wybierz z listy**lub **Użyj klucza dostępu**.
    * W polu **Wybierz z listy**wybierz swoje **podstawowe konto magazynu** z listy rozwijanej lub wybierz pozycję **Utwórz nową**.
    * Aby **użyć klucza dostępu**, wprowadź **nazwę konta magazynu**. Następnie podaj **klucz dostępu**.

1. W polu **kontener**Zaakceptuj wartość domyślną lub wprowadź nową.

### <a name="additional-azure-storage"></a>Dodatkowa usługa Azure Storage

Opcjonalne: wybierz pozycję **Dodaj usługę Azure Storage** , aby uzyskać dodatkowe miejsce w magazynie klastra. Użycie dodatkowego konta magazynu w innym regionie niż klaster usługi HDInsight nie jest obsługiwane.

### <a name="metastore-settings"></a>Ustawienia magazynu metadanych

Opcjonalnie: Określ istniejące SQL Database, aby zapisać w metadanych Apache Hive, Apache Oozie i/lub Apache Ambari poza klastrem. Azure SQL Database, który jest używany dla magazynu metadanych, musi zezwalać na łączność z innymi usługami platformy Azure, w tym usługą Azure HDInsight. Podczas tworzenia magazynu metadanych nie należy nazwać bazy danych za pomocą łączników ani łączników. Te znaki mogą spowodować niepowodzenie procesu tworzenia klastra.

Wybierz pozycję **Dalej: zabezpieczenia i sieć > >** , aby przejść do następnej karty.

## <a name="security--networking"></a>Bezpieczeństwo i obsługa sieci

![Tworzenie sieci zabezpieczeń klastrów w usłudze HDInsight](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-security-networking.png "Tworzenie sieci zabezpieczeń klastrów w usłudze HDInsight")

Na karcie **zabezpieczenia i sieć** podaj następujące informacje:

|Właściwość |Opis |
|---|---|
|Pakiet zabezpieczeń przedsiębiorstwa|Opcjonalne: zaznacz pole wyboru, aby użyć **pakiet Enterprise Security**. Aby uzyskać więcej informacji, zobacz [Konfigurowanie klastra usługi HDInsight za pomocą pakiet Enterprise Security przy użyciu Azure Active Directory Domain Services](./domain-joined/apache-domain-joined-configure-using-azure-adds.md).|
|TLS|Opcjonalne: Wybierz wersję protokołu TLS z listy rozwijanej. Aby uzyskać więcej informacji, zobacz [Transport Layer Security](./hdinsight-plan-virtual-network-deployment.md#transport-layer-security).|
|Sieć wirtualna|Opcjonalne: wybierz istniejącą sieć wirtualną i podsieć z listy rozwijanej. Aby uzyskać więcej informacji, zobacz [Planowanie wdrożenia sieci wirtualnej dla klastrów usługi Azure HDInsight](hdinsight-plan-virtual-network-deployment.md). Artykuł zawiera określone wymagania dotyczące konfiguracji dla sieci wirtualnej.|
|Ustawienia szyfrowania dysku|Opcjonalne: zaznacz pole wyboru, aby użyć szyfrowania. Aby uzyskać więcej informacji, zobacz [szyfrowanie dysków kluczy zarządzanych przez klienta](./disk-encryption.md).|
|Serwer proxy REST Kafka|To ustawienie jest dostępne tylko dla typu klastra Kafka. Aby uzyskać więcej informacji, zobacz [Korzystanie z serwera proxy REST](./kafka/rest-proxy.md).|
|Tożsamość|Opcjonalnie: wybierz istniejącą tożsamość usługi przypisaną przez użytkownika z listy rozwijanej. Aby uzyskać więcej informacji, zobacz [zarządzane tożsamości w usłudze Azure HDInsight](./hdinsight-managed-identities.md).|

Wybierz kolejno pozycje **Dalej: Konfiguracja i cennik > >** , aby przejść do następnej karty.

## <a name="configuration--pricing"></a>Konfiguracja i Cennik

![Konfiguracja tworzenia klastra usługi HDInsight](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-configuration.png "Karta Konfiguracja i Cennik")

Na karcie **Konfiguracja i Cennik** podaj następujące informacje:

|Właściwość |Opis |
|---|---|
|+ Dodaj aplikację|Opcjonalne: Wybierz wszystkie aplikacje, które chcesz. Firma Microsoft, niezależni dostawcy oprogramowania (ISV) lub mogą opracowywać te aplikacje. Aby uzyskać więcej informacji, zobacz [Instalowanie aplikacji podczas tworzenia klastra](hdinsight-apps-install-applications.md#install-applications-during-cluster-creation).|
|Rozmiar węzła|Opcjonalne: wybierz węzeł o różnej wielkości.|
|Liczba węzłów|Opcjonalnie: wprowadź liczbę węzłów dla określonego typu węzła. Jeśli planujesz więcej niż 32 węzłów procesu roboczego, wybierz rozmiar węzła głównego z co najmniej 8 rdzeniami i 14 GB pamięci RAM. Planowanie węzłów podczas tworzenia klastra lub skalowanie klastra po utworzeniu.|
|Włącz automatyczne skalowanie|Opcjonalne: zaznacz pole wyboru, aby włączyć tę funkcję. Aby uzyskać więcej informacji, zobacz [Automatyczne skalowanie klastrów usługi Azure HDInsight](./hdinsight-autoscale-clusters.md).|
|+ Dodaj akcję skryptu|Opcjonalnie: Ta opcja działa, jeśli chcesz użyć niestandardowego skryptu w celu dostosowania klastra podczas tworzenia klastra. Aby uzyskać więcej informacji na temat akcji skryptu, zobacz [Dostosowywanie klastrów usługi HDInsight opartych na systemie Linux za pomocą akcji skryptu](hdinsight-hadoop-customize-cluster-linux.md).|

Wybierz pozycję **Przegląd + utwórz > >** , aby sprawdzić poprawność konfiguracji klastra i przejść do ostatniej karty.

## <a name="review--create"></a>Przegląd + tworzenie

![Podsumowanie tworzenia klastra usługi HDInsight](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-review-create-hadoop.png "Określ liczbę węzłów klastra")

Przejrzyj ustawienia. Wybierz opcję **Utwórz**, aby utworzyć klaster.

Tworzenie klastra zajmuje trochę czasu, zwykle około 20 minut. Monitoruj **powiadomienia** , aby sprawdzić proces aprowizacji.

## <a name="post-creation"></a>Tworzenie po utworzeniu

Po zakończeniu procesu tworzenia wybierz pozycję **Przejdź do zasobu** w ramach powiadomienia o **pomyślnym wdrożeniu** . Okno klaster zawiera poniższe informacje.

![HDI klaster Azure Portal — Omówienie](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-completed.png "Właściwości klastra")

Niektóre ikony w oknie są wyjaśnione w następujący sposób:

|Właściwość | Opis |
|---|---|
|Omówienie|Zawiera wszystkie podstawowe informacje o klastrze. Przykładami są nazwa, Grupa zasobów, do której należy, lokalizacja, system operacyjny i adres URL pulpitu nawigacyjnego klastra.|
|Pulpity nawigacyjne klastra|Kieruje użytkownika do portalu Ambari skojarzonego z klastrem.|
|SSH + logowanie do klastra|Zapewnia informacje konieczne do uzyskania dostępu do klastra przy użyciu protokołu SSH.|
|Usuń|Usuwa klaster usługi HDInsight.|

## <a name="customize-clusters"></a>Dostosowywanie klastrów

* [Dostosowywanie klastrów usługi HDInsight przy użyciu narzędzia Bootstrap](hdinsight-hadoop-customize-cluster-bootstrap.md)
* [Dostosowywanie klastrów usługi HDInsight opartych na systemie Linux za pomocą akcji skryptu](hdinsight-hadoop-customize-cluster-linux.md)

## <a name="delete-the-cluster"></a>Usuwanie klastra

Zobacz [usuwanie klastra usługi HDInsight przy użyciu przeglądarki, programu PowerShell lub interfejsu wiersza polecenia platformy Azure](./hdinsight-delete-cluster.md).

## <a name="troubleshoot"></a>Rozwiązywanie problemów

W razie problemów podczas tworzenia klastrów usługi HDInsight zapoznaj się z [wymaganiami dotyczącymi kontroli dostępu](./hdinsight-hadoop-customize-cluster-linux.md#access-control).

## <a name="next-steps"></a>Następne kroki

Klaster usługi HDInsight został pomyślnie utworzony. Teraz Dowiedz się, jak korzystać z klastra.

### <a name="apache-hadoop-clusters"></a>Klastry Apache Hadoop

* [Korzystanie z Apache Hive z usługą HDInsight](hadoop/hdinsight-use-hive.md)
* [Korzystanie z MapReduce z usługą HDInsight](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Klastry Apache HBase

* [Wprowadzenie do platformy Apache HBase w usłudze HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Tworzenie aplikacji Java dla platformy Apache HBase w usłudze HDInsight](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="apache-storm-clusters"></a>Klastry Apache Storm

* [Tworzenie topologii języka Java dla Apache Storm w usłudze HDInsight](storm/apache-storm-develop-java-topology.md)
* [Używanie składników języka Python w Apache Storm w usłudze HDInsight](storm/apache-storm-develop-python-topology.md)
* [Wdrażanie i monitorowanie topologii za pomocą Apache Storm w usłudze HDInsight](storm/apache-storm-deploy-monitor-topology-linux.md)

### <a name="apache-spark-clusters"></a>Klastry Apache Spark

* [Tworzenie aplikacji autonomicznej za pomocą Scala](spark/apache-spark-create-standalone-application.md)
* [Zdalne uruchamianie zadań w klastrze Apache Spark przy użyciu oprogramowania Apache usługi Livy](spark/apache-spark-livy-rest-interface.md)
* [Apache Spark z usługą BI: wykonywanie interakcyjnej analizy danych przy użyciu platformy Spark w usłudze HDInsight przy użyciu narzędzi analizy biznesowej](spark/apache-spark-use-bi-tools.md)
* [Apache Spark z Machine Learning: korzystanie z platformy Spark w usłudze HDInsight do przewidywania wyników inspekcji żywności](spark/apache-spark-machine-learning-mllib-ipython.md)
