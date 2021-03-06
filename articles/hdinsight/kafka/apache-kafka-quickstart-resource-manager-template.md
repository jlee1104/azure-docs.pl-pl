---
title: 'Szybki start: Apache Kafka przy użyciu usługi Azure Resource Manager — HDInsight'
description: W tym przewodniku Szybki start dowiesz się, jak utworzyć klaster Platformy Kafka apache w usłudze Azure HDInsight przy użyciu szablonu usługi Azure Resource Manager. Zdobędziesz także informacje o tematach, subskrybentach i odbiorcach platformy Kafka.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 03/13/2020
ms.openlocfilehash: f5f92044a0274b809388eeb164be9f1587013e0b
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "80064603"
---
# <a name="quickstart-create-apache-kafka-cluster-in-azure-hdinsight-using-resource-manager-template"></a>Szybki start: tworzenie klastra Apache Kafka w usłudze Azure HDInsight przy użyciu szablonu Menedżera zasobów

W tym przewodniku Szybki start można użyć szablonu usługi Azure Resource Manager do utworzenia klastra [Platformy Apache Kafka](./apache-kafka-introduction.md) w usłudze Azure HDInsight. Kafka to rozproszona platforma przesyłania strumieniowego typu „open source”. Jest ona często używana jako broker komunikatów, ponieważ oferuje funkcje podobne do kolejki komunikatów dotyczących publikowania i subskrybowania.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Dostęp do interfejsu API platformy Kafka mogą uzyskać tylko zasoby będące w tej samej sieci wirtualnej. W tym przewodniku Szybki start uzyskasz dostęp do klastra bezpośrednio przy użyciu protokołu SSH. Aby do platformy Kafka podłączyć inne usługi, sieci lub maszyny wirtualne, należy najpierw utworzyć sieć wirtualną, a następnie utworzyć zasoby w obrębie tej sieci. Aby uzyskać więcej informacji, zobacz dokument [Connect to Apache Kafka using a virtual network (Nawiązywanie połączenia z platformą Apache Kafka za pomocą sieci wirtualnej)](apache-kafka-connect-vpn-gateway.md).

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

## <a name="create-an-apache-kafka-cluster"></a>Tworzenie klastra platformy Apache Kafka

### <a name="review-the-template"></a>Przejrzyj szablon

Szablon używany w tym przewodniku Szybki start pochodzi z [szablonów szybki start platformy Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-kafka).

:::code language="json" source="~/quickstart-templates/101-hdinsight-kafka/azuredeploy.json" range="1-150":::

Dwa zasoby platformy Azure są zdefiniowane w szablonie:

