---
title: Korzystanie z apache Flink for Apache Kafka — usługi Azure Event Hubs | Dokumenty firmy Microsoft
description: Ten artykuł zawiera informacje dotyczące sposobu łączenia aplikacji Apache Flink z centrum zdarzeń platformy Azure z włączoną usługą Azure
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: db877279bcfa7e132841e342cfc25b66bb3ec384
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80283603"
---
# <a name="use-apache-flink-with-azure-event-hubs-for-apache-kafka"></a>Korzystanie z platformy Apache Flink z usługą Azure Event Hubs dla platformy Apache Kafka
W tym samouczku pokazano, jak połączyć apache Flink z centrum zdarzeń bez zmiany klientów protokołu lub uruchamiania własnych klastrów. Usługa Azure Event Hubs obsługuje [apache kafka w wersji 1.0.](https://kafka.apache.org/10/documentation.html).

Jedną z kluczowych zalet korzystania z Apache Kafka jest ekosystem struktur, z którymi może się połączyć. Usługa Event Hubs łączy elastyczność platformy Kafka ze skalowalnością, spójnością i obsługą ekosystemu platformy Azure.

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Tworzenie przestrzeni nazw usługi Event Hubs
> * Klonowanie projektu przykładowego
> * Uruchom producenta Flink 
> * Uruchamianie konsumenta Flink

> [!NOTE]
> Ten przykład jest dostępny w witrynie [GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/flink)

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten samouczek, upewnij się, że masz następujące wymagania wstępne:

* Zapoznaj się z artykułem [Usługa Event Hubs dla platformy Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md). 
* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* [Zestaw java development (JDK) 1.7+](https://aka.ms/azure-jdks)
    * W systemie Ubuntu uruchom polecenie `apt-get install default-jdk`, aby zainstalować zestaw JDK.
    * Upewnij się, że zmienna środowiskowa JAVA_HOME wskazuje folder, w którym zainstalowano zestaw JDK.
* [Pobierz](https://maven.apache.org/download.cgi) i [zainstaluj](https://maven.apache.org/install.html) archiwum binarne Maven
    * W systemie Ubuntu możesz uruchomić polecenie `apt-get install maven`, aby zainstalować narzędzie Maven.
* [Git](https://www.git-scm.com/downloads)
    * W systemie Ubuntu możesz uruchomić polecenie `sudo apt-get install git`, aby zainstalować usługę Git.

## <a name="create-an-event-hubs-namespace"></a>Tworzenie przestrzeni nazw usługi Event Hubs

Obszar nazw centrum zdarzeń jest wymagany do wysyłania lub odbierania z dowolnej usługi Centrum zdarzeń. Zobacz [Tworzenie centrów zdarzeń z włączoną platformą Kafka, aby](event-hubs-create.md) uzyskać informacje na temat uzyskiwania punktu końcowego usługi Event Hubs Platformy Kafka. Pamiętaj, aby skopiować parametry połączenia usługi Event Hubs do późniejszego użycia.

## <a name="clone-the-example-project"></a>Klonowanie projektu przykładowego

Teraz, gdy masz parametry połączenia usługi Event Hubs, sklonuj usługi Azure Event `flink` Hubs dla repozytorium platformy Kafka i przejdź do podfolderu:

```shell
git clone https://github.com/Azure/azure-event-hubs-for-kafka.git
cd azure-event-hubs-for-kafka/tutorials/flink
```

## <a name="run-flink-producer"></a>Uruchom producenta Flink

Za pomocą podanego przykładu producenta Flink wysyłaj wiadomości do usługi Event Hubs.

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Podaj punkt końcowy usługi Event Hubs Platformy Kafka

#### <a name="producerconfig"></a>producent.config

Zaktualizuj `bootstrap.servers` wartości i `sasl.jaas.config` wartości, `producer/src/main/resources/producer.config` aby skierować producenta do punktu końcowego usługi Event Hubs Platformy Kafka z poprawnym uwierzytelnianiem.

```xml
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
client.id=FlinkExampleProducer
sasl.mechanism=PLAIN
security.protocol=SASL_SSL
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required \
   username="$ConnectionString" \
   password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

### <a name="run-producer-from-the-command-line"></a>Uruchom producenta z wiersza polecenia

Aby uruchomić producenta z wiersza polecenia, wygenerować JAR, a następnie uruchomić z wewnątrz Maven (lub wygenerować JAR przy użyciu Maven, a następnie uruchomić w języku Java, dodając niezbędne Kafka JAR(s) do ścieżki klasy):

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="FlinkTestProducer"
```

Producent rozpocznie teraz wysyłanie zdarzeń do centrum zdarzeń z `test` włączoną platformą Kafka na temat i drukowanie zdarzeń do stdout.

## <a name="run-flink-consumer"></a>Uruchamianie konsumenta Flink

Korzystając z podanego przykładu konsumenta, odbieraj wiadomości z centrów zdarzeń z włączoną platformą Kafka.

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Podaj punkt końcowy usługi Event Hubs Platformy Kafka

#### <a name="consumerconfig"></a>consumer.config

Zaktualizuj `bootstrap.servers` wartości i `sasl.jaas.config` wartości, `consumer/src/main/resources/consumer.config` aby skierować konsumenta do punktu końcowego usługi Event Hubs Platformy Kafka z poprawnym uwierzytelnianiem.

```xml
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
group.id=FlinkExampleConsumer
sasl.mechanism=PLAIN
security.protocol=SASL_SSL
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required \
   username="$ConnectionString" \
   password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

### <a name="run-consumer-from-the-command-line"></a>Uruchamianie konsumenta z wiersza polecenia

Aby uruchomić konsumenta z wiersza polecenia, wygenerować JAR, a następnie uruchomić z wewnątrz Maven (lub wygenerować JAR przy użyciu Maven, a następnie uruchomić w języku Java, dodając niezbędne Kafka JAR(s) do ścieżki klasy):

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="FlinkTestConsumer"
```

Jeśli centrum zdarzeń zawiera zdarzenia (na przykład, jeśli producent jest również uruchomiony), konsument `test`zaczyna teraz odbierać zdarzenia z tematu .

Zapoznaj się [z Flink's Kafka Connector Guide,](https://ci.apache.org/projects/flink/flink-docs-stable/dev/connectors/kafka.html) aby uzyskać bardziej szczegółowe informacje na temat łączenia flink z platformą Kafka.

## <a name="next-steps"></a>Następne kroki
W tym samouczku dowiesz się, jak połączyć apache Flink z centrum zdarzeń bez zmiany klientów protokołu lub uruchamiania własnych klastrów. W ramach tego samouczka wykonano następujące kroki: 

> [!div class="checklist"]
> * Tworzenie przestrzeni nazw usługi Event Hubs
> * Klonowanie projektu przykładowego
> * Uruchom producenta Flink 
> * Uruchamianie konsumenta Flink

Aby dowiedzieć się więcej na temat usługi Event Hubs i usługi Event Hubs dla platformy Kafka, zobacz następujący temat:  

- [Dowiedz się więcej na temat usługi Event Hubs](event-hubs-what-is-event-hubs.md)
- [Usługa Event Hubs dla platformy Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md)
- [Jak utworzyć centra Event Hubs z obsługą platformy Kafka](event-hubs-create.md)
- [Przesyłanie strumieniowe do usługi Event Hubs z aplikacji platformy Kafka](event-hubs-quickstart-kafka-enabled-event-hubs.md)
- [Dublowanie brokera platformy Kafka w centrum zdarzeń](event-hubs-kafka-mirror-maker-tutorial.md)
- [Łączenie platformy Apache Spark z centrum zdarzeń](event-hubs-kafka-spark-tutorial.md)
- [Integracja platformy Kafka Connect z centrum zdarzeń](event-hubs-kafka-connect-tutorial.md)
- [Łączenie strumieni Akka z centrum zdarzeń](event-hubs-kafka-akka-streams-tutorial.md)
- [Eksplorowanie przykładów w witrynie GitHub](https://github.com/Azure/azure-event-hubs-for-kafka)
