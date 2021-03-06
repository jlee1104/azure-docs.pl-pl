---
title: Architektura łączności — usługa Azure Database for MySQL
description: W tym artykule opisano architekturę łączności dla usługi Azure Database dla serwera MySQL.
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 03/16/2020
ms.openlocfilehash: e7098056356c84469d3dc6bd4e3222fbb5b7f908
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79474292"
---
# <a name="connectivity-architecture-in-azure-database-for-mysql"></a>Architektura łączności w bazie danych platformy Azure dla mysql
W tym artykule opisano usługę Azure Database for MySQL architektury łączności, a także jak ruch jest kierowany do usługi Azure Database dla mySQL wystąpienia z klientów zarówno w ramach platformy Azure i poza nią.

## <a name="connectivity-architecture"></a>Architektura łączności
Połączenie z usługą Azure Database for MySQL jest ustanawiane za pośrednictwem bramy, która jest odpowiedzialna za routing połączeń przychodzących do fizycznej lokalizacji serwera w naszych klastrach. Na poniższym diagramie przedstawiono przepływ ruchu.

![Omówienie architektury łączności](./media/concepts-connectivity-architecture/connectivity-architecture-overview-proxy.png)

Gdy klient łączy się z bazą danych, otrzymują parametry połączenia, które łączy się z bramą. Ta brama ma publiczny adres IP, który nasłuchuje portu 3306. Wewnątrz klastra bazy danych ruch jest przekazywany do odpowiedniej usługi Azure Database dla mysql. W związku z tym, aby połączyć się z serwerem, na przykład z sieci firmowych, konieczne jest otwarcie zapory po stronie klienta, aby umożliwić ruch wychodzący, aby móc dotrzeć do naszych bram. Poniżej znajdziesz pełną listę adresów IP używanych przez nasze bramy w powiecie.

## <a name="azure-database-for-mysql-gateway-ip-addresses"></a>Adresy IP bramy Usługi Azure Database dla bramy MySQL
W poniższej tabeli wymieniono podstawowe i pomocnicze pliki IP bramy usługi Azure Database dla mysql dla wszystkich regionów danych. Podstawowy adres IP jest bieżącym adresem IP bramy, a drugi adres IP jest używanym w pracy awaryjnej adresem IP w przypadku awarii podstawowego. Jak wspomniano, klienci powinni zezwolić na wychodzące do obu adresów IP. Drugi adres IP nie nasłuchuje w żadnych usług, dopóki nie zostanie aktywowany przez usługę Azure Database for MySQL do akceptowania połączeń.