* [Microsoft.Storage/storageKonta:](https://docs.microsoft.com/azure/templates/microsoft.storage/storageaccounts)tworzenie konta usługi Azure Storage.
* [Microsoft.HDInsight/cluster](https://docs.microsoft.com/azure/templates/microsoft.hdinsight/clusters): tworzenie klastra HDInsight.

### <a name="deploy-the-template"></a>Wdrożenie szablonu

1. Wybierz przycisk **Wdrażanie na platformie Azure** poniżej, aby zalogować się na platformę Azure i otworzyć szablon Menedżera zasobów.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fhdinsight-kafka-java-get-started%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="./media/apache-kafka-quickstart-resource-manager-template/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

1. Wprowadź lub wybierz poniższe wartości:

    |Właściwość |Opis |
    |---|---|
    |Subskrypcja|Z listy rozwijanej wybierz subskrypcję platformy Azure, która jest używana dla klastra.|
    |Grupa zasobów|Z listy rozwijanej wybierz istniejącą grupę zasobów lub wybierz pozycję **Utwórz nowy**.|
    |Lokalizacja|Wartość zostanie automatycznie wypełniona lokalizacją używaną dla grupy zasobów.|
    |Nazwa klastra|Podaj globalnie unikatową nazwę. W tym szablonie należy używać tylko małych liter i cyfr.|
    |Nazwa użytkownika logowania klastra|Podaj nazwę użytkownika, domyślnie jest **admin**.|
    |Hasło logowania klastra|Podaj hasło. Hasło musi mieć co najmniej 10 znaków i musi zawierać co najmniej jedną cyfrę, jedną wielką literę i jedną wielką literę, jedną znak niealtalumeryczny (z wyjątkiem znaków " " ). |
    |Nazwa użytkownika Ssh|Podaj nazwę użytkownika, domyślnie jest **sshuser**|
    |Hasło Ssh|Podaj hasło.|

    ![Zrzut ekranu przedstawiający właściwości szablonu](./media/apache-kafka-quickstart-resource-manager-template/resource-manager-template-kafka.png)

1. Zapoznaj się z **regulaminem**. Następnie wybierz **zgadzam się z warunkami określonymi powyżej,** a następnie **zakup.** Otrzymasz powiadomienie, że wdrożenie jest w toku. Utworzenie klastra trwa około 20 minut.

## <a name="review-deployed-resources"></a>Przeglądanie wdrożonych zasobów

Po utworzeniu klastra otrzymasz powiadomienie **o pomyślnym wdrożeniu** z łączem **Przejdź do zasobu.** Strona Grupa zasobów będzie zawierać listę nowego klastra usługi HDInsight oraz domyślny magazyn skojarzony z klastrem. Każdy klaster ma konto [usługi Azure Storage](../hdinsight-hadoop-use-blob-storage.md) lub zależność konta usługi Azure Data Lake [Storage.](../hdinsight-hadoop-use-data-lake-store.md) Jest on określany jako domyślne konto magazynu. Klaster USŁUGI HDInsight i jego domyślne konto magazynu muszą być współlokowane w tym samym regionie platformy Azure. Usunięcie klastrów nie powoduje usunięcia konta magazynu.

## <a name="get-the-apache-zookeeper-and-broker-host-information"></a>Pobierz informacje dotyczących hostów Apache Zookeeper i Broker

Podczas pracy z platformą Kafka musisz znać hosty *Apache Zookeeper* i *Broker*. Te hosty są używane z interfejsem API platformy Kafka i wieloma narzędziami oferowanymi z platformą Kafka.

W tej sekcji uzyskasz informacje o hoście z interfejsu API REST Ambari w klastrze.

1. Użyj [polecenia ssh,](../hdinsight-hadoop-linux-use-ssh-unix.md) aby połączyć się z klastrem. Edytuj poniższe polecenie, zastępując clustername nazwą klastra, a następnie wprowadź polecenie:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Aby zainstalować narzędzie, z połączenia SSH `jq` użyj następującego polecenia. To narzędzie służy do analizowania dokumentów JSON i jest przydatne podczas pobierania informacji o hoście:

    ```bash
    sudo apt -y install jq
    ```

1. Aby ustawić zmienną środowiskową na nazwę klastra, użyj następującego polecenia:

    ```bash
    read -p "Enter the Kafka on HDInsight cluster name: " CLUSTERNAME
    ```

    Po wyświetleniu monitu wprowadź nazwę klastra platformy Kafka.

1. Aby ustawić zmienną środowiskową z informacjami o hoście Zookeeper, użyj poniższego polecenia. Polecenie pobiera wszystkie hosty Zookeeper, a następnie zwraca tylko dwa pierwsze wpisy. Taka nadmiarowość jest wymagana, jeśli jeden z hostów będzie nieosiągalny.

    ```bash
    export KAFKAZKHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`
    ```

    Po wyświetleniu monitu wprowadź hasło dla konta logowania klastra (nie dla konta SSH).

1. Aby sprawdzić, czy zmienna środowiskowa jest poprawnie ustawiona, użyj następującego polecenia:

    ```bash
     echo '$KAFKAZKHOSTS='$KAFKAZKHOSTS
    ```

    To polecenie zwraca informacje podobne do następującego tekstu:

    `zk0-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181,zk2-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181`

1. Aby ustawić zmienną środowiskową na informacje hosta brokera platformy Kafka, użyj następującego polecenia:

    ```bash
    export KAFKABROKERS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`
    ```

    Po wyświetleniu monitu wprowadź hasło dla konta logowania klastra (nie dla konta SSH).

1. Aby sprawdzić, czy zmienna środowiskowa jest poprawnie ustawiona, użyj następującego polecenia:

    ```bash
    echo '$KAFKABROKERS='$KAFKABROKERS
    ```

    To polecenie zwraca informacje podobne do następującego tekstu:

    `wn1-kafka.eahjefxxp1netdbyklgqj5y1ud.cx.internal.cloudapp.net:9092,wn0-kafka.eahjefxxp1netdbyklgqj5y1ud.cx.internal.cloudapp.net:9092`

## <a name="manage-apache-kafka-topics"></a>Zarządzanie tematami platformy Apache Kafka

Platforma Kafka przechowuje strumienie danych w *tematach*. Tematami można zarządzać za pomocą narzędzia `kafka-topics.sh`.

* **Aby utworzyć temat**, użyj następującego polecenia, korzystając z połączenia SSH:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic test --zookeeper $KAFKAZKHOSTS
    ```

    To polecenie umożliwia nawiązanie połączenia z hostem Zookeeper przy użyciu informacji o hoście przechowywanych w elemencie `$KAFKAZKHOSTS`. Następnie tworzy ono temat platformy Kafka o nazwie **test**.

    * Dane przechowywane w tym temacie są podzielone między osiem partycji.

    * Każda partycja jest replikowana w trzech węzłach procesu roboczego w klastrze.

        Jeśli klaster został utworzony w regionie świadczenia usługi Azure, który udostępnia trzy domeny błędów, użyj współczynnika replikacji o wartości 3. W przeciwnym razie użyj współczynnika replikacji o wartości 4.
        
        W regionach z trzema domenami błędów współczynnik replikacji o wartości 3 umożliwia rozmieszczenie replik w różnych domenach błędów. W regionach z dwoma domenami błędów współczynnik replikacji o wartości cztery umożliwia równomierne rozmieszczenie replik między domenami.
        
        Aby uzyskać informacje dotyczące liczby domen błędów w regionie, zobacz dokument [Availability of Linux virtual machines (Dostępność maszyn wirtualnych z systemem Linux)](../../virtual-machines/windows/manage-availability.md#use-managed-disks-for-vms-in-an-availability-set).

        Platforma Kafka nie zna domen błędów platformy Azure. Utworzone repliki partycji tematów mogą nie zostać prawidłowo rozpowszechnione w celu zapewnienia wysokiej dostępności.

        Aby zapewnić wysoką dostępność, użyj [narzędzia do ponownego równoważenia partycji platformy Apache Kafka](https://github.com/hdinsight/hdinsight-kafka-tools). To narzędzie należy uruchomić, korzystając z połączenia SSH z węzłem głównym klastra platformy Kafka.

        Aby zapewnić najwyższą dostępność danych na platformie Kafka, należy stosować ponowne równoważenie replik partycji dla tematu, gdy:

        * Tworzysz nowy temat lub partycję

        * Skalujesz klaster w górę

* **Aby wyświetlić listę tematów**, użyj następującego polecenia:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $KAFKAZKHOSTS
    ```

    To polecenie wyświetla listę dostępnych tematów w klastrze platformy Kafka.

* **Aby usunąć temat**, użyj następującego polecenia:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --delete --topic topicname --zookeeper $KAFKAZKHOSTS
    ```

    To polecenie usuwa temat o nazwie `topicname`.

    > [!WARNING]  
    > W przypadku usunięcia utworzonego wcześniej tematu `test` konieczne będzie jego ponowne utworzenie. Jest on używany w czynnościach opisanych w dalszej części tego dokumentu.

Aby uzyskać więcej informacji na temat poleceń dostępnych w narzędziu `kafka-topics.sh`, użyj następującego polecenia:

```bash
/usr/hdp/current/kafka-broker/bin/kafka-topics.sh
```

## <a name="produce-and-consume-records"></a>Tworzenie i używanie rekordów

Platforma Kafka przechowuje *rekordy* w tematach. Rekordy są tworzone przez *producentów* i używane przez *odbiorców*. Producenci i odbiorcy komunikują się z usługą *brokera platformy Kafka*. Każdy węzeł procesu roboczego w klastrze usługi HDInsight jest hostem brokera platformy Kafka.

Poniżej przedstawiono procedurę zapisywania rekordów w utworzonym wcześniej temacie testowym i odczytywania ich za pomocą odbiorcy:

1. Aby zapisać rekordy w temacie, użyj narzędzia `kafka-console-producer.sh`, korzystając z połączenia SSH:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $KAFKABROKERS --topic test
    ```

    Po wykonaniu tego polecenia przejdziesz do pustego wiersza.

1. Wprowadź wiadomość tekstową do pustego wiersza, a następnie naciśnij klawisz Enter. Wprowadź w ten sposób kilka wiadomości, a następnie użyj kombinacji klawiszy **Ctrl + C**, aby powrócić do normalnego monitu. Każdy wiersz jest wysyłany do tematu platformy Kafka jako oddzielny rekord.

1. Aby odczytać rekordy z tematu, użyj narzędzia `kafka-console-consumer.sh`, korzystając z połączenia SSH:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server $KAFKABROKERS --topic test --from-beginning
    ```

    To polecenie umożliwia pobranie rekordów z tematu i ich wyświetlenie. Polecenie `--from-beginning` informuje odbiorcę, aby rozpocząć od początku strumienia w celu pobrania wszystkich rekordów.

    Jeśli używasz starszej wersji platformy Kafka, zamień `--bootstrap-server $KAFKABROKERS` na `--zookeeper $KAFKAZKHOSTS`.

1. Użyj klawiszy __Ctrl+C__, aby zatrzymać odbiorcę.

Producentów i odbiorców można również utworzyć programowo. Przykład korzystania z tego interfejsu API znajduje się w dokumencie [Apache Kafka Producer and Consumer API with HDInsight](apache-kafka-producer-consumer-api.md) (Interfejs API producenta i odbiorcy platformy Apache Kafka w usłudze HDInsight).

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po zakończeniu szybkiego startu można usunąć klaster. Dzięki funkcji HDInsight dane są przechowywane w usłudze Azure Storage, dzięki czemu można bezpiecznie usunąć klaster, gdy nie jest używany. Naliczana jest również opłata za klaster HDInsight, nawet jeśli nie jest używana. Ponieważ opłaty za klaster są wielokrotnie większe niż opłaty za magazyn, ma ekonomiczny sens usuwanie klastrów, gdy nie są używane.

W witrynie Azure Portal przejdź do klastra i wybierz pozycję **Usuń**.

![Szablon Menedżera zasobów HBase](./media/apache-kafka-quickstart-resource-manager-template/azure-portal-delete-kafka.png)

Dodatkowo możesz wybrać nazwę grupy zasobów, aby otworzyć stronę grupy zasobów, a następnie wybrać pozycję **Usuń grupę zasobów**. Usuwając grupę zasobów, można usunąć zarówno klaster HDInsight, jak i domyślne konto magazynu.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start dowiesz się, jak utworzyć klaster Platformy Kafek apache w usłudze HDInsight przy użyciu szablonu Menedżera zasobów. W następnym artykule dowiesz się, jak utworzyć aplikację, która używa interfejsu API Apache Kafka strumieni i uruchomić go z platformą Kafka w programie HDInsight.

> [!div class="nextstepaction"]
> [Korzystanie z interfejsu API strumieni apache platformy Kafka w usłudze Azure HDInsight](./apache-kafka-streams-api.md)