| **Nazwa regionu** | **Adresy IP bramy** |
|:----------------|:-------------|
| Australia Środkowa| 20.36.105.0     |
| Australia Central2     | 20.36.113.0     |
| Australia Wschodnia | 13.75.149.87, 40.79.161.1     |
| Australia Południowo-Wschodnia |191.239.192.109, 13.73.109.251     |
| Brazylia Południowa | 104.41.11.5, 191.233.201.8, 191.233.200.16     |
| Kanada Środkowa |40.85.224.249     |
| Kanada Wschodnia | 40.86.226.166     |
| Środkowe stany USA | 23.99.160.139, 13.67.215.62     |
| Chiny Wschodnie | 139.219.130.35     |
| Chiny Wschodnie 2 | 40.73.82.1     |
| Chiny Północne | 139.219.15.17     |
| Chiny Północne 2 | 40.73.50.0     |
| Azja Wschodnia | 191.234.2.139, 52.175.33.150, 13.75.33.20, 13.75.33.21     |
| Wschodnie stany USA | 40.121.158.30, 191.238.6.43     |
| Wschodnie stany USA 2 |40.79.84.180, 191.239.224.107, 52.177.185.181     |
| Francja Środkowa | 40.79.137.0, 40.79.129.1     |
| Niemcy Środkowe | 51.4.144.100     |
| Niemcy Północny wschód | 51.5.144.179     |
| Indie Środkowe | 104.211.96.159     |
| Indie Południowe | 104.211.224.146     |
| Indie Zachodnie | 104.211.160.80     |
| Japonia Wschodnia | 13.78.61.196, 191.237.240.43     |
| Japonia Zachodnia | 104.214.148.156, 191.238.68.11, 40.74.96.7, 40.74.96.6 |
| Korea Środkowa | 52.231.32.42     |
| Korea Południowa | 52.231.200.86     |
| Północno-środkowe stany USA | 23.96.178.199, 23.98.55.75, 52.162.104.35, 52.162.104.36     |
| Europa Północna | 40.113.93.91, 191.235.193.75     |
| Republika Południowej Afryki Północ  | 102.133.152.0     |
| Republika Południowej Afryki Zachód    | 102.133.24.0     |
| Południowo-środkowe stany USA |13.66.62.124, 23.98.162.75, 104.214.16.39, 20.45.120.0     |
| Azja Południowo-Wschodnia | 104.43.15.0, 23.100.117.95, 40.78.233.2, 23.98.80.12     |
| Zjednoczone Emiraty Zjednoczone Emiraty Zjednoczone Emiraty Zjednoczone Emiraty Zjednoczone Emirat | 20.37.72.64     |
| Zjednoczone Emiraty Północne | 65.52.248.0     |
| Południowe Zjednoczone Królestwo | 51.140.184.11     |
| Zachodnie Zjednoczone Królestwo | 51.141.8.11     |
| Zachodnio-środkowe stany USA | 13.78.145.25     |
| Europa Zachodnia | 40.68.37.158, 191.237.232.75     |
| Zachodnie stany USA | 104.42.238.205, 23.99.34.75     |
| Zachodnie stany USA 2 | 13.66.226.202     |
||||

## <a name="connection-redirection"></a>Przekierowanie połączenia

Usługa Azure Database for MySQL obsługuje dodatkowe zasady połączenia, **przekierowanie,** które pomaga zmniejszyć opóźnienia sieci między aplikacjami klienckimi a serwerami MySQL. Dzięki tej funkcji po ustanowieniu początkowej sesji TCP na serwerze Usługi Azure Database for MySQL serwer zwraca klientowi adres wewnętrznej bazy danych węzła obsługującego serwer MySQL. Następnie wszystkie kolejne pakiety przepływają bezpośrednio do serwera, omijając bramę. Ponieważ pakiety przepływają bezpośrednio do serwera, opóźnienie i przepustowość mają lepszą wydajność.

Ta funkcja jest obsługiwana w usłudze Azure Database dla serwerów MySQL z aparatem w wersjach 5.6, 5.7 i 8.0.

Wsparcie przekierowania jest dostępne w rozszerzeniu [PHP mysqlnd_azure,](https://github.com/microsoft/mysqlnd_azure) opracowanym przez firmę Microsoft, i jest dostępne na [PECL](https://pecl.php.net/package/mysqlnd_azure). Zobacz artykuł [o konfigurowaniu przekierowania,](./howto-redirection.md) aby uzyskać więcej informacji na temat używania przekierowania w aplikacjach.

> [!IMPORTANT]
> Obsługa przekierowania w rozszerzeniu [PHP mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) jest obecnie w wersji zapoznawczej.

## <a name="next-steps"></a>Następne kroki

* [Tworzenie reguł zapory usługi Azure Database dla mysql i zarządzanie nimi przy użyciu witryny Azure portal](./howto-manage-firewall-using-portal.md)
* [Tworzenie reguł zapory usługi Azure Database dla mysql i zarządzanie nimi przy użyciu interfejsu wiersza polecenia platformy Azure](./howto-manage-firewall-using-cli.md)
* [Konfigurowanie przekierowania za pomocą usługi Azure Database dla mysql](./howto-redirection.md)